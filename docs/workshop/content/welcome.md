## Welcome!

Welcome to the OpenShift Virtualization self-paced lab on Red Hat Product Demo System (RHPDS). We've put this together to give you an overview and technical deep dive into how OpenShift Virtualization works, and how it will bring virtualization capabilities to OpenShift over the next few months.

**OpenShift Virtualization** is now the official *product name* for the Container-native Virtualization operator for OpenShift. This has more commonly been referred to as "**CNV**" and is the downstream offering for the upstream [Kubevirt project](https://kubevirt.io/). While some aspects of this lab will still have references to "CNV" any reference to "CNV", "Container-native Virtualization" and "OpenShift Virtualization" can be used interchangeably.

In these labs you'll utilise a virtual environment that mimics, as close as (feasibly) possible, to a real baremetal OpenShift 4.6 deployment, although in this configuration we're using OpenShift on OpenStack given that RHPDS is driving the deployment. This will help get you up to speed with the concepts of OpenShift Virtualization. In this hands-on lab you won't need to deploy OpenShift as you'll have access to a freshly deployed cluster, ready to be configured and used with OpenShift Virtualization.

This is the self-hosted lab guide that will run you through the following topics:

* **Validating the OpenShift deployment**
* **Deploying OpenShift Virtualization**
* **Setting up Storage for OpenShift Virtualization**
* **Setting up Networking for OpenShift Virtualization**
* **Deploying Test Workloads**
* **Cloning Workloads**
* **Performing Live Migrations and Node Maintenance**
* **Utilising pod networking for VM's**
* **Using the OpenShift Web Console with OpenShift Virtualization** 

## Lab Setup

The lab be run from within the hosted lab environment you are reading this from now. The lab environment that you're using now provides both a CLI with the tools you need (oc, virtctl) as well as access to the OpenShift console as a privileged user. Switching between the console and the CLI enviornment is easy. At the top middle of the lab guide you'll find a link to switch between the "**Terminal**" and the "**Console**".

<img src="img/console-button.png"/>

Selecting either will highlight your choice in blue and change the window's focus to the requested environment. 

Within the lab you can cut and paste commands directly from the instructions; but also be sure to review all commands carefully both for functionality and syntax!

> **NOTE**: In some browsers and operating systems you may need to use Ctrl-Shift-C / Ctrl-Shift-V to copy/paste!

## Environment

The lab environment consists of three (3) OpenShift masters and two (2) OpenShift workers. 
The installation consists of two networks, one for internal OpenShift communication, and another to represent a "public" network unrelated to OpenShift. This second network is connected to all OpenShift workers. We use this public network in the labs but it should be noted that the network uses an unroutable range and is just an example. The point is that it is external to OpenShift and could be any extra network.

In addition to the OpenShift deployment we are running a bastion host with a few extra services:

* A squid proxy server to allow a web browser on your local system access to the "public" network mentioned above. Instructions on how to connect your browser to this are below.
* An NFS server to provide basic storage to our OpenShift environment. It is full configured and available to the lab.

Conceptually, the environment looks like this:

<center>
    <img src="img/labarch.png"/>
</center>

## Accessing the bastion host

As mentioned, this host serves a few purposes and **it is required to connect to it before starting the lab**.

On the bastion is a squid proxy server. This server allows your local browser to have access to the "public" network we utilise in the lab. As this network is actually a non-routable IP range within RHPDS, you'll need the squid to access it. 

The following steps can be performed on the browser you are using now, or an alternative one. The squid will allow you to also connect to the internet, so using your current browser won't compromise your connectivity - **just remember to change the settings back when you are done with the lab!**

> **NOTE**: All connections to the bastion are made using the username and password supplied in the RHPDS email, below is just an example:

### Step 1 
Initiate an SSH forward from port 8080 on your local machine to port 3128 (squid) on the bastion:

~~~bash
$ ssh roxenham-redhat.com@bastion.bfb0.green.osp.opentlc.com -L 8080:localhost:3128
roxenham-redhat.com@bastion.bfb0.green.osp.opentlc.com's password: (see email)

Activate the web console with: systemctl enable --now cockpit.socket

This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
To register this system, run: insights-client --register

Last login: Sun Jul 26 19:31:07 2020 from 106.69.159.19
[roxenham-redhat.com@bastion ~]$ oc get nodes
~~~

> **NOTE**: You will also use the login for some non-OpenShift commands in the lab. However, the bastion has the openshift client, `oc`, configured and useable. Try `oc get nodes` when you are there!

~~~
[roxenham-redhat.com@bastion ~]$ oc get nodes
NAME                              STATUS     ROLES    AGE   VERSION
cluster-bfb0-nzlnx-master-0       Ready      master   78m   v1.18.3+47c0e71
cluster-bfb0-nzlnx-master-1       Ready      master   79m   v1.18.3+47c0e71
cluster-bfb0-nzlnx-master-2       Ready      master   79m   v1.18.3+47c0e71
cluster-bfb0-nzlnx-worker-59z7x   Ready      worker   65m   v1.18.3+47c0e71
cluster-bfb0-nzlnx-worker-xm8kw   Ready      worker   65m   v1.18.3+47c0e71
~~~


### Step 2
Configure your browser to utilise the port forward. Set your browser (we've tested Firefox and had the most success with this - your mileage may vary with other browsers) to use **localhost:8080** for all protocols, and make sure you enable ***DNS over SOCKSv5*** - this avoids any challenges with local DNS:

<center>
<img src="img/firefox-proxy.png" width="80%"/>
</center>

As mentioned this connection now allows you to access the. lab's "public" network on 192.168.47.0/24 as well as the lab guide and the internet beyond.

# Feedback Please!

We very much welcome feedback on the content, what's missing, what would be good to have, etc. Please feel free to submit PR's or raise [GitHub](https://github.com/RHFieldProductManagement/openshift-virt-labs/tree/rhpds) issues! We are always excited to continue to evolve this lab to fit your requirements. So please do let us know what we can add to make it work even better for your needs.
