## Servicios
- Ofrece a los deployment ser accedido de diversas formas
- Deployment -> [Uno o Multiples Pods, cada pod tiene su IP]
  - NO tiene IP fija ni puerto fijo

- Para eso se crean los servicios
- Cliente -> Servicio -> Deployment [Pods]
  - Tiene IP Fija, Puerto Fijo
- El cliente se conecta al servicio

- ClusterIP Solo accesible dentro del cluster, Por ejemplo MuSQL
- NodePort Accesible desde afuera del cluster
- LoadBalancer, Accesible desde afuera del cluster, Se integra con Clouds de 3eros.

## Crear un servicio desde la linea de comandos
- Se crea un NodePort con Apache para que sea accesible fuera del cluster
- Crear un deployment :
- oc apply -f deploy.yaml
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
- verificar que este funcionando
- oc get deploy apache1
- Crear el servicio
  - oc expose --name=apache1-svc deploy apache1 --type=NodePort
  - oc get svc => [apache1-svc    NodePort    10.217.5.53    <node>    80:31767    8s]
- Mapean el puerto Interno:Externo   
- Preguntar por cual IP escucha la instalacion
  - crc ip  => [127.0.0.1]
- Finalmente accedo : [http://127.0.0.1:31767]

## Propiedades de los Servicios
- Desde la consola web se observa en :
  - Administrador -> Networking -> Services
- Los Pod selector, son importantes ya que asocian los pods con el servicio

## Crear un servicio desde la consola web
- Crear un deployment
  - oc create deployment nginx-dep --image=nginx
    - Output : deployment.apps/nginx-dep created
  - oc get deploy
  - oc get pod
- Administrador -> Networking -> Services -> Create Service [Se debe editar el YAML]
- Al editar la seccion :
spec:
  selector:
    app: MyApp
- ES IMPORTANTE SABER EL SELECTOR QUE APUNTA HACIA LOS PODS 
- oc get pods -o wide --show-labels  => se obtiene [app=nginx-dep]
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  namespace: desa1
spec:
  selector:
    app: nginx-dep
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 31800
  type: NodePort
```
- Para acceder => http:127.0.0.1/31800


## Crear un servicio desde YAML

- oc apply -f service.yaml
- oc get deploy web1
- oc get svc
- Acceso => http://127.0.0.1:30002

```
#Crear un Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web1
spec:
  selector:   
    matchLabels:
      app: web1
  replicas: 2 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: web1
    spec:
      containers:
      - name: apache
        image: nginx
        ports:
        - containerPort: 80		
---

# Crear el servicio asociado
apiVersion: v1
kind: Service
metadata:
  name: web1-svc
  labels:
     app: web1
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30002
    protocol: TCP
  selector:
     app: web1
```








