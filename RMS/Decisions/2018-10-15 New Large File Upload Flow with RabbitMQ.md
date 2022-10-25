### New Large File Upload Flow with RabbitMQ
#### RabbitMQ
1. [Messaging that just works — RabbitMQ](https://www.rabbitmq.com)
2. We use the RabbitMQ service as the notification system to solve
    - upload waiting time issue for mobile client
    - notification system in rms
      - Project notification (e.g. new file uploaded)
      - MySpace notification (e.g. new file shared with me)
      - etc.

#### Upload Issue
1. [Bug 50032 – Need to supoprt upload file with fixed size and response quick for file uploading](http://bugs.cn.nextlabs.com/show_bug.cgi?id=50032)

#### New Upload Flow
```sequence
Title: Upload large file flow
client->RMS: 1. Call getTicket API 
RMS-->client: 2. Return a ticket
client->RabbitMQ: 3. Subscribe to RabbitMQ
client->RMS: 4. Call uploadLargeFile API
RMS->RabbitMQ: 5. Publish the response message
RabbitMQ-->client: 6. Send the message to client via channel
```
1. The __Ticket__ in step 2&3 contains the exhange name and the routing key. Client should use these two information to subscribe the correct queue.
2. At step 4, client does not need to waiting for the response any more. Any response message will send by RabbitMQ service.

#### New APIs
1. MyDrive getTicket
2. MyDrive uploadLargeFile
3. MyValut getTicket
4. MyVault uploadLargeFile
5. Project getTicket
6. Project uploadLargeFile

#### Client-side sample code (Java)
```java
package com.nextlabs.rms.rabbitmqproducer.main;

import com.google.gson.Gson;
import com.google.gson.JsonObject;
import com.rabbitmq.client.AMQP;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Consumer;
import com.rabbitmq.client.DefaultConsumer;
import com.rabbitmq.client.Envelope;
import okhttp3.MediaType;
import okhttp3.MultipartBody;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

import java.io.File;
import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class UploadTest {
    public static final String HEADER_CLIENT_ID = "clientId";
    public static final String CLIENT_ID = "D1EB40F93A87C06EB1CCA00806BD71F4";
    public static final String HEADER_TICKET = "ticket";
    public static final String TICKET = "D88F6EE85C5329B561271D5B26E28854";
    public static final String HEADER_USER_ID = "userId";
    public static final String USER_ID = "62";
    public static final String HEADER_CONTENT_TYPE = "content-type";
    public static final String HEADER_MULTIPART_FORM = "multipart/form-data";
    public static final String HEADER_PLATFORM_ID = "platformId";
    public static final String PLATFORM_ID = "1000";
    public static final String GET_TICKET_URL = "http://vancouver.nextlabs.com/rms/rs/myDrive/uploadTicket";
    public static final String UPLOAD_LARGE_FILE_URL_BASE = "http://vancouver.nextlabs.com/rms/rs/myDrive/uploadLargeFile/";
    public static final String JSON_EXCHANGE = "exchange";
    public static final String JSON_ROUTING_KEY = "routing_key";
    public static final String JSON_RESULTS = "results";
    public static final String RABBITMQ_URL = "vancouver.nextlabs.com";
    public static final String RABBITMQ_USER = "rms";
    public static final String RABBITMQ_PASSWORD = "rms";
    public static final MediaType MEDIA_TYPE_PNG = MediaType.get("image/png");

    public static void main(String[] argv) {
        OkHttpClient client = new OkHttpClient();
        Request request = new Request.Builder()
                .url(GET_TICKET_URL)
                .addHeader(HEADER_CLIENT_ID, CLIENT_ID)
                .addHeader(HEADER_TICKET, TICKET)
                .addHeader(HEADER_USER_ID, USER_ID)
                .build();
        Response response;
        String exchange = null;
        String routingKey = null;
        try {
            response = client.newCall(request).execute();
            String responseJson = response.body().string();
            Gson gson = new Gson();
            JsonObject jsonObject = gson.fromJson(responseJson, JsonObject.class);
            JsonObject resultsJson = jsonObject.getAsJsonObject(JSON_RESULTS);
            exchange = resultsJson.get(JSON_EXCHANGE).getAsString();
            System.out.println("Exchange name: " + exchange);
            routingKey = resultsJson.get(JSON_ROUTING_KEY).getAsString();
            System.out.println("Routing key: " + routingKey);
        } catch (IOException e) {
            e.printStackTrace();
        }

        if (isEmpty(exchange) || isEmpty(routingKey)) {
            return;
        }

        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost(RABBITMQ_URL);
        factory.setUsername(RABBITMQ_USER);
        factory.setPassword(RABBITMQ_PASSWORD);
        try {
            Connection connection = factory.newConnection();
            Channel channel = connection.createChannel();
            channel.exchangeDeclare(exchange, "direct");
            String queueName = channel.queueDeclare().getQueue();
            channel.queueBind(queueName, exchange, routingKey);
            System.out.println(" [*] Waiting for messages.");
            Consumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope,
                                           AMQP.BasicProperties properties, byte[] body) throws IOException {
                    String message = new String(body, "UTF-8");
                    System.out.println(" [x] Received '" + envelope.getRoutingKey() + "':'" + message + "'");

                    try {
                        channel.close();
                        connection.close();
                    } catch (TimeoutException e) {
                        e.printStackTrace();
                    }
                }
            };
            channel.basicConsume(queueName, true, consumer);
        } catch (IOException | TimeoutException e) {
            e.printStackTrace();
        }
        File uploadFile = new File("C:\\Users\\yzhao\\Downloads\\wedding-cake2.png");
        RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("name", "file")
                .addFormDataPart("filename", "wedding-cake2.png")
                .addFormDataPart("file", "wedding-cake2.png",
                        RequestBody.create(MEDIA_TYPE_PNG, uploadFile))
                .addFormDataPart("API-input", "{\n" +
                        "  \"parameters\" : {\n" +
                        "    \"parentPathId\": \"/\", \n" +
                        "      \"name\":\"wedding-cake2.png\"\n" +
                        "  }\n" +
                        "}")
                .build();
        String uploadUrl = UPLOAD_LARGE_FILE_URL_BASE + routingKey;
        request = new Request.Builder()
                .url(uploadUrl)
                .addHeader(HEADER_CLIENT_ID, CLIENT_ID)
                .addHeader(HEADER_TICKET, TICKET)
                .addHeader(HEADER_USER_ID, USER_ID)
                .addHeader(HEADER_CONTENT_TYPE, HEADER_MULTIPART_FORM)
                .addHeader(HEADER_PLATFORM_ID, PLATFORM_ID)
                .post(requestBody)
                .build();
        try {
            client.newCall(request).execute();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    public static boolean isEmpty(String string) {
        return string == null || string.length() == 0;
    }
}
```