Driver Installation and Configuration
======================================

Follow these steps to include your custom Linux kernel driver in the kernel source code.

1. **Add entries to** ``drivers/hellodriver/Kconfig``:

   Create or modify the ``drivers/hellodriver/Kconfig`` file with the following content::

      .. code-block:: makefile

         config HELLO_DRIVER
             tristate "Hello Driver Support"
             help
               This enables support for the Hello Driver.

2. **Add entries to** ``drivers/hellodriver/Makefile``:

   Create or modify the ``drivers/hellodriver/Makefile`` file with the following content::

      .. code-block:: makefile

         # Specify the source file for your driver
         obj-$(CONFIG_HELLO_DRIVER) += hello_driver.o

3. **Create** ``drivers/hellodriver/hello_driver.c``:

   Create the ``hello_driver.c`` file inside the ``drivers/hellodriver/`` directory with your driver's implementation.

4. **Include** ``drivers/hellodriver/Kconfig`` **in the top-level** ``drivers/Kconfig``:

   Open or create the ``drivers/Kconfig`` file and add the following line::

      .. code-block:: makefile

         source "drivers/hellodriver/Kconfig"

5. **Modify the top-level** ``drivers/Makefile``:

   Open or create the ``drivers/Makefile`` file and add the following line to include your driver directory::

      .. code-block:: makefile

         obj-$(CONFIG_HELLO_DRIVER) += hellodriver/

6. **Configure and build the kernel:**

   Run the following commands in the terminal::

      ```bash
      make menuconfig
      ```

      Inside the configuration menu, navigate to "Device Drivers" and enable your driver (``Hello Driver Support``). Save the configuration and exit.

      ```bash
      make
      ```

   This will build the Linux kernel with your driver if the configuration option ``CONFIG_HELLO_DRIVER`` is selected.

7. **Directory Structure:**

   Your directory structure should look like this::

      .. code-block:: none

         drivers/
         |-- hellodriver/
         |   |-- Kconfig
         |   |-- Makefile
         |   |-- hello_driver.c
         |-- Kconfig
         |-- Makefile
