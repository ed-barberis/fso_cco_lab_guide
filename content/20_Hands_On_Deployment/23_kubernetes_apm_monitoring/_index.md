+++
title = "Install K8s and APM Monitoring"
chapter = false
weight = 3
+++

Before proceeding with the installation of the Kubernetes operator and OpenTelemetry collector, you'll first verify access to the appropriate Kubernetes cluster API using local CLI tool *kubectl*.
 
<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In your Cloud9 environment's terminal window, run the command `aws eks --region $aws_region_name update-kubeconfig --name $aws_eks_cluster_name` to retrieve AWS EKS *kubeconfig*.

```bash
cco-lab-06-vm[ec2-user]$ aws eks --region $aws_region_name update-kubeconfig --name $aws_eks_cluster_name
Updated context arn:aws:eks:eu-central-1:395719258032:cluster/CCO-Lab-06-i0xoc-EKS in /home/ec2-user/.kube/config
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Confirm your access to the Kubernetes cluster by executing any `kubectl` commands you're comfortable with.

```bash
cco-lab-06-vm[ec2-user]$ kubectl get nodes -o wide
NAME                                            STATUS   ROLES    AGE   VERSION               INTERNAL-IP    EXTERNAL-IP     OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-172-16-1-212.eu-central-1.compute.internal   Ready    <none>   27d   v1.27.5-eks-43840fb   172.16.1.212   35.157.74.151   Amazon Linux 2   5.10.192-183.736.amzn2.x86_64   containerd://1.6.19
ip-172-16-2-17.eu-central-1.compute.internal    Ready    <none>   27d   v1.27.5-eks-43840fb   172.16.2.17    3.70.180.185    Amazon Linux 2   5.10.192-183.736.amzn2.x86_64   containerd://1.6.19
```

## Deploying Components to the Kubernetes Cluster
In the next section, you'll begin deploying essential components needed for successful Kubernetes and application monitoring using CCO.

### Deploy Certification Manager
In Kubernetes, the API server needs to securely communicate with the OpenTelemetry™ Operator's webhook, as part of the Cisco Cloud Observability Helm charts. This requires a trusted TLS certificate. Cert-manager automates the generation of self-signed certificates for this purpose.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the most recent version of the certificate manager (cert-manager) onto the cluster.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.1/cert-manager.yaml
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify *cert-manager* deployment. Make sure the status of all pods is **Running**

```bash
cco-lab-06-vm[ec2-user]$ kubectl get pods -n cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-75d57c8d4b-58w5w              1/1     Running   0          4m36s
cert-manager-cainjector-69d6f4d488-mh26g   1/1     Running   0          4m36s
cert-manager-webhook-869b6c65c4-hf2wq      1/1     Running   0          4m36s
```

### Install AppDynamics Operators and Collectors
You will use a set of Helm charts and custom configuration files that you previously generated, to deploy the [AppDynamics Operator](https://docs.appdynamics.com/fso/cloud-native-app-obs/en/kubernetes-and-app-service-monitoring/install-kubernetes-and-app-service-monitoring/configure-cisco-appdynamics-operators) and [AppDynamics Kubernetes Collectors](https://docs.appdynamics.com/fso/cloud-native-app-obs/en/kubernetes-and-app-service-monitoring/install-kubernetes-and-app-service-monitoring/configure-cisco-appdynamics-kubernetes-collectors).

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Add the AppDynamics Helm Chart repository using the `helm repo add` command.

```bash
cco-lab-06-vm[ec2-user]$ helm repo add appdynamics-cloud-helmcharts https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
"appdynamics-cloud-helmcharts" has been added to your repositories
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the installed charts using the `helm repo list` command.

```bash
cco-lab-06-vm[ec2-user]$ helm repo list
NAME                            URL                                                                   
open-telemetry                  https://open-telemetry.github.io/opentelemetry-helm-charts            
appdynamics-cloud-helmcharts    https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Create *appdynamics* Kubernetes namespace using the command `kubectl create namespace appdynamics`.

```bash
cco-lab-06-vm[ec2-user]$ kubectl create namespace appdynamics
namespace/appdynamics created
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the Appdynamics Operators using the uploaded **operators-values.yaml** file. Use the command `helm install appdynamics-operators appdynamics-cloud-helmcharts/appdynamics-operators -n appdynamics -f operators-values.yaml --wait`.


```bash
cco-lab-06-vm[ec2-user]$ helm install appdynamics-operators appdynamics-cloud-helmcharts/appdynamics-operators -n appdynamics -f operators-values.yaml --wait

<... output omitted ...>

The chart installs the following components

1) OpenTelemetry Operator

2) AppDynamics Cloud Operator
        Description: Uses custom resources to manage the life cycle of Cluster Collector, Infrastructure Collector and Log Collector.

3) FSO Agent Management Orchestration Client
    Description: Performs and manages operations of collector agents in couple of ways
                    1. create, update and delete agents provided in orchestration client config, it can work with multiple agents
                    2. manages configuration by fetching it from the fso management solution and passing down to collector agents

<... output omitted ...>
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Modify the existing **collectors-values.yaml** to configure logging. Use the command `./enable_cco_collectors_logging_config.sh`.

> **Note:** This script will integrate the appropriate configuration into the initial collector values that you've downloaded from the CCO platform.

```bash
cco-lab-06-vm[ec2-user]$ ./enable_cco_collectors_logging_config.sh
Begin processing Helm Chart files...
Extracting CCO configuration values...

