+++
title = "Explore Kubernetes Infrastructure"
chapter = false
weight = 1
+++

## Introduction

In the area of cloud-native applications, the Kubernetes cluster serves as a crucial foundation for your deployed services. Familiarizing yourself with CCO's monitoring capabilities will give you a comprehensive overview of the [Kubernetes domain and its associated entities](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/kubernetes-and-app-service-monitoring/kubernetes-entities) such as namespaces, workloads, pods, and containers. This introduction to monitoring with CCO will guide you through the process of observing the health, performance, and intricacies of your Kubernetes cluster. 

You will learn about various entities and their relationships within the Kubernetes domain of the CCO platform.

## Navigate the Kubernetes Domain
You will learn to identify and analyze the important metrics, understand the relationships between the various entities, and use this knowledge to maintain a robust and efficient cloud infrastructure. Whether you want to troubleshoot potential issues or optimize resource allocation, CCO will provide you with the insights you need to keep your Kubernetes environment running smoothly.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Navigate to your CCO tenant via the **CCO Tenant URL**.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Go to the **Observe** page and click the **Clusters** entity within the Kubernetes domain.

![image](/images/31_explore_ui/ui_cluster_initial.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Adjust the view to specifically target your cluster by appending `&& attributes(k8s.cluster.name) = 'cco_k8s_apm_name'` to the existing filter. Ensure you replace *cco_k8s_apm_name* with the actual environment variable from your Cloud9 environment, which is the name of your EKS Cluster converted to lowercase.

![image](/images/31_explore_ui/ui_cluster_filtered.png)

In the Observe and list views, filters allow you to tailor your display to show only the entities and data of interest. Cisco Cloud Observability enables filtering based on:

- Attributes
- EntityStatus
- EntityType
- Id
- Tags
- Metrics

Learn more about filters by navigating [here](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/navigate-cisco-cloud-observability/observe-ui-overview/filters).

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click on your cluster's name to view a detailed overview of its performance.

![image](/images/31_explore_ui/ui_cluster_full.png)

The **RELATIONSHIPS** panel on the left provides a gateway to all entities associated with your Kubernetes domain.

The central panel presents a detailed snapshot of the cluster's current performance.

The right-hand menu consolidates data from various views, offering a quick summary of cluster health and essential properties like tags, names, and versions.


<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; You can adjust the time range in the upper-right corner, which will update the data displayed in the views to reflect the chosen time period.

![image](/images/31_explore_ui/ui_cluster_time_select.png)


### Health Violation Timeline
Health rules enable you to define parameters that represent normal or expected operational conditions in your environment, such as CPU Utilization for a host. By default, certain health rules are pre-configured for monitored domains and entities.

A health rule violation happens when the set critical or warning conditions of that rule are met. You can see the details of these violations for a specific entity or any health rule in the Health Violation Timeline.

![image](/images/31_explore_ui/4_expand_health_violation.png)

> **Note:** Your screen might look a bit different from the image shown above.

Learn more about Health Rules by navigating [here](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/entity-health-monitoring/health-rules).

### Namespaces Entity
The Namespaces page serves as a hub for both real-time and historical performance data of Kubernetes namespaces, offering:

- Namespace-level metrics.
- Aggregated metrics summaries from workloads, PVCs, pods, and containers within each namespace.
- Summaries of changes in Kubernetes Configurations for each namespace.
- Kubernetes Events linked to each namespace.


<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the RELATIONSHIP panel select the **Namespace** entity.

Only the namespaces deployed to your Kubernetes cluster are shown here, because the relationship between the entities is pertained.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click the namespace to which your app was deployed.

![image](/images/31_explore_ui/5_select_namespace.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; The namespace view expands to show the overall performance of the components and entities associated with the Kubernetes namespace.

![image](/images/31_explore_ui/6_observe_namespace.png)

Learn more about Namespaces entity by navigating [here](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/kubernetes-and-app-service-monitoring/kubernetes-entities/namespaces).

### Logs
Logs are crucial for troubleshooting as they provide detailed insights into actions, errors, warnings, unexpected conditions, and various other events.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the namespace entity center pane, scroll to the **Logs** window and click **View All Logs** to view the logs associated with the current Kubernetes namespace.

![image](/images/31_explore_ui/7_observe_logs.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Change to **Natural Language** search mode, and in the search bar, enter a query like `of which messages including "recommendation_server.py"`.

> You can play around with different natural language search prompts.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select one of the log messages from the list, and observe the log details in the panel on the right.
![image](/images/31_explore_ui/8_observe_logs_detail.png)

Learn more about troubleshooting with Logs by navigating [here](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/navigate-cisco-cloud-observability/explore-melt-data/explore-logs).

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click on the namespace name displayed in the breadcrumbs at the top of the interface, and return back to the *Namespace* entity.

![image](/images/31_explore_ui/9_go_back.png)


### Workloads Entity
The Workloads page serves as a hub for both real-time and historical performance data of Kubernetes workloads. In Kubernetes, a workload represents an application in operation, with examples including Deployments and ReplicaSets.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select the *Workloads* entity from the relationships panel to display all workloads. Observe that these workloads are linked to a specific namespace, indicated at the top of the screen, due to your access to the Workload entity via the Namespace entity.

![image](/images/31_explore_ui/workloads_01.png)

<!---
<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click on **View Unhealthy Workloads** to apply a filter that displays only those Workloads in a *Warning* or *Critical* state.

![image](/images/31_explore_ui/11_workloads_filter.png)

Alternatively, you can input the filter manually, though some views offer convenient filter shortcuts like this one.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select one of the *Critical* workloads from the list by clicking its name to delve into its details.

### Kubernetes Cost and Workload Profiler
The Efficiency and Risk Profiler evaluates Kubernetes workloads for performance and reliability risks, as well as inefficiencies. It examines settings and metrics such as pod quality of service (QoS), replica count, resource allocation, limits, and actual usage.


<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Observe the *Efficiency Profile* pane, which gives you some reliability risk analysis and suggestions to improve the workload performance

![image](/images/31_explore_ui/ui_cluster_workload_aro.png)

Analysis results for deployments consistently feature the Analysis Summary, Reliability Risk, and Efficiency Rate. However, specific Opportunities and Cautions are provided only when sufficient data is available for an accurate assessment.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the **Health and Alerting** panel on the right, the **Security** section displays vulnerabilities in your workloads and services. The Cisco Secure Application uses **Kenna** and **CVSS** scores to indicate these vulnerabilities.

The panel displays an entity overall risk level, considering factors like vulnerabilities, Kubernetes misconfigurations, and its impact on the business transaction's risk score, which also factors in business impact and data leakage risks. Learn more about Kenna vulnerability scoring [here](https://help.kennasecurity.com/hc/en-us/articles/360026160592-Vulnerability-Scoring-in-Cisco-Vulnerability-Management).

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; As you scroll down, you'll see various metrics and statuses providing an overview of the workload's performance.

![image](/images/31_explore_ui/12_workloads_details_2.png)
--->

### Pods Entity
You can use Pods entity to:

- Identify pods that are experiencing issues related to resource usage.
- Determine if pods are experiencing restarts or crashing issues that impact the availability.
- Determine if there are any changes in Kubernetes Configurations (ConfigMaps and Secrets) at pods level.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Choose the related **Pods** entity from the **RELATIONSHIPS** panel.

![image](/images/31_explore_ui/13_pods.png)


### Containers Entity

The Containers page displays real-time and historical performance data for Kubernetes containers, with metrics sourced from cAdvisor via the Kubelet API. This page highlights resource utilization metrics for your containerized applications.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click the **Container** entity. Using this entity menu, you can concentrate on the performance metrics specific to the related container.

![image](/images/31_explore_ui/14_containers.png)

<!---
### Hosts Entity

The Containers page displays real-time and historical performance data for Kubernetes containers, with metrics sourced from cAdvisor via the Kubelet API. This page highlights resource utilization metrics for your containerized applications.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click the **Container** entity. Using this entity menu, you can concentrate on the performance metrics specific to the related container.

![image](/images/31_explore_ui/14_containers.png)


### Cloud Entities

After you [Configure Cloud Connections](https://docs.appdynamics.com/observability/cisco-cloud-observability/en/cloud-and-infrastructure-monitoring/aws-cloud-observability/configure-aws-cloud-connections), you can also monitor cloud specific services such as API Gateways, Load Balabncers, Elastic Container Services, and hosts in AWS.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Choose the **Host** entity from the container relationship panel to inspect the single host where the container is deployed.

![image](/images/31_explore_ui/cloud_1.png)

In the relationships panel, observe that the host is linked to other cloud entities like Load Balancers and Storage. 

On the right panel, host properties are displayed, and in the center, graphs provide a visual representation of the entity's performance.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select the **Load Balancer** Entity from the relationship panel. 

![image](/images/31_explore_ui/cloud_2.png)

In Cisco Cloud Observability (CCO), you can observe how Amazon Application, Classic, and Network load balancers manage incoming HTTP and HTTPS traffic across multiple Availability Zones. These load balancers assess the health status of backend targets, directing traffic only to those that are healthy and dynamically adjusting to shifting traffic patterns. Additionally, you can see the Load Balancer entity within CCO, providing insights into its operation and integration with your infrastructure.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Choose the **Storage** Entity from the relationship panel. To view the Storage entity, you may need to navigate back in your browser, as there is no direct relationship defined between the current load balancer and storage in the interface.

![image](/images/31_explore_ui/cloud_3.png)

You've explored Kubernetes domain entities in the CCO interface, learning to identify issues related to Kubernetes infrastructure, observing vulnerability detection for workloads, and understanding the relationship between Kubernetes entities and those managed by Cloud Connections like AWS.
--->

<br>

## Coming Up Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

Although you may have noticed the Service entities in the UI, you haven't explored them yet. This exploration, focusing on application performance, is what you'll tackle next.
