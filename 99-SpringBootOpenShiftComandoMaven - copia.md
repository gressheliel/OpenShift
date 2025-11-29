## Despliegue de una app de SpringBoot

0. Desde la raíz del proyecto :   C:\WorkSpace\codeSpringToolSuite\msach-b-business-application-data>

1. Version del oc
```
"C:\WorkSpace\oc-4.15.18-windows\oc" version
```

2. Para obtener las credenciales, se da click en el nombre de usuario en sesión -> Copy login Command

```
"C:\WorkSpace\oc-4.15.18-windows\oc" login --token=sha256~nWotLjVjRQ2UhAWA6nLKZRoTZFBDh0no1vG-zfq8qxI --server=https://api.bcpl-bex-osd.v49u.p1.openshiftapps.com:6443
```


3. Cambiar a la  rama adecuada
```
"C:\WorkSpace\oc-4.15.18-windows\oc" project bex-negocio-stg
```


4. Ejecutar construccion
```
mvn oc:deploy -Popenshift -Djkube.generator.from=registry.access.redhat.com/openjdk/openjdk-11-rhel7:1.1 -Djkube.namespace=<NAMESPACE>

mvn oc:deploy -Popenshift -DskipTests=true -Dmaven.test.skip=true -Djkube.namespace=bex-negocio-stg 
-Djkube.generator.from=registry.access.redhat.com/openjdk/openjdk-11-rhel7:1.1

mvn oc:deploy -Popenshift 
              -DskipTests=true 
              -Dmaven.test.skip=true 
              -Djkube.namespace=bex-negocio-stg 
              -Djkube.generator.from=registry.access.redhat.com/openjdk/openjdk-11-rhel7:1.1
```

5. CMD OK
```
"C:\Program Files\apache-maven-3.6.3\bin\mvn" oc:deploy -Popenshift -DskipTests=true -Dmaven.test.skip=true -Djkube.namespace=bex-negocio-stg -Djkube.generator.from=registry.access.redhat.com/openjdk/openjdk-11-rhel7:1.1
```





OTROS COMMANDS	
"C:\Program Files\apache-maven-3.6.3\bin\mvn" package oc:build -P openshift

"C:\Program Files\apache-maven-3.6.3\bin\mvn" oc:deploy -P openshift

i+I6N,WpV8xb1*xxlKu2j3v!NH9LSMqu

"C:\Program Files\apache-maven-3.6.3\bin\mvn" oc:deploy -Popenshift -DskipTests=true -Dmaven.test.skip=true -Djkube.namespace=bex-negocio-stg 
-Djkube.generator.from=registry.access.redhat.com/openjdk/openjdk-11-rhel7:1.1








 