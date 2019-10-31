Set up and run your project up locally
============================================================

..  admonition:: Introducing

    * ``divio login``
    * ``divio project list``
    * ``divio project setup``
    * ``docker-compose up``


The next step is to set up your project on your own computer, for development purposes and also to understand better
how the system works.

For this, we will use the Divio CLI that you installed in the :ref:`installation` section.


Log in with the CLI
-------------------

Run::

    divio login

This will fetch a token from https://control.divio.com/account/desktop-app/access-token/.


Upload your public key to the Divio Control Panel
--------------------------------------------------

Upload your public key to https://control.divio.com/account/ssh-keys/


Set up your project locally
------------------------------

Get your project's *slug* from the Control Panel:

.. image:: /images/project-slug.png
   :alt: 'Project slug'
   :width: 400px

Or you can see it by listing your projects::

    divio project list

Set up the project::

    divio project setup <project slug>

This will *build* your project.


..  admonition:: Explanation

    Optional further reading: :ref:`What happens during the set-up process of a Divio project?
    <explanation-local-build>`



Run the local site with ``docker-compose up``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To get the site up and running, execute::

    docker-compose up

You should be able to log in to your site at http://localhost:8000.

``docker-compose up`` is one of the key commands for working with your Docker project locally.


Stop the project
~~~~~~~~~~~~~~~~

If you hit ``control-c`` in the console when the project is running, it will stop it.
