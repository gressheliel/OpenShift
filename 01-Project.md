## Developer
- Project es el objeto mas globlal, contienen otros componentes
- Es un namespace de kubernetes,con posibilidad de tener mas objetos


## Crear project
- oc login -u kubeadmin -p <password> https://api.crc.testing:6443 [como Administrador]
- oc new-project <nombre_project>

- oc get projects
- oc get ns

Cuando se crea un project, crea tambien una namespace por defecto.

## Ficheros YAML
- Tiene bloques que describe como es el objeto que queremos crear
  Al menos tiene 4 bloques (apiVersion, kind, metadata, spec)

## Caracteristicas de un project
- oc get project <name_project>
- oc get describe project <name_project>
- oc get project <name_project> -o yaml

## Proyectos desde la consola web
- Administrador -> Home  -> Projects (Listado de projects)
  En los 3 puntos (Editar y borrar)
  Click en el project
  En la pestaña del YAML se pueden editar y guardar algunas propiedades

- Developer -> Project -> Seleccionar project

## Crear proyecto desde un YAML
- oc apply -f proyecto.yaml
- oc get projects -l tipo=desa
- oc describe project desa2

```
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  annotations:
    openshift.io/description: Esto es la descripcion del proyecto
    openshift.io/display-name: Ejemplo de creacion de una proyecto Openshift
    openshift.io/requester: developer
    documentacion: Ejemplo para crear un proyecto en openshift
  name: desa2
  labels:
     tipo: desa
spec:
  finalizers:
  - kubernetes
```

## Crear un proyecto desde la consola Web
- Administrador -> Home -> Projects -> Create project (Name, Display Name, Description) -> Create
- Developer -> Project -> Seleccionar project

## Borrar un proyecto
- oc get projects
- oc delete project <name_project>
- Administrador -> Home -> Projects -> 3 puntos Delete(Pide una confirmacion)

## Que sucede al crear un NameSpace
- Si creas un project se crea un namespace, 
- ¿Qué sucede si creo NameSpace?
- Se crea un project

- oc create namespace <name_namespace>
- Crea el project y el namespace
- Si se borra uno se elimina el otro.





