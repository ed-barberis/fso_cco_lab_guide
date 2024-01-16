+++
title = "Deploy and Instrument Application"
chapter = false
weight = 4
+++

In this segment, you'll roll out the OpenTelemetry demo application, which comes pre-instrumented with the OpenTelemetry SDK and agents. This eliminates the need for you to manually instrument the app or deploy auto-instrumentation agents. The only modification you'll need to make in the application's deployment code is to direct the OpenTelemetry data to the collectors that were set up in earlier sections.

OpenTelemetry Demo consists of microservices written in different programming languages that communicate with each other via gRPC and HTTP, and a load generator that uses Locust to fake user traffic. The figure below showcases the application service diagram.

![image](/images/24_app_deploy/app_architecture.png)


## Deploy and Verify Application
You will use a Helm Chart to deploy the demo application and override the default OpenTelemetry collector exporter settings to send the data to the AppDynamics Distribution of OpenTelemetry collectors that were previously set up.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Navigate to your Cloud9 environment and ensure that a terminal window is open.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Add the OpenTelemetry Helm Chart repository using the `helm repo add` command.

```bash
cco-lab-06-vm[ec2-user]$ helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts/
"open-telemetry" has been added to your repositories
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify the installed charts using the `helm repo list` command.

```bash
cco-lab-06-vm[ec2-user]$ helm repo list
NAME                            URL                                                                   
open-telemetry                  https://open-telemetry.github.io/opentelemetry-helm-charts            
appdynamics-cloud-helmcharts    https://appdynamics.jfrog.io/artifactory/appdynamics-cloud-helmcharts/
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Execute the command `kubectl create namespace $cco_lab_id` in the terminal window to create a new Kubernetes namespace.

```bash
cco-lab-06-vm[ec2-user]$ kubectl create namespace $cco_lab_id
namespace/cco-lab-06-i0xoc created
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Deploy OpenTelemetry Demo application by overriding default values for the OpenTelemetry Collector exporters. Use the following command:

```bash 
helm install \
    ${cco_lab_id}-otel-demo open-telemetry/opentelemetry-demo \
    --set components.frontendProxy.service.type=LoadBalancer \
    --values otel-demo-webstore-override.yaml \
    -n $cco_lab_id
```

The **otel-demo-webstore-override.yaml** file modifies the default OpenTelemetry settings to ensure telemetry data is directed to the AppDynamics version of the OpenTelemetry Collector, previously deployed on your Kubernetes cluster. View the file's contents using the command `cat otel-demo-webstore-override.yaml`.
```yaml

opentelemetry-collector:
  config:
    exporters:
      otlp/cnao:
        endpoint: "appdynamics-otel-collector-service.appdynamics.svc.cluster.local:4317"
        ...
<... output omitted ...>
```
<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify OpenTelemetry Demo application deployment using the command `kubectl get pods -n $cco_lab_id`.

```bash
cco-lab-06-vm[ec2-user]$ kubectl get pods -n $cco_lab_id
NAME                                                              READY   STATUS    RESTARTS   AGE
cco-lab-06-i0xoc-otel-demo-accountingservice-64c68558cd-wl765    1/1     Running   0          6m6s
cco-lab-06-i0xoc-otel-demo-adservice-76fc57b775-9c68b            1/1     Running   0          6m7s
cco-lab-06-i0xoc-otel-demo-cartservice-84cc94999b-glx67          1/1     Running   0          6m6s
cco-lab-06-i0xoc-otel-demo-checkoutservice-6448b5998f-rsthx      1/1     Running   0          6m7s

<... output omitted ...>
```

Make sure all pods are displayed in a 'Running' state.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Verify creation of the Load Balancer using the command `kubectl get services -n $cco_lab_id | grep 'otel-demo-frontendproxy'`.

```bash
cco-lab-06-vm[ec2-user]$ kubectl get services -n $cco_lab_id | grep 'otel-demo-frontendproxy'
cco-lab-06-i0xoc-otel-demo-frontendproxy    LoadBalancer    10.100.118.220    420102547.eu-central-1.elb.amazonaws.com ...
```

Make sure that an IP address and service URL are available.


<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Check the pod logs to make sure they show activity, indicating that tracing data is being transmitted to the collector. Use the command `kubectl logs $(kubectl get pods -n $cco_lab_id | awk '/adservice/ {print $1}') -n $cco_lab_id` with *adservice* as a test example.

```bash
cco-lab-06-vm[ec2-user]$ kubectl logs $(kubectl get pods -n $cco_lab_id | awk '/adservice/ {print $1}') -n $cco_lab_id

