=======
AnyPath
=======
AnyPath makes it trivial to fetch remote ressources and work with them locally.

Getting Started
===============

Installation
------------
To install simply do::

    pip install anypath

Dependencies
^^^^^^^^^^^^
By default AnyPath does not install the dependencies for the different providers. You should install them as needed:

    +-----------+---------------------------------+
    | Provider  | Dependencies                    |
    +===========+=================================+
    | git       | local git installation          |
    +-----------+---------------------------------+
    | mercurial | local mercurial installation    |
    +-----------+---------------------------------+
    | http      | `pip install requests`          |
    +-----------+---------------------------------+
    | sftp      | `pip install paramiko`          |
    +-----------+---------------------------------+

Basic Usage
-----------
AnyPath uses `pathproviders` to handle different remote resources. The resources are then fetched to a new temporary directory where you can work with them.
The newly fetched ressources are wrapped in a `pathlib.Path`.

.. code-block:: python

   from anypath.anypath import AnyPath, path_provider
   from anypath.pathprovider.http import HttpPath

   path_provider.add(HttpPath)

   with AnyPath('http://example.org') as path:
       path.open().read()

First you register all the providers that you want to use (note: remember to install the dependencies per provider).
Now you can open any uri that has a scheme known to one of the registered providers.

    +-----------+-----------------------------------------+
    | Provider  | Schemes                                 |
    +===========+=========================================+
    | git       | - `git+http://`                         |
    |           | - `git+https://`                        |
    |           | - `git://`                              |
    +-----------+-----------------------------------------+
    | mercurial | - `hg+http://`                          |
    |           | - `hg+https://`                         |
    +-----------+-----------------------------------------+
    | http      | - `http://`                             |
    |           | - `https://`                            |
    +-----------+-----------------------------------------+
    | sftp      | - `sftp://`                             |
    +-----------+-----------------------------------------+
    | local     | - `file://`                             |
    +-----------+-----------------------------------------+

You can use AnyPath either as a contextmanager (`with AnyPath ...`) or directly by calling `fetch()`.
Beware that you will have to call `close()` manually when not using the contextmanager to cleanup the temporary files.

.. code-block:: python

   path_provider.add(HttpPath)
   ap = AnyPath('http://example.org')
   path = ap.fetch() # type: Path
   path.open().read()
   ap.close()

Persistance
^^^^^^^^^^^
The example so far was useful if you are only interested in the content of a fetched resource. They are created in a temporary folder, where you can work with them, and are deletetd afterwards.
Sometimes however you may want to persist the remote resource outside of a temporary location.

.. code-block:: python

   path_provider.add(HttpPath)

   with AnyPath('sftp://user@localhost:/path/on/host', persist_dir='/your/local/path') as path:
       path.open().read()

Instead of copying the files manually you can specify a `persist_dir` when creating the AnyPath. The temporary resources will then be copied to that location.
As a result you will get the `persist_dir` wrapped as an `pathlib.Path` instead of the temporary location and you can directly work with it.

Providers and options
^^^^^^^^^^^^^^^^^^^^^
While the defaults for fetching resources might be fine for many usecases there are many situations where you might want to pass some options to a provider.
You might for example want to do a POST with an HttpPath or pass credentials to a GitPath.

Options are always passed as keyword arguments. Following you will find all providers and their available options.

Http
####
The options are passed to a requests.Request object, they behave the same and are named accordingly.

.. code-block:: python

   AnyPath('http://example.org', method='GET', data=None, headers=None, params=None)

=========   ============================================================
Option      Description
=========   ============================================================
method      Default: 'GET'

            Specifies the HTTP method to be used as a string.

            E.g. POST, DELETE, PUT


data        Default: None

            The body to attach to the request.

            If a dictionary is provided, form-encoding will take place.


headers     Default: None

            A dictionary of headers to send in the request.


params      Default: None

            A dictionary of URL parameters to append to the URL.
=========   ============================================================

Sftp
####
The path for Sftp is expected to be in the format `sftp://user@host:/path/on/host`, additional options can be set via arguments.

.. code-block:: python

   AnyPath('sftp://user@localhost:/path/on/host', password=None, private_key=None, port=22)

============    ============================================================
Option          Description
============    ============================================================
password        Default: None

                The password for the user.

                Also used if private_key is given,
                and the key requires a password


private_key     Default: None

                The path to the local private_key (as a string)
                if it is used to login


port            Default: 22

                The ssh port to be used.
============    ============================================================

Git
###
None

Mercurial
#########
None

Local
#####
None

Limitations
^^^^^^^^^^^
AnyPath is useful if you want to fetch e.g. some config files or a small project directory.

However you might not want to use AnyPath if you are working with a huge remote resource.
Everything is fetched to your local machine, which might take some time and cost a lot of space if you try to work with a whole filesystemn of a remote host for example.

Contributing
============
You can contribute in any of the following areas, no matter if it is your first OSS contribution or your thousandst.
Contributions are welcome for example:
- If you find any issue or bug when using AnyPath
- If you want to add to the documentation or fix incorrect or missing documentation.
- If you want to add features or work on the codebase in general

Just file an issue in the tracker first describing what you would like to do and then create a pull-request.

Everything related to this project is governed by the Code of Conduct (CODE_OF_CONDUCT.rst).

License
-------
AnyPath is licensed under "Mozilla Public License Version 2.0". See LICENSE.txt for the full license.
