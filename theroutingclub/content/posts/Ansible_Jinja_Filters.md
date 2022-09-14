---
title: "Ansible Data Filtering"
date: 2022-05-19
draft: false
tags: [Ansible, Filters, Jinja2]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "In this post, we will see how we can use jinja2 filters for data manipulation as per the requirement in the Ansible playbook."
# AuthorName: "Nilesh Gore"
---

In this post, we will see how we can use jinja2 filters for data manipulation as per the requirement in the Ansible playbook.

We will take example of the following table which is for Cisco ACI nodes registration. When the Cisco ACI fabric is brought up, the newly connected switches are shown as unregistered with just their serial number in the APIC controller.

In order to register the newly connected switches we need to provide the node id and the hostname for the switch.

| hostname  | serial    | nodeid |
|-----------|-----------|--------|
| leaf-101  | TEP-1-101 | 101    |
| leaf-102  | TEP-1-102 | 102    |
| spine-201 | TEP-1-103 | 201    |


We have defined 2 variables in this playbook.
1.  **nodes**: this is list of dictionary. Thus each element starts with a hyphen (-)
1.  **discovered_id**: this is list of strings  

```yaml
---
- name: Data Filtering
  hosts: localhost
  vars:
    - nodes:
      - serial: TEP-1-101
        id: 101
        name: leaf-101
      - serial: TEP-1-102
        id: 102
        name: leaf-102
      - serial: TEP-1-103
        id: 201
        name: spine-201
    - discovered_id:
      - TEP-1-101
      - TEP-1-102
      - TEP-1-103
  tasks:
    - name: Print Filtered Values
      debug:
        msg: "{{ nodes }}"
```

Yaml Linted Playbook

```yaml
- hosts: localhost
  name: Data Filtering
  vars:
  - nodes:
    - {id: 101, name: leaf-101, serial: TEP-1-101}
    - {id: 102, name: leaf-102, serial: TEP-1-102}
    - {id: 201, name: spine-201, serial: TEP-1-103}
  - discovered_id: [TEP-1-101, TEP-1-102, TEP-1-103]
  tasks:
  - debug: {msg: '{{ nodes }}'}
    name: Print Filtered Values
```

Firstly, let us see how does the output looks like when we just print the variables as is one by one

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] ****************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [localhost]

TASK [Print Filtered Values] *******************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        {
            "id": 101,
            "name": "leaf-101",
            "serial": "TEP-1-101"
        },
        {
            "id": 102,
            "name": "leaf-102",
            "serial": "TEP-1-102"
        },
        {
            "id": 201,
            "name": "spine-201",
            "serial": "TEP-1-103"
        }
    ]
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Note that in the output the message value is of type list, because the variable we provided to the debug module is list (nodeids)

Now let us see how the output looks when we provide both the variables to the debug module



```yaml
  tasks:
  - name: Print Filtered Values
    debug:
      msg:
          - "{{ nodes }}"
          - "{{ discovered_id }}"
```
Yaml linted playbook task

```yaml
  tasks:
  - name: Print Filtered Values
    debug:
      msg: ['{{ nodes }}', '{{ discovered_id }}']
```

Output after playbook modified to print both the variables

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [check ansible filter] ****************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        [
            {
                "id": 101,
                "name": "leaf-101",
                "serial": "TEP-1-101"
            },
            {
                "id": 102,
                "name": "leaf-102",
                "serial": "TEP-1-102"
            },
            {
                "id": 201,
                "name": "spine-201",
                "serial": "TEP-1-103"
            }
        ],
        [
            "TEP-1-103",
            "TEP-1-101",
            "TEP-1-102"
        ]
    ]
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Now in this case, the msg value is list of list.
Since we provided 2 list variables to debug module's msg method in a list. It is printing the variables one after another as elements of list.


Next let us use ansible loop to print each element of the list.

```yaml
---
- name: Data Filtering
  hosts: localhost
  vars:
    - nodes:
      - serial: TEP-1-101
        id: 101
        name: leaf-101
      - serial: TEP-1-102
        id: 102
        name: leaf-102
      - serial: TEP-1-103
        id: 201
        name: spine-201
    - discovered_id:
      - TEP-1-101
      - TEP-1-102
      - TEP-1-103
  tasks:
    - name: Print Filtered Values
      debug:
        msg:
          - "{{ item }}"
      loop:
        - "{{ discovered_id }}"
```

yaml linted playbook

```yaml
- hosts: localhost
  name: Data Filtering
  tasks:
  - debug:
      msg: ['{{ item }}']
    loop: ['{{ discovered_id }}']
    name: Print Filtered Values
  vars:
  - nodes:
    - {id: 101, name: leaf-101, serial: TEP-1-101}
    - {id: 102, name: leaf-102, serial: TEP-1-102}
    - {id: 201, name: spine-201, serial: TEP-1-103}
  - discovered_id: [TEP-1-103, TEP-1-101, TEP-1-102]
```

