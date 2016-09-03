ansible-group_to_marathon
=========

[![Build Status](https://travis-ci.org/ernestas-poskus/ansible-group_to_marathon.svg?branch=master)](https://travis-ci.org/ernestas-poskus/ansible-group_to_marathon)
[![BSD License](http://img.shields.io/badge/license-BSD-blue.svg)](http://opensource.org/licenses/BSD-3-Clause)

Ansible role for creating Mesosphere Marathon apps using /v2/ REST API

Requirements
------------

Mesosphere Marathon

Role Variables
--------------

```yaml
---
# defaults file for ansible-group_to_marathon
marathon_group_wait_for_deployment: true
marathon_group_url: http://127.0.0.1:8080
marathon_group_retries: 300
```


Dependencies
------------

Mesosphere Marathon

Example Playbook
----------------

```yaml
- hosts: all
  roles:
    - role: ernestas-poskus.group_to_marathon
      marathon_group_url: http://localhost:8080
      marathon_group:
        id: "/consulpush"
        apps:
          -
            id: "basic-0"
            cmd: "while [ true ] ; do echo 'Hello Marathon' ; sleep 5 ; done"
            cpus: 0.1
            mem: 10
            instances: 1
          -
            id: "bridged-webapp"
            cmd: "python3 -m http.server 8080"
            cpus: 0.1
            mem: 64
            instances: 1
            container:
              type: "DOCKER"
              docker:
                image: "python:3"
                network: "BRIDGE"
                portMappings:
                  -
                    containerPort: 8080
                    hostPort: 0
                    servicePort: 9000
                    protocol: "tcp"
                  -
                    containerPort: 161
                    hostPort: 0
                    protocol: "udp"
            healthChecks:
              -
                protocol: "HTTP"
                portIndex: 0
                path: "/"
                gracePeriodSeconds: 5
                intervalSeconds: 20
                maxConsecutiveFailures: 3
```

> To create application group
```
ansible-playbook your_application.yml --tags "create"
```

> To scale application group
```
ansible-playbook your_application.yml --tags "scale" -e 'scale_to=3'
```

> To destroy application group
```
ansible-playbook your_application.yml --tags "destroy" -e 'want_to_destroy=1'
```


License
-------

Copyright (c) 2016, Ernestas Poskus
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of ansible-group_to_marathon nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.


Author Information
------------------

Twitter: @ernestas_poskus
