# Openshift TV Demo

1. Filter the list of CRD's to show the Cockroach CRD.

```
oc get crd --field-selector metadata.name=crdbclusters.crdb.cockroachlabs.com
```

1. See the Controller for the Operator running in the 
```
oc get po -n cockroachdb
```

1. Deploy a Cockroachdb Cluster using the Custom Resource Definition created by the Operator.

```
oc apply -f example.yaml -n cockroachdb
```

1. By applying the YAML example it create a Custom Resource called CrdbCluster. This is where the Cockroachdb Cluster definition is stored inside Kubernetes.
```
oc get CrdbCluster -n cockroachdb
```

1. Get the status of the StatefulSet for the Kubernetes API.
```
oc get statefulset -n cockroachdb
```

1. Get the status of the Pods in the StatefulSet from the Kubernetes API.
```
oc get po -n cockroachdb
```

1. To use the CockroachDB SQL client, first launch a secure pod running the cockroach binary.
```
kubectl create -f https://raw.githubusercontent.com/cockroachdb/cockroach-operator/master/examples/client-secure-operator.yaml
```

1. Get a shell into the client pod:
```
kubectl exec -it cockroachdb-client-secure -- ./cockroach sql --certs-dir=/cockroach/cockroach-certs --host=cockroachdb-public
```

1. If you want to access the DB Console, create a SQL user with a password while you're here:
```
CREATE USER roach WITH PASSWORD 'Q7gc8rEdS';
```
Add this user as an Admin.
```
GRANT admin TO roach;
```
Then exit out of the SQL Client.
```
\q
```

1. We can now access the Cockroach Admin Console.
```
kubectl expose statefulset cockroachdb -n cockroachdb --port=8080 --target-port=8080 \
        --name=cockroachdb-public-external --type=LoadBalancer

kubectl port-forward service/cockroachdb-public 8080
```

# Run Sample Workload

1. We are going to initialize the Movr workload:
```
oc exec -it crdb-client-secure --namespace cockroachdb -- ./cockroach workload init movr 'postgresql://root@crdb-tls-example-0.crdb-tls-example.cockroachdb:26257?sslcert=%2Fcockroach%2Fcockroach-certs%2Fclient.root.crt&sslkey=%2Fcockroach%2Fcockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=%2Fcockroach%2Fcockroach-certs%2Fca.crt'
```

1. Running a Movr workload is as follows:
```
oc exec -it crdb-client-secure --namespace cockroachdb -- ./cockroach workload run movr --duration=3m --tolerate-errors --max-rate=20 --concurrency=1 --display-every=10s 'postgresql://root@crdb-tls-example-0.crdb-tls-example.cockroachdb:26257?sslcert=%2Fcockroach%2Fcockroach-certs%2Fclient.root.crt&sslkey=%2Fcockroach%2Fcockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=%2Fcockroach%2Fcockroach-certs%2Fca.crt'
```





