Using external services
============================================================

The example below is just a simple one, but it demonstrates some key aspects of configuring external services - you will most likely need to:

* install some additional software specific to that
  service and the interfaces it provides
* obtain some sort of key to authenticate with the external service
* configure your settings appropriately


Logging (with LogDNA)
---------------------

You may be used to having access to your server to check its logs. When your
project is running inside a container, you won't have the same kind of access.
For example, :ref:`you can't rely on being able to SSH into it in the usual
way. <no-ssh>`, and your project may not even have a persistent filesystem that
it can save its logs to. (On Divio Cloud, minimal logging is provided in the
Control Panel, but it really is a bare minimum).

Instead, you need to configure logging to send your logs to an external
service, such as LogDNA.

If you don't already have a LogDNA account, visit https://logdna.com and
register for a free account. LogDNA will provide you with an *ingestion key*.

Install the LogDNA Python library to your project - add ``logdna`` to its
requirements.

The LogDNA library provides a logging handler, via the class
``logdna.LogDNAHandler``, and this will need to be added to your logging
configuration.

..  code-block:: python
    :emphasize-lines: 5-12

    LOGGING = {
        [...]
        'handlers': {
            [...]
            'logdna': {
                'class': 'logdna.LogDNAHandler',
                'key': '<insert your ingestion key here>',
                'options': {
                    'hostname': 'your-website-name',
                    'index_meta': True
                }
            },
        },
    }

Next, you need to add logger mappings, so that your loggers send their logs to
the new handler. This is Django's default ``loggers`` dictionary - to have the
``django`` and ``django.server`` loggers forward logs to the new ``logdna``
handler, you'd need to add ``'logdna'`` to each of the lists of ``handlers``
below.

..  code-block:: python
    :emphasize-lines: 5, 9

    LOGGING = {
        [...]
        'loggers': {
            'django': {
                'handlers': ['console', 'mail_admins'],
                'level': 'INFO',
            },
            'django.server': {
                'handlers': ['django.server'],
                'level': 'INFO',
                'propagate': False,
            },
        }
    }



..  admonition:: Divio Cloud developer handbook links

    For specific directions for Divio Cloud projects, see:

    * `How to configure an external logging service <https://docs.divio.com/en/latest/how-to/configure-external-logging.html>`_

