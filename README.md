<p align="center">
<img src="Broadcom-vmware.png" width="300" alt="Online Boutique" />
</p>

<p align="center">
<img src="app-failover.png" width="800" alt="Online Boutique" />
</p>

### Deploy the app in Primary Foundation.
```
avannala@avannalaCX6H4 cf-butler % cf a
Getting apps in org cf-butler / space dev as admin...

name               requested state   processes           routes
cf-butler          started           web:1/1, task:0/0   cf-butler-relaxed-mandrill-td.apps.keep-716898.cf-app.com
cf-hoover          started           web:1/1, task:0/0   cf-hoover-balanced-shark-kv.apps.keep-716898.cf-app.com
cf-hoover-ui       started           web:0/1, task:0/0   cf-hoover-ui-insightful-wombat-qv.apps.keep-716898.cf-app.com
pivotal-mysqlweb   started           web:1/1, task:0/0   pivotal-mysqlweb-rested-bushbuck-hv.apps.keep-716898.cf-app.com
```

### Create a private domain on primary foundation.
```
avannala@avannalaCX6H4 cf-hoover-ui % cf create-domain cf-butler tas.arullab.com
Creating private domain tas.arullab.com for org cf-butler as admin...
OK

TIP: Domain 'tas.arullab.com' is a private domain. Run 'cf share-private-domain' to share this domain with a different org.
```

```
avannala@avannalaCX6H4 cf-hoover-ui % cf domains
Getting domains in org cf-butler as admin...

name                          availability   internal   protocols
apps.internal                 shared         true       http
apps.keep-716898.cf-app.com   shared                    http
tas.arullab.com               private                   http
```
### Map a DNS route to primary application

```
avannala@avannalaCX6H4 cf-hoover-ui % cf map-route cf-butler tas.arullab.com --hostname primary-butler
Creating route primary-butler.tas.arullab.com for org cf-butler / space dev as admin...
OK

Mapping route primary-butler.tas.arullab.com to app cf-butler in org cf-butler / space dev as admin...
OK
```


```
avannala@avannalaCX6H4 cf-hoover-ui % cf a
Getting apps in org cf-butler / space dev as admin...

name               requested state   processes           routes
cf-butler          started           web:1/1, task:0/0   cf-butler-relaxed-mandrill-td.apps.keep-716898.cf-app.com, primary-butler.tas.arullab.com
cf-hoover          started           web:1/1, task:0/0   cf-hoover-balanced-shark-kv.apps.keep-716898.cf-app.com
cf-hoover-ui       started           web:0/1, task:0/0   cf-hoover-ui-insightful-wombat-qv.apps.keep-716898.cf-app.com
pivotal-mysqlweb   started           web:1/1, task:0/0   pivotal-mysqlweb-rested-bushbuck-hv.apps.keep-716898.cf-app.com
```
### Updates you DNS  with CNAME records on primary foundation

Map primary-butler.tas.arullab.com ---> cf-butler-relaxed-mandrill-td.apps.keep-716898.cf-app.com

### Optional: Share the route with secondary application
#### If deploying the secondary app in same foundation
```
avannala@avannalaCX6H4 cf-butler % cf share-route arullab.com --hostname primary -s test -o cf-butler
Sharing route primary.arullab.com to space test as admin
OK
```

### Secondary Foundation

```
avannala@avannalaCX6H4 cf-app-failover % cf a
Getting apps in org cf-butler / space test as admin...

name               requested state   processes           routes
cf-butler          started           web:1/1, task:0/0   cf-butler-patient-lynx-vc.apps.keep-716898.cf-app.com, primary.tas.arullab.com
pivotal-mysqlweb   started           web:1/1, task:0/0   pivotal-mysqlweb-funny-camel-mg.apps.keep-716898.cf-app.com
```
### Updates you DNS  with CNAME records on secondary foundation

map primary.tas.arullab.com ---> cf-butler-patient-lynx-vc.apps.keep-716898.cf-app.com


### Now you have active-active deployments

### Consider Data replication
