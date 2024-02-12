# Linux Device Drivers

Hello World Driver
===============

Introduction
------------

This documentation provides information about the MyDriver Linux kernel module.

.. code-block:: c

   #include <linux/module.h>
   #include <linux/kernel.h>

   static int __init mydriver_init(void)
   {
       printk(KERN_INFO "My driver initialized\\n");
       return 0;
   }

   static void __exit mydriver_exit(void)
   {
       printk(KERN_INFO "My driver exited\\n");
   }

   module_init(mydriver_init);
   module_exit(mydriver_exit);

   MODULE_LICENSE("GPL");
   MODULE_AUTHOR("Abhi");
   MODULE_DESCRIPTION("TEMPLATE");

Initialization and Exit
-----------------------

The module has two main functions: initialization and exit.

- :func:`mydriver_init`: Initializes the MyDriver module.

- :func:`mydriver_exit`: Exits the MyDriver module.

Module Information
------------------

- License: GPL

- Author: Abhi

- Description: This module serves as a template.

``module_init`` and ``module_exit``
-----------------------------------

- :func:`mydriver_init`: Initialization function for the MyDriver module.

- :func:`mydriver_exit`: Exit function for the MyDriver module.

``MODULE_LICENSE``, ``MODULE_AUTHOR``, and ``MODULE_DESCRIPTION``
-----------------------------------------------------------------

These macros provide information about the module.

``printk`` Messages
-------------------

The module uses ``printk`` to log messages to the kernel log.

- Initialization message: "My driver initialized"

- Exit message: "My driver exited"

``MODULE_LICENSE``, ``MODULE_AUTHOR``, and ``MODULE_DESCRIPTION``
-----------------------------------------------------------------

These macros provide information about the module.

``MODULE_LICENSE``: GPL

``MODULE_AUTHOR``: Abhi

``MODULE_DESCRIPTION``: TEMPLATE
