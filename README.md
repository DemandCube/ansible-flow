ansible-flow
====

The repository contains custom ansible modules that make developing playbooks easier.

- This is part of [NeverwinterDP the Data Pipeline for Hadoop](https://github.com/DemandCube/NeverwinterDP)

Ansible Modules
====


**check_version**
----

check_version allows you to easily check to see if a program/binary exists and optionally to check what version it is to test if you should install, upgrade or replace the program using ansible.

INSTALLATION
----
cd path/to/toplevel/directory/where/your/playbooks/are

mkdir library

cd library

git clone https://github.com/DemandCube/ansible-flow.git

mv ./ansible-flow/* ./



Use cases
----
What if I want to just see if a module is installed?
```
- name: test if python is installed
  version_check: command="python"
  register: installed
  
- name: python install
  yum: name='python' state=installed
  sudo: yes
  when: installed.install_required == true
```

What if I want to compare versions?
```
  #Choose one option for installif
  #installif can be one of ["lessthan", "lessthanequal", "equal", "notequal", "greaterthan", "greaterthanequal"]
    # lessthan - sets install_required if found version is less than expected version
    # lessthanequal - sets install_required if found version is less than or equal to expected version
    # equal - sets install_required if found version is equal to the expected version
    # notequal - sets install_required if found version is not equal to the expected version
    # greaterthan - sets install_required if found version is greater than expected version
    # greaterthanequal - sets install_required if found version is greater than or equal to expected version
- name: test if python is installed and do an install if the version is less than 3.0
  version_check: command="python --version" version=3.0 installif="lessthan"
  register: installed
  
- name: python install
  yum: name='python' state=installed version="3.1"
  sudo: yes
  when: installed.install_required == true
```


What if I want to use a custom regex to parse the output from command
```
- name: test if python is installed and use a custom regex to parse the version info
  version_check: command="python --version" regex="\d\.\d\.\d" 
  register: installed
  
- name: do something if python version is 2.7
  shell: ls -la
  sudo: yes
  when: installed.version == 2.7
```


What if my regex matches the version string, but its the second match? (This is not a normal use case, tread lightly using this option).
Refer to the documentation at the top of version_check for more info.
```
- name: test if python is installed and version is between 2.7 and 2.9
  version_check: command="python --version" regex="\d\.\d\.\d\." line=0
  register: installed
  
- name: python install
  yum: name='python' state=installed 
  sudo: yes
  when: installed.version="2.7.5"
```


##USE
Example playbook:
```
- name: Test version_check, simple case
  version_check: command="python --version"
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check if version is less
  version_check: command="python --version" installif="lessthan" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version
  
- name: Test version_check, check if version is less than or equal to
  version_check: command="python --version" installif="lessthanequal" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check if version is greater than
  version_check: command="python --version" installif="greaterthan" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version
  
- name: Test version_check, check if version is greater than or equal to
  version_check: command="python --version" installif="greaterthanequal" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check if version is equal to 2.7
  version_check: command="python --version" installif="equal" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version
  
- name: Test version_check, check if version is not equal to 2.7
  version_check: command="python --version" installif="notequal" version=2.7
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check if version is less than, uses a custom regex
  version_check: command="python --version" installif="lessthan" version=2.7 regex="\d\.+"
  register: version

- name: debug version variable from the test
  debug: var=version
  
- name: Test version_check, check if version is less, use custom regex and custom line
  version_check: command="python --version" installif="lessthan" version=2.7 regex="\d\.+" line=1
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check if non-existant command needs to be installed (it does)
  version_check: command="notarealcommand --version" 
  register: version

- name: debug version variable from the test
  debug: var=version
```

Output from this file:
```
rcduar@ApplePoptarts:DemandCubePlaybooks $ ansible-playbook -i ./inventory/localhost test_version_check.yml
 
PLAY [localhost] **************************************************************

GATHERING FACTS ***************************************************************
ok: [localhost]

TASK: [test_version_check | Test version_check, simple case] ******************
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\"",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is less] *****
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"lessthan\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is less than or equal to] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"lessthanequal\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is greater than] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"greaterthan\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is greater than or equal to] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"greaterthanequal\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is equal to 2.7] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"equal\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is not equal to 2.7] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"notequal\" version=2.7",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2.7.5"
    }
}

TASK: [test_version_check | Test version_check, check if version is less than, uses a custom regex] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"lessthan\" version=2.7 regex=\"\\d\\.+\"",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": "2."
    }
}

TASK: [test_version_check | Test version_check, check if version is less, use custom regex and custom line] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" installif=\"lessthan\" version=2.7 regex=\"\\d\\.+\" line=1",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "",
        "stdout_lines": [],
        "version": false
    }
}

TASK: [test_version_check | Test version_check, check if non-existant command needs to be installed (it does)] ***
changed: [localhost]

TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": true,
        "install_required": true,
        "installed": false,
        "invocation": {
            "module_args": "command=\"notarealcommand --version\"",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "",
        "stdout": "",
        "stdout_lines": [],
        "version": false
    }
}

PLAY RECAP ********************************************************************
localhost                  : ok=21   changed=10   unreachable=0    failed=0

```



