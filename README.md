<h1>Openstack No NAT Challengs</h1>

<p>
    here i try Openstack without NAT so local network can directly accessed from outside
</p>
<p>here what i do :</p>
<ul>
    <li>Deploy Openstack All in One with kolla ansible</li>
    <li>1 chr vm</li>
    <li>1 Network /24 from mikrotik which divided on neutron /26</li>
    <li>I do this research inside Vcenter</li>
</ul>

<p>bassicly i follow <a href="https://superuser.openstack.org/articles/disable-nat-ipv4-openstack/">this tutorial</a> but i use different command when create network</p>

<h3>Step By Step Guide :</h3>
<ol>
    <li>I deploy All in one Openstack with kulla ansible and venv, take a look Global yaml and inventory in this repository</li>
    <li>Because i use python3 venv, install <a href="https://pypi.org/project/python-openstackclient/">neutron client</a> and <a href="https://pypi.org/project/python-neutronclient/">openstack client</a></li>
    <li>next is networking section</li>
</ol>

<p>here i use 3 network interface : 1 for external network, 1 for internal network(kolla deploynemt), 1 for neutron(without ip address i create mikrotik network like this</p>
<p>10.10.30.x is for external network</p>
<p>192.168.231.0/24 for internal network</p>
<p>192.168.152.1/24 for neutron (vm network)</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/ip-address.png?raw=true)

<p>create address scope :</p>

```bash
neutron address-scope-create --shared test-no-nat 4
```

<p>create subnet pool :</p>

```bash
neutron subnetpool-create --address-scope test-no-nat --shared --pool-prefix 192.168.152.0/24 --default-prefixlen 26 nonat-pool-ip4
```

<p>Create a new network for the Public/External :</p>

```bash
neutron net-create --shared --provider:physical_network physnet1 --provider:network_type flat Public-Net --shared --router:external
```

<p>create new subnet for External network</p>

```bash
neutron subnet-create Public-Net --name Public-Subnet --subnetpool nonat-pool-ip4 --dns-nameserver 8.8.8.8
```

<p>Create Internal Network :</p>

```bash
neutron net-create nonat-net-1
```


<p>create subnet for internal network:</p>

```bash
neutron subnet-create --name nonat-subnet-1 --subnetpool nonat-pool-ip4 nonat-net-1
```

<p>Create router: </p>

```bash
neutron router-create no-nat-router
```


<p>attach network which created before to the router:</p>

```bash
neutron router-interface-add no-nat-router nonat-subnet-1
neutron router-gateway-set no-nat-router Public-Net
```

<p>try to deploy vm and make sure security group is open all (for testing only)</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/test-instance.png?raw=true)

<p>make sure router gateway is pingable from VM and mikrotik router</p>

<p>ping vm to router Openstack</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/vm-to-openstack-router.png?raw=true)

<p>ping mikrotik to router Openstack</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/router-to-mikrotik.png?raw=true)


<p>if router pingable from mikrotik and openstack aio VM the next step is route external network to openstack internal network</p>

<p>routing from miktorik to internal network (geli geli) </p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/static-routing.png?raw=true)

<p>ping vm to Openstack VM</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/ping-test-from-AIO-VM.png?raw=true)

<p>ping mikrotik to Openstack VM</p>

![alt text](https://github.com/bt66/Openstack-No-Nat-Challengs/blob/main/images/ping-from-mikrotik.png?raw=true)

<p>Done</p>
