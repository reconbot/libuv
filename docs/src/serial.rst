.. _serial:

:c:type:`uv_serial_t` --- Serial Port handle
=================================

Serial Port handles represent a stream for a serial port.

:c:type:`uv_serial_t` is a 'subclass' of :c:type:`uv_stream_t`.


Data types
----------

.. c:type:: uv_serial_t

    Serial Port handle type.

.. c:type:: uv_serial_open_t

    Serial Port open options

.. c:type:: uv_serial_baudrate_t

    Serial Port baud rate type:

      typedef enum {
       /* Maybe shouldn't be an enum but a number */
      } uv_serial_baudrate_t;

.. c:type:: uv_serial_flow_control_t

    Serial Port flow control type:

      typedef enum {
       /*  rts/cts and xon/xoff we support xany, but QSerialPort support no flow control must research*/
      } uv_serial_flow_control_t;

.. c:type:: uv_serial_stop_bits_t

.. c:type:: uv_serial_data_bits_t


Public members
^^^^^^^^^^^^^^

N/A

.. seealso:: The :c:type:`uv_stream_t` members also apply.


API
---

.. c:function:: int uv_serial_init(uv_loop_t* loop, uv_serial_t* handle, uv_file fd, int readable)

    Initialize a new TTY stream with the given file descriptor. Usually the
    file descriptor will be:

    * 0 = stdin
    * 1 = stdout
    * 2 = stderr

    `readable`, specifies if you plan on calling :c:func:`uv_read_start` with
    this stream. stdin is readable, stdout is not.

    On Unix this function will determine the path of the fd of the terminal
    using :man:`ttyname_r(3)`, open it, and use it if the passed file descriptor
    refers to a TTY. This lets libuv put the tty in non-blocking mode without
    affecting other processes that share the tty.

    This function is not thread safe on systems that don't support
    ioctl TIOCGPTN or TIOCPTYGNAME, for instance OpenBSD and Solaris.

    .. note::
        If reopening the TTY fails, libuv falls back to blocking writes for
        non-readable TTY streams.

    .. versionchanged:: 1.9.0: the path of the TTY is determined by
                        :man:`ttyname_r(3)`. In earlier versions libuv opened
                        `/dev/tty` instead.

    .. versionchanged:: 1.5.0: trying to initialize a TTY stream with a file
                        descriptor that refers to a file returns `UV_EINVAL`
                        on UNIX.

.. c:function:: int uv_serial_set_mode(uv_serial_t* handle, uv_serial_mode_t mode)

    .. versionchanged:: 1.2.0: the mode is specified as a
                        :c:type:`uv_serial_mode_t` value.

    Set the TTY using the specified terminal mode.

.. c:function:: int uv_serial_reset_mode(void)

    To be called when the program exits. Resets TTY settings to default
    values for the next process to take over.

    This function is async signal-safe on Unix platforms but can fail with error
    code ``UV_EBUSY`` if you call it when execution is inside
    :c:func:`uv_serial_set_mode`.

.. c:function:: int uv_serial_get_winsize(uv_serial_t* handle, int* width, int* height)

    Gets the current Window size. On success it returns 0.

.. seealso:: The :c:type:`uv_stream_t` API functions also apply.
```
