.. _explanation-practical-implications:

Some practical implications of containerisation
========================================================

In :ref:`explanation-thinking`, we discuss some general implications for thinking about development and deployment when
using containerisation.

Here, I will discuss some more practical implications, in some cases specifically for the Aldryn Django project you are
working with, that you will already have encountered, but are worth considering again.

There are many advantages to containerisation, such as knowing that the site running on your own computer is running in
the same environment (as far as possible) as it will be in deployment. But this means that it's not as directly
accessible to you may expect - it's not running directly on your computer, but in another environment on your
computer.

This can take some getting used to, and it does mean that often an extra step will be required to interact with the project.

For example...


Debugging - *what* are you looking at?
----------------------------------------

Suppose that the application in our container is misbehaving, and we need to do some interactive debugging in the
Django shell. Once again, instead of doing ``python manage.py shell``, we need to do::

    docker-compose run web python manage.py shell

That's fine - but remember, the shell you have just invoked is a shell *in a brand new instance*, not in the actual
instance that was exhibiting the troublesome behaviour. The new shell is not receiving the same traffic or load as
the "real" one. It's not like SHHing into a live server to find out what it's doing. his is why making good use of
logging is the approach to take.


.. _no-ssh:

You can't just SSH into your server any more
--------------------------------------------

It's very nice to be able to SSH into a live server to see what's going on, to inspect a Python process or even see
what the web server or operating system are doing. It's very reassuring, and a quick way to get insights and maybe fix
things.

You can't do that any more, because you don't really have a server any more. All you have is a containerised environment that is dedicated to running your application and all the other components are elsewhere.

For example, on Divio, although there is a link to SSH into a live container, it's not the same thing as being
able to SSH into your server. It's just a new container that has been spun up specifically for you to SSH into, just as
was the case when you executed ``docker-compose run`` commands.

That means you will never be able to use it to see the requests that are hitting your site - they are going elsewhere.

You might ask, "Why can't I SSH into the container that *is* receiving those requests?" In fact there's no guarantee
that there is a single container that's receiving them. In many projects, multiple, parallel containers handle the requests allocated to them by a load-balancer.

Instead, you have to get used to gaining insights in a different way. The web containers report to a log; you can also
make use of services such as Sentry.

These are less immediate and interactive - but in the long run, they are more valuable tools.


Interacting with the database
------------------------------

On the other hand, suppose you do a ``.save()`` on an object in the shell. Now this makes a change to the *database*.
The database, unlike the environment in a container (but like, for example your media storage) is and needs to be
*persistent* . So that change will be picked up by every container that connects to the database. To achieve this, the
database stores its data outside the container.

As you can see, containerisation obliges us to think not about "the server" but about "the services". A database is
a different kind of thing from an application container; it behaves differently and has different needs. Once
again, it can be difficult to start thinking in the new way, but when you do, it's liberating.

You may be used to connecting to your live database to run queries using the tool of your choice, or even to
make changes directly. That's no longer possible; the database is **only** accessible from within your application.
Added security, reduced convenience.

You can SSH into a container and use the ``dbshell`` or ``psql`` client - but the
price of containerisation is more distance between you and the services you're relying on.

You can of course also use other tools to interact with the database, such as Django's ``dbshell``::

    docker-compose run web python manage.py dbshell

Or the psql tool (Divio projects use Postgres, but the same principle applies for other databases)::

    docker-compose run web psql -h postgres -U postgres db

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


..  admonition:: Divio Cloud developer handbook links

    * `How to interact with your project’s database <http://docs.divio.com/en/latest/how-to/interact-database.html>`_


You can't store media files the way you once did
------------------------------------------------------

Although container instance running your application will have its own local file storage, this will be **independent**
of each of the others, and it won't persist - once that instance ceases to exist, so will the files. That storage will
also be inaccessible to any other instances of the application.

This means a project's applications, cron jobs or other process can't expect to save files to its
local storage, and then expect to find them again: **if you save files to local storage you will lose them**.

Instead, use the media storage - Amazon S3 - that is provided as a backing service to your project.

You can do this by using Django's storage APIs. The storage defined by Django's
``django.core.files.storage.default_storage`` will do the right thing.

See `Working with your project’s media storage in Python applications
<http://docs.divio.com/en/latest/reference/work-media-storage.html>`_ and `How to interact with your project’s media
storage <http://docs.divio.com/en/latest/how-to/interact-storage.html>`_ for more.


Interact with a running container
---------------------------------

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


Common operations
-------------------

When do you need to...

Restart the local server?
~~~~~~~~~~~~~~~~~~~~~~~~~

If you:

* add a new module that hasn't yet been loaded
* change your local environment variables (in ``.env-local``)

you will need to restart the local server.

It's also necessary to restart the local sever when you make a change to existing Python code, but the Django runserver
restarts (just as usual) to reload it.


Rebuild the project?
~~~~~~~~~~~~~~~~~~~~

If you make a change to:

* your Python requirements
* your ``Dockerfile``
* your ``docker-compose.yml`` file

you will (in some cases, may) need to run ``docker-compose build`` to build them into the image.
