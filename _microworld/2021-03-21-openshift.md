---
layout: post
author-id: sbasischeck
title: Openshift
feature-img: "/assets/img/background/search-map.jpeg"
img: "/assets/img/background/search-map.jpeg"
date: 2021-03-21 20:31:00  +0200
tags: [openshift, microworld]
---

# OpenShift

Beschreibung der Nutzung von OpenShift im Cluster.
Bereitgestellt von der TSY
Die Erkenntnisse basieren auf der OpenShift Version `4.2`

* [Product Documentation for OpenShift Container Platform 4.2](https://access.redhat.com/documentation/de-de/openshift_container_platform/4.2/)

## Architektur

High-Level Übersicht

<img src="/assets/img/microworld/openshift/product-workflow-overview.png" width="100%" title="High-Level Übersicht" />

Installationsübersicht

<img src="/assets/img/microworld/openshift/create-nodes.png" width="100%" title="Nodes-Übersicht" />

## Service

Inhalt von service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
```

Inhalt der statefulset.yaml

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

## Installing on vSphere

## Applications

### Projects

Namespaces

Procedure

1. Navigate to Home → Projects.
2. Click Create Project.
3. Enter your project details.
4. Click Create.

### Application Lifecycle Management

#### Creating Applications

<img src="/assets/img/microworld/openshift/odc_add_view.png" width="100%" title="Add Application" />

Sourcen:
- From Git: Use this option to import an existing codebase in a Git repository to create, build, and deploy an application on OpenShift Container Platform.
- Container Image: Use existing images from an image stream or registry to deploy it on to OpenShift Container Platform.
- From Catalog: Explore the Developer Catalog to select the required applications, services, or source to image builders and add it to your project.
- From Dockerfile: Import a dockerfile from your Git repository to build and deploy an application.
- YAML: Use the editor to add YAML or JSON definitions to create and modify resources.
- Add Database: See the Developer Catalog to select the required database service and add it to your application.

## Commands

### Read logs

Lese aus nginx Container
```
oc logs --timestamps=true --tail=-1 -l 'app.kubernetes.io/instance in (occonnect-epa-apigateway,occonnect-digen-apigateway)' -c nginx  --max-log-requests=12 | grep -v "Prometheus/2.22.2" | grep -v "/actuator/health/readiness"
```
Lese Logs aus allen Containern und schreibe in File
```
oc logs --timestamps=true --tail=-1 -l 'app.kubernetes.io/instance in (occonnect-epa-alvi-soe-service)' --all-containers  --max-log-requests=12 | grep -v "Prometheus/2.22.2" | grep -v "actuator" > no-sapp-digen-reg-sor-c.log
```
auch ohne "Postman" Einträge im Log
```
oc logs --timestamps=true --tail=-1 -l 'app.kubernetes.io/instance in (occonnect-epa-apigateway,occonnect-digen-apigateway)' --all-containers  --max-log-requests=12 | grep -v "Prometheus/2.22.2" | grep -v "actuator" | grep -v "Postman"
```

### Clean up scenario
```
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | xargs oc scale --replicas=0 deployments
oc get deployments
oc get KafkaTopics --no-headers -o custom-columns=":metadata.name"  | grep -e digen -e epa | xargs oc delete KafkaTopics 
oc get KafkaTopics
oc get KafkaTopics --no-headers -o custom-columns=":metadata.name"  | grep -e digen -e epa -e error-handler | xargs oc delete KafkaTopics 
oc list statefulsets
oc get statefulsets
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep surgery | xargs oc scale --replicas=0 statefulset
oc get statefulsets
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc scale --replicas=0 statefulset
oc get statefulsets
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs echo oc delete pvc
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc delete pvc
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc scale --replicas=1 statefulset
oc project aok-nw-sapm
oc get statefulset
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc scale --replicas=1 statefulset
oc get statefulset
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc scale --replicas=0 statefulset
oc get statefulset
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs echo oc delete pvc
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc delete pvc
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep occonnect- | grep -v epa | xargs oc scale --replicas=1 statefulset
oc get pvc
oc get statefulset
helm list
```
```
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | grep -v epa
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | grep -v epa| xargs oc scale --replicas=0 deployments
oc exec  -it postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; echo \"SELECT distinct 'drop schema '||table_schema||' cascade;' FROM information_schema.tables where table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\" | \
     psql -U digen -d db-occonnect -h postgresql-ha-pgpool -t | \
     psql -U digen -d db-occonnect -h postgresql-ha-pgpool"
oc exec postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; psql -U digen -h postgresql-ha-pgpool -d db-occonnect -c \"SELECT * FROM  information_schema.tables WHERE table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\""
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | grep -v epa| xargs oc scale --replicas=1 deployments
oc exec postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; psql -U digen -h postgresql-ha-pgpool -d db-occonnect -c \"SELECT * FROM  information_schema.tables WHERE table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\""
oc project aok-nw-sapp
oc project aok-no-sapp
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | grep -v epa| xargs oc scale --replicas=0 deployments
oc exec  -it postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; echo \"SELECT distinct 'drop schema '||table_schema||' cascade;' FROM information_schema.tables where table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\" | \
     psql -U digen -d db-occonnect -h postgresql-ha-pgpool -t | \
     psql -U digen -d db-occonnect -h postgresql-ha-pgpool"
oc exec postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; psql -U digen -h postgresql-ha-pgpool -d db-occonnect -c \"SELECT * FROM  information_schema.tables WHERE table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\""
oc get KafkaTopics --no-headers -o custom-columns=":metadata.name"
oc get KafkaTopics --no-headers -o custom-columns=":metadata.name"  | grep -e digen
oc get KafkaTopics --no-headers -o custom-columns=":metadata.name"  | grep -e digen| xargs oc delete KafkaTopics 
oc login --token=8NnseC846DpL3ytscyddkZwk6GuyynX6gt2CSqnzuLI --server=https://api.ocp01.d100.loc:6443
oc get statefulset --no-headers -o custom-columns=":metadata.name" 
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep -v epa
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep -v epa | xargs oc scale --replicas=0 statefulset
oc get deployment --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer 
oc get deployment --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer | xargs oc scale --replicas=0 deployment
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer 
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer  -v epa
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer | grep  -v epa
oc get pvc --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer | grep  -v epa | xargs oc delete pvc
oc get statefulset --no-headers -o custom-columns=":metadata.name" | grep -v epa | xargs oc scale --replicas=1 statefulset
oc get deployment --no-headers -o custom-columns=":metadata.name" | grep -e sor-consumer -e sor-producer | xargs oc scale --replicas=1 deployment
oc login --token=tdfTBKEgXSHu0texwi_TmbmsWqT-D9tqYUqlUrlRRog --server=https://api.ocp02.d100.loc:6443
oc get deployments --no-headers -o custom-columns=":metadata.name" | grep soe-service | grep -v epa| xargs oc scale --replicas=1 deployments
oc exec postgresql-ha-postgresql-0 -- bash -c "export PGPASSWORD=\$POSTGRES_PASSWORD; psql -U digen -h postgresql-ha-pgpool -d db-occonnect -c \"SELECT * FROM  information_schema.tables WHERE table_schema <> 'pg_catalog' and table_schema <> 'information_schema' and table_catalog = 'db-occonnect';\""
```

## Graylog

https://graylog-mcs-logging.apps.ocp02.d100.loc/

### Suche

 Query: k8s_container_name:occonnect-digen-apigateway AND message:error

 ## Helm Commands

### list value file (also from older revisions)
```
helm list
helm get values occonnect-digen-apigateway
helm history occonnect-digen-apigateway
helm get values occonnect-digen-apigateway --revision 24
helm list
helm history occonnect-digen-record-adapter
helm get values occonnect-digen-record-adapter --revision 23
helm get values occonnect-digen-record-adapter 
```
