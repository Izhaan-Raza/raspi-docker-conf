# Prayana Electric - Backend API Documentation


## Prerequisites
- Docker installed on your machine
- Docker Hub account
- Postman or any API testing tool

## Pulling the Docker Images
To get started, pull the necessary Docker images from Docker Hub:
```
docker pull izzupro/prayana-mongo-v1

docker pull izzupro/flask-mongo-bridge-v1
```

## Running the Containers
Run the MongoDB container:
```
docker run -d --name prayana-mongo --network bridge izzupro/prayana-mongo-v1
```
Run the Flask API container:
```
docker run -d -p 5000:5000 --name esp32-flask --network bridge izzupro/flask-mongo-bridge-v1
```

## Testing the API with Postman
To test data insertion:
- Send a `POST` request to `http://localhost:5000/data`
- Use the following JSON body:
```json
{
    "name": "sensor1",
    "id": "18uhkrar",
    "time": "12:21:45"
}
```

To fetch data:
- Send a `GET` request to `http://localhost:5000/data`

## ESP32 Code
```cpp
#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
const char* serverUrl = "http://your_server_ip:5000/data";

void setup() {
    Serial.begin(115200);
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");
}

void loop() {
    if (WiFi.status() == WL_CONNECTED) {
        HTTPClient http;
        http.begin(serverUrl);
        http.addHeader("Content-Type", "application/json");

        String jsonPayload = "{\"name\": \"sensor1\", \"id\": \"18uhkrar\", \"time\": \"12:21:45\"}";
        int httpResponseCode = http.POST(jsonPayload);

        if (httpResponseCode > 0) {
            String response = http.getString();
            Serial.println(httpResponseCode);
            Serial.println(response);
        } else {
            Serial.print("Error on sending POST: ");
            Serial.println(httpResponseCode);
        }

        http.end();
    }
    delay(5000);
}
```

## Stopping the Containers
To stop the containers, run:
```
docker stop esp32-flask prayana-mongo
```
To remove the containers:
```
docker rm esp32-flask prayana-mongo
```