<... output omitted ...>

2023-10-31 19:58:45 - oteldemo.AdService - Non-targeted ad request received, preparing random response. trace_id=5f507c6f8a868a50266db7e758ac61e8 span_id=b77445ac3b7df0af trace_flags=01 
2023-10-31 19:58:47 - oteldemo.AdService - Targeted ad request received for [telescopes] trace_id=fdf22914b0d7ae98d8946d2b6adaa205 span_id=eeb9512efb3638bb trace_flags=01 
2023-10-31 19:58:48 - oteldemo.AdService - Targeted ad request received for [accessories] trace_id=3fe1a3e1adab4b9eb798aa4b7ece7ac9 span_id=9ff9350dc778559f trace_flags=01 
2023-10-31 19:58:53 - oteldemo.AdService - Non-targeted ad request received, preparing random response. trace_id=930df0529caabb1c87c3cf108fa33529 span_id=6fd39ae7e74ba6d7 trace_flags=01 
2023-10-31 19:59:02 - oteldemo.AdService - Targeted ad request received for [books] trace_id=509799fac3a9769c4f6819b85dc3aba2 span_id=9823d7f43641f858 trace_flags=01 
2023-10-31 19:59:10 - oteldemo.AdService - Targeted ad request received for [telescopes] trace_id=c6c087b6e5169df3bff0af38027422b1 span_id=bd7b36d7bf2e8ff5 trace_flags=01

<... output omitted ...>
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Run the command `otel_demo_urls` to obtain all URLs related to the otel demo services.

```bash
cco-lab-06-vm[ec2-user]$ otel_demo_urls

OpenTelemetry Demo Application URLs
  The following services will be available once the Load Balancer has completed its Health checks:
  Webstore:          http://a9c277705358e45ac9d0c70170d31082-420102547.eu-central-1.elb.amazonaws.com:8080/
  Grafana:           http://a9c277705358e45ac9d0c70170d31082-420102547.eu-central-1.elb.amazonaws.com:8080/grafana/
  Feature Flags UI:  http://a9c277705358e45ac9d0c70170d31082-420102547.eu-central-1.elb.amazonaws.com:8080/feature/
  Load Generator UI: http://a9c277705358e45ac9d0c70170d31082-420102547.eu-central-1.elb.amazonaws.com:8080/loadgen/
  Jaeger UI:         http://a9c277705358e45ac9d0c70170d31082-420102547.eu-central-1.elb.amazonaws.com:8080/jaeger/ui/
```

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select the *Webstore* link and click on **Open** to confirm that the application has been deployed successfully.

![image](/images/24_app_deploy/demo_home_page.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Use the OpenTelemetry Demo Application URLs to access the *Feature Flags UI* in your browser and modify the **CartServiceFailure** feature.

![image](/images/24_app_deploy/features_1.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Activate the feature by checking the **Enabled** box and save your changes.

![image](/images/24_app_deploy/features_2.png)

This ensures that the application produces errors for later observation in CCO.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Navigate to your CCO tenant via the **CCO Tenant URL** and check to see if the OpenTelemetry collector is sending any data to the platform.

Go to the **Observe** page and apply a filter `EntityStatus = 'active'  && attributes(service.namespace) = 'cco-lab-06-i0xoc'` to narrow down the display to services specific to your lab environment. Be sure to use your own namespace name, which is stored in the **cco_lab_id** environment variable.

> **Note:** Retrieve your own service namespace by using the command `echo $cco_lab_id` in your Cloud9 instance terminal.

![image](/images/24_app_deploy/CNAO_deployed.png)

You've successfully configured OpenTelemetry collectors to transmit data to the CCO platform!

> **Note:** It may take a while for CCO to display significant data, so now is a great opportunity to take a break!

<br>

## Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the next section, you'll utilize the Cisco Cloud Observability platform to examine the application's performance.
