# MLeap Serving (Spring Boot)

This Spring Boot project provides a lightweight docker image for setting up RESTful API services with MLeap models. 

## Installation

MLeap Spring Boot is a Docker image hosted on [Docker Hub](https://hub.docker.com/r/combustml/mleap-spring-boot/).

To get started, pull the image to your local machine, replacing the version with the desired one.

```
docker pull combustml/mleap-spring-boot:{VERSION}
```

## Start Server

First let's start the Docker image. Make sure to mount a directory containing your models on the host
machine into the container. In this example, we will be storing our models in `/tmp/models` and mounting it in the container at `/models`.

```
mkdir /tmp/models
docker run -p 8080:8080 -v /tmp/models:/models combustml/mleap-spring-boot:{VERSION}
```

This will expose the model server locally on port `8080`.

## Available Endpoints

1. POST /models : Loading a model, replacing the name of your bundle zip ```{BUNDLE_ZIP}``` and the chosen model name ```{YOUR_MODEL_NAME}```

```
body='{"modelName":"{YOUR_MODEL_NAME}","uri":"file:/models/{BUNDLE_ZIP}","config":{"memoryTimeout":900000,"diskTimeout":900000},"force":false}'

curl --header "Content-Type: application/json" \
  --request POST \
  --data "$body" http://localhost:8080/models
```

2. DELETE /models/{YOUR_MODEL_NAME} : Unloading a model, replacing the chosen model name ```{YOUR_MODEL_NAME}```

```
  curl --header "Content-Type: application/json" \
    --request DELETE \
    http://localhost:8080/models/{YOUR_MODEL_NAME}
```

3. GET /models/{YOUR_MODEL_NAME} : Retrieving a loaded model, replacing the chosen model name ```{YOUR_MODEL_NAME}```

```
curl --header "Content-Type: application/json" \
    --request GET \
    http://localhost:8080/models/{YOUR_MODEL_NAME}
```

4. GET /models/{YOUR_MODEL_NAME}/meta : Retrieving a loaded model meta information, replacing the chosen model name ```{YOUR_MODEL_NAME}```

```
  curl --header "Content-Type: application/json" \
    --request GET \
    http://localhost:8080/models/{YOUR_MODEL_NAME}/meta

```

5. POST /models/{YOUR_MODEL_NAME}/transform: Transform or scoring request, replacing the chosen model name ```{YOUR_MODEL_NAME}```, format ```{YOUR_FORMAT}``` and the appropriately encoded leap frame ```{ENCODED_LEAP_FRAME}```

```
body='{"modelName":"{YOUR_MODEL_NAME}","format":"{YOUR_FORMAT}","initTimeout":"35000","tag":0,"frame":"{ENCODED_LEAP_FRAME}"}'

curl --header "Content-Type: application/json" \
  --request POST \
  --data "$body" http://localhost:8080/models/transform
```

Format can be either `ml.combust.mleap.binary` or `ml.combust.mleap.json` and your leap frame needs to be encoded with that format.

Note: The above endpoints are available either using: 
- JSON (`Content-Type` header set to `application/json`)
- Protobuf (`Content-Type` header set to `application/x-protobuf`).

The previous scoring endpoints require you to encode the leap frame, either as JSON or protobuf. If you'd like to try out scoring without having to
encode the leap frame, you can use the following endpoint, replacing the chosen model name and your leap frame:

```
body='{YOUR_LEAP_FRAME_JSON}'

curl --header "Content-Type: application/json" \
  --header "timeout: 1000" \
  --request POST \
  --data "$body" http://localhost:8080/models/{YOUR_MODEL_NAME}/transform
```

Note: The above endpoint is available either using: 
- JSON (`Content-Type` header set to `application/json`) with a JSON leap frame.
- Protobuf (`Content-Type` header set to `application/x-protobuf`) with a protobuf leap frame.

Check out the available Swagger API documentation [mleap_serving_1.0.0_swagger.yaml](https://github.com/combust/mleap/blob/master/mleap-spring-boot/src/main/resources/mleap_serving_1.0.0_swagger.yaml) for more information or trying out the API.

Example models:
1. [AirBnB Linear Regression](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.lr.zip)
2. [AirBnB Random Forest](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.rf.zip)

Example leap frames:
1. [AirBnB Leap Frame](https://s3-us-west-2.amazonaws.com/mleap-demo/frame.airbnb.json)

See the README.md in [mleap-executor](https://github.com/combust/mleap/tree/master/mleap-executor) project about using gRPC instead of HTTP.