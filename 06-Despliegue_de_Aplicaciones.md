## Crear una Aplicacion desde una image
- oc new-app apasoft/blog    [apasoft es el nombre de una imagen, busca por defecto en dockerHub]
- Crea un ImageStream
  - Es un puntero a una imagen concreta que se ha creado
- Crea un Deployment
- Crea un Service
- No crea la ruta, pero sugiere la creación de una.
  - oc expose service/blog

- oc get is
- oc describe is blog
- oc get images | grep blog

Para acceder : http://blog-desa1.apps-crc.testing

## Verificar componentes creados
- Developer -> Topology -> blog
- Administrador -> Workload -> deployment
- Administrador -> Networking -> Services
- Administrador -> Networking -> Routes

## DeploymentConfig esta deprecated, Usar Deployment
- Forzar a que cree un deployment-config
- oc new-app --name=nginx10 --as-deployment-config=true --image=nginx
- Crea un ImageStream
  - Es un puntero a una imagen concreta que se ha creado
- Crea un DeploymentConfig
- Crea un Service
- No crea la ruta, pero sugiere la creación de una.
  - oc expose service/blog

## NextCloud imagen de nube, otro ejemplo
- oc new-app --name=mi-nube nextcloud
- Crea un ImageStream
  - Es un puntero a una imagen concreta que se ha creado
- Crea un Deployment
- Crea un Service
- No crea la ruta, pero sugiere la creación de una.
  - oc expose service/mi-nube
- oc get all -l app=mi-nube

## Comprobar que componentes va crear sin lanzar la aplicacion
- oc new-app --name=ejemplo --image=tomcat -o yaml > tomcat.yaml
- oc apply -f tomcat.yaml
- oc expose svc ejemplo

## Crear una app basada en imagen desde la consola Web
- Developer -> Add -> Container Images ->
  - Image name from external registry :   http   [Busca imagen en dockerHub]
  - Runtime icon: Lo que sea
  - Application name : mi-entorno-web [Nombre Logico que le pone dentro de Topology ] 
  - Name: apache-web [Representa el nombre real de la app]
  - Resource Type: Deployment
  - Target port : 80
  - Create a route : Checked option
  - Scaling: 2 [Numero de instancias de la imagen]
  - Labels: entorno=desarrollo
  - Create

## Crear una aplicacion basada en imagen de un repositorio interno
- Ver las imagenes en el repo interno
  - oc get is
  - oc get is -n openshift
  - oc get images | grep mariadb
- Developer -> Add -> Container Images ->
  - Image stream  tag from internal registry :  
    - Project: openshift
    - Image Stream: mariadb
    - Tag: 10.3
  - Runtime icon: Lo que sea
  - Application name : mi-entorno-web [Nombre Logico que le pone dentro de Topology ] 
  - Name: mariadb1 [Representa el nombre real de la app]
  - Resource Type: Deployment
  - Target port : 3306
  - Create a route : Checked option
  - Scaling: 1 [Numero de instancias de la imagen]
  - Labels: entorno=desarrollo-mariadb
  - Create

## Ejemplo practico: Wordpress y MYSQL
- Ver documento : 06-2-Prácticas-EjemploMysql_Wordpress.pdf

## Estrategias para construir desde codigo fuente
- Ver documento : 06-3-Estrategias_codigo_fuente.pdf

## Despliegue desde codigo fuente. Introduccion
- Ver documento : 06-4-Repaso_objetos_creados_BUILD_COURSE.pdf

## Despliegue desde codigo fuente. Repositorio GIT
- oc new-app python:3.5~https://github.com/apasofttraining/blog.git --name blog1       
  - [python es el ImageBuilder a utilizar]
  - Crea un ImageStream
    - Es un puntero a una imagen concreta que se ha creado
  - Crea un BuildConfig [Este objeto es nuevo]
  - Crea un Deployment
  - Crea un Service
  - No crea la ruta, pero sugiere la creación de una.

- oc get bc blog1 [BuildConfig]
- oc get build  [Procesos que lanza el BuildConfig]
- oc get pod   [Pods construidos con el BuildConfig]

- oc expose svc blog1

