tcconfig
========

.. image:: https://img.shields.io/pypi/pyversions/tcconfig.svg
   :target: https://pypi.python.org/pypi/tcconfig

.. image:: https://travis-ci.org/thombashi/tcconfig.svg?branch=master
   :target: https://travis-ci.org/thombashi/tcconfig

.. image:: https://img.shields.io/github/stars/thombashi/tcconfig.svg?style=social&label=Star
   :target: https://github.com/thombashi/tcconfig

Summary
-------

A Simple tc command wrapper tool.
Easy to set up traffic control of network bandwidth/latency/packet-loss to a network interface.

Traffic control features
------------------------

Network
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Traffic control can be specified network to apply to:

-  Outgoing/Incoming packets
-  Certain IP address/network and port

Available parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following parameters can be set to network interfaces.

-  Network bandwidth rate [G/M/K bps]
-  Network latency [milliseconds]
-  Packet loss rate [%]
-  Packet corruption rate [%]

.. image:: docs/gif/tcset_example.gif

Usage
=====

Set traffic control (``tcset`` command)
---------------------------------------

``tcset`` is a command to impose traffic control to a network interface (device).

e.g. Set a limit on bandwidth up to 100Kbps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --rate 100k

e.g. Set 100ms network latency
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --delay 100

e.g. Set 0.1% packet loss
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --loss 0.1

e.g. All of the above at once
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --rate 100k --delay 100 --loss 0.1

e.g. Specify the IP address of traffic control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --delay 100 --network 192.168.0.10

e.g. Specify the IP network and port of traffic control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --delay 100 --network 192.168.0.0/24 --port 80

Delete traffic control (``tcdel`` command)
------------------------------------------

``tcdel`` is a command to delete traffic shaping rules from a network interface (device).

e.g. Delete traffic control of eth0
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcdel --device eth0


Display traffic control configurations (``tcshow`` command)
-----------------------------------------------------------

``tcshow`` is a command to display traffic control to network interface(s).

Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: console

    # tcset --device eth0 --delay 10 --delay-distro 2  --loss 0.01 --rate 0.25M --network 192.168.0.10 --port 8080
    # tcset --device eth0 --delay 1 --loss 0.02 --rate 500K --direction incoming
    # tcshow --device eth0
    {
        "eth0": {
            "outgoing": {
                "network=192.168.0.10/32, port=8080": {
                    "delay": "10.0",
                    "loss": "0.01",
                    "rate": "250K",
                    "delay-distro": "2.0"
                },
                "network=0.0.0.0/0": {}
            },
            "incoming": {
                "network=0.0.0.0/0": {
                    "delay": "1.0",
                    "loss": "0.02",
                    "rate": "500K"
                }
            }
        }
    }

For more information
--------------------

More examples are available at 
http://tcconfig.rtfd.io/en/latest/pages/usage/index.html

Installation
============

Installing from PyPI
------------------------------
``tcconfig`` can be installed from `PyPI <https://pypi.python.org/pypi>`__ via
`pip <https://pip.pypa.io/en/stable/installing/>`__ (Python package manager) command.

.. code:: console

    sudo pip install tcconfig


Installing from binary
------------------------------
``tcconfig`` can be installed environments which cannot access to
`PyPI <https://pypi.python.org/pypi>`__ directly:

1. ``https://github.com/thombashi/tcconfig/releases/download/v0.7.0/tcconfig_wheel.tar.gz``
2. ``tar xvf tcconfig_wheel.tar.gz``
3. ``cd tcconfig_wheel/``
4. ``./install.sh``


Dependencies
============

Linux packages
--------------
- iproute2 (mandatory: required for tc command)
- iptables (optional: required to when you use ``--iptables`` option)

Linux kernel module
----------------------------
- sch_netem

Python packages
---------------
Dependency python packages are automatically installed during
``tcconfig`` installation via pip.

- `DataPropery <https://github.com/thombashi/DataProperty>`__
- `ipaddress <https://pypi.python.org/pypi/ipaddress>`__
- `logbook <http://logbook.readthedocs.io/en/stable/>`__
- `pyparsing <https://pyparsing.wikispaces.com/>`__
- `six <https://pypi.python.org/pypi/six/>`__
- `subprocrunner <https://github.com/thombashi/subprocrunner>`__
- `voluptuous <https://github.com/alecthomas/voluptuous>`__

Optional
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- `netifaces <https://bitbucket.org/al45tair/netifaces>`__
    - Suppress excessive error messages if this package is installed

Test dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- `allpairspy <https://github.com/thombashi/allpairspy>`__
- `pingparsing <https://github.com/thombashi/pingparsing>`__
- `pytest <http://pytest.org/latest/>`__
- `pytest-runner <https://pypi.python.org/pypi/pytest-runner>`__
- `tox <https://testrun.org/tox/latest/>`__

Documentation
=============

http://tcconfig.rtfd.io/

Troubleshooting
========================

Phenomenon
------------------------
`tcset` command failed with an error message `RTNETLINK answers: No such file or directory`.


Solutions
--------------------------
The cause of this error is `sch_netem` kernel module is not loaded in your system.
Execute the following command to solve this problem:

.. code:: console

    # modprobe sch_netem

The command is loading the `sch_netem` module.
If the command failed with below message, you need to install additional kernel module.

.. code:: console

    # modprobe: FATAL: Module sch_netem not found in directory /lib/modules/xxxxxx

Execute the following command to install kernel modules (includes the `sch_netem` module).

.. code:: console

    # dnf install kernel-modules-extra

(in the case of `RHEL`/`CentOS`/`Fedora`).
After that, re-execute `modprobe sch_netem` command.

.. code:: console

    # modprobe sch_netem
    #

