.. _key-docker-compose-operations:

Control the local project with ``docker-compose``
============================================================

..  admonition:: Introducing

    * ``docker-compose stop``
    * ``docker-compose build``

``docker-compose`` is a command that uses the project's ``docker-compose.yml`` file to control the local project. The
``docker-compose.yml`` file contains information about how the project should be built, what containers should be
created, how they should be linked to each other, and so on.


``docker-compose up`` and ``stop``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You already know ``docker-compose up``, which runs your project. If you hit ``control-c`` in the console, it will stop
it.

Another way to stop it is by executing ``docker-compose stop`` in the same directory.


``docker-compose build``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you created the project the first time, you *built* it locally. Sometimes you need to rebuild a project (for
example, if you made a change to its requirements) with ``docker-compose build``.

Try it.

Look at the output::

    ➜  docker-compose build
    db uses an image, skipping
    Building web
    [...]

It builds the ``web`` container, but skips building the ``db`` container. That's because it doesn't need to. The ``db``
container, as you will see from the ``docker-compose.yml`` file, uses an off-the-shelf image. In Docker, images can be
built and saved for re-use, saving time and making things repeatable.

Now that you know how to stop, start and build a local project, we are going to enter the local development cycle.

..  admonition:: Explanation

    Further reading: :ref:`Understanding the docker-compose.yml file <explanation-docker-compose>`
