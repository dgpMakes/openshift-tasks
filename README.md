## TASK: Deploying an app in Openshift

### STEP 1: Creating API

- We'll use SwaggerEditor to generate the code of our API.
- Once we have written the YAML, we generate the server code for spring (java).
- When finished downloading, we can execute `mvn clean package` to build the .jar and `mvn clean spring-boot:run` to run the server.
- We can use the API by making calls with Postman for example.

### STEP 2: Creating image on Docker
- In this case, we copy files with ADD and choose the correct entrypoint.
