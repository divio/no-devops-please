Local development
============================================================

* everything happens inside a docker container

Installing packages
-------------------

* add items to requirements
* docker-compose build

Debugging
----------------------------------------

* use the console
* use the shell
* where is the shell


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
