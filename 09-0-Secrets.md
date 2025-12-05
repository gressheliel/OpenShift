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


