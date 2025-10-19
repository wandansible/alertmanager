Ansible role: alertmanager
==========================

Install and configure alertmanager for prometheus.

Role Variables
--------------

```
ENTRY POINT: *main* - Install and configure alertmanager for prometheus

Options (= indicates it is required):

- alertmanager_amtool_config  Contents of the alertmanager amtool
                               config file, as string or dict
          default:
            alertmanager.url: http://localhost:9093
          type: raw

- alertmanager_amtool_config_dir  Directory for alertmanager amtool
                                   configuration
          default: /etc/amtool/
          type: str

- alertmanager_amtool_config_file  Filename for alertmanager amtool
                                    config file
          default: config.yml
          type: str

- alertmanager_arch_map  Mapping of the possible values of
                          ansible_architecture to the alertmanager
                          package architectures
          default: null
          type: dict

- alertmanager_bin_dir  Directory for the alertmanager binaries
          default: /opt/prometheus/alertmanager
          type: str

- alertmanager_checksum_type  The alertmanager package checksum type
          default: sha256
          type: str

- alertmanager_clean_src_dir  Remove old downloaded archive files
                               from alertmanager src directory
          default: true
          type: bool

- alertmanager_cli_tools  List of CLI tool binaries that should have
                           symlinks created in /usr/local/bin/
          default: [amtool]
          type: list

- alertmanager_config  Contents of the alertmanager config file, as
                        string or dict
          default: null
          type: raw

- alertmanager_config_dir  Directory for alertmanager configuration
          default: /etc/prometheus/alertmanager
          type: str

- alertmanager_config_file  Filename for alertmanager config file
          default: alertmanager.yml
          type: str

- alertmanager_db_dir  Directory for alertmanager database
          default: /var/lib/alertmanager
          type: str

- alertmanager_external_url  The URL under which Alertmanager is
                              externally reachable (for example, if
                              Alertmanager is served via a reverse
                              proxy). Used for generating relative and
                              absolute links back to Alertmanager
                              itself.
          default: null
          type: str

- alertmanager_extra_flags  Extra flags to run alertmanager with
          default: null
          type: dict

- alertmanager_flags  List of flags to run alertmanager with, as
                       string or list
          default: null
          type: raw

- alertmanager_github_checksum_filename  Filename for the
                                          alertmanager package
                                          checksums file on github
          default: sha256sums.txt
          type: str

- alertmanager_github_org  Name of organisation for alertmanager
                            github repository
          default: prometheus
          type: str

- alertmanager_github_repo  Name of alertmanager github repository
          default: alertmanager
          type: str

- alertmanager_group  Name of the alertmanager unix group
          default: alertmanager
          type: str

- alertmanager_install  If true, install alertmanager
          default: true
          type: bool

- alertmanager_listen  Listen address and port
          default: localhost:9093
          type: str

- alertmanager_log_format  Output format of log messages
          choices: [logfmt, json]
          default: logfmt
          type: str

- alertmanager_log_level  Only log messages with the given severity
                           or above
          choices: [debug, info, warn, error]
          default: warn
          type: str

- alertmanager_manage_user  If true, add alertmanager unix user and
                             group
          default: true
          type: bool

- alertmanager_port  Listen port
          default: 9093
          type: int

- alertmanager_src_dir  Directory for the downloaded alertmanager src
                         archive
          default: /opt/prometheus/alertmanager/src
          type: str

- alertmanager_src_files  List of files to extract from the source
                           archive
          default: [alertmanager, amtool]
          elements: str
          type: list

- alertmanager_storage_retention  How long to keep data for, specify
                                   as a number followed by time unit.
                                   The units supported are y, w, d, h,
                                   m, s, ms.
          default: null
          type: str

- alertmanager_strip_components  Strip NUMBER leading components from
                                  file names on extraction
          default: 1
          type: int

- alertmanager_template_dir  Directory for alertmanager templates
          default: /etc/prometheus/alertmanager/templates
          type: str

- alertmanager_template_files  List of alertmanager template files to
                                create
          default: null
          elements: dict
          type: list
          options:

          = config  Template file contents
            type: str

          = name  Name of template file
            type: str

- alertmanager_user  Name of the alertmanager unix user
          default: alertmanager
          type: str

- alertmanager_version  Version to install (use "latest" for the
                         latest version)
          default: latest
          type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/alertmanager,main,wandansible.alertmanager
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.alertmanager
      src: https://github.com/wandansible/alertmanager

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
        - role: wandansible.alertmanager
          become: true
          vars:
            alertmanager_config:
              route:
                receiver: dummy
                group_wait: 30s
                group_interval: 5m
                repeat_interval: 4h
                group_by: [instance, alertname]

              receivers:
                - name: dummy

              inhibit_rules:
                - source_matchers:
                    - severity="critical"
                  target_matchers:
                    - severity="warning"
                  # Apply inhibition if the alertname is the same.
                  # CAUTION:
                  #   If all label names listed in `equal` are missing
                  #   from both the source and target alerts,
                  #   the inhibition rule will apply!
                  equal: ['alertname']
