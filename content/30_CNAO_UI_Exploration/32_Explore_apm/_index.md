+++
title = "Explore Application Performance"
chapter = false
weight = 2
+++

## Introduction

After completing the [Kubernetes and App Service Monitoring Deployment](/20_hands_on_deployment.html) with OpenTelemetry for APM, you can begin tracking the performance of your application services and related entities. This is achieved through various views and selections, offering detailed insights into the specific entity being monitored.

## Navigate the Application Performance Monitoring Domain

You will acquire skills to pinpoint and interpret key application metrics, comprehend the interconnections among different entities, and leverage this understanding to resolve application-related problems.

There are three main entities in the APM Domain:

- **Services**
    - A Service is a software component designed for a specific task and may include numerous Service Instances.
- **Service Instances**
    - A specific realization of a Service, executing tasks and utilizing resources, with a Service potentially consisting of multiple such instances
- **Business Transactions**
    - Encompass essential services like login and checkout, reflecting user interactions across various applications and systems.

When in the APM domain, you can navigate and cross-corelate the performance with related entities from other domains, such as [Kubernetes Domain](/31_explore_infrastructure.html)

### Services
Cisco Cloud Observability offers detailed insights into Service entities. A Service, functioning as a specific software module, may encompass multiple Service Instances, each performing designated tasks

CCO enables you to view data detail in two ways:  

- Flow - provides a data-flow pictorial of Service interactions with other entities. Flow is the default view.
- List - data displays in a table where some columns are sortable.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Navigate to your CCO tenant via the **CCO Tenant URL**.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Go to the **Observe** page and adjust the view to specifically target your cluster. Appending `&& attributes(service.namespace) = 'cco_lab_id'` to the existing filter. Ensure you replace *cco_lab_id* with the actual environment variable from your Cloud9 environment.

![image](/images/32_explore_apm/initial.png)

> **Note:** You can adjust the time range in the upper-right corner, which will update the data displayed in the views to reflect the chosen time period.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click the **Services** entity within the Application Performance Monitoring domain.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; To filter only the services with errors, add `&& metrics('apm:errors_min') > 0` to the current filter.

![image](/images/32_explore_apm/filtered.png)

The widgets and the flow map view should adjust so you can focus only on services with errors.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Double-click the **cartservice** in the flow map.

You should see a single service entity along with a flow map specifically for the **cartservice**.

![image](/images/32_explore_apm/service_details.png)

On the service details page, you'll find the service's relationships with other entities, options to view related services in flow or list format, service metrics, and navigation options to delve into service traces and spans.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Scroll down to view the service endpoints and their associated metrics.

![image](/images/32_explore_apm/apm_metrics.png)

In the Service detail view, you can easily check if the specific service has a higher average response time or an unusual increase in requests.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select the **Related Traces** button in the right-hand Service panel.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Sort the traces by duration by clicking on the **Duration** column heading, then select the first trace by clicking its **Trace ID**.

![image](/images/32_explore_apm/traces.png)

Cisco Cloud Observability offers detailed insights into OpenTelemetry Traces, which are transactions through your system. Each Trace comprises multiple Spans, each representing a service or unit involved in the transaction. Traces are crucial for identifying the root causes of errors or performance issues in a distributed system.

Note the last column which indicates that most of the traces have errors.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; The Trace properties panel displays key details like Entry Service, Start Time, and Error. The **Trace Flowmap** visually represents the transaction flow through services, with each node labeled by service name. Clicking a node reveals related span details in the Properties panel and highlights the span in the request flow timeline.

![image](/images/32_explore_apm/trace_details.png)



<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Scroll down to view the **Request Flow** timeline.

![image](/images/32_explore_apm/request_flow.png)

The timeline illustrates the trace flow, showing span and service names within each hierarchy. Clicking a span reveals its Trace properties, OpenTelemetry attributes, and FSO Platform tags in the Properties panel. Purple highlights indicate the selected trace flow and span duration, while red highlights signal an error.

> This types of graphs are also referred to as flamegraphs.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Examine the request flow timeline to pinpoint the spans and services with the longest execution times. What insights did you gather?

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select the **Related Logs** option in the right Trace Overview panel.

> If you don't see the **Related Logs** option, because you were examining the request flow, navigate back to the traces view, and select the same trace again.

![image](/images/32_explore_apm/related_logs_1.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Select an error-indicating log entry from the list and review its details in the right-side panel. Are you able to pinpoint the root cause of the issue?

![image](/images/32_explore_apm/related_logs_2.png)


By skillfully navigating the APM domain, you've successfully uncovered the root cause of the application issue.

<br>

## Coming Up Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the upcoming section, you'll continue exploring the APM domain, focusing on observing and configuring Business Transactions (BTs) entity data, while also extracting key business metrics.
