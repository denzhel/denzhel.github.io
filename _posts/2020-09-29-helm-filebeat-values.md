---
layout: post
title: Filebeat Helm3 chart values
date:   2020-09-29
categories: Kubernetes
---
Before getting to this setup, I've searched a lot and found bits of information each time.

I wanted to share our helm values setup that actually works:
```yml
image:
  repository: docker.elastic.co/beats/filebeat-oss
  tag: "7.8.0"

config:
  filebeat.registry.path: ./fb_registry
  filebeat.inputs:
    - enabled: false

  filebeat.autodiscover:
    providers:
      - type: kubernetes
        scope: node
        hints.enabled: false
        templates:
          - condition:
              has_fields: ['kubernetes.container.id']
            config:
              - type: container
                paths:
                  - /var/lib/docker/containers/$${data.kubernetes.container.id}/*.log
                encoding: utf-8
                processors:
                  - drop_event:
                      when:
                        contains:
                          kubernetes.container.name: "telepresence"
                  - drop_event:
                      when:
                        equals:
                          kubernetes.namespace: "monitoring"
                  - drop_event:
                      when:
                        equals:
                          kubernetes.namespace: "kube-system"
                  - decode_json_fields:
                      fields: ["message"]
                      max_depth: 1
                      target: ""
                      overwrite_keys: true
                      when:
                        regexp:
                          message: '^{'
                  - rename:
                      fields:
                        - from: "organization"
                          to: "organization_name"
                      ignore_missing: true
                      fail_on_error: true

  processors:
    - add_cloud_metadata:
    - add_host_metadata:
    - add_kubernetes_metadata:

  http.enabled: true
  logging.to_stderr: true
    #logging.level: debug
  output.file:
    enabled: false
  output.elasticsearch:
    bulk_max_size: 1024
    hosts: ["${logs_es_protocol}://${logs_es_cluster}:${logs_es_port}"]
    index: "filebeat-%%{[kubernetes.namespace]}-%%{[agent.version]}-%%{+yyyy.MM.dd}"
  setup.template.name: "filebeat"
  setup.template.pattern: "filebeat-*"
  setup.ilm.enabled: false
```

Explenations:
```
filebeat.registry.path		# If you're not downgrading from 7.9.X <, you don't have to configure it.
rename.fields.from.organization		# Apparently, elasticsearch has a default mapping key named organization, had to rename it since of conflicts with our data.
```
