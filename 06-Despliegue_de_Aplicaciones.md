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
- oc new-app python:3.5~https://github.com/apasofttraining/blog --name blog1       
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


















