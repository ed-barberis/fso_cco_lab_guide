+++
title = "Lab Access"
chapter = false
weight = 1
+++

You access the lab environment through an AWS-hosted Cloud9 instance, which gives you a fully configured workspace right in your browser. Make sure to obtain your CNAO Lab User Credentials before the class starts. If you haven't acquired them yet, reach out to your instructor for guidance.

Here is an example of the essential URLs you need for smooth access to the lab, along with the credentials stored in 1Password. Make sure you have all variables that are listed below:

- URLs:
    - **Credentials URL**: https://share.1password.com/s#jCYAV5XdnwxsjKs3
    - **AWS Login URL**: https://39558032.signin.aws.amazon.com/console
    - **Cloud9 URL**: https://us-east-2.console.aws.amazon.com/cloud9/ide/abcde012345678e0123456789ab
    - **CNAO Tenant URL**: https://appd-se-channel.observe.appdynamics.com/ui/observe

- Credentials:
    - **Lab Participant**: John Doe
    - **AWS Region**: us-east-2 [Ohio]
    - **Lab User**: CNAO Lab 01
    - **AWS User**: cloud9-lab-user-01
    - **CNAO Tenant User**: lab-user-01

> **Note**: The examples provided above are solely for illustrative purposes; you should obtain your own unique set of lab access information.

The URL and Credentials variables will be cited in the relevant sections of the instructions as required.

### AWS Cloud9 Login Instructions
Here are the guidelines for accessing your AWS Cloud9 instance, which will serve as your lab environment for the duration of this course.

1. Open a new tab in your browser and paste the 1Password **Credentials URL**. This 1Password instance contains all the credentials you need.
![image](/images/21_access/pass_manager.png)

2. In a new tab, paste the **AWS Login URL**. Then, switch to the 1Password tab to copy both the *username* and *password* fields before clicking the *Sign in* button.
![image](/images/21_access/aws_login.png)

4. From the 1Password tab, copy the *one-time password* for the MFA Code and click the *Submit* button.
![image](/images/21_access/aws_mf.png)

5. Navigate to the upper-right corner of the AWS Console and ensure that the region selected from the drop-down menu is *us-east-2*.
![image](/images/21_access/aws_us_east.png)

6. Open a new tab in your browser and paste the **Cloud9 URL** to access your CNAO lab environment. Your lab Cloud9 instance will look similar to the figure below.
![image](/images/21_access/cloud9_initial.png)

> **Note:** You have the option to customize the look of your Cloud9 instance via the initial Welcome page.

## Validate CNAO Workshop environment variables

To open a new terminal window in your Cloud9 instance, click the plus sign (+) in the central pane and then select *New Terminal*.

![image](/images/21_access/c9_new_terminal.png)

Next, you will verify that all required environment variables are set in your instance. You will use some of the variables in the following exercises, so make sure all are set. Copy the following commands into the terminal window you created in the previous step.

    echo $cnao_lab_number
    echo $aws_region_name
    echo $aws_eks_cluster_name
    echo $cnao_k8s_apm_name
    echo $cnao_lab_id

Below is an example of the output. The output of the variables in your instance will be different.
![image](/images/21_access/c9_env_check.png)


You have successfully accessed and verified your lab environment!
<br>

## Next <span style="color: #143c76;"><i class='fas fa-cog fa-spin fa-sm'></i></span>&nbsp;

In the next section, you will learn how to generate and retrieve configuration files required to connect the system under observation with Cloud Native Observability (CNAO) platform.

<br>
