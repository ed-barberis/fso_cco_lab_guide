+++
title = "Generate Configuration Files"
chapter = false
weight = 2
+++

The initial step in configuring Kubernetes and application monitoring involves creating credentials and generating a series of Helm charts. These charts are essential for deploying the components needed for cluster monitoring.
The Cloud Native Application Observability (CNAO) platform offers a guided, step-by-step methodology to walk you through the entire procedure.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Use a new browser tab to access the CNAO tenant using the **CNAO Tenant URL** and log in with the provided credentials.

After you log in, you'll arrive at the **Observe** page, which serves as the launching pad for the monitoring tasks you will perform later.

![image](/images/22_k8s_and_app/cnao_initial.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Use the left navigation pane and navigate to the **Kubernetes and APM** menu. </span>

![image](/images/22_k8s_and_app/k8s_app_initial.png)

Here, you'll generate your own set of credentials, which will be used for setting up Kubernetes and APM data collectors.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In your Cloud9 environment's terminal window, run the command `echo $cnao_k8s_apm_name` to retrieve the value of the **cnao_k8s_apm_name** environment variable. 

Use this value for both the credential set name in the first field and the unique name of the Kubernetes cluster in the second field. </span>

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the **ENABLE ADDITIONAL CONFIGURATIONS** section, ensure that **Cluster Collector**, **Infra Collector**, and **Log Collector Agent** are all selected, choosing *Linux* for the OS option.</span>

> TODO: In the configuration instructions the security monitoring is selected. We don't have access to that. Additionally, it looks like the UI changed, so you can also select Log configuration from the menu.

Below is an example showcasing both values configured. Make sure to use your own data in the input fields.

![image](/images/22_k8s_and_app/k8s_app_configure.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Click the **Generate configuration file** button. </span>

Two files will be produced based on the new credential set. The first, **operators-values.yaml**, is for the installation and configuration of CNAO operators on the Kubernetes cluster. The second, **collectors-values.yaml**, is for setting up the OpenTelemetry collectors that you'll deploy to the Kubernetes cluster in subsequent steps.

![image](/images/22_k8s_and_app/k8s_app_generate.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; Save the two files **operators-values.yaml** and **collector-values.yaml** to your local machine, by clicking on the corresponding download buttons. </span>

![image](/images/22_k8s_and_app/k8s_app_download.png)


## Upload the Configuration Files to your Cloud9 instance
Having successfully generated the essential configuration files for monitoring the application environment with CNAO, your next step is to upload these files to your Cloud9 lab environment for use.

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the Cloud9 left sidebar, select the top-level directory labeled "CNAO-Lab-*user-info*-Cloud9 - /home/ec2-user." Then, navigate to the **File** menu and choose the **Upload Local Files** option. </span>

![image](/images/22_k8s_and_app/c9_upload_local_files.png)

<span style="color: #143c76;"><i class='fas fa-circle fa-sm'></i></span>&nbsp; In the popup window that appears, upload the two files you previously downloaded: **operators-values.yaml** and **collectors-values.yaml**. </span>

![image](/images/22_k8s_and_app/c9_select_file.png)

You've successfully created the configuration files needed for setting up Kubernetes and APM service monitoring. 

<br>

## Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the following section, you'll utilize the generated files to implement the monitoring setup.