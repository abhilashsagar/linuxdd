.. SPDX-License-Identifier: GPL-2.0
.. _mydriver:

===========================
Hello World Device tree
===========================
Introduction
------------

The "mydriver" Linux kernel module is a simple example that demonstrates the integration of a platform driver with a Device Tree overlay. The module reads a device ID from the Device Tree during initialization and performs some basic operations in the probe function. Additionally, a Device Tree overlay is provided to configure the device in the Device Tree.
  
Driver Code
-----------

The following is the C code for the "mydriver" Linux kernel module:

.. code:: c

    #include <linux/module.h>
    #include <linux/kernel.h>
    #include <linux/platform_device.h>
    #include <linux/of.h>

    static int mydriver_probe(struct platform_device *pdev)
    {
        struct device_node *np = pdev->dev.of_node;
        int device_id;

        if (!np) {
            pr_err("Device tree node not found\\n");
            return -ENODEV;
        }

        // Read the device ID property
        if (of_property_read_u32(np, "mydriver,device-id", &device_id)) {
            pr_err("Failed to read device ID from device tree\\n");
            return -EINVAL;
        }

        pr_info("Device ID from device tree: %d\\n", device_id);

        // Your driver logic goes here

        return 0;
    }

    static int mydriver_remove(struct platform_device *pdev)
    {
        // Your removal logic goes here
        return 0;
    }

    static const struct of_device_id mydriver_of_match[] = {
        { .compatible = "mydriver,device", },
        {},
    };
    MODULE_DEVICE_TABLE(of, mydriver_of_match);

    static struct platform_driver mydriver_driver = {
        .driver = {
            .name = "mydriver",
            .owner = THIS_MODULE,
            .of_match_table = mydriver_of_match,
        },
        .probe = mydriver_probe,
        .remove = mydriver_remove,
    };

    module_platform_driver(mydriver_driver);

    MODULE_LICENSE("GPL");
    MODULE_AUTHOR("Abhi");

Explanation
------------

1. **Include Headers:**
   - Include necessary Linux kernel headers for modules, kernel, platform devices, and device tree handling.

2. **Probe Function (`mydriver_probe`):**
   - The `mydriver_probe` function is the probe function called when a device with a matching compatible string is found.
   - It retrieves the device tree node associated with the platform device.
   - Checks if the device tree node exists; if not, it prints an error message and returns an error code.
   - Reads the "mydriver,device-id" property from the device tree node and stores it in the `device_id` variable.
   - If reading fails, it prints an error message and returns an error code.
   - Prints the device ID to the kernel log.
   - You can add your specific driver logic in the designated section.

3. **Remove Function (`mydriver_remove`):**
   - The `mydriver_remove` function is called when the driver is being removed.
   - You can implement any cleanup logic specific to your driver in this function.

4. **Device Table (`mydriver_of_match`):**
   - Defines a device table entry with the compatible string "mydriver,device."

5. **Platform Driver Structure (`mydriver_driver`):**
   - Defines the platform driver structure, specifying the driver name, owner, and the device table for matching.
   - Associates the probe and remove functions.

6. **Module Initialization (`module_platform_driver`):**
   - Initializes the module and registers the platform driver.

7. **Module License:**
   - Specifies the GPL license for the module.

Device Tree Overlay
-------------------

The following is the Device Tree overlay for the "mydriver" module:

.. code:: dts

    / {
        mydriver_node {
            compatible = "mydriver,device";
            mydriver,device-id = <123>; 
        };
    };

Explanation
------------

1. **Device Tree Overlay:**
   - Defines a device tree overlay specifying a device node named `mydriver_node`.
   - Specifies the compatible string as "mydriver,device," indicating compatibility with the driver.
   - Sets the "mydriver,device-id" property to a specific value (123 in this example). Replace this with your actual device ID.

2. **Device Node (`mydriver_node`):**
   - Represents the device node in the device tree overlay.
   - Specifies the compatible string to match with the driver.

3. **Device ID Property (`mydriver,device-id`):**
   - Sets a specific device ID for the driver to read during initialization.
   - The value (123) is just an example; replace it with the actual device ID you want to use.
