+++
title = "Install K8s and APM Monitoring"
chapter = false
weight = 3
+++

Before proceeding with the installation of the Kubernetes operator and AppDynamics OpenTelemetry collector, you'll first verify 
access to the appropriate Kubernetes cluster API using local CLI tool *kubectl*.
 
<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In your Cloud9 environment's terminal window, run the command *aws eks update-kubeconfig* to retrieve AWS EKS *kubeconfig*.

```bash
aws eks --region $aws_region_name update-kubeconfig --name $aws_eks_cluster_name
```

```bash
# example
$ aws eks --region $aws_region_name update-kubeconfig --name $aws_eks_cluster_name
Added new context arn:aws:eks:us-east-2:395719258032:cluster/CCO-Lab-01-nrmf4-EKS to /home/ec2-user/.kube/config
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Confirm your access to the Kubernetes cluster by executing any *kubectl* commands you're comfortable with, such as in this example below.

```bash
kubectl get nodes -o wide
```

```bash
# example
$ kubectl get nodes -o wide
NAME                                         STATUS   ROLES    AGE   VERSION               INTERNAL-IP    EXTERNAL-IP      OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-172-16-1-105.us-east-2.compute.internal   Ready    <none>   17h   v1.29.0-eks-5e0fdde   172.16.1.105   18.222.132.193   Amazon Linux 2   5.10.205-195.807.amzn2.x86_64   containerd://1.7.11
ip-172-16-2-69.us-east-2.compute.internal    Ready    <none>   17h   v1.29.0-eks-5e0fdde   172.16.2.69    3.22.63.34       Amazon Linux 2   5.10.205-195.807.amzn2.x86_64   containerd://1.7.11
```

## Deploying Components to the Kubernetes Cluster
In the next section, you'll begin deploying essential components needed for successful Kubernetes and application monitoring using CCO.

### Deploy Certificate Manager
In Kubernetes, the API server needs to securely communicate with the OpenTelemetry™ Operator's webhook, as part of the Cisco Cloud Observability 
Helm charts. This requires a trusted TLS certificate. The certificate manager automates the generation of self-signed certificates for this purpose.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the most recent version of the certificate manager 
(cert-manager) onto the cluster.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
```

```bash
# example
$ kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
namespace/cert-manager created
customresourcedefinition.apiextensions.k8s.io/certificaterequests.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificates.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/challenges.acme.cert-manager.io created

<... output omitted ...>

deployment.apps/cert-manager-webhook created
mutatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook created
validatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook created
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the *cert-manager* deployment. Make sure the status of all pods is **Running**.

```bash
kubectl get pods -n cert-manager
```

```bash
# example
$ kubectl get pods -n cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-9f74c854d-z6jgv               1/1     Running   0          4m54s
cert-manager-cainjector-665cd78979-cm86x   1/1     Running   0          4m54s
cert-manager-webhook-65767c6f65-nxntg      1/1     Running   0          4m54s
```

### Install AppDynamics Operators and Collectors
You will use a set of Helm charts and custom configuration files that you previously generated, to deploy the [AppDynamics Operator](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/kubernetes-and-app-service-monitoring/install-kubernetes-and-app-service-monitoring/configure-cisco-appdynamics-operators).

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Add the AppDynamics Helm Chart repository using the *helm repo add* command.

```bash
helm repo add appdynamics-cloud-helmcharts https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
```

```bash
# example
$ helm repo add appdynamics-cloud-helmcharts https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
"appdynamics-cloud-helmcharts" has been added to your repositories
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the installed charts using the *helm repo list* command.

```bash
helm repo list
```

```bash
# example
$ helm repo list
NAME                            URL
appdynamics-cloud-helmcharts    https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Create *appdynamics* Kubernetes namespace using the *kubectl create namespace* command.

```bash
kubectl create namespace appdynamics
```

```bash
# example
$ kubectl create namespace appdynamics
namespace/appdynamics created
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the Appdynamics Operators using the uploaded **operators-values.yaml** file with the *helm install* command.

```bash
helm install appdynamics-operators appdynamics-cloud-helmcharts/appdynamics-operators -n appdynamics -f operators-values.yaml --wait
```

