# dft-k8s

## CLUSTER
```
kind create cluster --config cluster.yaml
kubectl cluster-info --context kind-kind
```

### 1 - Buildear la imagen de la API y subirla a la registry de docker que utilice el cluster.
```
docker build -t devopsggq/api-simpson:1.0.0 .
docker push devopsggq/api-simpson:1.0.0
```

### 2 - Generar los correspondientes Deployments para las aplicaciones y verificar que están visibles entre sí utilizando el objeto Service.
## APPS
```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

### 3 - Generar un Volumen persistente para la Base de datos.
## DATABASE
```
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f deployment.yaml
kubectl apply -f configmap.yaml
kubectl apply -f service.yaml
```

### 4 - Generar un Secrets de K8S para evitar acceder a la contraseña de la base por texto plano.
```
kubectl create secret generic simpson-secret --from-literal=password="Password123*"
```

### Validate Database
```
kubectl run mariadb-client --image=mysql:8.0 -i -t --rm --restart=Never -- mysql -h mysql -u appuser -p
show databases;
use simpsons_quotes;
select * from simpsons_quotes;
```

### 5 - Generar el Ingress para que la API sea accesible y pueda consultarse mediante curl, o desde un navegador.
```
kubectl apply -f ingress.yaml
```

### Validate App
```
curl -v -k http://127.0.0.1:8000/quotes
curl -v -k http://127.0.0.1:8000/docs
```