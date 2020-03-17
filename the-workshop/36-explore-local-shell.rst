Explore the local environment
=============================

..  admonition:: Introducing

    * ``docker-compose run``
    * the *statelessness* of containerised environments

We're going to dive in further to the local environment, and get used to some of the ways available to interact with it.

So far you have used two ``docker-compose`` commands:

* ``docker-compose up``, to run the project
* ``docker-compose build``, to make it rebuild the image

For this exercise, we will assume that you plan to install a reusable Django application into your project that
requires `Pillow, the Python imaging library <https://pillow.readthedocs.io/en/stable/>`_. The steps you will follow
here are ones you might follow in a real-life example.


Use ``docker-compose run`` to run a command
--------------------------------------------------

First, is Pillow already installed and available in the environment? We can check::

    docker-compose run web pip list

This runs the command ``pip list`` inside a ``web`` container.

You will find yourself using ``docker-compose run`` a lot. In this example, we're using it as a "one-off" command. The
command:

* launched a container
* ran ``pip list`` in it
* shut down the container

(it created the container from the image just to run ``pip list``).


Use ``docker-compose run`` to work inside a container
-----------------------------------------------------

As you may already know, Pillow requires some third-party system libraries to support various image types. You will
find two sample images included in this documentation (note that your web bowser may not support the WebP format):

.. |jpg| image:: /images/test.jpg
   :alt: ''
   :width: 30

.. |webp| image:: /images/test.webp
   :alt: ''
   :width: 30


* `a JPEG image </_images/test.jpg>`_ |jpg| - every browser should render this file
* `a WebP image </_images/test.webp>`_ |webp| - not all browsers can render this; download it anyway

Download them, and copy them to your project. Let's check which
images are supported, by starting a shell in the environment and trying to open them:

..  code-block:: bash

    ✗ docker-compose run web bash
    Starting nodevops_db_1 ... done
    root@59d5a381b0ff:/app#

In this case, the container is launched and remains running while we work in it. We can open a Python shell:

..  code-block:: bash

    /app# python
    Starting example_db_1 ... done
    Python 3.6.8 (default, Mar 27 2019, 08:53:45)
    [GCC 6.3.0 20170516] on linux
    Type "help", "copyright", "credits" or "license" for more information.

Now in the Python shell, let's see if it has JPEG support ready:

..  code-block:: pycon

    >>> from PIL import Image
    >>> Image.open("test.jpg")
    <PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=1024x768 at 0x7FA2A2E1C4A8>

That indicates that it does.

And WebP support:

..  code-block:: pycon

    >>> Image.open("test.webp")
    /usr/local/lib/python3.6/site-packages/PIL/Image.py:2817: UserWarning: image file could not be identified because WEBP support not installed
      warnings.warn(message)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "/usr/local/lib/python3.6/site-packages/PIL/Image.py", line 2818, in open
        raise IOError("cannot identify image file %r" % (filename if filename else fp))
    OSError: cannot identify image file 'test.webp'

So, our environment can support JPEG images but not WebP. For that, we need to install the ``webp-dev`` system library.
Exit the Python shell, and use ``apt`` for the installation:

..  code-block:: bash

    /app# apt-get update
    Ign:1 http://deb.debian.org/debian stretch InRelease
    [...]
    Reading package lists... Done
    
    /app# apt-get install libwebp-dev
    Reading package lists... Done
    [...]
    Processing triggers for libc-bin (2.24-11+deb9u4) ...

You can start up Python in the shell again, and try once more:

..  code-block:: pycon

    >>> from PIL import Image
    >>> Image.open("test.webp")
    <PIL.WebPImagePlugin.WebPImageFile image mode=RGB size=540x405 at 0x7FADB5085A58>

Success!

And now you know that in order to use WebP images with Pillow, you're going to need ``libwebp-dev`` installed in the
environment.


Discover the statelessness of containerised environments
-----------------------------------------------------------------

However, try this:

#. Exit the Python shell.
#. Exit the Bash shell (this will exit the container).
#. Start the environment again with Bash (``docker-compose run web bash``).
#. Try opening the Python shell, and opening the image as you did before with ``Image.open("test.webp")``.

You may be surprised (or annoyed) to find that it fails, as if you had never installed ``libwebp-dev``. This is because
every time your environment is launched, it is created anew from the image. Nothing you do to the container *persists*.
The container is **stateless**.

If we need something to persist in the environment, it will need to be baked into the image itself.

We will do this in the next section by editing the ``Dockerfile``.

