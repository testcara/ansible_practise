Using Ansible uri module to do the api testing
== 
Uri is one ansible module to connect and talk to the web service.
We can get and create/update/delete resouce with it from/against the web service. 
It supports the multiple authentication mode way to login the web service.
One very common scenario is that we use the module to do the API testing or get some instant credentials or key files.

Examples
- To get the instant credentials or key files
```
---
- name: Request Certificate SSL
  uri:
    url: "{{ controller_url }}/ca/errata"
    method: POST
    body: "{\"sans\": [\"errata\",\"errata-{{ pipeline_id }}.{{ paas_domain }}\"]}"
    body_format: json
    return_content: True
  register: certificate
- name: Get CA certificate
  uri:
    url: "{{ controller_url }}/ca/cacert"
    return_content: True
  register: cacert
- name: Get DB Password
  uri:
    url: "{{ controller_url }}/vars/ERRATA_DB_PASSWORD?default=generate"
    return_content: True
  register: dbpassword
```
- to do the API testing
```
  - name: Create the user of the web service by the web API
    uri:
      url: https://errata-web-03.host.qe.eng.pek2.redhat.com/api/v1/user
      user: "{{ username }}"
      password: "{{ password }}"
      method: POST
      status_code: 201
      body: "{{  lookup('file', 'user.json') }}"
      return_content: yes
      body_format: json
    register: user_content

  - assert: 
      that:
        - '"{{ name}}" in "{{ user_content.json.realname }}"'
      fail_msg: 'User is not created as expect!'
```
BTW, you see the variables are used in above examples.
When you run this kind ansible, you can run cmds like 'ansible-playbook *.yml -e "name=testingansible username=wlin@redhat.com password=password"' 