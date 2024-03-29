---
title : "Sakuli Dashboard"
date :  2019-09-12T14:16:46+02:00
weight : 6
---

# Sakuli Dashboard

The Sakuli Dashboard streamlines your e2e application monitoring with Sakuli. It helps you start your Sakuli test
automatically with a cronjob to monitor your application continuously or trigger it manually with a button.
While watching a live video feed of your Sakuli check, you can track the monitoring metrics of your application by
embedding your monitoring graphs into the Sakuli Dashboard. 

## How to get the Sakuli Dashboard

Sakuli Dashboard releases are versioned following the [semantic versioning scheme](https://semver.org/).
Images for stable releases are tagged accordingly.
Tech-previews of upcoming stable releases are available via the `latest` tag.  
It is *highly* discouraged to run tech-previews in production as there is no guarantee of stability.

- Deploy the Sakuli Dashboard with [Docker](#deployment-with-docker)
- Deploy the Sakuli Dashboard with [Docker-Compose](#deployment-with-docker-compose)
- Deploy the Sakuli Dashboard with [Kubernetes](#deployment-with-k8s)
- Deploy the Sakuli Dashboard with [Openshift](#deployment-with-openshift)

> You will come across configurations such as the `DASHBOARD_CONFIG`, the `ACTION_CONFIG`, the `CLUSTER_CONFIG` and
the `CRONJOB_CONFIG` which configure your Sakuli Dashboard. 
For further information about how to properly configure the dashboard check out the [Sakuli Dashboard Configuration](/docs/modules/sakuli-dashboard-configuration) documentation.

### Sakuli Dashboard with Docker {#deployment-with-docker}

#### 1 Obtaining the image

{{<highlight bash>}}
docker pull taconsol/sakuli-dashboard:<IMAGE_TAG>
{{</highlight>}}

#### 2 Running the Sakuli Dashboard

{{< highlight bash >}}
docker run --rm \
 -p 8080:8080 \
 -e DASHBOARD_CONFIG="${DASHBOARD_CONFIG}" \
 -e ACTION_CONFIG="${ACTION_CONFIG}" \
 -e CLUSTER_CONFIG="${CLUSTER_CONFIG}" \
 -e CRONJOB_CONFIG="${CRONJOB_CONFIG}" \
 taconsol/sakuli-dashboard:<IMAGE_TAG>
{{</ highlight >}}

Parameters:

- **\-\-rm**: The dashboard container will be removed after execution, not just stopped
- **-p**: Port forwarding to access the dashboard container on port 8080
- **-e**: Environment variable flags which are used to configure the dashboard

### Sakuli Dashboard with Docker-Compose {#deployment-with-docker-compose}

The following template allows you to run a dashboard using `docker-compose`:

{{< highlight yaml >}}
version: "3"
services:
    sakuli:
        container_name: sakuli-dashboard
        image: taconsol/sakuli-dashboard:<IMAGE_TAG>
        ports:
            - 8080:8080
        environment:
            - DASHBOARD_CONFIG=${DASHBOARD_CONFIG}
            - ACTION_CONFIG=${ACTION_CONFIG}
            - CLUSTER_CONFIG=${CLUSTER_CONFIG}
            - CRONJOB_CONFIG=${CRONJOB_CONFIG}
{{</ highlight >}}

After creating the `<filename>.yml` configuration file, you can start the dashboard with:
{{< highlight bash >}}
docker-compose up -f /path/to/file/<filename>.yml
{{</ highlight >}}

### Sakuli Dashboard on Kubernetes {#deployment-with-k8s}

You can start your Sakuli Dashboard using two different approaches:
- Set up the Sakuli Dashboard manually via [CLI](#k8s-cli).
- Set up the Sakuli Dashboard using a ready to use [template](#k8s-dashboard-template).
 
#### Set up the Sakuli Dashboard manually {#k8s-cli}

As a first step, create a deployment based on the Sakuli Dashboard image and expose the service.

{{<highlight bash>}}
kubectl create deployment sakuli-dashbaord --image=taconsol/sakuli-dashboard:<IMAGE_TAG>

kubectl expose deployment sakuli-dashboard --type=LoadBalancer --port=8080
{{</highlight>}}

The `--type=LoadBalancer` flag is important to make your service available outside your cluster.

Now add your Sakuli Dashboard configurations such as the dashboard, action, cluster and cronjob configs to 
the environment of your deployment.

{{<highlight bash>}}
kubectl set env deployment/sakuli-dashboard --overwrite \
 DASHBOARD_CONFIG="${DASHBOARD_CONFIG}" \
 ACTION_CONFIG="${ACTION_CONFIG}" \
 CLUSTER_CONFIG="${CLUSTER_CONFIG}" \
 CRONJOB_CONFIG="${CRONJOB_CONFIG}"
{{</highlight>}}

#### Set up Sakuli Dashboard using a Template {#k8s-dashboard-template}

The following ready-to-use template will configure a Sakuli Dashboard and deploy it to your cluster
Just copy and save the dashboard template below as `dashboard-template.yml`. Furthermore, add your dashboard, action, 
cluster and cronjob configurations listed under ConfigMap as well as the image tag.

{{<highlight yaml>}}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sakuli-dashboard
  labels:
    app: sakuli-dashboard
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sakuli-dashboard
  template:
    metadata:
      labels:
        app: sakuli-dashboard
    spec:
      containers:
        - name: sakuli-dashboard
          # Specify the Sakuli Dashboard version you want to use
          image: taconsol/sakuli-dashboard:${IMAGE_TAG}
          imagePullPolicy: Always
          env:
            - name: ACTION_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: sakuli-dashboard
                  key: ACTION_CONFIG
            - name: CLUSTER_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: sakuli-dashboard
                  key: CLUSTER_CONFIG
            - name: DASHBOARD_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: sakuli-dashboard
                  key: DASHBOARD_CONFIG
            - name: CRONJOB_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: sakuli-dashboard
                  key: CRONJOB_CONFIG
---
apiVersion: v1
kind: Service
metadata:
  name: sakuli-dashboard
spec:
  selector:
    app: sakuli-dashboard
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
  type: LoadBalancer
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: sakuli-dashboard
data:
  DASHBOARD_CONFIG: >-
    Dashboard configuration
  ACTION_CONFIG: >-
    Action configuration
  CLUSTER_CONFIG: >-
    Cluster configuration
  CRONJOB_CONFIG: >-
    Cronjob configuration
{{</highlight>}}  

To create the deployment in your Kubernetes cluster, just use the following command.

{{<highlight bash>}}
kubectl apply -f dashboard-template.yml
{{</highlight>}}  

### Sakuli Dashboard on Openshift {#deployment-with-openshift}

#### Prerequisites

A Sakuli Dashboard setup on your OpenShift cluster requires you to import the Sakuli Dashboard image from Docker Hub.

{{<highlight bash>}}
oc import-image sakuli-dashboard \
 --from=docker.io/taconsol/sakuli-dashboard \
 --confirm \
 --scheduled=true \
 --all=true
{{</highlight>}}

When using OpenShift v3.10 or v3.11, add the `--reference-policy=local` flag:

{{<highlight bash>}}
oc import-image sakuli-dashboard \
 --from=docker.io/taconsol/sakuli-dashboard \
 --confirm \
 --scheduled=true \
 --all \
 --reference-policy=local
{{</highlight>}}

*Note: The `oc import-image` statement is configured to not only import all available sakuli-dashboard images but also to
check for updates automatically.*

You can start your Sakuli Dashboard using two different approaches:
- Set up the Sakuli Dashboard manually via [CLI](#oc-cli).
- Set up the Sakuli Dashboard using a ready to use [template](#oc-dashboard-template).

#### Set up the Sakuli Dashboard manually via CLI {#oc-cli}

An easy way to set up a Sakuli Dashboard is by using `oc new-app`. Required environment variables can be passed along via the `-e` parameter.

{{<highlight bash>}}
oc new-app sakuli-dashboard \ 
 -e DASHBOARD_CONFIG="${DASHBOARD_CONFIG}"  \
 -e ACTION_CONFIG="${ACTION_CONFIG}"  \
 -e CLUSTER_CONFIG="${CLUSTER_CONFIG}"  \
 -e CRONJOB_CONFIG="${CRONJOB_CONFIG}"
{{</highlight>}}

Once completed, the service has to be exposed to make available from outside the cluster.

{{<highlight bash>}}
oc expose svc/sakuli-dashboard
{{</highlight>}}


#### Set up the Sakuli Dashboard using a ready to use template {#oc-dashboard-template}

The following ready-to-use template will configure a Sakuli Dashboard and deploy it to your cluster
Just copy and save the dashboard template below as `dashboard-template.yml`. Furthermore, add your dashboard, action, 
cluster and cronjob configurations listed under ConfigMap as well as the containers image configuration.

{{<highlight yml>}}
apiVersion: v1
kind: Template
metadata:
  name: ${SERVICE_NAME}
objects:
  - apiVersion: v1
    kind: DeploymentConfig
    metadata:
      name: ${SERVICE_NAME}
      labels:
        app: ${SERVICE_NAME}
    spec:
      replicas: 1
      selector:
        app: ${SERVICE_NAME}
      template:
        metadata:
          labels:
            app: ${SERVICE_NAME}
        spec:
          containers:
            - name: ${SERVICE_NAME}
              #change the address to your openshift registry, your namespace and the image tag you want to use
              image: ${OPENSHIFT_REGISTRY_ADDRESS}/${NAMESPACE}/sakuli-dashboard:${IMAGE_TAG}
              imagePullPolicy: Always
              ports:
                - containerPort: 8080
                  protocol: TCP
              env:
                - name: ACTION_CONFIG
                  valueFrom:
                    configMapKeyRef:
                      name: ${SERVICE_NAME}
                      key: ACTION_CONFIG
                - name: DASHBOARD_CONFIG
                  valueFrom:
                    configMapKeyRef:
                      name: ${SERVICE_NAME}
                      key: DASHBOARD_CONFIG
                - name: CLUSTER_CONFIG
                  valueFrom:
                    configMapKeyRef:
                      name: ${SERVICE_NAME}
                      key: CLUSTER_CONFIG
                - name: CRONJOB_CONFIG
                  valueFrom:
                    configMapKeyRef:
                      name: ${SERVICE_NAME}
                      key: CRONJOB_CONFIG
      triggers:
        - type: ConfigChange
  - apiVersion: v1
    kind: Service
    metadata:
      name: ${SERVICE_NAME}
      labels:
        app: ${SERVICE_NAME}
    spec:
      ports:
        - name: 8080-tcp
          port: 8080
          protocol: TCP
          targetPort: 8080
      selector:
        app: ${SERVICE_NAME}
  - apiVersion: v1
    kind: ConfigMap
    metadata:
      name: ${SERVICE_NAME}
    data:
      ACTION_CONFIG: >-
        <action configuration>
      CLUSTER_CONFIG: >-
        <cluster Configuration>
      DASHBOARD_CONFIG: >-
        <dashboard configuration>
      CRONJOB_CONFIG: >-
        <cronjob configuration>
parameters:
  - name: SERVICE_NAME
    description: Service name for dashboard
    value: sakuli-dashboard
{{</highlight>}}

After configuring the template, run:

{{<highlight bash>}}
oc process -f <filename> | oc create -f -
{{</highlight>}}
Whereas `<filename>` is the name of a file containing the template above.

Expose the service to make the dashboard available to clients outside of your cluster:

{{<highlight bash>}}
oc expose svc/sakuli-dashboard
{{</highlight>}}
