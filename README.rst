::

     _    ___   ___ ___ _  _   _
    | |  / _ \ / __/ __| \| | /_\
    | |_| (_) | (_ \__ \ .` |/ _ \
    |____\___/ \___|___/_|\_/_/ \_\


**logsna** is a small Python library that provides a sane log output
format.

Installation
------------

::

    $ [sudo] pip install slimit

Or the bleeding edge version from the git master branch:

::

    $ [sudo] pip install git+https://github.com/rspivak/logsna.git#egg=logsna


How to use it
-------------

**logsna** provides custom formatter class *logsna.Formatter* that can
  be used in a file configuration:

::

    # sanefmt.py
    import logging
    import logging.config
    from StringIO import StringIO

    CONFIG = """\

    [loggers]
    keys=root

    [handlers]
    keys=console

    [handler_console]
    class=logging.StreamHandler
    args=(sys.stderr,)
    formatter=sane

    [formatters]
    keys=sane

    [logger_root]
    level=DEBUG
    handlers=console

    # Our custom formatter class
    [formatter_sane]
    class=logsna.Formatter
    """

    config = StringIO(CONFIG)
    logging.config.fileConfig(config)

    log = logging.getLogger('mylogger.component1')

    log.debug('debug message')
    log.info('info message')
    log.warning('warning message')
    log.critical('critical message')
    try:
        1 / 0
    except:
        log.exception('Houston we have a problem')


The Log Format
--------------

Here is an output from the above program:
::

    DEBUG    [2012-05-21 01:59:23,686] mylogger.component1: debug message
    INFO     [2012-05-21 01:59:23,686] mylogger.component1: info message
    WARNING  [2012-05-21 01:59:23,686] mylogger.component1: warning message
    CRITICAL [2012-05-21 01:59:23,686] mylogger.component1: critical message
    ERROR    [2012-05-21 01:59:23,686] mylogger.component1: Houston we have a problem
    ! Traceback (most recent call last):
    !   File "/home/alienoid/python/sanefmt.py", line 67, in <module>
    !     1 / 0
    ! ZeroDivisionError: integer division or modulo by zero


The Log Format Goals
--------------------

1. To be human readable as much as possible

2. Make it easy to use with standard Unix utilities *tail* and *grep*
   to help quickly figure out why things are going south


The Log Format Notes
--------------------

1. All timestamps are in **ISO8601** and **UTC** format

2. To grep for messages of a specific level

::

    $ tail -f sanefmt.log | grep '^INFO'

3. To grep for messages from a particular logger

::

    $ tail -f sanefmt.log | grep 'component1:'

4. To pull out full exception tracebacks with a corresponding log message

::

    $ tail -f sanefmt.log | grep -B 1 '^\!'


Acknowledgments
---------------

- `Release It! <http://pragprog.com/book/mnee/release-it>`_
- `Logula <https://github.com/codahale/logula>`_


License
-------

Copyright (c) 2012 Ruslan Spivak

Published under The MIT License, see LICENSE