Local development
============================================================

There are many advantages to containerisation, such as knowing that the site running on your own computer is running in
the same environment (as far as possible) as it will be in deployment. But this means that it's not as directly
accessible to you may expect - it's not running directly on your computer, but in another environment on your
computer.

This can take some getting used to, and it does mean that often an extra step will be required to interact with the project.

For example...

Installing packages
-------------------

Perhaps you're used to pip installing Python packages as you develop:

* <code> <code> <code>
* ``pip install some-package``
* <code> <code> <code>

This isn't going to work in the same way - and it's one of the most common stumbling-blocks for programmers getting
used to containerisation. You're going to have to get into the container first.


Get into the container to use pip
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can't just run ``pip install`` - you have to run it inside the web container. To do that, you can do
``docker-compose run web`` followed by the command you want. For example::

    docker-compose run web bash

This will launch a web container, and instead of running :ref:`the default command <default-web-command>`, will run ``bash``.

Now install something with pip, say::

    pip install rsa

And you can check that it has been installed, by running ``pip list``.


It's gone!
~~~~~~~~~~

Now when you ``pip install`` a package, you expect to come back later and find it still there. So try this - go back into a bash shell, with ``docker-compose run web bash``, and do ``pip list`` once more.

Where has the package that you installed - just a few moments ago - gone?

The answer is that it hasn't gone anywhere, and in fact, *you didn't even install it* - at least, not in *this*
container. Every time you do ``docker-compose run web``, you are not "running the web container", you are creating and
launching a brand new instance of it, based on its image, because :ref:`containers are cheap and disposable
<cheap-disposable>`.

If you exited the bash shell, the Docker container would have ceased to exist at that moment; even if you left it
running and when you ran ``docker-compose run web bash`` the second time, that would simply have created a second
container.

The point is that you can no longer do things inside your containerised environments, and expect them to last longer
than the life of the container - which is probably going to be quite short.

(In fact, you could even have done::

    docker-compose run web pip install rsa

And this would have spun up a container, installed the RSA package in it, and then immediately destroyed the container -
taking RSA with it. Quite pointless, but philosophically interesting, to execute a command that will wipe all trace of itself.)


Making things persist
~~~~~~~~~~~~~~~~~~~~~

Remember our remarks about :ref:`'traditional deployment' <traditional-deployment>` thinking? Now is the time to stop
thinking about *building* your system, and to think instead about *describing* it. That is, instead of *doing* things
to the container, we should attend to the *instructions* that build the container, and take care of our installation
there.

So, add your requirements to the ``requirements.in`` file (that's processed by the ``Dockerfile``), say::

    django-axes==5.0

and run::

    docker-compose build

Now the *image* for the project will contain that requirement. Did it build successfully? Good! That means your
instructions work.

..  note::

    Notice that we specified ``django-axes==5.0``. That's always a good idea. Otherwise, if Django Axes 5.1 comes along
    in a couple of weeks' time and introduces a dependency conflict, we don't want to find it appearing in our project
    next time we run ``build``.


Advantages and disadvantages
----------------------------

*The disadvantage*: this is a slower and less streamlined, less interactive way to work. There's initially more
overhead from Docker and its set-up.

*The advantage*: you'll quickly gain back the little extra effort you spend, and more.

In fact you very quickly get to used to not thinking:

    I will make the changes on the Test server [or perhaps, in the development environment].

and instead start thinking:

    I'll write the change to the project repository and rebuild.


Use the ``--rm`` option with ``docker-compose run``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One last thing. In the examples above, the container wiped itself away when we exited. This won't always happen, so it's
good practice to use the ``--rm`` option to ensure that you don't end up with stray running containers::

    docker-compose run --rm web ...

You can check what containers are running::

    docker ps


Debugging
----------------------------------------

The example of pip illustrates the general question. Suppose that we need to do some interactive debugging in the
Django shell. Once again, instead of doing ``python manage.py shell``, we need to do::

    docker-compose run --rm web python manage.py shell

That's fine - but remember, the shell you have just invoked is a shell in a brand new instance. **You cannot expect any
changes you make there to exist outside that particular container, or beyond its lifetime**.


Interacting with the database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the other hand, suppose you do a ``.save()`` on an object in the shell. Now this makes a change to the *database*.
The database, unlike the environment in a container (but like, for example your media storage) is and needs to be
persistent. So that change will be picked up by every container that connects to the database. To achieve this, the
database stores its data outside the container, in the host environment.

As you can see, containerisation obliges us to think not about "the server" but about "the services". A database is
a different kind of thing from an application container; it behaves differently and has different needs. Once
again, it can be difficult to start thinking in the new way, but when you do, it's liberating.

You can of course also use other tools to interact with the database, such as Django's ``dbshell``::

    docker-compose run --rm web python manage.py dbshell

Or the psql tool (Divio projects use Postgres, but the same principle applies for other databases)::

    docker-compose run --rm web psql -h postgres -U postgres db

Note that the tools these commands launch are tools *inside* the ``web`` container, talking to a service inside a
``db`` container. What if you have a favourite GUI tool on your own computer, that you want to connect to the database
running inside its container?

This becomes bit more complex. First you must:

.. _expose-database-ports:

Expose the database's port
^^^^^^^^^^^^^^^^^^^^^^^^^^

The database is tucked away inside its own container. In order to the connect to the database from a tool running
directly on your own machine, you will need to expose its port (5432) on that container, by adding a ``ports`` section
to the ``db`` service in ``docker-compose.yml`` that maps the port to your host:

..  code-block:: yaml
    :emphasize-lines: 3,4

    db:
        image: postgres:9.4
        ports:
            - 5432:5432

This means that external traffic reaching the container on port 5432 will be routed to port 5432 internally.

The ports are ``<host port>:<container port>`` - you could choose another host
port if you are already using 5432 on your host.

Then restart the ``db`` container with: ``docker-compose up -d db``


Connect to the database
^^^^^^^^^^^^^^^^^^^^^^^

You will nned then to provide the connection details to the Postgres client. The connection port will be ``5432`` of
course.

For a Divio project, the other values will be:

* username: ``postgres``
* password: not required
* database: ``db``
* address: ``127.0.0.1``

Now you can access the database using your Postgres tool of choice. For example, if you're using the ``psql`` command
line tool, you can connect to the project database with::

    psql -h 127.0.0.1 -U postgres db


Common operations
-------------------

Restart the local server
~~~~~~~~~~~~~~~~~~~~~~~~~

If you:

* add a new module that hasn't yet been loaded
* change your local environment variables (in ``.env-local``)

you will need to restart the local server.

It's also necessary to restart the local sever when you make a change to existing Python code, but the Django runserver
restarts (just as usual) to reload it.


Rebuild the project
~~~~~~~~~~~~~~~~~~~

If you make a change to:

* your Python requirements
* your ``Dockerfile``
* your ``docker-compose.yml`` file

you will need to run ``docker-compose build`` to build them into the image.


Interact with a running container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In all the examples so far, we have fired up new containers with ``docker-compose run ...`` whenever we needed to use
one, with the caveat that each time it's a brand new container with no memory of what has happened in or to other
containers.

You can in fact interact directly with a container that is already running. First, you need to know its name; run::

    docker ps

and look for the name, which might be something like ``example_web``. Now you can do::

    docker exec -i example_web python manage.py shell

(The ``-i`` flag gives you an interactive console.)

This gives you *some* persistent access into to a container, and can be useful when you do need that persistence while
developing or debugging - but it only persists for the lifetime of that particular container.
