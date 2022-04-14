# Openshift TV Demo

## Inspect the Operator and deploy CockroachDB

1. Filter the list of CRD's to show the Cockroach CRD.

```
oc get crd --field-selector metadata.name=crdbclusters.crdb.cockroachlabs.com
```

2. See the Controller for the Operator running in the 
```
oc get po -n cockroachdb
```

3. Deploy a Cockroachdb Cluster using the Custom Resource Definition created by the Operator.

```
oc apply -f example.yaml -n cockroachdb
```

4. By applying the YAML example it create a Custom Resource called CrdbCluster. This is where the Cockroachdb Cluster definition is stored inside Kubernetes.
```
oc get CrdbCluster -n cockroachdb
```

5. Get the status of the StatefulSet for the Kubernetes API.
```
oc get statefulset -n cockroachdb
```

6. Get the status of the Pods in the StatefulSet from the Kubernetes API.
```
oc get po -n cockroachdb
```

7. To use the CockroachDB SQL client, first launch a secure pod running the cockroach binary.
```
kubectl create -f client-secure-operator.yaml -n cockroachdb
```

8. Get a shell into the client pod:
```
kubectl exec -it cockroachdb-client-secure -n cockroachdb -- ./cockroach sql --certs-dir=/cockroach/cockroach-certs --host=cockroachdb-public
```

9. If you want to access the DB Console, create a SQL user with a password while you're here:
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

10. We can now access the Cockroach Admin Console.
```
kubectl port-forward service/cockroachdb-public -n cockroachdb 8080
```

## Run Sample Workload

1. We are going to initialize the Movr workload:
```
oc exec -it cockroachdb-client-secure -n cockroachdb -- ./cockroach workload init movr 'postgresql://cockroachdb-0.cockroachdb.cockroachdb:26257?sslcert=%2Fcockroach%2Fcockroach-certs%2Fclient.root.crt&sslkey=%2Fcockroach%2Fcockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=%2Fcockroach%2Fcockroach-certs%2Fca.crt&user=root'
```

2. Running a Movr workload is as follows:
```
oc exec -it cockroachdb-client-secure -n cockroachdb -- ./cockroach workload run movr --duration=10m --tolerate-errors --max-rate=20 --concurrency=1 --display-every=10s 'postgresql://cockroachdb-0.cockroachdb.cockroachdb:26257?sslcert=%2Fcockroach%2Fcockroach-certs%2Fclient.root.crt&sslkey=%2Fcockroach%2Fcockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=%2Fcockroach%2Fcockroach-certs%2Fca.crt&user=root'
```

3. Kill a Cockroach node (pod) via the CLI.
```
oc delete pod cockroachdb-2 --namespace=cockroachdb
```

## Cleanup

1. Clean down you environment.
```
crc stop
crc delete
crc cleanup
crc start
```







