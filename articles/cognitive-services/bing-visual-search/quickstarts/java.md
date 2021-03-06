---
title: "Quickstart: Get image insights using the Bing Visual Search REST API and Java"
titleSuffix: Azure Cognitive Services
description: Learn how to upload an image to the Bing Visual Search API and get insights about it.
services: cognitive-services
author: swhite-msft
manager: cgronlun

ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
---

# Quickstart: Get image insights using the Bing Visual Search REST API and Java

Use this quickstart to make your first call to the Bing Visual Search API and view the search results. This simple C# application uploads an image to the API, and displays the information returned about it. While this application is written in Java, the API is a RESTful Web service compatible with most programming languages.

When uploading a local image, the form data must include the Content-Disposition header. Its `name` parameter must be set to "image" and the `filename` parameter may be set to any string. The contents of the form is the binary of the image. The maximum image size you may upload is 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## Prerequisites

* The [Java Development Kit(JDK) 7 or 8](https://aka.ms/azure-jdks)
* The [Gson library](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## Create and initialize a project

1. Create a new Java project in your favorite IDE or editor, and import the following libraries.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Create variables for your API endpoint, subscription key, and the path to your image. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## Create the JSON parser

Create a method to make the JSON response from the API more readable using `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## Construct the search request and query

1. In the main method of your application, create a Http Client using `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Create an `HttpEntity` to upload your image to the API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Create a `httpPost` object with your endpoint, and set the header to use your subscription key.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## Receive and process the JSON response

1. Use `HttpClient.execute()` to send a request to the API, and store the response in an `InputStream` object.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Store the JSON string, and print out the response.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](../tutorial-bing-visual-search-single-page-app.md)