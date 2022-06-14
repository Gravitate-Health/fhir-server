Gravitate-Health FHIR Server
=================================================

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Table of contents
-----------------

- [Gravitate-Health FHIR Server](#gravitate-health-fhir-server)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Installation](#installation)
    - [Kubernetes deployment](#kubernetes-deployment)
  - [Usage](#usage)
  - [Known issues and limitations](#known-issues-and-limitations)
  - [Getting help](#getting-help)
  - [Contributing](#contributing)
  - [License](#license)
  - [Authors and history](#authors-and-history)
  - [Acknowledgments](#acknowledgments)


Introduction
------------
This repository contains the files needed for the deployment of a FHIR Server in a Kubernetes cluster. For the instalation the public [Helm Chart](https://github.com/hapifhir/hapi-fhir-jpaserver-starter/tree/master/charts/hapi-fhir-jpaserver) is used, as well as the official version of [HAPI FHIR JPA Server Starter](https://github.com/hapifhir/hapi-fhir-jpaserver-starter).

Installation
------------

### Kubernetes deployment

Following the instructions in the [Helm Chart](https://github.com/hapifhir/hapi-fhir-jpaserver-starter/tree/master/charts/hapi-fhir-jpaserver) documentation porceed with the installation. The [values.yaml](gh-values.yaml) file has been modified for the Gravitate Health platform, you can find the full list of variables in the original repository, below is a list of the changes made:

| Variable                       | value       |
|--------------------------------|-------------|
| image.tag                      | v6.0.1      |
| nameOverride                   | fhir-server |
| fullnameOverride               | fhir-server |

To install run:

```bash
helm repo add hapifhir https://hapifhir.github.io/hapi-fhir-jpaserver-starter/
helm install --render-subchart-notes hapi-fhir-jpaserver hapifhir/hapi-fhir-jpaserver --values=gh-values.yaml
```
The response should look like the following:

```
NAME: hapi-fhir-jpaserver
LAST DEPLOYED: Tue Jun 14 10:06:05 2022
NAMESPACE: development
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace development -l "app.kubernetes.io/name=fhir-server,app.kubernetes.io/instance=hapi-fhir-jpaserver" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace development $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace development port-forward $POD_NAME 8080:$CONTAINER_PORT

CHART NAME: postgresql
CHART VERSION: 11.6.2
APP VERSION: 14.3.0

** Please be patient while the chart is being deployed **

PostgreSQL can be accessed via port 5432 on the following DNS names from within your cluster:

    hapi-fhir-jpaserver-postgresql.development.svc.cluster.local - Read/Write connection

To get the password for "postgres" run:

    export POSTGRES_PASSWORD=$(kubectl get secret --namespace development hapi-fhir-jpaserver-postgresql -o jsonpath="{.data.postgres-password}" | base64 -d)

To connect to your database run the following command:

    kubectl run hapi-fhir-jpaserver-postgresql-client --rm --tty -i --restart='Never' --namespace development --image docker.io/bitnami/postgresql:14.3.0-debian-10-r20 --env="PGPASSWORD=$POSTGRES_PASSWORD" \
      --command -- psql --host hapi-fhir-jpaserver-postgresql -U postgres -d fhir -p 5432

    > NOTE: If you access the container using bash, make sure that you execute "/opt/bitnami/scripts/postgresql/entrypoint.sh /bin/bash" in order to avoid the error "psql: local user with ID 1001} does not exist"

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace development svc/hapi-fhir-jpaserver-postgresql 5432:5432 &
    PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d fhir -p 5432
```

To check if the resources have been created successfully run:

```bash
kubectl get all | grep "fhir"
```
```bash
# Deployments
NAME                                              READY   STATUS    RESTARTS        AGE
pod/fhir-server-56dc46f89c-nt4s8                  1/1     Running   0               6d2h
pod/hapi-fhir-jpaserver-postgresql-0              1/1     Running   0               6d2h
# Services
NAME                                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/hapi-fhir-jpaserver-postgresql-hl   ClusterIP   None             <none>        5432/TCP            3m24s
service/fhir-server                         ClusterIP   10.152.183.72    <none>        8080/TCP,9090/TCP   3m23s
service/hapi-fhir-jpaserver-postgresql      ClusterIP   10.152.183.19    <none>        5432/TCP            3m22s
```

If the pods are ready you can access the service by other services in the same namespace by using the name of its Kubernetes service and the port. Moreover, if the Kubernetes cluster has a DNS manager other services can access services in other namespaces using the following URL: ```http://<service-name>.<namespace>.svc.cluster.local```. To learn more about the types of services and its uses in Kubernetes, here is the [official documentation](https://kubernetes.io/docs/concepts/services-networking/). Alternatively if the [Gateway](https://github.com/Gravitate-Health/Gateway) has been deployed, the service will be proxied to the outside of the cluster at `https://<DNS>/fhir/`.

Usage
-----

Please refer to the official documentation of [HAPI FHIR](https://hapifhir.io/hapi-fhir/docs/).

Known issues and limitations
----------------------------
None are known at this time

Getting help
------------

In case you find a problem or you need extra help, please use the issues tab to report the issue.

Contributing
------------

To contribute, fork this repository and send a pull request with the changes squashed.

License
-------

This project is distributed under the terms of the [Apache License, Version 2.0 (AL2)](http://www.apache.org/licenses/LICENSE-2.0).  The license applies to this file and other files in the [GitHub repository](https://github.com/Gravitate-Health/Gateway) hosting this file.

```
Copyright 2022 Universidad Politécnica de Madrid

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

Authors and history
---------------------------
- Jens Kristian Villadsen ([@jkiddo](https://github.com/jkiddo))
- Álvaro Belmar ([@abelmarm](https://github.com/abelmarm))

Acknowledgments
---------------
 - [HAPI FHIR Server project](https://github.com/hapifhir/hapi-fhir)

