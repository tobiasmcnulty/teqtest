
Teqtest
========================

Below you will find basic setup and deployment instructions for the teqtest
project. To begin you should have the following applications installed on your
local development system:

- Python >= 3.5
- NodeJS >= 4.2
- `pip <http://www.pip-installer.org/>`_ >= 1.5
- `virtualenv <http://www.virtualenv.org/>`_ >= 1.10
- `virtualenvwrapper <http://pypi.python.org/pypi/virtualenvwrapper>`_ >= 3.0
- Postgres >= 9.3
- git >= 1.7

A note on NodeJS 4.2 for Ubuntu users: this LTS release may not be available through the
Ubuntu repository, but you can configure a PPA from which it may be installed::

    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

You may also follow the manual instructions if you wish to configure the PPA yourself:

    https://github.com/nodesource/distributions#manual-installation

Django version
------------------------

The Django version configured in this template is conservative. If you want to
use a newer version, edit ``requirements/base.txt``.

Getting Started
------------------------

First clone the repository from Github and switch to the new directory::

    $ git clone git@github.com:[ORGANIZATION]/teqtest.git
    $ cd teqtest

To setup your local environment you can use the quickstart make target `setup`, which will
install both Python and Javascript dependencies (via pip and npm) into a virtualenv named
"teqtest", configure a local django settings file, and create a database via
Postgres named "teqtest" with all migrations run::

    $ make setup
    $ workon teqtest

If you require a non-standard setup, you can walk through the manual setup steps below making
adjustments as necessary to your needs.

To setup your local environment you should create a virtualenv and install the
necessary requirements::

    # Check that you have python3.5 installed
    $ which python3.5
    $ mkvirtualenv teqtest -p `which python3.5`
    (teqtest)$ pip install -r requirements/dev.txt
    (teqtest)$ npm install

Next, we'll set up our local environment variables. We use `django-dotenv
<https://github.com/jpadilla/django-dotenv>`_ to help with this. It reads environment variables
located in a file name ``.env`` in the top level directory of the project. The only variable we need
to start is ``DJANGO_SETTINGS_MODULE``::

    (teqtest)$ cp teqtest/settings/local.example.py teqtest/settings/local.py
    (teqtest)$ echo "DJANGO_SETTINGS_MODULE=teqtest.settings.local" > .env

Create the Postgres database and run the initial migrate::

    (teqtest)$ createdb -E UTF-8 teqtest
    (teqtest)$ python manage.py migrate

If you want to use `Travis <http://travis-ci.org>`_ to test your project,
rename ``project.travis.yml`` to ``.travis.yml``, overwriting the ``.travis.yml``
that currently exists.  (That one is for testing the template itself.)::

    (teqtest)$ mv project.travis.yml .travis.yml

Development
-----------

You should be able to run the development server via the configured `dev` script::

    (teqtest)$ npm run dev

Or, on a custom port and address::

    (teqtest)$ npm run dev -- --address=0.0.0.0 --port=8020

Any changes made to Python, Javascript or Less files will be detected and rebuilt transparently as
long as the development server is running.


Deployment
----------

The deployment of requires Fabric but Fabric does not yet support Python 3. You
must either create a new virtualenv for the deployment::

    # Create a new virtualenv for the deployment
    $ mkvirtualenv teqtest-deploy -p `which python2.7`
    (teqtest-deploy)$ pip install -r requirements/deploy.txt

or install the deploy requirements
globally::

    $ sudo pip install -r requirements/deploy.txt


You can deploy changes to a particular environment with
the ``deploy`` command::

    $ fab staging deploy

New requirements or migrations are detected by parsing the VCS changes and
will be installed/run automatically.
