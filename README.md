# Ansible Playbook for Elasticsearch
This is an [Ansible](http://www.ansibleworks.com/) playbook for [Elasticsearch](http://www.elasticsearch.org/).

## Features
- Support for installing plugins
- Support for installing custom JARs in the Elasticsearch classpath (e.g. custom Lucene Similarity JAR)
- Support for installing the [Marvel](http://www.elasticsearch.org/guide/en/marvel/current/) plugin

## Requirements
- Java (version 8 recommended)
- Maven (3.1 or higher)

## Some role variables for custom installation
- `elasticsearch_version` - Elasticsearch version to install (default: `1.5.2`)
- `elasticsearch_network_http_port` - Elasticsearch http port (default: `9200`)
- `elasticsearch_service_startonboot` - Make sure Elasticsearch service is running after system boot (default: `yes`)
- `elasticsearch_service_state` - Run Elasticsearch service (default: `started`)

## Enabling Added Features

### Installing plugins

You will need to define an array called `elasticsearch_plugins` in the host/group vars file, such that:

```
elasticsearch_plugins:
 - { name: '<plugin name>', url: '<[optional] plugin url>' }
 - ...

```
By default searchguard, head and analysis-icu plugins are installed (see defaults/main.yml).

Concerning [searchguard plugin](https://github.com/floragunncom/search-guard) it is important to note that it [didn't support Elasticsearcl 1.7](https://github.com/floragunncom/search-guard/issues/41) yet. Otherwise, for the >1.7 versions it is possible to build and/or install the plugin. Just set the `elasticsearch_install_searchguard` flag to `tue` (enabled by default). The search-guard plugin should not be specified within "elasticsearch_plugins" list as it will has a custom installation.

### Installing Custom JARs
Custom jars are made available to the Elasticsearch classpath by being downloaded into the elasticsearch_home_dir/lib folder. You will need to define an array called `elasticsearch_custom_jars` in the corresponding playbook or inventory file, such that:

```
elasticsearch_custom_jars:
 - { uri: '<URL where JAR can be downloaded from: required>', filename: '<alternative name for final JAR if different from file downladed: leave blank to use same filename>', user: '<BASIC auth username: leave blank of not needed>', passwd: '<BASIC auth password: leave blank of not needed>' }
 - ...
```

### Configuring Thread Pools
Elasticsearch [thread pools](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/modules-threadpool.html) can be configured using the `elasticsearch_thread_pools` list variable:

```
elasticsearch_thread_pools:
  - "threadpool.bulk.type: fixed"
  - "threadpool.bulk.size: 50"
  - "threadpool.bulk.queue_size: 1000"
```

### Configuring Marvel

The following variables need to be defined in the playbook or inventory:

- elasticsearch_plugin_marvel_version

The following variables provide configuration for the plugin. More options may be available in the future (see [http://www.elasticsearch.org/guide/en/marvel/current/#stats-export](http://www.elasticsearch.org/guide/en/marvel/current/#stats-export)):

- elasticsearch_plugin_marvel_agent_enabled
- elasticsearch_plugin_marvel_agent_exporter_es_hosts
- elasticsearch_plugin_marvel_agent_indices
- elasticsearch_plugin_marvel_agent_interval
- elasticsearch_plugin_marvel_agent_exporter_es_index_timeformat

### Change timezone
If you prefer to set the time zone, typically if you are not using `ntp`, use the `elasticsearch_timezone` parameter:

```
elasticsearch_timezone: "Europe/Paris"
```