## Despliegue de una aplicacion desde codigo fuente, desde consola web
- Developer -> Add -> Git Repository Import from Git
  - Git Repo URL: https://github.com/apasofttraining/blog.git
    - Builder Image: Buscaria una imagen constructora para construir la imagen [py = python, mvn = java]
    - Dockerfile: Deberia haber un Dockerfile
    - Devfile: Es un fichero YAML, que describe la estructura de la app
  - Para el ejemplo detecta que existe un Dockerfile, Pero se revierte la seleccionpor Builder Image
    - Edit Import Strategy
    - Seleccionar la builder image
  - Version de Image Builder
  - Application Name : Vacio,   Es un nombre generico para organizar elementos dentro de la consola
  - Name: blog2
  - Resource Type: Deployment
  - Target Port: 8080
  - Create a Route: Opcional
  - Create
- Para ver como esta construyendo
  - oc get bc
  - oc get build
  - oc get deploy
  - oc get rs
  - oc get pod

## Despliegue de una aplicacion usando un Dockerfile, Comandos
- oc new-app --name blog3 --strategy=docker https://github.com/apasofttraining/blog.git
- oc get bc
- oc get builds
- oc get pods
- oc logs blog3-1-build
- oc logs -f blog3-1-build

## Despliegue de una aplicacion usando un Dockerfile, Desde la consola web
- Developer -> Add -> Git Repository Import from Git
  - Git Repo URL: https://github.com/apasofttraining/blog.git
    - Builder Image: Buscaria una imagen constructora para construir la imagen [py = python, mvn = java]
    - Dockerfile: Deberia haber un Dockerfile
    - Devfile: Es un fichero YAML, que describe la estructura de la app
  - Para el ejemplo detecta que existe un Dockerfile
  - Application Name : Vacio,   Es un nombre generico para organizar elementos dentro de la consola
  - Name: blog4
  - Resource Type: Deployment
  - Target Port: 8080
  - Create a Route: Opcional
  - Create
- Para ver como esta construyendo
  - oc get bc
  - oc get build
  - oc get deploy
  - oc get rs
  - oc get pod

## Despliegue de aplicaciones desde el catalogo
- Developer -> Add -> Developer Catalog -> All Items

## Despliegue de una plantilla desde el catalogo
- Developer -> Add -> Developer Catalog -> All Items -> Databases -> MariaDB Template -> Instantiate Template
  - Namespace: desa1
  - Memory Limit: 512Mi
  - Namespace: openshift
  - Database Service Name: mariadb-svc
  - MariaDb Connection Username: usu1
  - MariaDB Connection Password: prueba
  - MariaDB root Password: prueba
  - MariaDB Database Name: db1
  - Version of MariaDB Image: 10.3
  - Create
  - Una vez creado dentro del pod  acceder a Terminal
    - mysql -u root
    - show databases;
  - oc get pod
  - oc rsh mariadb-svc-1-514r6
      - mysql -u root
      - show databases;

## Despliegue de un Image Builder  desde el catalogo
- Developer -> Add -> Developer Catalog -> All Items -> Type -> Builder Images -> nodeJs
- Creara : BuildConfig, ImageStream, DeploymentConfig, Service, Route
- Create
  - Run: start
  - Git Repo URL: https://github.com/sclorg/node-ex.git
  - Application name:
  - Name: node1
  - Resource type: Deployment
  - target port :8080
  - Checked create route
  - Create

## Create un recurso YAML desde la consola Web
- ReplicationController y ReplicaSet controlan el numero pods que se crean

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: rep-controller1
spec:
  replicas: 3
  selector:
    app: apasoft-rc
  template:
    metadata:
      name: apasoft-rc
      labels:
        app: apasoft-rc
    spec:
      containers:
      - name: apasoft-rc
        image: apasoft/blog
        ports:
        - containerPort: 80
```
- Developer -> Add -> Import YAML -> Create

## Application Grouping
- Crear 2 aplicaciones separadas
- Se usaran las que se crearon previamente : wordpress1, mysql1
- Developer -> Topology -> Click en la App -> Actions -> Edit Application Grouping -> Select Application
  -> Create Aplication -> wordpress
- Developer -> Topology -> Click en la App -> Actions -> Edit Application Grouping -> Select Application
  -> Ya aparece wordpress -> wordpress -> Save
- La esfera ahora envuelve a las 2 applications : wordpress, mysql.



























