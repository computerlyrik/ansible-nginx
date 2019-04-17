[![Build Status](https://travis-ci.org/computerlyrik/ansible-nginx.svg?branch=master)](https://travis-ci.org/computerlyrik/ansible-nginx)

NGinx Installer for simple redirect and proxy tasks
=========

Sets up a nginx docker container to

* Proxy
* Redirect
* Passwd-Protect

Make sure, you select a proper task (see example below)

Requirements
------------

Runs on Debianoids which are capable of running docker machines

Role Variables
--------------


Example Playbook
----------------

### SSL Reverse Proxy
For the following playbook to work, you neet have SSL certificates pre-installed.

Adds the following capabilities:
- set up in an certain docker network
- proyx an app on internal name resolution and port
- include nginx settings for proxying websockets

```
    - name: set up nginx ssl reverse proxy
      import_role:
        name: computerlyrik.nginx
        tasks_from: proxy
      vars:
        nginx__proxy_websocket: true
        nginx__proxies:
          - name: myApp
            url: "{{ inventory_hostname }}"
            backend_url: "myApp:8052"
            ssl:
              cert: "myCert.cert"
              key: "myKey.key"
        nginx__ssl_directory: "{{ ssl_path }}"
        nginx__docker_network: myDockerNetwork
```

### Redirect

The following example expects files like "mydomain.com.crt" and "mydomain.com.key" inside the `nginx__ssl_dir`. 

The Role will discover certs for redirect if needed

The following defines two redirect rules, enables SSL and prevents SSL for the first entry.

```
    - name: redirect several domains
      import_role:
        name: computerlyrik.nginx
        tasks_from: redirect
      vars:
        nginx__redirect__ssl_enable: true
        nginx__redirects:

          - name: no ssl
            ssl_off: true
            target: https://my-target.com
            hosts:
              - name: somesourceurlwithnossl.com
                ssl: no # default
              - name: somesourcewithssl.com
                truncate: yes # default
                ssl: yes # expects an certificate and key for sourcewithssl.com in configurable nginx__redirect__ssl_dir
```

License
-------

Apache2

Testing
-------

TODO
----

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
