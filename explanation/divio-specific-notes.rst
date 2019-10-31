Some Divio-specific notes
============================================================

.. _settings:

The addons system
----------------------

If you have a look at your ``settings.py`` file, you will find that it looks a bit different.

This is because in pre-packaged Divio project (recommended) full use is made of the *Divio addons system*.

Your Django project contains Django (a completely standard Django installed via pip), but it also contains *Aldryn
Django*. Aldryn Django is a wrapper for Django. It installs Django, and provides some convenience code to configure it
automatically for the Divio environments.

For example, you will see that there is no ``DATABASES`` setting in the file. In fact, all the settings are there in the
module; try this::

    ✗ docker-compose run --rm web python manage.py shell
    >>> from django.conf import settings
    >>> settings.DATABASES
    {'default': {'NAME': 'db', 'USER': 'postgres', 'PASSWORD': '', 'HOST': 'postgres', 'PORT': 5432, 'CONN_MAX_AGE': 0, 'ENGINE': 'django.db.backends.postgresql_psycopg2', 'ATOMIC_REQUESTS': False, 'AUTOCOMMIT': True, 'OPTIONS': {}, 'TIME_ZONE': None, 'TEST': {'CHARSET': None, 'COLLATION': None, 'NAME': None, 'MIRROR': None}}}


How does this work?
~~~~~~~~~~~~~~~~~~~

In an application that has been installed via an addon (a wrapper), at least some of its configuration can be taken
care of by the wrapper, in its ``aldryn_config.py`` file. For example, the `Aldryn Django DATABASES setting
<https://github.com/divio/aldryn-django/blob/support/1.11.x/aldryn_config.py#L110-L129 >`_.

This looks for environment variables providing the database credentials, allowing the cloud Test and Live environments
to supply them knowing that Django will pick them up.

Locally, ``DATABASES`` is set in ``.env-local``, and functions in just the same way.

The lines::

    import aldryn_addons.settings
    aldryn_addons.settings.load(locals())

in your ``settings.py`` import all the settings that are automatically configured by the addons system.


You don't have to use this
--------------------------

It's important to note that **using this is entirely optional**. You don't need to use Aldryn Django, or the addons
system, and you can configure all your settings manually or otherwise if you prefer.

However, not only can you use it in the case of packages such as Aldryn Django, you can even create wrappers for your
own applications.


Some advantages of using the addons system
------------------------------------------

Managing settings this way does have some advantages:


Seamless operation across different environments
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Whether in the local, Test or Live environments, Django will have the correct value, *provided by the environment*. You
- as an application programmer who doesn't want to do DevOps - now longer need to care whether your database or media
settings are right, you can just get on with your application programming.


Settings are optimised for the platform
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The settings in the Divio addons are optimised for the platform - they've been configured expertly and on the basis of
experience. *You* don't need to be responsible for them.


Settings can be exposed and managed via the Control Panel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the project dashboard on the Control Panel, take a look at *Addons* > *Aldryn Django* > *Configure*. You'll see that
some settings can be exposed in the GUI.


Settings can be configured programmatically
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some settings need to be added in just the right place. You can ensure that for example a ``MIDDLEWARE`` for an
application will be inserted just where it needs to go.
