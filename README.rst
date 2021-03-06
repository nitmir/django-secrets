Keep your secrets secret! |BuildStatus|_ |Coverage|_
======================================================

.. |BuildStatus| image:: https://travis-ci.org/kakulukia/django-secrets.svg
.. _BuildStatus: https://travis-ci.org/kakulukia/django-secrets

.. |Coverage| image:: https://codecov.io/gh/kakulukia/django-secrets/branch/master/graph/badge.svg
.. _Coverage: https://codecov.io/gh/kakulukia/django-secrets

This little app helps you to not commit your secrets to a repo and adds
a nice way of exporting/importing secrets for a new deployment or automated testing with environment variables.

Though this never happened to me during coding, Travis had some problems to reliably execute the tests
and hot load the new secrets, so I had move the secrets module into the my_secrets package to not have
two modules named secrets. Python obviously cant handle that. Lesson learned after about 40 Travis builds. :(

django-secrets is working under Django 1.5+ and Python 2.7, 3.5 and 3.6

Installation
------------

::

    pip install django-secrets

Usage
-----

After installing the package please add it to your ``INSTALLED_APPS``
settings.

::

    INSTALLED_APPS = (
        ...
        'django_secrets',
    )

Because we want to be able to hide our secret settings, we have to alter
manage.py to run some code before the Django magic happens. Open
``manage.py`` and alter it like this:

::

    if __name__ == "__main__":

        from django_secrets.startup import check
        check()

        ...


Now that the check is in place, run ``manage.py`` to initialize your
project. This will create a new my_secrets package in your project root with
the following contents:

::

    my_secrets
    ├── .gitignore
    ├── __init__.py
    ├── definitions.py
    └── secrets.py

The package also features a .gitignore file to prevent you from checking
in any secrets to git. Now open ``definitions.py`` to add your secrets
to the list. Start with the Django secret key for example. When you are
done adding all secrets, run ``manage.py`` again and you will be asked to
enter your secrets.

Now you can remove your secrets from ``settings.py`` and instead replace
them like this:

::

    from my_secrets import secrets

    SECRET_KEY = secrets.SECRET_KEY

Since the secrets are saved in a normal python package, you can just use
them the normal way including code completion, but now they are safe! :)

Import / Export
---------------

This package adds a new management command: ``export_secrets``. This
will print out export statements so you can easily create environment
variables on a new machine and let the ``check`` function do the rest
for you, because it will also read in any known environment variables as
secret values. This is quite handy for dynamically spawned instances or
CI testing.

Have fun and stay safe!
