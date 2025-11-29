## Pods VS Deployment
- Los Pods
  - No son escalables
  - No se recuperan ante caidas
  - No tiene rollbacks
- Los deployment estan conformados por : (replicaSet, deployment)
  - Pueden ser replicados clonados
  - Si se recuperan ante caidas
  - Mecanismos de rollback 

## Crear un deployment desde la línea de comandos
- oc create deployment d1 --image=httpd
- oc get deploy
- oc get rs [Genera un replicaSet]
- oc describe rs <name_replicaset>
- oc get pod
- oc describe pod <name_pod>


- Al crear un deployment se crean 3 componentes [Pod, ReplicaSet, Deployment]
- Se pueden visulizaer desde la consola web
  - Development -> Topology -> Aparecen los Pods
  - Administrador -> Workloads -> Deployment

## Crear un deployment desde consola Web
- Administrador -> Workloads -> Deployments -> Create Deployment -> [Se puede crear via asistente o YAML]

## Deployment desde el YAML
- oc apply -f deploy.yaml
- oc get deploy
- oc get rs
- oc get pod

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache1
  namespace: desa1
spec:
  selector:
    matchLabels:
      app: ejemplo-apache
  replicas: 3
  template:
    metadata:
      labels:
        app: ejemplo-apache
    spec:
      containers:
        - name: apache
          image: httpd
          ports:
            - containerPort: 80
```
## Escalar un deployment
- Administrador -> Workloads -> Deployments -> Listado de deployments -> ... 
- oc get deploy <name-deployment>

- oc scale deploy <name_deployment> --replicas=5
- Administrador -> Workloads -> Deployments -> Listado de deployments -> ... -> Edit Pod count



