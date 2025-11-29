## Configurar permisos para evitar errores de seguridad
- Como algunas imagenes requieren privilegios de ROOT
- Se requiere ejecutar el commando en cada uno de los proyectos que se creen en el curso
  - oc adm policy add-scc-to-user anyuid -z default

- Es posible usar repositories distintos a Docker donde hay imagenes para Openshift
  - registry.redhat.io
  - quay.io

## Repaso de PODs
- Objeto basico ó unidad minima de trabajo que tiene kubernetes
- Es una especie de capsula que envuelve al contenedor docker
- imagen -> contenedor -> pod

## Crear un POD con el commando RUN
- oc run nginx1 --image=nginx
- oc get pod
- oc describe pod nginx1

## POD desde la consola web
- Administrador -> Workloads -> Pods
- Developer -> Topology -> Seleccionar el project -> Click en el pod da detalles

## Crear un POD desde la consola web
- Administrador -> Workloads -> Pods -> Create Pod -> Manda un editor de YAML [Editar] -> Create
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-openshift
  labels:
    zona: prod
    version: v1
  annotations:
    descripcion: Curso de Openshift de Apasoft Training
spec:
  containers:
   - name: nginx   
     image: nginx
```
- oc get pods

- Tambien es posible generar el pod desde la opcion del signo + e importar el YAML

## Crear un pod desde un fichero YAML
- oc apply -f nginx.yaml


