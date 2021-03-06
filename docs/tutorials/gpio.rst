GPIO-简单IO操作
===============

掌控板可以通过拓展板将IO引脚拓展并连接控制或读取其他元器件或模块。

创建引脚对象::

        >>> from mahchine import Pin
        >>> p0 =Pin(33)
    
.. Note::

    使用前，请务必先将 ``machine`` 模块 ``Pin`` 导入，方可使用。“33”是您要访问的引脚。如果未指定mode，则默认为Pin.IN
    如果您希望将引脚配置为输入或输出，在构造时配置引脚 ``mode`` 。

IO读
------------------    
    
配置引脚为输入,上拉::

    >>> p0 = Pin(33, Pin.IN, Pin.PULL_UP)

.. Note::

    您可以使用 ``PULL_UP`` 或 ``None`` 。作为pull-mode，如果未指定，则默认为None，不拉电阻。

接下来，您可以使用以下方法读取引脚上的值::

    >>> p0.value()
    0

.. Note::

    返回引脚电平值。高电平(1),低电平(0)。


IO写
------------------    

接下来初始化引脚并配置为输出模式::

    >>> p0.init(Pin.OUT)

.. Hint::

    初始化参数与构建对象的方法一样，有关参数的详细信息，请参阅  :class:`machine.Pin`  构建对象文档

对引脚写高低电平::

    >>> pin.value(0)
    >>> pin.value(1)


外部中断
-------------------

当输入引脚发生电平变化时触发硬件中断，触发器会执行中断处理函数。你可以定义回调函数来做中些断响应的工作。


让我们首先定义一个回调函数，它必须采用一个参数，``p`` 是触发函数的引脚号。我们将使该功能打印引脚：

    >>> def callback(p):
    ...     print('pin change', p)

接下来，我们将创建两个引脚并将它们配置为输入::

    >>> from machine import Pin
    >>> p0 = Pin(33, Pin.IN)
    >>> p1 = Pin(32, Pin.IN)

最后我们需要告诉引脚何时触发，以及在检测到事件时调用的函数::

    >>> p0.irq(trigger=Pin.IRQ_FALLING, handler=callback)
    >>> p1.irq(trigger=Pin.IRQ_RISING | Pin.IRQ_FALLING, handler=callback)

.. Note::

    我们将p0设置为仅在下降沿触发（当它从高电平变为低电平时），并将p2设置为在上升沿和下降沿触发。
    输入此代码后，您可以向引脚0和2施加高电压和低电压，以查看正在执行的中断。

事件发生后，硬中断将立即触发，并将中断任何正在运行的代码，包括Python代码。
因此，您的回调函数受限于它们可以执行的操作（例如，它们无法分配内存），并且应尽可能简短。

