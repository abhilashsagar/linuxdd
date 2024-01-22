Driver Installation and Configuration
======================================

Follow these steps to include your custom Linux kernel driver in the kernel source code.

**Add entries to** ``drivers/hellodriver/Kconfig``:

Create or modify the ``drivers/hellodriver/Kconfig`` file with the following content:

.. code-block:: makefile

   config HELLO_DRIVER
       tristate "Hello Driver Support"
       help
         This enables support for the Hello Driver.

**Add entries to** ``drivers/hellodriver/Makefile``:

Create or modify the ``drivers/hellodriver/Makefile`` file with the following content:

.. code-block:: makefile

   # Specify the source file for your driver
   obj-$(CONFIG_HELLO_DRIVER) += hello_driver.o

**Create** ``drivers/hellodriver/hello_driver.c``:

Create the ``hello_driver.c`` file inside the ``drivers/hellodriver/`` directory with your driver's implementation.

**Include** ``drivers/hellodriver/Kconfig`` **in the top-level** ``drivers/Kconfig``:

Open or create the ``drivers/Kconfig`` file and add the following line:

.. code-block:: makefile

   source "drivers/hellodriver/Kconfig"

**Modify the top-level** ``drivers/Makefile``:

Open or create the ``drivers/Makefile`` file and add the following line to include your driver directory:

.. code-block:: makefile

   obj-$(CONFIG_HELLO_DRIVER) += hellodriver/

**Configure and build the kernel:**

Run the following commands in the terminal:

.. code-block:: bash

   $ make menuconfig

Inside the configuration menu, navigate to "Device Drivers" and enable your driver (``Hello Driver Support``). Save the configuration and exit.

.. code-block:: bash

   $ make