```bash
# example
$ helm install appdynamics-operators appdynamics-cloud-helmcharts/appdynamics-operators -n appdynamics -f operators-values.yaml --wait
NAME: appdynamics-operators

<... output omitted ...>

The chart installs the following components

1) OpenTelemetry Operator
  Enabled: true

2) AppDynamics Cloud Operator
  Enabled: true
  Description: Uses custom resources to manage the life cycle of Cluster Collector, Infrastructure Collector and Log Collector.

3) FSO Agent Management Orchestration Client
  Enabled: true
  Description: Performs and manages operations of collector agents in a couple of ways
    1. create, update and delete agents provided in orchestration client config, it can work with multiple agents
    2. manages configuration by fetching it from the fso management solution and passing down to collector agents

<... output omitted ...>
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Next, you're going to create a new **collectors-values-with-logging.yaml** 
file based on the downloaded **collectors-values.yaml** file which will configure the logging message parser with log4j and a format specific to the 
OTel Demo application. For convenience, you will use the **enable_cco_collectors_logging_config.sh** script to create the new file.

> **Note:** This script will integrate the appropriate configuration into the initial collector values that you've downloaded from the CCO platform.

```bash
./enable_cco_collectors_logging_config.sh
```

```bash
# example
$ ./enable_cco_collectors_logging_config.sh
Begin processing Helm Chart files...
Extracting CCO configuration values...

client_id: agt_3W4jsQQoFw4TGErvfKXC35
client_secret: dtsUfjdNOS7jpiAJ8aCBFL1mvCRgpamjhxqho973Uvo
cluster_name: cco-lab-01-nrmf4-eks
collector_endpoint: https://appd-channel.observe.appdynamics.com/data
token_url: https://appd-channel.observe.appdynamics.com/auth/5bcb9501-4f1f-4f2b-8366-52ace9aa7bb4/default/oauth2/token

Creating new 'collectors-values-with-logging.yaml' file with logging configuration...

CCO Collectors file: 'collectors-values-with-logging.yaml' created.
Enable CCO Collectors Logging configuration operation complete.
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the new collectors configuration in **collectors-values-with-logging.yaml** 
file. Use the linux **cat** command to view the file.

```bash
cat collectors-values-with-logging.yaml
```

```yaml
global:
  clusterName: cco-lab-01-nrmf4-eks
  oauth:
    clientId: agt_3W4jsQQoFw4TGErvfKXC35
    clientSecret: dtsUfjdNOS7jpiAJ8aCBFL1mvCRgpamjhxqho973Uvo
    endpoint: https://appd-channel.observe.appdynamics.com/data
    tokenUrl: https://appd-channel.observe.appdynamics.com/auth/5bcb9501-4f1f-4f2b-8366-52ace9aa7bb4/default/oauth2/token
appdynamics-cloud-k8s-monitoring:
  install:
    clustermon: true
    defaultInfraCollectors: true
    logCollector: true
  clustermonConfig:
    os: linux
    events:
      enabled: true
      severityToExclude: []
      reasonToExclude: []
      severeGroupByReason: []
  containermonConfig:
    os:
      - linux
  servermonConfig:
    os:
      - linux
  logCollectorConfig:
    os:
      - linux
    container:
      logging:
        level: debug
      conditionalConfigs:
        - condition:
            operator: contains
            key: kubernetes.pod.name
            value: ad-service
          config: # new
            messageParser:
              log4J:
                enabled: true
                pattern: "%d{yyyy-MM-dd HH:mm:ss} - %logger{36} - %msg trace_id=%X{trace_id} span_id=%X{span_id} trace_flags=%X{trace_flags} %n"
            multiLineMatch: after
            multiLinePattern: '^\d{4}-\d{2}-\d{2}'
            multiLineNegate: true
      dropFields: ["agent", "stream", "ecs", "input", "orchestrator", "k8s.annotations.appdynamics", "k8s.labels", "k8s.node.labels", "cloud"]
      batchSize: 1000
      maxBytes: 1000000
appdynamics-otel-collector:
  clientId: agt_3W4jsQQoFw4TGErvfKXC35
  clientSecret: dtsUfjdNOS7jpiAJ8aCBFL1mvCRgpamjhxqho973Uvo
  endpoint: https://appd-channel.observe.appdynamics.com/data
  tokenUrl: https://appd-channel.observe.appdynamics.com/auth/5bcb9501-4f1f-4f2b-8366-52ace9aa7bb4/default/oauth2/token
  enableNetworkMonitoring: true
appdynamics-network-monitoring:
  enabled: true
```

