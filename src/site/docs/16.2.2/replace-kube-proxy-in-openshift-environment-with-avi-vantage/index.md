---
title: Replace kube-proxy in OpenShift Environment With Avi Vantage
layout: default
---
In an OpenShift environment, a user may opt to use Avi vantage alongside with kube-proxy or instead disable kube-proxy and use Avi vantage as the only application proxy.

If kube-proxy is enabled, it uses the service subnet (default is 172.30.0.0/16) to allocate east-west VIPs to services. In this case, east-west VIPs handled by Vantage have to be configured to use other subnets. Kube-proxy will be running, but unused, since services use Avi-allocated VIPs for east-west traffic, instead of OpenShift-allocated VIPs from the service network.

If a user wishes to use service subnet to load balance traffic using Avi, kube-proxy must be disabled. This mode offers operational advantages, since OpenShift’s APIs and CLIs are in sync with the VIP used for the service. That is to say, if someone does a “oc get service,” the VIPs shown in the output are the same VIPs on which Avi provides the service.

## Disable kube-proxy

To disable kube-proxy on your OpenShift environment, follow the below steps:

* Login to the OpenShift Master node.
<pre><code class="language-lua">ssh username@os_master_ip oc delete service --all OPTIONS="--loglevel=2 --disable proxy" systemctl restart origin-node.service</code></pre>
* Delete all services that maybe running (run this on master node only).
<pre><code class="language-lua">ssh username@os_master_ip oc delete service --all OPTIONS="--loglevel=2 --disable proxy" systemctl restart origin-node.service</code></pre>
* Edit /etc/sysconfig/origin-node and change the OPTIONS variable to read as below:
<pre><code class="language-lua">ssh username@os_master_ip oc delete service --all OPTIONS="--loglevel=2 --disable proxy" systemctl restart origin-node.service</code></pre>
* Save and exit the editor.
* Restart the origin-node service.
<pre><code class="language-lua">ssh username@os_master_ip oc delete service --all OPTIONS="--loglevel=2 --disable proxy" systemctl restart origin-node.service</code></pre>

Repeat the above steps for all other nodes in the OpenShift cluster

## Configuration changes on Avi

Make below changes while following the <a href="/docs/latest/avi-vantage-openshift-installation-guide">OpenShift installation guide</a>.

* Configure the east-west VIP network to use the service network (default 172.30.0.0/16).<a href="img/Fig7.png"><img src="img/Fig7.png" alt="Fig7" width="2168" height="1464"></a>
*  In cloud configuration, set 'ProxyService Placement Subnet' as 172.30.0.0/16 and select ‘Use Cluster IP of service as VIP for East/West’ checkbox.<a href="img/Fig22.png"><img src="img/Fig22.png" alt="Fig22" width="2156" height="1450"></a>