# Crowdsec
This ansible roles installs Crowdsec incl. hub, collections, scenarios, postoverflows, parsers, bouncers and prometheus endpoint.
You can use docker type installation for crowdset agent and LAPI server parts. See files in `templates/docker` subdir for details.

This version of the role is very different from the one used for the base. New logic, variables, templates, support for
installation in Docker have been added.
At the same time, its use has been simplified, duplicate settings and unnecessary files have been removed.

## Role Variables
Available variables with default values (see `defaults/main.yml`)
variables can be host specific in group_vars/host.yml

## Example Playbook
```yaml
- hosts: all

  vars:
    cs_install_type: 'docker'
    cs_enabled: true
    cs_lapi_agent: true
    cs_lapi_server: true
    cs_firewall_bouncer: true

    cs_bouncer_secret: CHANGE-IT-SOME-SECRET-BOUNCER-STRING
    cs_agent_secret: CHANGE-IT-SOME-SECRET-AGENT-STRING

    cs_parsers_mywhitelists_ip:
      - 8.8.8.8
      - 1.1.1.1
    cs_collections_list:
      - crowdsecurity/linux
      - crowdsecurity/nginx
      - crowdsecurity/base-http-scenarios
      - crowdsecurity/http-cve
      - crowdsecurity/vsftpd
      - pserranoa/openvpn
    cs_collections_remove_list:
      - crowdsecurity/apache2
      - crowdsecurity/iptables
    cs_prometheus_listen_addr: 0.0.0.0
    crowdsec_lapi_listen: 0.0.0.0
  roles:
    - zver.crowdsec
```

## Manual tasks could be handy
ansible somehostname -m shell -a "docker exec crowdsec cscli parsers install crowdsecurity/whitelists --force"
ansible 'cs_agents' -m shell -a "docker exec crowdsec cscli parsers remove crowdsecurity/whitelists --force"
ansible 'cs_server' -m shell -a "systemctl restart crowdsec-docker"

## Register agents and bouncers to lapi server
The role automatically detects lists of bouncers and agents and registers them in LAPI crowdsec server.

## Use lapi server with external MySQL database

This role was originally created to use local psql database for lapi server. Now you can use 
an external MySQL database with:

```
crowdsec_lapi_db: mysql # Use mysql or psql
crowdsec_mysql_db_user: crowdsec
crowdsec_mysql_db_password: 'VeryLongPasswordPsqlChangeme2024!'
crowdsec_mysql_db_name: crowdsec
crowdsec_mysql_db_host: localhost
```

The role asumes the MySQL database is already configured and the access is granted.

## Error reporting. 
Use github issues or make a PR. 

## Author Information
------------------
Initial role idea:
[Alf149](https://github.com/alf149)

Some improvments:
[Artefactual-Labs](https://github.com/artefactual-labs)

Almost the entire role has been rewritten by
[Zver](https://github.com/zver)
