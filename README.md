ansible-flow
============


###EXEC_CHECK
##INSTALLATION
cd path/to/where/your/playbooks/are
mkdir library
cd library
git clone https://github.com/DemandCube/ansible-flow.git


##Use cases
What if I want to just see if a module is installed?
```
- name: test if python is installed
  version_check: command="python"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed
  sudo: yes
  when: installed.installed != true
```

What if I want to make sure a minimum version is met
```
- name: test if python is installed and minimum version is met
  version_check: command="python --version" minimum="3.0"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed version="3.0"
  sudo: yes
  when: installed.minVer != true
```


What if I want to make sure a maximum version is met
```
- name: test if python is installed and minimum version is met
  version_check: command="python --version" maximum="2.9"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed version="2.7"
  sudo: yes
  when: installed.maxVer != true
```


What if I want to make sure a maximum and minimum version is met
```
- name: test if python is installed and minimum version is met
  version_check: command="python --version" maximum="2.9" minimum="2.7"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed version="2.7"
  sudo: yes
  when: installed.maxVer != true and installed.minVer != true
```

What if I want to use a custom regex to parse the version
```
- name: test if python is installed and minimum version is met and use a custom regex
  version_check: command="python --version" maximum="2.9" minimum="2.7" regex="\d.\d.\d.\d"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed version="2.7"
  sudo: yes
  when: installed.installed != true
```

What if I just want to compare the version as a string
```
- name: test if python is installed and minimum version is met and use a custom regex
  version_check: command="python --version"
  ignore_errors: yes
  register: installed
  
- name: python install
  yum: name='python' state=installed version="2.7.9"
  sudo: yes
  when: installed.version != "2.7.9"
```


##USE
Example playbook:
```- name: Test version_check, simple case
  version_check: command="python --version"
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check minimum version
  version_check: command="python --version" minimum="1.8"
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check maximum version
  version_check: command="python --version" maximum="2.9"
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, check minimum and maximum version
  version_check: command="python --version" minimum="1.8" maximum="2.9"
  register: version

- name: debug version variable from the test
  debug: var=version  

- name: Test version_check, minimum version should fail
  version_check: command="python --version" minimum="9.0.1"
  ignore_errors: yes
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, maximum version should fail
  version_check: command="python --version" maximum="1.0"
  ignore_errors: yes
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, minimum version should fail but check for bth
  version_check: command="python --version" minimum="9.0.1" maximum="9.0"
  ignore_errors: yes
  register: version

- name: debug version variable from the test
  debug: var=version

- name: Test version_check, maximum should fail but check for both
  version_check: command="python --version" minimum="1.1" maximum="1.0"
  ignore_errors: yes
  register: version

- name: debug version variable from the test
  debug: var=version
  
- name: test bogus program should cause failure
  version_check: command="goobleygoop"
  ignore_errors: yes
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
ok: [localhost]
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\"",
            "module_name": "version_check"
        },
        "item": "",
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, check minimum version] ********
ok: [localhost]
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" minimum=\"1.8\"",
            "module_name": "version_check"
        },
        "item": "",
        "minVer": true,
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, check maximum version] ********
ok: [localhost]
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" maximum=\"2.9\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": true,
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, check minimum and maximum version] ***
ok: [localhost]
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" minimum=\"1.8\" maximum=\"2.9\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": true,
        "minVer": true,
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, minimum version should fail] ***
failed: [localhost] => {"changed": false, "failed": true, "installed": true, "item": "", "minVer": false, "version": "2.7.5"}
stderr: Python 2.7.5
 
stdout: Python 2.7.5
 
msg: Version was insufficient.
Version: 2.7.5
Required version >=9.0.1
...ignoring
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "failed": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" minimum=\"9.0.1\"",
            "module_name": "version_check"
        },
        "item": "",
        "minVer": false,
        "msg": "Version was insufficient.\nVersion: 2.7.5\nRequired version >=9.0.1",
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, maximum version should fail] ***
failed: [localhost] => {"changed": false, "failed": true, "installed": true, "item": "", "maxVer": false, "version": "2.7.5"}
stderr: Python 2.7.5
 
stdout: Python 2.7.5
 
msg: Version was insufficient.
Version: 2.7.5
Required version <= 1.0
...ignoring
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "failed": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" maximum=\"1.0\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": false,
        "msg": "Version was insufficient.\nVersion: 2.7.5\nRequired version <= 1.0",
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, minimum version should fail but check for bth] ***
failed: [localhost] => {"changed": false, "failed": true, "installed": true, "item": "", "maxVer": true, "minVer": true, "version": "2.7.5"}
stderr: Python 2.7.5
 
stdout: Python 2.7.5
 
msg: Version was insufficient.
Version: 2.7.5
Required version >=9.0.1 and <= 9.0
...ignoring
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "failed": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" minimum=\"9.0.1\" maximum=\"9.0\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": true,
        "minVer": true,
        "msg": "Version was insufficient.\nVersion: 2.7.5\nRequired version >=9.0.1 and <= 9.0",
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | Test version_check, maximum should fail but check for both] ***
failed: [localhost] => {"changed": false, "failed": true, "installed": true, "item": "", "maxVer": true, "minVer": true, "version": "2.7.5"}
stderr: Python 2.7.5
 
stdout: Python 2.7.5
 
msg: Version was insufficient.
Version: 2.7.5
Required version >=1.1 and <= 1.0
...ignoring
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "failed": true,
        "installed": true,
        "invocation": {
            "module_args": "command=\"python --version\" minimum=\"1.1\" maximum=\"1.0\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": true,
        "minVer": true,
        "msg": "Version was insufficient.\nVersion: 2.7.5\nRequired version >=1.1 and <= 1.0",
        "stderr": "Python 2.7.5\n",
        "stdout": "Python 2.7.5\n",
        "stdout_lines": [
            "Python 2.7.5"
        ],
        "version": "2.7.5"
    }
}
 
TASK: [test_version_check | test bogus program should cause failure] **********
failed: [localhost] => {"changed": false, "failed": true, "installed": false, "item": "", "maxVer": false, "minVer": false}
msg: Error running version command : goobleygoop
...ignoring
 
TASK: [test_version_check | debug version variable from the test] *************
ok: [localhost] => {
    "item": "",
    "version": {
        "changed": false,
        "failed": true,
        "installed": false,
        "invocation": {
            "module_args": "command=\"goobleygoop\"",
            "module_name": "version_check"
        },
        "item": "",
        "maxVer": false,
        "minVer": false,
        "msg": "Error running version command : goobleygoop"
    }
}
 
PLAY RECAP ********************************************************************
localhost                  : ok=19   changed=0    unreachable=0    failed=0
```



