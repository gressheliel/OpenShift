## ImageStream
- Biblioteca de tags o etiquetas que apuntan a imagenes reales
- Un Image Stream (Flujo de Imágenes) en OpenShift es una abstracción que permite referenciar y gestionar imágenes de contenedores, actuando como un puntero inteligente a una versión específica de una imagen en un registro interno o externo, asegurando que siempre se use la versión correcta y permitiendo construir nuevas imágenes o desplegar aplicaciones automáticamente cuando una imagen base se actualiza, todo sin manejar directamente la complejidad del registro de contenedores. 


## Crear un ImageStream
- oc new-app wordpress --name=w1
  - Extrae la imagen de docker
  - Crea un imageStream como "w1:latest"
  - oc expose svc/w1
  - oc get route
  - oc get dc [deploymentConfig]
  - oc get rc [replicationController]
  - oc get is [imageStream]
  - oc describe is w1
  - oc get is w1 -o yaml [genera archivo]


## Crear un ImageStream, que apunte a una imagen externa
- oc import-image cassandra:latest --from="docker.io/cassandra:latest" --confirm  [Cargar referencia]
- oc get is
- oc get is cassandra -o yaml
- oc new-app cassandra:latest --name=c1  [Cargar imagen real]
- oc describe is c1   [Marca error de permisos]
- oc get pods
- oc exec -it <nombre_pod> bash
  - /# cqlsh
  - cqlsh> describe keyspaces
  - cqlsh> exit
    - /# exit
  
## Crear un ImageStream interna
- oc import-image mariadb:10.2 --confirm
- oc get is
- oc describe is mariadb
- oc get images | grep mariadb
- oc new-app mariabdb:10.2  --name=mariadb -e MYSQL_ROOT_PASSWORD=secret
- oc get pods
- oc rsh <nombre_pod>
  - # mysql -u root -p
  - > show databases
  - > exit
  - # exit
- oc get is
- oc describe is mariadb

## Etiquetar imageStream
- Crear etiquetas que apunten a registros internos o externos
- oc import-image odoo:13 --confirm
- oc get is
- oc tag odoo:13 odoo:latest
- oc get is   [Se mostrarán dos etiquetas: 13,latest]
- oc describe is odoo [Una imagen con 2 etiquetas]
- oc tag docker.io/odoo:12 odoo:12
- oc get is [Se mostrarán tres etiquetas: 13, latest,12]
- oc describe is odoo [Dos imagenes con 3 etiquetas]
  - latest y 13 apuntan a la misma imagen
  - 12 apunta a otra imagen diferente a la de docker.io

## ImageStream en la consola web
- Administrador -> Builds -> Image Streams -> Click en alguna imagen stream

## Crear una imagen desde un YML
- oc apply -f 10-1-is.yml
- oc get is
- oc describe is mi-web
- oc tag mi-web:1.0 mi-web:ultima
- oc describe is mi-web  [Una imagen con dos etiquetas]

- oc new-app mi-web:ultima --name=web1
- oc get is
- oc expose svc web1
- oc get route
- Abrir URL en el navegador

## Actualizar de forma automatica un ImageStream
- Actualizar de forma automatica cada  15 min, es una configuración por defecto
- Subir la imagen a DockerHub

```
FROM alpine
CMD echo 'Hola, estas en la imagen con la versión segunda de la aplicacion' && exec sleep infinity
```
- Renombrar el archivo a Dockerfile
- Crear la imagen localmente
  - sudo docker build -t apasoft/image1 .
- Subir la imagen a DockerHub
  - sudo docker login
  - sudo docker push apasoft/image1

- oc import-image apasoft/image1 --schedule=true --confirm [schedule=true para que revise cada 15 min]

- oc new-app imagen1 --name=im1
- oc get pods
- oc logs <nombre_pod>
- oc describe is imagen1


- Modificar el Dockerfile
- sudo docker build -t apasoft/image1 .
- sudo docker push apasoft/image1
- Esperar 15 min

- oc new-app imagen1 --name=im2
- oc get pods
- oc logs <nombre_pod>
- oc describe is imagen1  [Se observa que la imagen se actualizó sola]

## Politica de Pull de ImageStream
- Determina cuando se revisa si hay una nueva versión de la imagen
  - always: Siempre que se cree un pod, se revisa si hay una nueva versión de la imagen
  - ifnotpresent: Solo se revisa la primera vez que se crea el pod
  - never: Nunca se revisa si hay una nueva versión de la imagen

- oc edit dc im2  [deploymentConfiguration]
  - imagePullPolicy: Always  -> Cambiar a IfNotPresent
  - Esc:wq!
- oc get pods
- oc get pod <nombre_pod>





