# sap-demo
SAP Demo 

Edit src/main/fabric8/deploymentconfig.yml and add value for "SPRING_TEIID_REST_SAPDB_ENDPOINT" property which should point the SAP Gateway OData v2 endpoint.
NOTE: I see an issue resolving a URL with https right now, I hope to fix this soon.

To deploy run

```
oc login
mvn clean install -P openshift
```

That should have deployed a image called "sap-demo" in your myproject namespace. 
This creates a service for JDBC and ODBC and Route for ODATA.

You can reach ODBC Route on port 35432, however since it is a service you can not reach from outside the minishift. For that you need to run 

```
$oc create -f - <<INGRESS
apiVersion: v1
kind: Service
metadata:
  name: sap-demo-pg-ingress
spec:
  ports:
  - name: pg
    port: 35432
  type: LoadBalancer 
  selector:
    app: sap-demo-pg
  sessionAffinity: ClientIP
INGRESS
```

then do 

```
oc get services
minishift ip
```

Then use the IP from the second command, and port shown in first command to use from your ODBC client like `psql`

I will have operator in 7.5 which will make this process lot more simpler! but you got to wait until then!!!
