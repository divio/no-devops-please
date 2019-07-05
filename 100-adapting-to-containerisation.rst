Adapting to containerised ways of working
============================================================

You might be used to having direct access to, and control of, your project in deployment, or deployed your projects in
an interactive way, step-by-step. In a containerised system - you don't. You can't manipulate or interact with it
directly.

You have to do things differently. For example...

.. _no-ssh:

You can't just SSH into your server any more
--------------------------------------------

It's very nice to be able to SSH into a live server to see what's going on, to inspect a Python process or even see
what the web server or operating system are doing. It's very reassuring, and a quick way to get insights and maybe fix
things.

You can't do that any more, because you don't really have a server any more. All you have is a containerised environment that is dedicated to running your application and all the other components are elsewhere.

For example, on Divio Cloud, although there is a link to SSH into a live container, it's not the same thing as being
able to SSH into your server. It's just a new container that has been spun up specifically for you to SSH into, just as
was the case when you executed ``docker-compose run`` commands.

That means you will never be able to use it to see the requests that are hitting your site - they are going elsewhere.

You might ask, "Why can't I SSH into the container that *is* receiving those requests?" In fact there's no guarantee
that there is a single container that's receiving them. In many projects, multiple, parallel containers handle the requests allocated to them by a load-balancer.

Instead, you have to get used to gaining insights in a different way. The web containers report to a log; you can also
make use of services such as Sentry.

These are less immediate and interactive - but in the long run, they are more valuable tools.


You can't connect to your database the way you used to
------------------------------------------------------------

Similarly, you may be used to connecting to your live database to run queries using the tool of your choice, or even to
make changes directly. That's no longer possible; the database is **only** accessible from within your application.
Added security, reduce convenience.

You can SSH into a container and use the ``dbshell`` or ``psql`` client - but the
price of containerisation is more distance between you and the services you're relying on.

See `Interact with the Cloud database
<http://docs.divio.com/en/latest/how-to/interact-database.html#interact-cloud-db>`_ for more.


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


On other systems
-----------------

On other systems, the details of these limitations and alternatve ways of interacting with the services of your site
will differ, but **the principle is always the same**. Instead of being immediately available to you, they will be a
little more removed. This will require you to adapt to different ways of working - but with a ittle effort, you will
find that you gaim more than it costs you.

