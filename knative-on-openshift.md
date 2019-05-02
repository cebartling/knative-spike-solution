# Knative on Openshift

## Build Hello World Spring Boot application locally

1. Change directory to project root directory: `cd helloworld`
1. `./mvnw package && java -jar target/helloworld-0.0.1-SNAPSHOT.jar`
1. Open a browser and navigate to http://localhost. You should see the message in the browser window.

## Openshift

1. Change directory to project root directory: `cd helloworld`
1. Login to Openshift: `oc login https://openshift-eng2-mgmt.healthpartners.com`
1. Make the *knative-serving* project as the current project: `oc project knative-serving`
1. Deploy the HelloWorld Spring Boot app as a Docker strategy build: `oc new-build --strategy=docker --name=helloworld --binary=true`
1. Start the newly created build: `oc start-build helloworld --from-dir=.`
1. Start the service: `kubectl apply -f service.yaml`
