# Ansible Snippets

A collection of Ansible snippets and examples.  These are small examples cut from [my Ansible playbooks repository](https://github.com/bonovoxly/playbook).

I simplified this to one page to make it a little more clear.

# Using a Default Variable

~~~yml
# sets a default fact, if the Ansible variable `var` doesn't exist.
- name: debug variable, default to `this is just text`.
  debug:
    msg: "{{ var | default('this is just text') }}"

- name: debug variable, default to another variable, `another_var`.
  debug:
    msg: "{{ var | default(another_var) }}"
~~~

# Getting EC2 Remote Facts

~~~yml
# gathers facts and registers the output as `ec2_facts`
- name: Gather EC2 facts.
  ec2_remote_facts:
    region: "{{ region | default(us-east-1) }}"
  register: ec2_facts

# debug that output
- name: Debug
  debug:
    msg: "{{ aws_ec2_facts }}"

# print out all running instance IDs and their IP addresses.
- name: Get only running instance IP addresses.
  debug:
    msg: "Instance: {{ item.0 }} has IP address: {{ item.1 }}"
  with_together:
    - "{{ aws_ec2_facts.instances|selectattr('state', 'equalto', 'running')|map(attribute='tags.Name')|list }}"
    - "{{ aws_ec2_facts.instances|selectattr('state', 'equalto', 'running')|map(attribute='private_ip_address')|list }}"
~~~

# Add EC2 Instances Found to an Ansible Group

~~~yml
{% raw %}
- name: Add instances to running Ansible group in memory (not persistent between playbook runs).
  add_host:
    groups: "{{ item.0 }}"
    hostname: "{{ item.1 }}"
  with_together:
    - "{{ ec2_facts.instances|selectattr('state', 'equalto', 'running')|map(attribute='tags.Name')|list }}"
    - "{{ ec2_facts.instances|selectattr('state', 'equalto', 'running')|map(attribute='private_ip_address')|list }}"
{% endraw %}
~~~

# Splitting a Variable

~~~yml
# set the variable facts
- name: Set our example path and filenames.
  set_fact:
    path: /path/to/location
    file: software-2.0.0.tar

- name: Untar the file
  unarchive:
    src: "{{ file  }}"
    dest: "{{ path }}"

- name: Move the symbolic link.
  file:
    dest: "{{ localhost_split.software_tar_file_location }}{{ localhost_split.software_tar_file.split('-')[0] }}"
    src: "{{ localhost_split.software_tar_file_location }}{{ localhost_split.software_tar_file.split('.tar')[0] }}/"
    state: link
~~~

# HipChat Notify

~~~yml
# Hipchat/templated message notification example
- name: Create notify message from template.
  template:
    src: notify.txt.j2
    dest: notify.txt
- name: HipChat notify.
  hipchat:
    api: "{{ hipchat_vars.api }}"
    color: "{{ hipchat_vars.color }}"
    from: "{{ hipchat_vars.from }}"
    msg: "{{ lookup('file', './notify.txt') }}"
    msg_format: html
    room: "{{ hipchat_vars.room }}"
    token: "{{ hipchat_vars.token }}"
- name: Delete temporary notify text.
  file:
    path: notify.txt
    state: absent
~~~