The output is list of list. Bacause when have provided the loop variable inside a list

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [localhost]

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item=['TEP-1-103', 'TEP-1-101', 'TEP-1-102']) => {
    "msg": [
        [
            "TEP-1-103",
            "TEP-1-101",
            "TEP-1-102"
        ]
    ]
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Let us fix the error and provide the variable to the loop without list. And also msg without list.

```yaml
  tasks:
    - name: Print Filtered Values
      debug:
        msg:
          "{{ item }}"
      loop:
         "{{ discovered_id }}"
```

Yaml linted

```yaml
  tasks:
  - debug:
      msg: '{{ item }}'
    loop: '{{ discovered_id }}'
    name: Print Filtered Values
```

Now the ouput below looks correct, wherein debug module is rightly displaying the msg of each element as one iteration.

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item=TEP-1-103) => {
    "msg": "TEP-1-103"
}
ok: [localhost] => (item=TEP-1-101) => {
    "msg": "TEP-1-101"
}
ok: [localhost] => (item=TEP-1-102) => {
    "msg": "TEP-1-102"
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Accessing the desired key/values from the list of dictionaries.
For example, let us access the serial of the nodes from the dictionaries inside the nodes list.

```yaml
  tasks:
    - name: Print Filtered Values
      debug:
        msg:
          "{{ item.serial }}"
      loop:
         "{{ nodes }}"
```

Yaml linted playbook

```yaml
  tasks:
  - debug: {msg: '{{ item.serial }}'} # or {msg: '{{ item["serial"] }}'}
    loop: '{{ nodes }}'
    name: Print Filtered Values
```


Output

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item={'id': 101, 'name': 'leaf-101', 'serial': 'TEP-1-101'}) => {
    "msg": "TEP-1-101"
}
ok: [localhost] => (item={'id': 102, 'name': 'leaf-102', 'serial': 'TEP-1-102'}) => {
    "msg": "TEP-1-102"
}
ok: [localhost] => (item={'id': 201, 'name': 'spine-201', 'serial': 'TEP-1-103'}) => {
    "msg": "TEP-1-103"
}
```

Accessing elements of list based on conditions

```yaml
  tasks:
    - name: Print Filtered Values
      debug:
        msg:
         "{{ nodes | selectattr('serial', 'equalto', item) }}"
      loop:
         "{{ discovered_id }}"
```
Yaml linted playbook

```yaml
  tasks:
  - debug: {msg: '{{ nodes | selectattr("serial", "equalto", item) }}'}
    loop: '{{ discovered_id }}'
    name: Print Filtered Values
```

Output

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item=TEP-1-103) => {
    "msg": [
        {
            "id": 201,
            "name": "spine-201",
            "serial": "TEP-1-103"
        }
    ]
}
ok: [localhost] => (item=TEP-1-101) => {
    "msg": [
        {
            "id": 101,
            "name": "leaf-101",
            "serial": "TEP-1-101"
        }
    ]
}
ok: [localhost] => (item=TEP-1-102) => {
    "msg": [
        {
            "id": 102,
            "name": "leaf-102",
            "serial": "TEP-1-102"
        }
    ]
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

since the filtering is applied on "nodes" which is of type list, each of the output which is filtered based on the "discovered_id" matching with the "serial" is also a list.

Going a step futher, now we want to print only the value of key "id" from the selected element of "nodes" list.

```yaml
  tasks:
    - name: Print Filtered Values
      debug:
        msg:
         "{{ nodes | selectattr('serial', 'equalto', item) | map(attribute='id') }}"
      loop:
         "{{ discovered_id }}"
```

Yaml linted playbook

```yaml
  tasks:
  - debug: {msg: '{{ nodes | selectattr("serial", "equalto", item) | map(attribute="id") }}'}
    loop: '{{ discovered_id }}'
    name: Print Filtered Values
```

Output

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item=TEP-1-103) => {
    "msg": [
        201
    ]
}
ok: [localhost] => (item=TEP-1-101) => {
    "msg": [
        101
    ]
}
ok: [localhost] => (item=TEP-1-102) => {
    "msg": [
        102
    ]
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

However, in this output, each element is still of type list because it is filtered from "nodes" which is list.
Next we will fix the playbook to get the output without list.

```yaml
  tasks:
  - debug: {msg: '{{ nodes | selectattr("serial", "equalto", item) | map(attribute="id") | first }}'}
    loop: '{{ discovered_id }}'
    name: Print Filtered Values
```

Output

```bash
[nilesh@localhost ansibleplaybooks]$ ansible-playbook filterplaybook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Data Filtering] **********************************************************************************************************************************

TASK [Print Filtered Values] ***************************************************************************************************************************
ok: [localhost] => (item=TEP-1-103) => {
    "msg": "201"
}
ok: [localhost] => (item=TEP-1-101) => {
    "msg": "101"
}
ok: [localhost] => (item=TEP-1-102) => {
    "msg": "102"
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
