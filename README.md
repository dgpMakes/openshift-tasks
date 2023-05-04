## TASK: Deploying an app in Openshift

### STEP 1: Defining the API

- We'll use [SwaggerEditor](https://editor.swagger.io/) to generate the code of our API.
- Once the YAML is written, download it.

### STEP 2: Creating the API with OpenAPI and Podman

- Inside the directory that contains the YAML file, run the following:


```bash
{
  podman run --rm \
  -v $PWD:/tmp/openapi/ \
  --security-opt label=disable docker.io/openapitools/openapi-generator-cli:latest generate \
  -i /tmp/openapi/<app-yaml-name>.yaml \
  -g spring \
  -o /tmp/openapi/out/spring
}
```
- Now you should have a folder with the API code called ``/out``.
- Get into that folder and use ``mvn clean package`` to generate the .jar.

### STEP 3: Creating the image with Podman
- In the directory that contains ``/out``, create a Dockerfile with the following:

```bash
FROM registry.access.redhat.com/ubi8/openjdk-11:1.15-1.1679485219
ADD spring/target/openapi-spring-1.0.6.jar /deployments
```
- Build the image with ``podman build -t <image-name> .``
- You should see the image with ``podman images``.
- To run the image, remember to open the 8080 port: ``podman run -p 8080:8080 localhost/<image-name>``.


### STEP 3: Uploading image to Quay


- Create a repository in [Quay](https://quay.io/), in this case it will be public.
- Log into Quay in the terminal with ``podman login quay.io``
- Before uploading, remember to tag the image. The [Semantic Versioning (Semver)](https://semver.org/) is a specification that ensures version names are coherent. As we are creating from zero this API, we'll start by version 0.1.0.
- Tag the image with ``podman tag <current-image-name> quay.io/<quay-username>/<quay-repository-name>:<version>``, for example ``podman tag localhost/my-app:latest quay.io/diego/my-repository:0.1.0``.
- Now push the image with ``podman push quay.io/<quay-username>/<quay-repository>:<version>``.
- Perfect, now the image is in the Quay repository!

### STEP 4: Deploying image in Openshift
- Log into your Openshift cluster.
- Change the view to Developer on the left panel and select ``+Add``.
- Select Project: <project> on the top and ``Create project``.
- Select ``Container images``.
- Select image name from external registry: ``<quay-username>/<quay-repository>:<version>``.
- Fill the rest of fields as wanted and select ``Create``.
- We've deployed the image!
