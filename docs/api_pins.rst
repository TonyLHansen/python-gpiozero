====
Pins
====

.. currentmodule:: gpiozero

As of release 1.1, the GPIO Zero library can be roughly divided into two
things: pins and the devices that are connected to them. The majority of the
documentation focuses on devices as pins are below the level that most users
are concerned with. However, some users may wish to take advantage of the
capabilities of alternative GPIO implementations or (in future) use GPIO
extender chips. This is the purpose of the pins portion of the library.

When you construct a device, you pass in a GPIO pin number. However, what the
library actually expects is a :class:`Pin` implementation. If it finds a simple
integer number instead, it uses one of the following classes to provide the
:class:`Pin` implementation (classes are listed in favoured order):

1. :class:`gpiozero.pins.rpigpio.RPiGPIOPin`

2. :class:`gpiozero.pins.rpio.RPIOPin`

3. :class:`gpiozero.pins.pigpiod.PiGPIOPin`

4. :class:`gpiozero.pins.native.NativePin`

You can change the default pin implementation by over-writing the
``pin_factory`` global in the ``devices`` module like so::

    from gpiozero.pins.native import NativePin
    import gpiozero.devices
    # Force the default pin implementation to be NativePin
    gpiozero.devices.pin_factory = NativePin

    from gpiozero import LED

    # This will now use NativePin instead of RPiGPIOPin
    led = LED(16)

``pin_factory`` is a concrete descendent of the abstract :class:`Pin` class.
The descendent may take additional parameters in its constructor provided they
are optional; GPIO Zero will expect to be able to construct instances with
nothing more than an integer pin number.

However, the descendent may take default information from additional sources.
For example, to default to creating pins with
:class:`gpiozero.pins.pigpiod.PiGPIOPin` on a remote pi called ``remote-pi``
you can set the :envvar:`PIGPIO_ADDR` environment variable when running your
script::

    $ PIGPIO_ADDR=remote-pi python my_script.py

It is worth noting that instead of passing an integer to device constructors,
you can pass an object derived from :class:`Pin` itself::

    from gpiozero.pins.native import NativePin
    from gpiozero import LED

    led = LED(NativePin(16))

In future, this separation of pins and devices should also permit the library
to utilize pins that are part of IO extender chips. For example::

    from gpiozero import IOExtender, LED

    ext = IOExtender()
    led = LED(ext.pins[0])
    led.on()

.. warning::

    While the devices API is now considered stable and won't change in
    backwards incompatible ways, the pins API is *not* yet considered stable.
    It is potentially subject to change in future versions. We welcome any
    comments from testers!

.. warning::

    The astute and mischievous reader may note that it is possible to mix pin
    implementations, e.g. using ``RPiGPIOPin`` for one pin, and ``NativePin``
    for another. This is unsupported, and if it results in your script
    crashing, your components failing, or your Raspberry Pi turning into an
    actual raspberry pie, you have only yourself to blame.


RPiGPIOPin
==========

.. autoclass:: gpiozero.pins.rpigpio.RPiGPIOPin


RPIOPin
=======

.. autoclass:: gpiozero.pins.rpio.RPIOPin


PiGPIOPin
=========

.. autoclass:: gpiozero.pins.pigpiod.PiGPIOPin


NativePin
=========

.. autoclass:: gpiozero.pins.native.NativePin


Abstract Pin
============

.. autoclass:: Pin
    :members:


Local Pin
=========

.. autoclass:: LocalPin
    :members:


Utilities
=========

The pins module also contains a database of information about the various
revisions of Raspberry Pi. This is used internally to raise warnings when
non-physical pins are used, or to raise exceptions when pull-downs are
requested on pins with physical pull-up resistors attached. The following
functions and classes can be used to query this database:

.. autofunction:: pi_info

.. autoclass:: PiBoardInfo

.. autoclass:: HeaderInfo

.. autoclass:: PinInfo

