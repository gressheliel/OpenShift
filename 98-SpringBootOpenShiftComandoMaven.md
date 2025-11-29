## Steps
1. Build an artifactory for our Spring Boot microservice
  - mvn clean package

2. Create a docker image[From Dockerfile]
  - docker build -t service-info .

3. Push the Docker image to the OpenShift image registry
  - oc projects [demoprj]
  - oc get is
  - docker tag service-info:latest $(oc regsitry info)/demoprj/service-info:latest
  - docker push $(oc registry info)/demoprj/service-info:latest

4. Create a deployment file
  - Tienen un Deployment y un Service
  - oc apply -f Deployment.yaml
  - oc port-forward service/myapp-service 30000:80
  