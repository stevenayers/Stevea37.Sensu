# stevenayers.Sensu

### Ansible role for configuring Sensu.

**WARNING** This playbook only supports RHEL/CentOS at the moment.


## Example configuration
In our configuration, we are using Host and Group variables, which you can find detailed in the [best practise documentation detailed here.](http://docs.ansible.com/ansible/playbooks_best_practices.html#group-and-host-variables)

### Playbook configuration
You will define a very simple role definition inside your playbook, like so:

```
- hosts: all
  remote_user: ansible 
  sudo: yes
  roles:
    - role: sensu
```

### Group Variable configurations
For variables which are consistent across all boxes, define them inside **group_vars/all.yml:**

```
gem_repo_url: "http://localhost:8081/nexus/content/repositories/gems/"

sensu_package_path: 'https://sensu.global.ssl.fastly.net/yum/6/x86_64/sensu-0.29.0-7.el6.x86_64.rpm'

subscriptions:
 - "base"

plugins:
 - "sensu-plugins-memory-checks"
 - "sensu-plugins-disk-checks"
 - "sensu-plugins-load-checks"

```

You can then overwrite these values at group or host level according to how your inventory is organised. For example:

**inventory:**
```
[webservers]
webserver1.domain.com
10.1.1.10
webserver2
```

**group_vars/webservers.yml:**
```
subscriptions:
 - "base"
 - "web"

plugins:
 - "sensu-plugins-memory-checks"
 - "sensu-plugins-disk-checks"
 - "sensu-plugins-load-checks"
 - "sensu-plugins-http-checks"
```

**host_vars/webserver1.domain.com.yml:**
```
subscriptions:
 - "base"
 - "web"
 - "jenkins"

plugins:
 - "sensu-plugins-memory-checks"
 - "sensu-plugins-disk-checks"
 - "sensu-plugins-load-checks"
 - "sensu-plugins-http-checks"
 - "sensu-plugins-jenkins-checks"
```


## Variables

| Name                  | Default Value                | Description                  |
|-----------------------|------------------------------|------------------------------|
| environment_name      | 'default'                    | Environment the Sensu Server and Client are in, a server and client can only be in one environment at a time. |
| gem_repo_url          | '' (empty string)            | Private gem repo url, specify the repo url here. |
| sensu_package_path    | '' (empty string)            | If installing sensu via package file, specify the path or URL here. |
| libselinux_python_package_path    | '' (empty string)            | If installing libselinux-python via package file, specify the path or URL here. |
| gem_executable        | '/opt/sensu/embedded/bin/gem'| The gem executable path used for installing sensu plugins. |
| rabbitmq_host         | 'localhost'                  | The hostname of your RabbitMQ Instance. |
| rabbitmq_port         | 5671                         | The port of your RabbitMQ Instance, the default port is the SSL port.|
| rabbitmq_vhost        | '/sensu'                     | The virtual host for RabbitMQ |
| rabbitmq_user         | 'sensu'                      | The default user for RabbitMQ |
| rabbitmq_password     | 'randompassword'             | The default password for RabbitMQ. You **MUST** change this password. |
| rabbitmq_use_ssl      | true                         | Specify whether or not you're using SSL for RabbitMQ transport. |
| rabbitmq_ssl_cert_path| '/etc/sensu/ssl/cert.pem'    | Destination for RabbitMQ SSL cert path. |
| rabbitmq_ssl_key_path | '/etc/sensu/ssl/key.pem'     | Destination for RabbitMQ SSL key path. |
| subscriptions         | [] (empty list)              | List of the subscriptions you want the target node to be enrolled with.|
| plugins               | [] (empty list)              | List of the plugins you want installed on the target node. |
