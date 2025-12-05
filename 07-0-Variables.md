## Variables
- Crear un nuevo project vacio
  - oc new-project variables
  - oc apply -f variables.yml
  - oc get deployment
  - oc get rc  (replicationController)
  - oc get pod
  - oc rsh  <nombre_pod>
    - $ env
    - echo $NOMBRE
    - echo $NAME
    - echo $OWNER
    - exit
    
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: 'example-variables'
spec:
  selector:
    matchLabels:
      app: variables
  replicas: 2
  template:
    metadata:
      labels:
        app: variables
    spec:
      containers:
        - name: variables
          image: gcr.io/google-samples/node-hello:1.0
          env:
          - name: NAME
            value: "OPENSHIFT COURSE"
          - name: OWNER
            value: "Apasoft Training"
```
## Modificar variables y configuracion a traves de oc edit
- oc edit dc ejemplo-variables (deploymentConfig -> deployment)
  - Es como vi (i=insertar, Esc:wq)
- oc get dc  (Se observa : REVISION = 2)

## Comandos interesantes para variables
- oc set env pod/<nombre_pod>  --list
- oc set env dc/<nombre-deploymentConfig>  --list
- oc set env dc/<nombre-deploymentConfig>  RESPONSABLE=pedro
- ¡¡ CUANDO SE HACE UNA MODIFICACION EL POD SE RE-DESPLIEGA (Se observa : REVISION = 3) !!
- oc rsh <nombre_pod>
  - $env
- oc set env dc/<nombre-deploymentConfig>  --overwrite RESPONSABLE=Rosa (Sobreescribe una variable)
- oc set env dc/<nombre-deploymentConfig>  --overwrite RESPONSABLE- (Eliminar una variable)

## Trabajar con variables desde la consola web
- Administrador -> Workload -> DeploymentConfig -> Click en el DeploymentConfig -> Environment
  - Aparecen (K,V) de las variables definidas

## Ejemplo practico OpenERP(Oddo) y Postgress
01. Crear la Bd postgresql
    - oc apply -f 07-2-dep_postgres.yaml
    - oc get dc (deploymentConfig)
    - oc get rc (replicationController)
    - oc get pod
    - oc get pod | grep postg
    - oc rsh <nombre_pod>
      - psql -u odoo postgres
        - postgres=# \l  (\l lista todas la DB)
        - exit
      - env
    - oc set env dc/<nombre_deploymentConfig> --list

02. Crear el servicio
    - oc apply -f 07-3-servicio_postgres.yaml
    - oc get svc
    - oc describe svc postgres-svc
   
    - En el deployment
    ```
    spec:
      replicas: 1
      selector:
        deploymentconfig: postgres-db
      template:
        metadata:
          labels:
            deploymentconfig: postgres-db  
    ```  
    - En el service
    ```
    selector:
      deploymentconfig: postgres-db    
    ```
    - ¡¡ EL SELECTOR DEBE COINCIDIR CON EL LABELs DE LOS POD !!

03. Crear el deployment Odoo
    - oc apply -f 07-4-odoo.yaml
    - oc get dc (deploymentConfig)
    - oc get rc (replicationController)
    - oc get pod | grep odoo   (Muestra solo los de odoo)
    - oc logs <nombre_pod>

04. Crear el servicio, y el route para exponerlo hacia afuera Odoo
    - oc apply -f 07-5-servicio-odoo.yaml
    - EN KUBERNETES SE HUBIERA CREADO UN SERVICIO DEL TIPO NODEPORT
    - oc expose svc odoo-svc
    - oc get route
    - Se accede por el HOST/PORT del route
    - oc set env dc/odoo --list

    - En el deployment
      ```
      spec:
        replicas: 1
        selector:
          deploymentconfig: odoo
        template:
          metadata:
            labels:
              deploymentconfig: odoo
      ```
    - En el service
      ```
        selector:
          deploymentconfig: odoo
      ```
    - ¡¡ EL SELECTOR DEBE COINCIDIR CON EL LABELs DE LOS POD !!


















