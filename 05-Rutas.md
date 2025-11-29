## Rutas
- Es puramente Openshift
- Permite obtener funcionalidad de cara a las applications
- Se parece a los Ingress Controller
- Permite disponer un objeto de manera externa al cluster de kubernetes
- Cliente -> Ruta -> Servicio -> Deployment -> Pod

## Ruta desde la linea de comandos
- oc get svc  => [Previamente se habia creado el servicio: web1-svc]
- oc expose svc web1-svc => [route.route.openshift.io/web1-svc exposed]
- oc get route => [web1-svc    web1-svc-desa1.apps-crc.testing     web1-svc    80    None]

- Para acceder al recurso =>[http://web1-svc-desa1.apps-crc.testing]

## Ruta desde la consola web
- Administrador -> Networking -> Services
  - Debe existir un servicio creado. 
- Administrador -> Networking -> Routes [Formulario ó YAML]
  - name : apache1-ruta
  - path : 
    - Se deja en blanco, ya que deberia existir un DNS que apunte a la direccion del Openshift
    - Se puede hacer una prueba editando el hosts [127.0.0.1 wwww.apasoft-pruebas.com]
  - service: seleccionar apache1-svc
  - Target port: 80 -> 80
  - Create
  - En Location aparece la ruta de acceso => [http://apache1-ruta-desa1.apps-crc.testing]
  - oc get route

