Observe that **logCollector** is enabled for installation; which comes with specific conditional configuration settings 
designed to process the logs prior to forwarding them to CCO.  

Additionally, if **Secure Application** is enabled for your CCO tenant, you will see that the **appdynamics-security-collector** is activated. 
This will enable Cisco Secure Application within the CCO tenant, providing insights into the security vulnerabilities of your deployments.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the AppDynamics OpenTelemetry collectors using the newly created 
**collectors-values-with-logging.yaml** file. Use the *helm install appdynamics-collectors* command.

```bash
helm install appdynamics-collectors appdynamics-cloud-helmcharts/appdynamics-collectors -n appdynamics -f collectors-values-with-logging.yaml
```

```bash
# example
$ helm install appdynamics-collectors appdynamics-cloud-helmcharts/appdynamics-collectors -n appdynamics -f collectors-values-with-logging.yaml
NAME: appdynamics-collectors

<... output omitted ...>

** Please be patient while the chart is being deployed **

The chart installs the following components

1) AppDynamics OpenTelemetry Collector

2) AppDynamics Cloud Infrastructure Collector
        Enabled: true
        Description: Installs the Server Collector and Container Collector to monitor the host and container metrics

3) AppDynamics Cloud Cluster Collector
        Enabled: true
        Description: Installs the Cluster Collector to monitor the kubernetes metrics and events

4) AppDynamics Cloud Log Collector
        Enabled: true
        Description: Installs the Log Collector to collect the logs from applications running in kubernetes cluster

5) AppDynamics Cloud Database Collector
        Enabled: false
        Description: Installs the DB Collector to collect metrics and monitors the Databases specified in DbConfigs

6) Cisco Cloud Observability Network Collector
        Enabled: true
        Description: Installs the Network Collector, to monitor network performance for applications and infrastructure

<... output omitted ...>
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify AppDynamics Operators and Collectors deployment using 
the *kubectl get pods* command.

```bash
kubectl get pods -n appdynamics
```

```bash
# example
$ kubectl get pods -n appdynamics
NAME                                                              READY   STATUS    RESTARTS   AGE
appdynamics-collectors-appdynamics-clustermon-b44468ddd-fl5tt     1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-inframon-5cxhw                 1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-inframon-h4vcx                 1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-log-collector-6vjdh            1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-log-collector-t8g5p            1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-network-monitoring-k8s-6dkhl   2/2     Running   0          5m26s
appdynamics-collectors-appdynamics-network-monitoring-kerndh7ds   1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-network-monitoring-kernzw6sb   1/1     Running   0          5m26s
appdynamics-collectors-appdynamics-network-monitoring-reduqzp6h   1/1     Running   0          5m26s
appdynamics-collectors-ds-appdynamics-otel-collector-c9wgs        1/1     Running   0          5m26s
appdynamics-collectors-ds-appdynamics-otel-collector-nhmxl        1/1     Running   0          5m26s
appdynamics-operators-appdynamics-cloud-operator-66f7cf766bg79s   2/2     Running   0          150m
appdynamics-operators-appdynamics-smartagent-55b85f94f7-schgl     1/1     Running   0          150m
appdynamics-operators-opentelemetry-operator-6b4f58b4cb-w9jgv     2/2     Running   0          150m
```

Make sure the status of all pods is **Running**.

<br>

The Kubernetes Operator and AppDynamics Distribution of OpenTelemetry collectors are now set up and configured to transmit data to the 
Cisco Cloud Observability backend.


## Coming Up Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the following section, you'll launch and set up an application to send its telemetry data to the OpenTelemetry collectors.
