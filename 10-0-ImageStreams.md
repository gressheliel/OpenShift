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




