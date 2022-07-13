# Logstash

Install logstash on a single node. No special configuration pushed

## Requirements

* Ubuntu 16.04 or Centos 7 on the target host

## Role Variables
> :information_source: Please check playbook.yml and default/main.yml file.

- xpack.monitoring.elasticsearch.hosts: Starting with v7 the parameter *xpack.monitoring.elasticsearch.__urls__* has been replaced; contains elasticsearch hosts used to stored i`.monitoring-*` indices.
- logstash_config_reload_automatic: if set to true, the conf.d/ files we be reload if changes occured
- logstash_config_reload_interval: default value is set to 30
- logstash_xpack_monitoring: activate monitoring feature, default is set to false in role. 
- logstash_xpack_monitoring_es_url: "{{ groups['default-elastic'] | map('regex_replace', '$', ':9200') |  map('regex_replace', '^', 'https://') | list }}"
- logstash_xpack_monitoring_es_username: monitoring user, default is set to logstash_system
- logstash_xpack_monitoring_es_password: monitoring user password; the default is set to ${logstash_system}; :warning: this value has to be use with keystore or env variable.
- logstash_config: insert below all other configuration parameters; see playbook.yml for an example.
- lg_heap_size => set xms,xmx value in jvm.options file

> e.g. You can use the following in your playbook.yml file: `lg_heap_size: "{{ '2' if ansible_memtotal_mb < 2048 else ( ansible_memtotal_mb /1024 - 1) | int }}g"`


### `es_major_version`

- Major version of the ES repo to pull the logstash packages from.
- Default value: **5.x**

### `es_yum_repo`

- The URL of the repository for logstash RPM's.
- Default value: **https://artifacts.elastic.co/packages/{{ es_major_version }}/yum**

### `es_yum_gpg`

- The URL for the GPG key that signed the packages in `es_yum_repo`.
- Default value: **https://artifacts.elastic.co/GPG-KEY-elasticsearch**

### `es_apt_repo`

- The URL of the repository for logstash DEB's.
- Default value: **deb https://artifacts.elastic.co/packages/{{ es_major_version }}/apt stable main**

### `es_apt_gpg`

- The URL for the GPG key that signed the packages in `es_apt_repo`.
- Default value: **{{ es_yum_gpg }}**

### `lg_heap_size`

- The default xms/xmx value set in `jvm.options`files.
- If ommitted, the default value will be set automatically by: 
`lg_heap_size: "{{ '2' if ansible_memtotal_mb < 2048 else ( ansible_memtotal_mb / 1024 - 1) | int }}g"`

### ssh credential
> :information_source: when your only option is to use the root user, you can start your playbook this way:

```bash
read -s PASS
   < enter your password here >
echo $PASS
ansible-playbook -i inventory play-logstash.yml -e "ansoible_ssh_pass=$PASS" --check --diff
```

## License

MIT

## Maintainer

[Gautier Franchini](mailto:gautier.franchini@data-essential.com)
[Rémi Laurent](mailto:remi.laurent@data-essential.com)

Highly inspired by ansible-role-kibana by [Spyridon Gouliarmis](mailto:spyridon.gouliarmis@data-essential.com)
