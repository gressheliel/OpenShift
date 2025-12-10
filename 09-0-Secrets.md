## Secrets
- Son (K,V), con la diferencia esta en Base64, no es legible

## Creacion de un secret y uso en un DeploymentConfig
- oc create secret generic secret-cm --from-literal=usuario=usu1 --from-literal=password=secret
- oc get secret

```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: ejemplo-secrets
spec:
  replicas: 1
  selector:
    app: secret
  template:
    metadata:
      labels:
        app: secret
    spec:
      containers:
       - name: secret
         image: gcr.io/google-samples/node-hello:1.0
         envFrom:
         - secretRef:
            name: secret-cm
```

## Ejemplo para Odoo
- echo secret | base64
- oc apply -f secreto1.yaml
- oc get secret
```
apiVersion: v1
kind: Secret
metadata:
  name: secreto1
type: Opaque
data:
  PASSWORD: c2VjcmV0Cg==
```

- En el servicio de odoo
- oc apply -f odoo.yaml

```
        - name: PASSWORD
          valueFrom:
           secretKeyRef:
             name: secreto1
             key: PASSWORD
```

## ConfigMap Secrets desde la consola web
- Administrador -> Workloads -> Secrets
- Administrador -> Workloads -> Config Maps
