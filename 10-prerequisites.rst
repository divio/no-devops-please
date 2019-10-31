Prerequisites
============================================================

.. |macintosh| image:: /images/macintosh.png
   :alt: ''

.. |windows| image:: /images/windows.png
   :alt: ''

.. |linux| image:: /images/linux.png
   :alt: ''


Required software
-----------------

You will need to have the following installed or configured, and know at least the basics of using them, before
proceeding:

* Git (`GitHub's set up Git guide <https://help.github.com/en/github/getting-started-with-github/set-up-git>`_)
* SSH, so that you can provide your public key to a server (`GitHub's guides to setting up SSH
  <https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh>`_)


Hardware requirements
---------------------

Please check the Docker hardware requirements below. It *is* possible to use older hardware and software, using
Virtual Box and Docker Toolbox - but it can require quite a bit of extra work to set up.


Macintosh users |macintosh|
~~~~~~~~~~~~~~~~~~~~~~~~~~~

* a 2010 or newer model. Run the command ``sysctl kern.hv_support`` to check for the required hardware
  virtualisation support (the response should be ``1``).
* macOS Sierra 10.12 or newer


Windows users |windows|
~~~~~~~~~~~~~~~~~~~~~~~

* virtualisation enabled in BIOS
* Windows 10 64bit Pro, Enterprise or Education


Linux users |linux|
~~~~~~~~~~~~~~~~~~~

You'll probably be OK...
