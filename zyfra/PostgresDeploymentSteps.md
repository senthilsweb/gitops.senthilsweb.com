alias k=kubectl
k apply -f zyfra-secrets.yaml
k apply -f pv.yaml
k apply -f pvc.yaml
k apply -f postgres-deployment.yaml
k apply -f postgres-service.yaml


--Once pods are running, you can try the sql command
kubectl exec -it postgres-57f4746d96-7z5q8 -- psql -U postgres
---you can see the below command prompt 
postgres=#

--create a database using 
create database testdb

--To see the list of database
\l


Now you can delete the pod
k delete pod <<podname>>

Enter into the pod with above exec and check the database is available, which proof the database not removed on pod terminates/db.



Data are stored in below path
--Enter into the pod 
k exec -it postgres-6c5c8586fd-snqmf --stdin --tty shell-demo -- /bin/bash

cd var/lib/postgresql/data/pgdata
