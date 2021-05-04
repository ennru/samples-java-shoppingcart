# Akka Serverless sample: Shopping Cart (using a Value Entity)

This example project implements an API for a shopping cart.

## Things to try out

1. [Deploy the pre-built container to Akka Serverless](#deploy-the-pre-built-container-to-akka-serverless)
1. [Build the image with Maven and deploy it](#building-the-container-image-using-maven)
1. Run the integration tests locally

## Deploy the pre-built container to Akka Serverless

## TODO: correct docker repo
You can use the pre-built `lightbend-docker-registry.bintray.io/cloudstate-samples/shopping-cart-java:latest` container image.

Alternatively, you can build an image from the sources and push it to your own container image repository.

### Prerequisites

* Install JDK (Java Development Kit) version 8 or later.
  * We recommend OpenJDK 11. (Check with `java -version`)
  * [SdkMan](https://sdkman.io/) provides convenient way to install and manage multiple Software Development Kits including JDK.
  * Alternatively, Windows, MacOS, and Linux installer packages are available from [AdoptOpenJDK](https://adoptopenjdk.net/installation.html#installers).

### Building the container image using Maven

1. Modify the Maven config file `pom.xml` as shown below. Use the location of your Docker repository and the correct tag.
   If you use original setting without any change, it generates image `my-docker-repo/samples-java-shoppingcart:1.0-SNAPSHOT`.
   
```xml
    <properties>
        <akkaserverless.dockerImage>my-docker-repo/${project.artifactId}</akkaserverless.dockerImage>
        <akkaserverless.dockerTag>${project.version}</akkaserverless.dockerTag>
    </properties>
```

2. Run Maven to generate the Java sources from the protobuf files, compile the source code, and generate the Docker image
```shell
mvn package
```

3. Run command `docker images | head` to make sure the Docker image is generated correctly.

4. (Optional) you can test your image loading in your local Docker by command
```shell
docker run my-docker-repo/samples-java-shoppingcart:1.0-SNAPSHOT
```
You should see the server loads without error.

5. Push your Docker image to your Docker repo.
   NOTE: you can get a free public Docker registry by signing up at [https://hub.docker.com](https://hub.docker.com/)

The commands are
```shell
docker login

# re-tag image if needed
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]   

docker push
```

### Deploying to Akka Serverless

The following steps use `akkasls` to deploy the application to [Akka Serverless](https://developer.lightbend.com/docs/akka-serverless/).

#### Prerequisites

* Get [Your Akka Serverless Account](https://developer.lightbend.com/docs/akka-serverless/getting-started/lightbend-account.html)
* Install the [Akka Serverless CLI `akkasls`](https://developer.lightbend.com/docs/akka-serverless/getting-started/set-up-development-env.html)

#### Login to Akka Serverless and set up a project

Login
```shell
akkasls auth login
```

Create a new Akka Serverless project
```shell
akkasls projects new sample-shopping-cart "Shopping Cart Sample"
```

List existing projects
```shell
akkasls projects list
```

You should see the project listed

```shell
  NAME                   DESCRIPTION            STATUS   ID
  sample-shopping-cart   Shopping Cart Sample   active   39ad1d96-466a-4d07-b826-b30509bda21b
```

Set it as your current project

```shell
akkasls config set project sample-shopping-cart
```

#### Deploy the frontend service

## TODO: correct image URL
A pre-built container image of the frontend service is provided as `lightbend-docker-registry.bintray.io/cloudstate-samples/frontend`.
If you have built your own container image, change the image in the following command to point to the one that you just pushed.

```shell
$ akkasls svc deploy frontend lightbend-docker-registry./akkaserverless-samples/frontend
```

#### Deploy your service image

If you have built your own container image, change the image in the following command to point to the one that you just pushed.

```shell
akkasls svc deploy shopping-cart \
    lightbend-docker-registry./akkaserverless-samples/shopping-cart-java
```

Wait for the shopping cart service `STATUS` to be `ready`.

```shell
akkasls svc get
```

#### Expose the service

```shell
$ akkasls svc expose shopping-cart --enable-cors
```

#### Visit the deployed shopping-cart frontend

The sample shopping cart is live. The frontend lives on the hostname previously
generated when deploying the frontend. Append `/pages/index.html` to the
provided hostname to see the shopping-cart frontend.

In the example above, the URL would be:
```
https://small-fire-5330.us-east1.apps.akkaserverless.com/pages/index.html
```



## Running locally

* Start the example from Maven
    ```shell
    mvn compile exec:java
    ```
* Start the Akka Serverless proxy

## TODO proxy command line  

* Send an AddItem command with grpcurl
  ```shell
  grpcurl --plaintext -d '{"cart_id": "cart1", "product_id": "akka-tshirt", "name": "Akka t-shirt", "quantity": 3}' localhost:9000  com.example.valueentity.shoppingcart.ShoppingCartService/AddItem
  ```

* Send a GetCart command:
  ```shell
  grpcurl --plaintext -d '{"cart_id": "cart1"}' localhost:9000  com.example.valueentity.shoppingcart.ShoppingCartService/GetCart
  ```

* Send a RemoveItem command:
  ```shell
  grpcurl --plaintext -d '{"cart_id": "cart1", "product_id": "akka-tshirt", "quantity": -1}' localhost:9000 com.example.valueentity.shoppingcart.ShoppingCartService/RemoveItem


## Running integration tests

The integration tests run the Akka Serverless container and the Akka Serverless proxy and exercise a few gRPC calls through the proxy to the services itself.

  ```shell
  mvn test
  ```

## Maintenance notes

### License

The license is Apache 2.0, see [LICENSE](LICENSE).

### Maintained by

__This project is NOT supported under the Lightbend subscription.__

This project is maintained mostly by the Akka Serverless team at [Lightbend](https://lightbend.com).

Feel free to suggest improvements and additions. Pull requests are very welcome. Thanks in advance!

### Disclaimer

[DISCLAIMER.txt](DISCLAIMER.txt)
