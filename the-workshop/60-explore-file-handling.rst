Explore file handling
============================================================

..  admonition:: Introducing

    * devolved media storage

In this section we will explore an aspect of containerised deployment that often takes people by surprise. It's
related to the *statelessness* and *lack of persistence* of the containerised environment, and has various
implications, not just for deployment, but also for how your code handles files.


Disable volume mapping locally
------------------------------

For convenience, the ``docker-compose.yml`` file in your project maps some directories *inside* the container
environment to directories accessible from outside it. That's how you can make changes to code and configuration using
your normal development tools, rather than having to work inside the container itself.

This is achieved with the ``volumes`` directive:

..  code-block:: Python
    :emphasize-lines: 5-7

    services:
      [...]
      web:
        [...]
        volumes:
          - ".:/app:rw"
          - "./data:/data:rw"

In order to understand the behaviour of containers better, we need to disable this.

Comment out the highlighted section.


Explore file behaviour
-----------------------

Launch a new bash shell inside the container:: ``docker-compose run web bash``

In the container, create a new file (``touch test_file.txt``).

You'll see it there if you run ``ls``. You will be able to open it from a Python application too.

However, you won't see it in your own environment - it stays inside the container. Similarly, if you add a file to the project directory in your environment, you won't see inside the container. Exit the container, and start one up again:
the file won't be there. It disappeared when the container disappeared.

Create a file once more in the container, and now, leaving your container running, start up another bash session, in a
new terminal window. You won't see the file in that session either. It's another container for the same project, but
the changes you make in the one container *will not appear in any others*.

Containers are their own little universe, as far as local file storage is concerned.

This has significant implications for your code. It means that you can't use local file storage locally (what would
happen in a project that used two containers running in parallel as a way of increasing performance?).

Instead, as Django Photologue does, you have to ensure that your code doesn't make assumptions about how images are
handled. In a Django project like this one, that means you should not use Django's :mod:`FileSystemStorage
<django:django.core.files.storage>`. Instead, you need to use ``django.core.files.storage.default_storage``, which
devolves handling to an appropriate backend.

In this project, Aldryn Django takes care of setting up storage, on Amazon's S3 system.


..  admonition:: Further reading

    Containerisation has implications for other aspects of coding too.

    For some of them, see :ref:`explanation-practical-implications`.

