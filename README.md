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
</ol>

<p>next is networking section</p>
<p>here i use 3 network interface : 1 for external network, 1 for internal network(kolla deploynemt), 1 for neutron(without ip address)</p>
<p>i create mikrotik netwk like this </p>

<p>create address scope :</p>

```bash
neutron address-scope-create --shared test-no-nat 4
```

<p>create subnet pool :</p>
```bash
neutron subnetpool-create --address-scope test-no-nat --shared --pool-prefix 192.168.152.0/24 --default-prefixlen 26 nonat-pool-ip4
```
