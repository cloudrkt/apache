Role Name
=========

Install the apache2 webserver. 

Requirements
------------

None

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

All possible variables you can set in your playbooks, host and group vars: 

```yaml
apache_server_version: 2.4
apache_server_state: "running"
apache_server_service_name: "apache2"
apache_server_update_packages: "installed"
apache_server_additional_packages: 
  - apachetop

apache_server_modules:
  - "libapache2-mod-bw"

apache_server_modules_disabled: []
apache_server_modules_enabled:
  - "rewrite"
  - "ssl"
  - "bw"

apache_server_cnf_path: "/etc/apache2/"
apache_server_cnf_file: "apache2.conf"

apache_server_enabled_vhosts: []
apache_server_disabled_vhosts: []

apache_server_global_cnf_mutex: "file:${APACHE_LOCK_DIR}"
apache_server_global_cnf_pidfile: "${APACHE_PID_FILE}"
apache_server_global_cnf_user: "${APACHE_RUN_USER}"
apache_server_global_cnf_group: "${APACHE_RUN_GROUP}"

apache_server_global_cnf_servername: "{{ ansible_fqdn }}"
apache_server_global_cnf_hostnamelookups: "Off"
apache_server_global_cnf_accessfilename: ".htaccess"

apache_server_global_cnf_timeout: 300
apache_server_global_cnf_keepalive: "On"
apache_server_global_cnf_maxkeepaliverequests: 100
apache_server_global_cnf_keepalivetimeout: 5

apache_server_global_cnf_errorlog: "${APACHE_LOG_DIR}/error.log"
apache_server_global_cnf_loglevel: "warn"
apache_server_global_cnf_logformats: 
  - name: "vhost_combined_special"
    value: '"%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\" %D"'

apache_server_global_cnf_file_includes: []
apache_server_global_cnf_file_includes_optional: []

apache_server_global_http_ports:
  - port: 80
apache_server_global_https_ports:
  - port: 443
```

Default vars file:

```yaml
apache_server_role_os_family_supported:
  - 'Debian'

apache_server_versions_supported:
  - '2.4'

apache_server_dependencies:
  - "apache2={{ apache_server_version }}*"

apache_server_main_cnf: "{{ apache_server_cnf_path }}{{ apache_server_cnf_file }}"
```

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
    - hosts: servers
      roles:
         - cloudrkt.apache
```

Delete vhost:

```yaml
apache_server_enabled_vhosts: 
  - vhost: "000-cloudrkt.com.conf"
```

Create vhost:

```yaml
apache_server_enabled_vhosts: 
  - vhost: "000-cloudrkt.com.conf"
    servername: "www.cloudrkt.com"
    serveralias: "cloudrkt.com"
    serveradmin: "systemadministrator@cloudrkt.com"
    document_root: "/var/www/html"
    setenv:
      - name: "APPLICATION_ENV"
        value: "Production"
      - name: "SPECIAL_THANKS_ENV"
        value: "CloudRKT"
    directory:
      - path: "/"
        parameters: >
          AllowOverride All
                Options -Indexes +FollowSymLinks
                Require all granted
      - path: "/conf"
        parameters: >
          AllowOverride All
                Options -Indexes +FollowSymLinks
                Require all denied
    http_listen:
      - port: 80
        ip: "*"
    extra_http_vhost_parameters: |
      RewriteEngine Off
    https_listen:
      - port: 443
        ip: "*"
    certificate_file: "/etc/ssl/certs/ssl-cert-snakeoil.pem"
    certificate_key_file: "/etc/ssl/private/ssl-cert-snakeoil.key"
    #certificate_chain_file: 
    sslhonorcipherorder: "on"
    sslusestapling: "on"
    sslcompression: "off"
    sslstaplingcache: "shmcb:logs/stapling-cache(150000)"
    sslciphersuite: "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH"
    extra_https_vhost_parameters: |
      RewriteEngine On
          RewriteCond "%{TIME_HOUR}" ">=20" [OR]
          RewriteCond "%{TIME_HOUR}" "<07" 
          RewriteRule "^/rkt"     "-" [F]
```

Simple vhost:

```yaml
  - vhost: "000-example.com.conf"
    servername: "www.example.com"
    document_root: "/var/www/html"
    http_listen:
      - port: 80
        ip: "*"
    directory:
      - path: "/"
        parameters: >
          AllowOverride All
                Options -Indexes +FollowSymLinks
                Require all granted
  ```

License
-------

BSD

Author Information
------------------

More about this apache role on https://cloudrkt.com/role-like-a-boss.html

