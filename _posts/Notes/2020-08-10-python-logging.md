---
title: Python Logging
---

## Logging

refs: 

- https://docs.python.org/3/howto/logging.html
- https://docs.python.org/3/library/logging.html
- https://docs.python.org/3/howto/logging-cookbook.html#logging-cookbook

| Task you want to perform                                     | The best tool for the task                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Display console output for ordinary usage of a command line script or program | [`print()`](https://docs.python.org/3/library/functions.html#print) |
| Report events that occur during normal operation of a program (e.g. for status monitoring or fault investigation) | [`logging.info()`](https://docs.python.org/3/library/logging.html#logging.info) (or [`logging.debug()`](https://docs.python.org/3/library/logging.html#logging.debug) for very detailed output for diagnostic purposes) |
| Issue a warning regarding a particular runtime event         | [`warnings.warn()`](https://docs.python.org/3/library/warnings.html#warnings.warn) in library code if the issue is avoidable and the client application should be modified to eliminate the warning <br /> [`logging.warning()`](https://docs.python.org/3/library/logging.html#logging.warning) if there is nothing the client application can do about the situation, but the event should still be noted |
| Report an error regarding a particular runtime event         | Raise an exception                                           |
| Report suppression of an error without raising an exception (e.g. error handler in a long-running server process) | [`logging.error()`](https://docs.python.org/3/library/logging.html#logging.error), [`logging.exception()`](https://docs.python.org/3/library/logging.html#logging.exception) or [`logging.critical()`](https://docs.python.org/3/library/logging.html#logging.critical) as appropriate for the specific error and application domain |

Keys:

- The basic classes defined by the module, together with their functions, are listed below.
  - Loggers expose the interface that application code directly uses.
  - Handlers send the log records (created by loggers) to the appropriate destination.
  - Filters provide a finer grained facility for determining which log records to output.
  - Formatters specify the layout of log records in the final output.
- Log event information is passed between loggers, handlers, filters and formatters in a [`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord) instance.
- Logging is performed by calling methods on instances of the [`Logger`](https://docs.python.org/3/library/logging.html#logging.Logger) class (hereafter called *loggers*). Each instance has a name, and they are conceptually arranged in a namespace hierarchy using dots (periods) as separators.

![../_images/logging_flow.png](https://docs.python.org/3/_images/logging_flow.png)

### Logger Object

Loggers have the following attributes and methods. Note that Loggers should *NEVER* be instantiated directly, but always through the module-level function `logging.getLogger(name)`. Multiple calls to [`getLogger()`](https://docs.python.org/3/library/logging.html#logging.getLogger) with the same name will always return a reference to the same Logger object.

The `name` is potentially a period-separated hierarchical value, like `foo.bar.baz` (though it could also be just plain `foo`, for example). Loggers that are further down in the hierarchical list are children of loggers higher up in the list. For example, given a logger with a name of `foo`, loggers with names of `foo.bar`, `foo.bar.baz`, and `foo.bam` are all descendants of `foo`. The logger name hierarchy is analogous to the Python package hierarchy, and identical to it if you organise your loggers on a per-module basis using the recommended construction `logging.getLogger(__name__)`. That’s because in a module, `__name__` is the module’s name in the Python package namespace.

| Level      | Numeric value |
| :--------- | :------------ |
| `CRITICAL` | 50            |
| `ERROR`    | 40            |
| `WARNING`  | 30            |
| `INFO`     | 20            |
| `DEBUG`    | 10            |
| `NOTSET`   | 0             |

#### propagate attribute

If this attribute evaluates to true, events logged to this logger will be passed to the handlers of higher level (ancestor) loggers, in addition to any handlers attached to this logger. Messages are passed directly to the ancestor loggers’ handlers - neither the level nor filters of the ancestor loggers in question are considered.

If this evaluates to false, logging messages are not passed to the handlers of ancestor loggers.

The constructor sets this attribute to `True`.

If you attach a handler to a logger *and* one or more of its ancestors, it may emit the same record multiple times. In general, you should not need to attach a handler to more than one logger - if you just attach it to the appropriate logger which is highest in the logger hierarchy, then it will see all events logged by all descendant loggers, provided that their propagate setting is left set to `True`. A common scenario is to attach handlers only to the root logger, and to let propagation take care of the rest.

### Handler Object

[`Handler`](https://docs.python.org/3/library/logging.html#logging.Handler) objects are responsible for dispatching the appropriate log messages (based on the log messages’ severity) to the handler’s specified destination. [`Logger`](https://docs.python.org/3/library/logging.html#logging.Logger) objects can add zero or more handler objects to themselves with an [`addHandler()`](https://docs.python.org/3/library/logging.html#logging.Logger.addHandler) method. As an example scenario, an application may want to send all log messages to a log file, all log messages of error or higher to stdout, and all messages of critical to an email address. This scenario requires three individual handlers where each handler is responsible for sending messages of a specific severity to a specific location.

The standard library includes quite a few handler types (see [Useful Handlers](https://docs.python.org/3/howto/logging.html#useful-handlers)); the tutorials use mainly [`StreamHandler`](https://docs.python.org/3/library/logging.handlers.html#logging.StreamHandler) and [`FileHandler`](https://docs.python.org/3/library/logging.handlers.html#logging.FileHandler) in its examples.

There are very few methods in a handler for application developers to concern themselves with. The only handler methods that seem relevant for application developers who are using the built-in handler objects (that is, not creating custom handlers) are the following configuration methods:

- The [`setLevel()`](https://docs.python.org/3/library/logging.html#logging.Handler.setLevel) method, just as in logger objects, specifies the lowest severity that will be dispatched to the appropriate destination. Why are there two `setLevel()` methods? The level set in the logger determines which severity of messages it will pass to its handlers. The level set in each handler determines which messages that handler will send on.
- [`setFormatter()`](https://docs.python.org/3/library/logging.html#logging.Handler.setFormatter) selects a Formatter object for this handler to use.
- [`addFilter()`](https://docs.python.org/3/library/logging.html#logging.Handler.addFilter) and [`removeFilter()`](https://docs.python.org/3/library/logging.html#logging.Handler.removeFilter) respectively configure and deconfigure filter objects on handlers.

Application code should not directly instantiate and use instances of [`Handler`](https://docs.python.org/3/library/logging.html#logging.Handler). Instead, the [`Handler`](https://docs.python.org/3/library/logging.html#logging.Handler) class is a base class that defines the interface that all handlers should have and establishes some default behavior that child classes can use (or override).

Handlers have the following attributes and methods. Note that [`Handler`](https://docs.python.org/3/library/logging.html#logging.Handler) is never instantiated directly; this class acts as a base for more useful subclasses. However, the [`__init__()`](https://docs.python.org/3/reference/datamodel.html#object.__init__) method in subclasses needs to call [`Handler.__init__()`](https://docs.python.org/3/library/logging.html#logging.Handler.__init__).

### Formatter Objects

-  objects have the following attributes and methods. They are responsible for converting a [`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord) to (usually) a string which can be interpreted by either a human or an external system. 
- The base [`Formatter`](https://docs.python.org/3/library/logging.html#logging.Formatter) allows a formatting string to be specified. If none is supplied, the default value of `'%(message)s'` is used, which just includes the message in the logging call. 
- A Formatter can be initialized with a format string which makes use of knowledge of the [`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord) attributes - such as the default value mentioned above making use of the fact that the user’s message and arguments are pre-formatted into a [`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord)’s *message* attribute. 
  - This format string contains standard Python %-style mapping keys. 
  - See section [printf-style String Formatting](https://docs.python.org/3/library/stdtypes.html#old-string-formatting) for more information on string formatting.

The useful mapping keys in a [`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord) are given in the section on [LogRecord attributes](https://docs.python.org/3/library/logging.html#logrecord-attributes).

### Filter Objects

- `Filters` can be used by `Handlers` and `Loggers` for more sophisticated filtering than is provided by levels. 
- The base filter class only allows events which are below a certain point in the logger hierarchy. For example, a filter initialized with ‘A.B’ will allow events logged by loggers ‘A.B’, ‘A.B.C’, ‘A.B.C.D’, ‘A.B.D’ etc. but not ‘A.BB’, ‘B.A.B’ etc. If initialized with the empty string, all events are passed.
- Note that filters attached to handlers are consulted before an event is emitted by the handler, whereas filters attached to loggers are consulted whenever an event is logged (using [`debug()`](https://docs.python.org/3/library/logging.html#logging.debug), [`info()`](https://docs.python.org/3/library/logging.html#logging.info), etc.), before sending an event to handlers. 
  - This means that events which have been generated by descendant loggers will not be filtered by a logger’s filter setting, unless the filter has also been applied to those descendant loggers.
- You don’t actually need to subclass `Filter`: you can pass any instance which has a `filter` method with the same semantics.

### LogRecord Objects

[`LogRecord`](https://docs.python.org/3/library/logging.html#logging.LogRecord) instances are created automatically by the [`Logger`](https://docs.python.org/3/library/logging.html#logging.Logger) every time something is logged, and can be created manually via [`makeLogRecord()`](https://docs.python.org/3/library/logging.html#logging.makeLogRecord) (for example, from a pickled event received over the wire).

The LogRecord has a number of attributes, most of which are derived from the parameters to the constructor. (Note that the names do not always correspond exactly between the LogRecord constructor parameters and the LogRecord attributes.) These attributes can be used to merge data from the record into the format string. The following table lists (in alphabetical order) the attribute names, their meanings and the corresponding placeholder in a %-style format string.

If you are using {}-formatting ([`str.format()`](https://docs.python.org/3/library/stdtypes.html#str.format)), you can use `{attrname}` as the placeholder in the format string. If you are using $-formatting ([`string.Template`](https://docs.python.org/3/library/string.html#string.Template)), use the form `${attrname}`. In both cases, of course, replace `attrname` with the actual attribute name you want to use.

In the case of {}-formatting, you can specify formatting flags by placing them after the attribute name, separated from it with a colon. For example: a placeholder of `{msecs:03d}` would format a millisecond value of `4` as `004`. Refer to the [`str.format()`](https://docs.python.org/3/library/stdtypes.html#str.format) documentation for full details on the options available to you.

### LoggerAdapter Objects

[`LoggerAdapter`](https://docs.python.org/3/library/logging.html#logging.LoggerAdapter) instances are used to conveniently pass contextual information into logging calls. For a usage example, see the section on [adding contextual information to your logging output](https://docs.python.org/3/howto/logging-cookbook.html#context-info).

### Cookbook

```
# --- My Logging template ---
import logging
import logging.config
logging.config.dictConfig({
    'version': 1,
    'formatters': {
        'standard': {
            'format': '%(asctime)s %(name)s %(levelname)s %(funcName)s %(lineno)d %(message)s', 
            'datefmt': '%Y/%m/%d %I:%M:%S %p%z'
        }
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'standard',
            'level': 'DEBUG'
        }
    },
    'loggers': {
        '': {
            'handlers': ['console'],
            'level': 'DEBUG'
        }
    }
})
logger = logging.getLogger()

# --- Logging from multiple modules ---
# myapp.py
import logging
import mylib

def main():
    logging.basicConfig(filename='myapp.log', level=logging.INFO)
    logging.info('Started')
    mylib.do_something()
    logging.info('Finished')

if __name__ == '__main__':
    main()
# mylib.py
import logging

def do_something():
    logging.info('Doing something')
```

#### Using logging in multiple modules

- Multiple calls to `logging.getLogger('someLogger')` return a reference to the same logger object. 
- This is true not only within the same module, but also across modules as long as it is in the same Python interpreter process. 
- It is true for references to the same object; 
- additionally, application code can define and configure a parent logger in one module and create (but not configure) a child logger in a separate module, and all logger calls to the child will pass up to the parent. Here is a main module:

```
import logging
import auxiliary_module

# create logger with 'spam_application'
logger = logging.getLogger('spam_application')
logger.setLevel(logging.DEBUG)
# create file handler which logs even debug messages
fh = logging.FileHandler('spam.log')
fh.setLevel(logging.DEBUG)
# create console handler with a higher log level
ch = logging.StreamHandler()
ch.setLevel(logging.ERROR)
# create formatter and add it to the handlers
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
fh.setFormatter(formatter)
ch.setFormatter(formatter)
# add the handlers to the logger
logger.addHandler(fh)
logger.addHandler(ch)

logger.info('creating an instance of auxiliary_module.Auxiliary')
a = auxiliary_module.Auxiliary()
logger.info('created an instance of auxiliary_module.Auxiliary')
logger.info('calling auxiliary_module.Auxiliary.do_something')
a.do_something()
logger.info('finished auxiliary_module.Auxiliary.do_something')
logger.info('calling auxiliary_module.some_function()')
auxiliary_module.some_function()
logger.info('done with auxiliary_module.some_function()')
```

Here is the auxiliary module:

```
import logging

# create logger
module_logger = logging.getLogger('spam_application.auxiliary')

class Auxiliary:
    def __init__(self):
        self.logger = logging.getLogger('spam_application.auxiliary.Auxiliary')
        self.logger.info('creating an instance of Auxiliary')

    def do_something(self):
        self.logger.info('doing something')
        a = 1 + 1
        self.logger.info('done doing something')

def some_function():
    module_logger.info('received a call to "some_function"')
```

The output looks like this:

```
2005-03-23 23:47:11,663 - spam_application - INFO -
   creating an instance of auxiliary_module.Auxiliary
2005-03-23 23:47:11,665 - spam_application.auxiliary.Auxiliary - INFO -
   creating an instance of Auxiliary
2005-03-23 23:47:11,665 - spam_application - INFO -
   created an instance of auxiliary_module.Auxiliary
2005-03-23 23:47:11,668 - spam_application - INFO -
   calling auxiliary_module.Auxiliary.do_something
2005-03-23 23:47:11,668 - spam_application.auxiliary.Auxiliary - INFO -
   doing something
2005-03-23 23:47:11,669 - spam_application.auxiliary.Auxiliary - INFO -
   done doing something
2005-03-23 23:47:11,670 - spam_application - INFO -
   finished auxiliary_module.Auxiliary.do_something
2005-03-23 23:47:11,671 - spam_application - INFO -
   calling auxiliary_module.some_function()
2005-03-23 23:47:11,672 - spam_application.auxiliary - INFO -
   received a call to 'some_function'
2005-03-23 23:47:11,673 - spam_application - INFO -
   done with auxiliary_module.some_function()
```

#### Logging to multiple destinations[¶](https://docs.python.org/3/howto/logging-cookbook.html#logging-to-multiple-destinations)

Let’s say you want to log to console and file with different message formats and in differing circumstances. Say you want to log messages with levels of DEBUG and higher to file, and those messages at level INFO and higher to the console. Let’s also assume that the file should contain timestamps, but the console messages should not. Here’s how you can achieve this:

```
import logging

# set up logging to file - see previous section for more details
logging.basicConfig(level=logging.DEBUG,
                    format='%(asctime)s %(name)-12s %(levelname)-8s %(message)s',
                    datefmt='%m-%d %H:%M',
                    filename='/temp/myapp.log',
                    filemode='w')
# define a Handler which writes INFO messages or higher to the sys.stderr
console = logging.StreamHandler()
console.setLevel(logging.INFO)
# set a format which is simpler for console use
formatter = logging.Formatter('%(name)-12s: %(levelname)-8s %(message)s')
# tell the handler to use this format
console.setFormatter(formatter)
# add the handler to the root logger
logging.getLogger('').addHandler(console)

# Now, we can log to the root logger, or any other logger. First the root...
logging.info('Jackdaws love my big sphinx of quartz.')

# Now, define a couple of other loggers which might represent areas in your
# application:

logger1 = logging.getLogger('myapp.area1')
logger2 = logging.getLogger('myapp.area2')

logger1.debug('Quick zephyrs blow, vexing daft Jim.')
logger1.info('How quickly daft jumping zebras vex.')
logger2.warning('Jail zesty vixen who grabbed pay from quack.')
logger2.error('The five boxing wizards jump quickly.')
```

When you run this, on the console you will see

```
root        : INFO     Jackdaws love my big sphinx of quartz.
myapp.area1 : INFO     How quickly daft jumping zebras vex.
myapp.area2 : WARNING  Jail zesty vixen who grabbed pay from quack.
myapp.area2 : ERROR    The five boxing wizards jump quickly.
```

and in the file you will see something like

```
10-22 22:19 root         INFO     Jackdaws love my big sphinx of quartz.
10-22 22:19 myapp.area1  DEBUG    Quick zephyrs blow, vexing daft Jim.
10-22 22:19 myapp.area1  INFO     How quickly daft jumping zebras vex.
10-22 22:19 myapp.area2  WARNING  Jail zesty vixen who grabbed pay from quack.
10-22 22:19 myapp.area2  ERROR    The five boxing wizards jump quickly.
```

As you can see, the DEBUG message only shows up in the file. The other messages are sent to both destinations.

This example uses console and file handlers, but you can use any number and combination of handlers you choose.

#### Using Filters to impart contextual information

You can also add contextual information to log output using a user-defined `Filter`. `Filter` instances are allowed to modify the `LogRecords` passed to them, including adding additional attributes which can then be output using a suitable format string, or if needed a custom `Formatter`.

For example in a web application, the request being processed (or at least, the interesting parts of it) can be stored in a threadlocal ([`threading.local`](https://docs.python.org/3/library/threading.html#threading.local)) variable, and then accessed from a `Filter` to add, say, information from the request - say, the remote IP address and remote user’s username - to the `LogRecord`, using the attribute names ‘ip’ and ‘user’ as in the `LoggerAdapter` example above. In that case, the same format string can be used to get similar output to that shown above. Here’s an example script:

```
import logging
from random import choice

class ContextFilter(logging.Filter):
    """
    This is a filter which injects contextual information into the log.

    Rather than use actual contextual information, we just use random
    data in this demo.
    """

    USERS = ['jim', 'fred', 'sheila']
    IPS = ['123.231.231.123', '127.0.0.1', '192.168.0.1']

    def filter(self, record):

        record.ip = choice(ContextFilter.IPS)
        record.user = choice(ContextFilter.USERS)
        return True

if __name__ == '__main__':
    levels = (logging.DEBUG, logging.INFO, logging.WARNING, logging.ERROR, logging.CRITICAL)
    logging.basicConfig(level=logging.DEBUG,
                        format='%(asctime)-15s %(name)-5s %(levelname)-8s IP: %(ip)-15s User: %(user)-8s %(message)s')
    a1 = logging.getLogger('a.b.c')
    a2 = logging.getLogger('d.e.f')

    f = ContextFilter()
    a1.addFilter(f)
    a2.addFilter(f)
    a1.debug('A debug message')
    a1.info('An info message with %s', 'some parameters')
    for x in range(10):
        lvl = choice(levels)
        lvlname = logging.getLevelName(lvl)
        a2.log(lvl, 'A message at %s level with %d %s', lvlname, 2, 'parameters')
```

which, when run, produces something like:

```
2010-09-06 22:38:15,292 a.b.c DEBUG    IP: 123.231.231.123 User: fred     A debug message
2010-09-06 22:38:15,300 a.b.c INFO     IP: 192.168.0.1     User: sheila   An info message with some parameters
2010-09-06 22:38:15,300 d.e.f CRITICAL IP: 127.0.0.1       User: sheila   A message at CRITICAL level with 2 parameters
2010-09-06 22:38:15,300 d.e.f ERROR    IP: 127.0.0.1       User: jim      A message at ERROR level with 2 parameters
2010-09-06 22:38:15,300 d.e.f DEBUG    IP: 127.0.0.1       User: sheila   A message at DEBUG level with 2 parameters
2010-09-06 22:38:15,300 d.e.f ERROR    IP: 123.231.231.123 User: fred     A message at ERROR level with 2 parameters
2010-09-06 22:38:15,300 d.e.f CRITICAL IP: 192.168.0.1     User: jim      A message at CRITICAL level with 2 parameters
2010-09-06 22:38:15,300 d.e.f CRITICAL IP: 127.0.0.1       User: sheila   A message at CRITICAL level with 2 parameters
2010-09-06 22:38:15,300 d.e.f DEBUG    IP: 192.168.0.1     User: jim      A message at DEBUG level with 2 parameters
2010-09-06 22:38:15,301 d.e.f ERROR    IP: 127.0.0.1       User: sheila   A message at ERROR level with 2 parameters
2010-09-06 22:38:15,301 d.e.f DEBUG    IP: 123.231.231.123 User: fred     A message at DEBUG level with 2 parameters
2010-09-06 22:38:15,301 d.e.f INFO     IP: 123.231.231.123 User: fred     A message at INFO level wi
```

#### Configuring filters with [`dictConfig()`](https://docs.python.org/3/library/logging.config.html#logging.config.dictConfig)

You *can* configure filters using [`dictConfig()`](https://docs.python.org/3/library/logging.config.html#logging.config.dictConfig), though it might not be obvious at first glance how to do it (hence this recipe). Since [`Filter`](https://docs.python.org/3/library/logging.html#logging.Filter) is the only filter class included in the standard library, and it is unlikely to cater to many requirements (it’s only there as a base class), you will typically need to define your own [`Filter`](https://docs.python.org/3/library/logging.html#logging.Filter) subclass with an overridden [`filter()`](https://docs.python.org/3/library/logging.html#logging.Filter.filter) method. To do this, specify the `()` key in the configuration dictionary for the filter, specifying a callable which will be used to create the filter (a class is the most obvious, but you can provide any callable which returns a [`Filter`](https://docs.python.org/3/library/logging.html#logging.Filter) instance). Here is a complete example:

```
import logging
import logging.config
import sys

class MyFilter(logging.Filter):
    def __init__(self, param=None):
        self.param = param

    def filter(self, record):
        if self.param is None:
            allow = True
        else:
            allow = self.param not in record.msg
        if allow:
            record.msg = 'changed: ' + record.msg
        return allow

LOGGING = {
    'version': 1,
    'filters': {
        'myfilter': {
            '()': MyFilter,
            'param': 'noshow',
        }
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'filters': ['myfilter']
        }
    },
    'root': {
        'level': 'DEBUG',
        'handlers': ['console']
    },
}

if __name__ == '__main__':
    logging.config.dictConfig(LOGGING)
    logging.debug('hello')
    logging.debug('hello - noshow')
```

This example shows how you can pass configuration data to the callable which constructs the instance, in the form of keyword parameters. When run, the above script will print:

```
changed: hello
```

which shows that the filter is working as configured.

A couple of extra points to note:

- If you can’t refer to the callable directly in the configuration (e.g. if it lives in a different module, and you can’t import it directly where the configuration dictionary is), you can use the form `ext://...` as described in [Access to external objects](https://docs.python.org/3/library/logging.config.html#logging-config-dict-externalobj). For example, you could have used the text `'ext://__main__.MyFilter'` instead of `MyFilter` in the above example.
- As well as for filters, this technique can also be used to configure custom handlers and formatters. See [User-defined objects](https://docs.python.org/3/library/logging.config.html#logging-config-dict-userdef) for more information on how logging supports using user-defined objects in its configuration, and see the other cookbook recipe [Customizing handlers with dictConfig()](https://docs.python.org/3/howto/logging-cookbook.html#custom-handlers) above.

### Basic

```
logging.<method>
# or use 
logger = logging.getLogger(__name__)
logger.<method>
```
#### logging
```
logging.basicConfig(filename='example.log',level=logging.DEBUG)

logging.basicConfig(format='%(levelname)s:%(message)s', level=logging.DEBUG)
# DEBUG:This message should appear on the console

logging.basicConfig(format='%(asctime)s %(message)s')
# 2010-12-12 11:41:42,612 is when this event was logged.

logging.basicConfig(format='%(asctime)s %(message)s', datefmt='%m/%d/%Y %I:%M:%S %p')
# 12/12/2010 11:46:36 AM is when this event was logged.
```
#### logger
```
logger = logging.getLogger('foo')
logger.addHandler(...)
	logging.NullHandler()
	logging.FileHandler()
```

## Config

1. logger methods
2. `logging.config.dictConfig()`
3. `logging.config.fileConfig('logging.conf')`

```
Here is the logging.conf file:

[loggers]
keys=root,simpleExample

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_simpleExample]
level=DEBUG
handlers=consoleHandler
qualname=simpleExample
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
datefmt=
```

In `YAML`

```
version: 1
formatters:
  simple:
    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
handlers:
  console:
    class: logging.StreamHandler
    level: DEBUG
    formatter: simple
    stream: ext://sys.stdout
loggers:
  simpleExample:
    level: DEBUG
    handlers: [console]
    propagate: no
root:
  level: DEBUG
  handlers: [console]
```

## Configuring Logging for a Library

When developing a library which uses logging, you should take care to document how the library uses logging - for example, the names of loggers used. Some consideration also needs to be given to its logging configuration. If the using application does not use logging, and library code makes logging calls, then (as described in the previous section) events of severity `WARNING` and greater will be printed to `sys.stderr`. This is regarded as the best default behaviour.

### Handler For the Library you wants to provide others to use

If for some reason you *don’t* want these messages printed in the absence of any logging configuration, you can attach a do-nothing handler to the top-level logger for your library. This avoids the message being printed, since a handler will always be found for the library’s events: it just doesn’t produce any output. If the library user configures logging for application use, presumably that configuration will add some handlers, and if levels are suitably configured then logging calls made in library code will send output to those handlers, as normal.

A do-nothing handler is included in the logging package: [`NullHandler`](https://docs.python.org/3/library/logging.handlers.html#logging.NullHandler) (since Python 3.1). An instance of this handler could be added to the top-level logger of the logging namespace used by the library (*if* you want to prevent your library’s logged events being output to `sys.stderr` in the absence of logging configuration). If all logging by a library *foo* is done using loggers with names matching ‘foo.x’, ‘foo.x.y’, etc. then the code:

```
import logging
logging.getLogger('foo').addHandler(logging.NullHandler())
```

should have the desired effect. If an organisation produces a number of libraries, then the logger name specified can be ‘orgname.foo’ rather than just ‘foo’.

It is strongly advised that you *do not add any handlers other than* [`NullHandler`](https://docs.python.org/3/library/logging.handlers.html#logging.NullHandler) *to your library’s loggers*. This is because the configuration of handlers is the prerogative of the application developer who uses your library. The application developer knows their target audience and what handlers are most appropriate for their application: if you add handlers ‘under the hood’, you might well interfere with their ability to carry out unit tests and deliver logs which suit their requirements.