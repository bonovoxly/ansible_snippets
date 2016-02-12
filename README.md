# ansible_snippets

A collection of Ansible snippets and examples, broken up by roles.  Playbook is run by:

~~~bash
ansible-playbook -i localhost --vault-password-file ~/.ansible/YOUR_VAULT snippets.yml
~~~

# roles

- [aws.ec2_remote_facts](https://github.com/bonovoxly/ansible_snippets/tree/master/roles/aws.ec2_remote_facts)
