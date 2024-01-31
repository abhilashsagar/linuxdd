.. _gpio-driver-documentation:

GPIO Device Driver Documentation
===============================

C Code for the GPIO Device Driver
----------------------------------

.. code-block:: c

    #include <linux/module.h>
    #include <linux/kernel.h>
    #include <linux/gpio.h>
    #include <linux/of.h>
    #include <linux/of_gpio.h>
    #include <linux/platform_device.h>

    #define DRIVER_NAME "my_gpio_driver"

    struct my_gpio_data {
        int gpio_pin;
    };

    static int my_gpio_probe(struct platform_device *pdev)
    {
        struct device_node *np = pdev->dev.of_node;
        struct my_gpio_data *data;
        int ret;

        if (!np) {
            pr_err("Device tree node not found\\n");
            return -ENODEV;
        }

        data = devm_kzalloc(&pdev->dev, sizeof(*data), GFP_KERNEL);
        if (!data)
            return -ENOMEM;

        // Read the GPIO pin property
        data->gpio_pin = of_get_gpio(np, 0);
        if (data->gpio_pin < 0) {
            pr_err("Failed to get GPIO pin from device tree\\n");
            return data->gpio_pin;
        }

        pr_info("GPIO pin from device tree: %d\\n", data->gpio_pin);

        // Your GPIO driver logic goes here

        // Store the driver-specific data in the platform device
        platform_set_drvdata(pdev, data);

        return 0;
    }

    static int my_gpio_remove(struct platform_device *pdev)
    {
        // Your removal logic goes here
        return 0;
    }

    static const struct of_device_id my_gpio_of_match[] = {
        { .compatible = "my_gpio_device", },
        {},
    };
    MODULE_DEVICE_TABLE(of, my_gpio_of_match);

    static struct platform_driver my_gpio_driver = {
        .driver = {
            .name = DRIVER_NAME,
            .owner = THIS_MODULE,
            .of_match_table = my_gpio_of_match,
        },
        .probe = my_gpio_probe,
        .remove = my_gpio_remove,
    };

    module_platform_driver(my_gpio_driver);

    MODULE_LICENSE("GPL");
    MODULE_AUTHOR("Your Name");

Device Tree Snippet for the GPIO Device
---------------------------------------

.. code-block:: dts

    /dts-v1/;
    /plugin/;

    / {
        compatible = "my_board";

        my_gpio_node {
            compatible = "my_gpio_device";
            gpios = <&gpio0 10 GPIO_ACTIVE_HIGH>; // Adjust the GPIO controller and pin accordingly
        };
    };

In this documentation:

- The C code for the GPIO device driver is presented in a code block using the `.. code-block:: c` directive.

- The Device Tree snippet is presented in a code block using the `.. code-block:: dts` directive.

.. _block-diagram-documentation:

Block Diagram Documentation
==========================

Device Tree Block Diagram
-------------------------

.. graphviz::

    digraph device_tree_diagram {
        rankdir=TB;
        node [shape=box];
        "Device Tree Specification" -> "Device Node: mydriver_node\n+ reg property: <0x10000000 0x1000>";
        "Device Node: mydriver_node\n+ reg property: <0x10000000 0x1000>" -> "Linux Kernel";
        "Linux Kernel" -> "mydriver Probe\n+ of_address_to_resource\n+ Memory Mapping";
        "mydriver Probe\n+ of_address_to_resource\n+ Memory Mapping" -> "Device Driver Initialization\n+ Memory Mapping\n+ Access Device\n+ Operations";
    }



This diagram illustrates the flow from the Device Tree specification to the device driver's initialization and operation. The `reg` property in the Device Tree is a key part of this process, providing information about the memory-mapped region associated with the device. The driver uses this information for memory mapping and subsequent interactions with the device.

GPIO Block Diagram
------------------

.. graphviz::

    digraph gpio_block_diagram {
        rankdir=TB;
        node [shape=box];
        "User Space" -> "User Application";
        "User Application" -> "Memory-Mapped Access Interface" [label="Memory-Mapped Access"];
        "Memory-Mapped Access Interface" -> "Kernel Space";
        "Kernel Space" -> "Linux Kernel";
        "Linux Kernel" -> "Memory-Mapped Access Implementation";
        "Memory-Mapped Access Implementation" -> "GPIO Controller (Memory-Mapped)" [label="Memory-Mapped Access"];
        "GPIO Controller (Memory-Mapped)" -> "Hardware";
    }


In this GPIO block diagram:

- **User Space:** Represents the user-level environment where applications run. User applications can interact with the GPIO controller through memory-mapped access.

- **Kernel Space:** The Linux kernel includes a GPIO subsystem that manages GPIO pins and provides APIs for accessing and controlling GPIOs. The GPIO subsystem includes a memory-mapped access interface.

- **Memory-Mapped Access Interface:** This represents the abstraction layer that allows user-space applications to access the GPIO controller using memory-mapped addresses. The memory-mapped access is facilitated by the GPIO subsystem.

- **GPIO Controller:** The GPIO controller is part of the hardware (Processor IP) and includes registers for configuring GPIO pins, reading input states, and writing output values. The GPIO controller is accessible through memory-mapped addresses.

