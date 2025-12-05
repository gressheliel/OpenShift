## Creación de ConfigMap linea de comandos
- oc create configmap <nombre_configMap> --from-literal=usuario=usu1 --from-literal=password=secret
- oc get cm
- oc describe cm <nombre_configMap>
- oc get cm <nombre_configMap> -o yaml
- oc delete cm <nombre_configMap>

## Para que un depyploment tome los datos del configMap
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: ejemplo-variables
spec:
  replicas: 2
  selector:
    app: variables
  template:
    metadata:
      labels:
        app: variables
    spec:
      containers:
       - name: variables
         image: gcr.io/google-samples/node-hello:1.0
         envFrom:
         - configMapRef:
            name: cf1
```
## Borrar todos los componentes de un project
- oc get projects
- oc describe project/<nombre_project>
- oc project
- oc delete all -all

## Ejemplo de ConfigMap Postgres
1. Creacion de archivo de propiedades de postgres
    - 08-1-postgres.properties
2. Creacion del configMap a partir de uno de properties
    - oc create configmap postgres-cm --from-env-file 08-1-postgres.properties
3. Creacion del archivo deployment del postgres
    - oc apply -f 08-2-dep_postgres.yaml
    - oc get dc  (DeploymentConfig)
    - oc get rc  (ReplicationController)
    - oc get pod
```
        envFrom:
         - configMapRef: 
              name: postgres-cm
```
4. Creacion del Servicio
     - oc apply -f 08-3-servicio_postgres.yaml
     - oc get svc

## Ejemplo de ConfigMap Odoo
1. Creacion de archivo de propiedades de Odoo
    - 08-4-odoo.properties
2. Creacion del configMap a partir de uno de properties
    - oc create configmap odoo-cm --from-env-file 08-4-odoo.properties
3. Creacion del archivo deployment del odoo
    - oc apply -f 08-5-odoo.yaml
    - oc get dc  (DeploymentConfig)
    - oc get rc  (ReplicationController)
    - oc get pod
    - SE PUEDEN REFERENCIAR CUANDO LAS PROPIEDADES NO SE LLAMAN IGUAL
```
        - name: HOST
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: SERVIDOR
```
4. Creacion del Servicio
     - oc apply -f 08-6-servicio-odoo.yaml
     - oc get svc