client_id: agt_36gQInfueAOlao7BtQOjf
client_secret: E_kWi1ToU2A3NYb6tglypBRiJZ2vp7I_w
cluster_name: cco-lab-06-i0xoc-eks
collector_endpoint: https://flintconsultingltd-nfr.observe.appdynamics.com/data
token_url: https://flintconsultingltd-nfr.observe.appdynamics.com/auth/8312fd12-51b3-48cf-9ae1-a7ec72f5f0/default/oauth2/token

Creating new 'collectors-values-with-logging.yaml' file with logging configuration...

CCO Collectors file: 'collectors-values-with-logging.yaml' created.
Enable CCO Collectors Logging configuration operation complete.
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the new collectors configuration in *collectors-values-with-logging.yaml* file. Use the command `cat collectors-values-with-logging.yaml`.

```yaml
global:
  clusterName: cco-lab-06-i0xoc-eks
appdynamics-cloud-k8s-monitoring:
  install:
    logCollector: true
  clustermonConfig:
    events:
      enabled: true
      severityToExclude: []
      reasonToExclude: []
      severeGroupByReason: []
  logCollectorConfig:
    container:
      logging:
        level: debug
      conditionalConfigs: # renamed from conditions -> conditionalConfigs
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
            multiLineNegate: true # default is false
      dropFields: ["agent", "stream", "ecs", "input", "orchestrator", "k8s.annotations.appdynamics", "k8s.labels", "k8s.node.labels", "cloud"]
      batchSize: 1000
      maxBytes: 1000000
appdynamics-otel-collector:
  clientId: agt_36gQInfA1Olao7BtQOjf
  clientSecret: E_kWi1ToU2A3NYb6tBwii1TRypBRiJZ2vp7I_w
  endpoint: https://appd-se-channel.observe.appdynamics.com/data
  tokenUrl: https://appd-se-channel.observe.appdynamics.com/auth/ec5424c4-19fe-4c29-nono-b585efa910bd/default/oauth2/token
appdynamics-security-collector:
  enabled: true
  panoptica:
    controller:
      agentID: fd6207f2-1d84-4db0-b582-d1b91466b2b5
      secret:
        sharedSecret: 2aOB78tYgyb0fGEWG34svL4T65lHdIMnonoLlSly9yjQ=
```

Observe that **logCollector** is enabled for installation, and it comes with specific conditional configuration settings designed to process the logs prior to forwarding them to CCO. Additionally, the **appdynamics-security-collector** is activated, enabling Cisco Secure Application within the CCO tenant, providing insights into the security vulnerabilities of your deployments.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Install the OpenTelemetry collectors using the newly created 'collectors-values-with-logging.yaml' file. Use the command `helm install appdynamics-collectors appdynamics-cloud-helmcharts/appdynamics-collectors -n appdynamics -f collectors-values-with-logging.yaml`.

```bash
cnao-lab-06-vm[ec2-user]$ helm install appdynamics-collectors appdynamics-cloud-helmcharts/appdynamics-collectors -n appdynamics -f collectors-values-with-logging.yaml

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


<... output omitted ...>
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify AppDynamics Operators and Collectors deployment using the command `kubectl get pods -n appdynamics`.

```bash
cnao-lab-06-vm[ec2-user]$ kubectl get pods -n appdynamics
NAME                                                              READY   STATUS    RESTARTS   AGE
appdynamics-collectors-appdynamics-clustermon-7fc6c6bb7b-msbfm    1/1     Running   0          96s
appdynamics-collectors-appdynamics-inframon-sznwl                 1/1     Running   0          96s
appdynamics-collectors-appdynamics-inframon-wdl4t                 1/1     Running   0          96s
appdynamics-collectors-appdynamics-log-collector-tslwk            1/1     Running   0          96s
appdynamics-collectors-appdynamics-log-collector-wwxfb            1/1     Running   0          96s
appdynamics-collectors-ds-appdynamics-otel-collector-cxbfj        1/1     Running   0          96s
appdynamics-collectors-ds-appdynamics-otel-collector-qm6rn        1/1     Running   0          96s
appdynamics-operators-appdynamics-cloud-operator-668ddc76dxrf47   2/2     Running   0          22h
appdynamics-operators-appdynamics-smartagent-7bb787547f-49q6z     1/1     Running   0          22h
appdynamics-operators-opentelemetry-operator-6969d869c8-vfh59     2/2     Running   0          22h
```

Make sure that the status of all is set to **Running**.

<br>

The Kubernetes Operator and AppDynamics Distribution of OpenTelemetry collectors are now set up and configured to transmit data to the Cisco Cloud Observability backend.


## Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the following section, you'll launch and set up an application to send its telemetry data to the OpenTelemetry collectors.