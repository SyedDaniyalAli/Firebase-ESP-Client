# Firebase Arduino Client Library for ESP8266 and ESP32


Google's Firebase Arduino Client Library for ESP8266 and ESP32 v2.5.0


This library supports ESP8266 and ESP32 MCU from Espressif. The following are platforms in which the libraries are also available (RTDB only).


* [Arduino MKR WiFi 1010, Arduino MKR VIDOR 4000 and Arduino UNO WiFi Rev.2](https://github.com/mobizt/Firebase-Arduino-WiFiNINA)

* [Arduino WiFi Shield 101 and Arduino MKR1000 WIFI](https://github.com/mobizt/Firebase-Arduino-WiFi101)


## Tested Devices

 * Wemos D1 Mini
 * NodeMCU (ESP8266)
 * ESP-12F
 * LinkNode (ESP8266)
 * Sparkfun ESP32 Thing
 * NodeMCU-32
 * WEMOS LOLIN32
 * TTGO T8 V1.8


 ## Unsupported AT command and Mobile Network modem Bridge

The library access to the Firebase server through the WiFi for the internet connection.  The others UART/Serial mobile network modem bridge connection and AT commands were not supported.


## Features



* **Supports Realtime database.**

* **Supports Firebase Cloud Firestore (REST APIs).**

* **Supports Firebase Storage.**

* **Supports Google Cloud Storage.**

* **Supports Firebase Cloud Messaging**

* **Supports Cloud Functions for Firebase**

* **Supports Email/Password, custom and access token authentications using Service Account**

* **Built-in JSON parser and builder.**

* **Supports Ethernet in ESP32 using LAN8720, TLK110 and IP101 Ethernet modules and ESP8266 using ENC28J60, W5100 and W5500 Ethernet modules.**



## Dependencies


This library required **ESP8266 or ESP32 Core SDK**.

For Arduino IDE, ESP8266 Core SDK can be installed through **Boards Manager**. 

For PlatfoemIO IDE, ESP8266 Core SDK can be installed through **PIO Home** > **Platforms** > **Espressif 8266 or Espressif 32**.




## Migrate from Firebase-ESP8266 or Firebase-ESP32 to Firebase-ESP-Client

All function for Realtime database between these libraries are compattible.  [See this guide](/examples/README.md) for migrating.



## Installation


### Using Library Manager


At Arduino IDE, go to menu **Sketch** -> **Include Library** -> **Manage Libraries...**

In Library Manager Window, search **"firebase"** in the search form then select **"Firebase ESP Client"**. 

Click **"Install"** button.



For PlatformIO IDE, using the following command.

**pio lib install "Firebase ESP Client""**

Or at **PIO Home** -> **Library** -> **Registry** then search **Firebase ESP Client**.


### Manual installation


For Arduino IDE, download zip file from the repository (Github page) by select **Clone or download** dropdown at the top of repository, select **Download ZIP** 

From Arduino IDE, select menu **Sketch** -> **Include Library** -> **Add .ZIP Library...**.

Choose **Firebase-ESP8266-master.zip** that previously downloaded.

Go to menu **Files** -> **Examples** -> **Firebase-ESP-Client-master** and choose one from examples.





## Usages


See [this](/examples) for complete usages.

See [this](/src/README.md) for all functions details.



### Initialization


```cpp


//Include WiFi library
#include <FirebaseESP8266.h>
#if defined(ESP32)
#include <WiFi.h>
#elif defined(ESP8266)
#include <ESP8266WiFi.h>
#endif

//Include Firebase library (this library)
#include <Firebase_ESP_Client.h>

//Define the Firebase Data object
FirebaseData fbdo;

//Define the FirebaseAuth data for authentication data
FirebaseAuth auth;

// Define the FirebaseConfig data for config data
FirebaseConfig config;

//Assign the project host and api key 
config.host = FIREBASE_HOST;

config.api_key = API_KEY;

//Assign the user sign in credentials
auth.user.email = USER_EMAIL;
auth.user.password = USER_PASSWORD;

//Initialize the library with the Firebase authen and config.
Firebase.begin(&config, &auth);

//Optional, set AP reconnection in setup()
Firebase.reconnectWiFi(true);

//Optional, set number of error retry
Firebase.RTDB.setMaxRetry(&fbdo, 3);

//Optional, set number of error resumable queues
Firebase.RTDB.setMaxErrorQueue(&fbdo, 30);

//Optional, use classic HTTP GET and POST requests.
//This option allows get and delete functions (PUT and DELETE HTTP requests) works for
//device connected behind the Firewall that allows only GET and POST requests.
Firebase.RTDB.enableClassicRequest(&fbdo, true);

#if defined(ESP8266)
//Optional, set the size of BearSSL WiFi to receive and transmit buffers
//Firebase may not support the data transfer fragmentation, you may need to reserve the buffer to match
//the data to transport.
fbdo.setBSSLBufferSize(1024, 1024); //minimum size is 512 bytes, maximum size is 16384 bytes
#endif


//Optional, set the size of HTTP response buffer
//Prevent out of memory for large payload but data may be truncated and can't determine its type.
fbdo.setResponseSize(1024); //minimum size is 1024 bytes
```






## IDE Configuaration for ESP8266 MMU - Adjust the Ratio of ICACHE to IRAM

### Arduino IDE

When you update the ESP8266 Arduino Core SDK to v3.0.0, the memory can be configurable from Arduino IDE board settings.

By default MMU **option 1** was selected, the free Heap can be low and may not suitable for the SSL client usage in this library.

To increase the Heap, choose the MMU **option 3**, 16KB cache + 48KB IRAM and 2nd Heap (shared).

![Arduino IDE config](/media/images/ArduinoIDE.png)


More about MMU settings.
https://arduino-esp8266.readthedocs.io/en/latest/mmu.html

### PlatformIO IDE

When Core SDK v3.0.0 becomes available in PlatformIO,

By default the balanced ratio (32KB cache + 32KB IRAM) configuration is used.

To increase the heap, **PIO_FRAMEWORK_ARDUINO_MMU_CACHE16_IRAM48_SECHEAP_SHARED** build flag should be assigned in platformio.ini.

At the time of writing, to update SDK to v3.0.0 you can follow these steps.

1. In platformio.ini, edit the config as the following

```ini
[env:d1_mini]
platform = https://github.com/platformio/platform-espressif8266.git
build_flags = -D PIO_FRAMEWORK_ARDUINO_MMU_CACHE16_IRAM48_SECHEAP_SHARED
board = d1_mini
framework = arduino
monitor_speed = 115200
```

2. Delete this folder **C:\Users\UserName\\.platformio\platforms\espressif8266@src-?????????????**
3. Delete .pio and .vscode folders in your project.
4. Clean and Compile the project.


The supportedd MMU build flags in PlatformIO.

- **PIO_FRAMEWORK_ARDUINO_MMU_CACHE16_IRAM48**

   16KB cache + 48KB IRAM (IRAM)

- **PIO_FRAMEWORK_ARDUINO_MMU_CACHE16_IRAM48_SECHEAP_SHARED**

   16KB cache + 48KB IRAM and 2nd Heap (shared)

- **PIO_FRAMEWORK_ARDUINO_MMU_CACHE16_IRAM32_SECHEAP_NOTSHARED**

   16KB cache + 32KB IRAM + 16KB 2nd Heap (not shared)

- **PIO_FRAMEWORK_ARDUINO_MMU_EXTERNAL_128K**

   128K External 23LC1024

- **PIO_FRAMEWORK_ARDUINO_MMU_EXTERNAL_1024K**

   1M External 64 MBit PSRAM

- **PIO_FRAMEWORK_ARDUINO_MMU_CUSTOM**

   Disables default configuration and expects user-specified flags

   

### Test code for MMU

```cpp

#include <Arduino.h>
#include <umm_malloc/umm_heap_select.h>

void setup() 
{
  Serial.begin(74880);
  HeapSelectIram ephemeral;
  Serial.printf("IRAM free: %6d bytes\r\n", ESP.getFreeHeap());
  {
    HeapSelectDram ephemeral;
    Serial.printf("DRAM free: %6d bytes\r\n", ESP.getFreeHeap());
  }
}

void loop() {
  // put your main code here, to run repeatedly:
}

```







## Authentication

This library supports many types of authentications.

See [Other authentication examples](/examples/Authentications) for more authentication methods.

Some authentication methods require the token generaion and exchanging process which take more time than using the legacy token.

The system time must be set before authenticate using the custom and OAuth2.0 tokens or when the root certificate was set for data transfer. 

The authentication with custom and OAuth2.0 tokens takes the time, several seconds in overall process which included the NTP time acquisition (system time setup), JWT token generation and signing process.

By setting the system time prior to calling the **`Firebase.begin`**, the internal NTP time acquisition process will be ignored.

You can set the system time using the RTC chip or manually by calling **`Firebase.setSystemTime`**.


While authenticate using Email and password, the process will be perform faster because no token generation and time setup required. 

The authenticate using the legacy token (database secret) does not have these delay time because the token is ready to use.



#### Speed of data transfer


This library focuses on the user privacy and user data protection which follows Google authentication processes. Setting the security rules to allow public access read and write, is not recommended even the data transmision time in this case was significantly reduced as it does not require any auth token then the overall data size was reduced, but anyone can steal, modify, or delete data in your database.


Some users may have the question why the time for sending/receiving data with this library was increased when using the different authentication methods which someone compares this with other libraries and platforms which some claims to be fast or has low latency in operation as it does not use any auth token and always requires public read/write allowance security rules which is not good for your privacy and data.


Once the auth token is importance and when it was created and ready for authentication process, the data transmission time will depend on the time used in SSL/TLS handshake process (only for new session opening), the size of http header (included auth token size) and payload to be transmitted and the SSL client buffer reserved size especially in ESP8266.


The legacy token size is relatively small, only 40 bytes, result in smallest header to send, while the size of id token generated using Email/Password is quite large, approx. 900 bytes. result in larger header to send.


There is a compromise between the speed of data transfer and the Rx/Tx buffer which then reduced the free memory available especially in ESP8266.


When the reserved SSL client Rx/Tx buffer is smaller than the size of data to be transmitted, the data need to be sent as multiple chunks which required more transmission time.

This affected especially in ESP8266 which has the limited free memory.


To speed up the data transmission in ESP8266, the larger reserved Rx/Tx buffer size is necessary.


The reserved SSL Rx/Tx buffer size in ESP8266 can be set through the function \<Firebase Data object\>.setBSSLBufferSize, e.g. **fbdo.setBSSLBufferSize(2048, 2048);**


The larger BearSSL buffer reserved for ESP8266, the lower free memory available as long as the session opened (server connection).


Therefore the time for data transfer will be varied from approx. neary 200 ms to 500 ms based on the reserved SSL client Rx/Tx buffer size and the size of data to transmit.


In ESP8266, when the free memory and speed are concerned, the legacy token should be used instead of other authentication to reduce the header size and the lower SSL Rx/Tx buffer i.e. 1024 for Rx and 512 for Tx are enough.


When the session was reused (in this library), the SSL handshake process will be ignored in the subsequence requests.


The session was close when the host or ip changes or server closed or the session timed out in 3 minutes. 


When the new session need to be opened, the SSL handshake will be processed again and used the time approx 1 - 2 seconds to be done.


For post (push) or put (set) request in RTDB, to speed up the data transfer, use pushAsync or setAsync instead.


With pushAsync and setAsync, the payload response will be ignored and the next data will be processed immediately.


#### The authenication credentials and prerequisite


To use Email/Password sign-in authentication as in the examples, the Email/Password Sign-in provider must be enabled.

![Enable Email/Password Sign-in provider](/media/images/Enable_Email_Password_Provider.png)

To get API Key used in Email/Password sign-in

![API Key](/media/images/API_Key.png)

To get the Service accounts key JSON file used in Custom and OAuth2.0 tokens athentications.

![Service Account Key File](/media/images/Service_Account_Key.png)


The Firebase Host and database secret for RTDB usages.

![Firebase Host](/media/images/Firebase_Host.png)

![Firebase Auth](/media/images/Firebase_Auth.png)




## Excludes the unused classes to save memory


The classes e.g. RTDB, Firestore, FCM, Storage, Cloud Storage, and Cloud Functions for Firebase in this library can be excluded or disabled to save memory usage through [**FirebaseFS.h**](/src/FirebaseFS.h).

By comment the following macros.

ENABLE_RTDB

ENABLE_FIRESTORE

ENABLE_FCM

ENABLE_FB_STORAGE

ENABLE_GC_STORAGE

ENABLE_FB_FUNCTIONS




## Realtime Database

See [RTDB examples](/examples/RTDB) for complete usages.


### Read Data


Data at a specific node in Firebase RTDB can be read through these get functions.

The functions included `get`, `getInt`, `getFloat`, `getDouble`, `getBool`, `getString`, `getJSON`, `getArray`, `getBlob`, `getFile`.


These functions return boolean value indicates the success of the operation which will be `true` if all of the following conditions were met.

* Server returns HTTP status code 200

* The data types matched between request and response.


For generic get, use Firebase.RTDB.get(&fbdo, <path>).

And check its type with fbdo.dataType() or fbdo.dataTypeEnum() and cast the value from it e.g. fbdo.to<int>(), fbdo.to<std::string>().

The data type of returning payload can be determined by `fbdo.dataType()` which returns String or `fbdo.dataTypeEnum()` returns enum value.

The String of type returns from `fbdo.dataType()` can be string, boolean, int, float, double, json, array, blob, file and null.

The enum value type, fb_esp_rtdb_data_type returns from `fbdo.dataTypeEnum()` can be fb_esp_rtdb_data_type_null (1), fb_esp_rtdb_data_type_integer, fb_esp_rtdb_data_type_float, fb_esp_rtdb_data_type_double, fb_esp_rtdb_data_type_boolean, fb_esp_rtdb_data_type_string, fb_esp_rtdb_data_type_json, fb_esp_rtdb_data_type_array, fb_esp_rtdb_data_type_blob, and fb_esp_rtdb_data_type_file (10)


The database data's payload (response) can be read or access through the casting value from FirebaseData object with to<type>() functions (since v2.4.0).

* `String s = fbdo.to<String>();`

* `std::string _s = fbdo.to<std::string>();`

* `const char *str = fbdo.to<const char *>();`

* `bool b = fbdo.to<bool>();`

* `int16_t _i = fbdo.to<int16_t>();`

* `int i = fbdo.to<int>();`

* `double d = fbdo.to<double>();`

* `float f = fbdo.to<float>();`

* `FirebaseJson *json = fbdo.to<FirebaseJson *>();` or

* `FirebaseJson &json = fbdo.to<FirebaseJson>();`

* `FirebaseJsonArray *arr = fbdo.to<FirebaseJsonArray *>();` or

* `FirebaseJsonArray &arr = fbdo.to<FirebaseJsonArray>();`

* `std::vector<uint8_t> *blob = fbdo.to<std::vector<uint8_t> *>();`

* `File file = fbdo.to<File>();`

Or through the legacy methods

* `int i = fbdo.intData();`

* `float f = fbdo.floatData();`

* `double d = fbdo.doubleData();`

* `bool b = fbdo.boolData();`

* `String s = fbdo.stringData();`

* `String js = fbdo.jsonString();`

* `FirebaseJson &json = fbdo.jsonObject();`

* `FirebaseJson *jsonPtr = fbdo.jsonObjectPtr();`

* `FirebaseJsonArray &arr = fbdo.jsonArray();` 

* `FirebaseJsonArray *arrPtr = fbdo.jsonArrayPtr();`

* `std::vector<uint8_t> blob = fbdo.blobData();`

 * `File file = fbdo.fileStream();`


Read the data which its type does not match the data type in the database from above functions will return empty (string, object or array).

BLOB and file stream data are stored as special base64 encoded string which are only supported and implemented by this library.

The encoded base64 string will be prefixed with some header string ("file,base64," and "blob,base64,") for data type manipulation. 


The following example showed how to read integer value from node "/test/int".


```cpp
  if (Firebase.RTDB.getInt(&fbdo, "/test/int")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_integer) {
      Serial.println(fbdo.to<int>());
    }

  } else {
    Serial.println(fbdo.errorReason());
  }
```



### Store Data

To store data at a specific node in Firebase RTDB, use these set functions.

The function included `set`, `setInt`, `setFloat`, `setDouble`, `setBool`, `setString`, `setJSON`, `setArray`, `setBlob` and `setFile`. 

For faster sending data, non-waits or async mode functions are available e.g. `setAsync`, `setIntAsync`, `setFloatAsync`, `setDoubleAsync`, `setBoolAsync`, `setStringAsync`, `setJSONAsync`, `setArrayAsync`, `setBlobAsync` and `setFileAsync`. 

For generic set, use Firebase.RTDB.set(&fbdo, <path>, <any variable or value>).

These async functions will ignore the server responses.


The above functions return boolean value indicates the success of the operation which will be `true` if all of the following conditions matched.

* Server returns HTTP status code 200

* The data types matched between request and response.


Only setBlob and setFile functions that make a silent request to Firebase server, thus no payload response returned. 

The **priority**, virtual node **".priority"** of each database node can be set through Firebase's set functions.

The priority value can be used in a query or filtering the children's data under a defined node.

Priority option was removed from File and Blob functions since v2.4.0.

**ETag** (unique identifier value) assigned to Firebase's set functions is used as conditional checking.

If defined Etag is not matched the defined path's ETag, the set operation will fail with result **412 Precondition Failed**.

ETag at any node can be read through `Firebase.RTDB.getETag`.  ETag value changed upon the data was set or delete.

The server's **Timestamp** can be stored in the database through `Firebase.RTDB.setTimestamp`. 

The returned **Timestamp** value can get from `fbdo.fbdo.to<int>()`. 

The file systems for flash and sd memory can be changed in [**FirebaseFS.h**](/src/FirebaseFS.h).


The following example showed how to store file data to flash memory at node "/test/file_data".



```cpp

if (Firebase.RTDB.getFile(&fbdo, mem_storage_type_flash, "/test/file_data", "/test.txt"))
{
  //The file systems for flash and SD/SDMMC can be changed in FirebaseFS.h.
  File file = DEFAULT_FLASH_FS.open("/test.txt", "r");

  while (file.available())
  {     
    Serial.print(file.read(), HEX);     
  }    
  file.close();
  Serial.println();

} else {
  Serial.println(fbdo.fileTransferError());
}
```



### Append Data

To append new data to a specific node in Firebase RTDB, use these push functions.

The function included `push`, `pushInt`, `pushFloat`, `pushDouble`, `pushBool`, `pushString`, `pushJSON`, `pushArray`, `pushBlob`, and `pushFile`.

For faster sending data, non-waits or async mode functions are available e.g. `pushAsync`, `pushIntAsync`, `pushFloatAsync`, `pushDoubleAsync`, `pushBoolAsync`, `pushStringAsync`, `pushJSONAsync`, `pushArrayAsync`, `pushBlobAsync` and `pushFileAsync`. 

These functions return boolean value indicates the success of the operation.

The **unique key** of a new appended node can be determined from `fbdo.pushName()`.

As set functions, the Firebase's push functions support **priority**.

**ETag** was not available after push unless read the **ETag** at that new appended unique key later with `Firebase.RTDB.getETag`.

The server's **Timestamp** can be appended in the database through `Firebase.RTDB.pushTimestamp`.

The unique key of Timestamp can be determined after Timestamp was appended.


The following example showed how to append new data (using FirebaseJson object) to node "/test/append.


```cpp

FirebaseJson json;
FirebaseJson json2;

json2.add("child_of_002", 123.456);
json.add("parent_001", "parent 001 text");
json.add("parent 002", json2);

if (Firebase.RTDB.pushJSON(&fbdo, "/test/append", &json)) {

  Serial.println(fbdo.dataPath());

  Serial.println(fbdo.pushName());

  Serial.println(fbdo.dataPath() + "/"+ fbdo.pushName());

} else {
  Serial.println(fbdo.errorReason());
}
```



### Patch Data

Firebase's update functions used to patch or update new or existing data at the defined node.

These functions, `updateNode` and `updateNodeSilent` are available and work with JSON object (FirebaseJson object only).

For faster sending data, non-waits or async mode functions are available e.g. `updateNodeAsync`, and `updateNodeSilentAsync`.

If any key name provided at a defined node in JSON object has not existed, a new key will be created.

The server returns JSON data payload which was successfully patched.

Return of large JSON payload will cost the network data, alternative function `updateNodeSilent` or `updateNodeSilentAsync` should be used to save the network data.


The following example showed how to patch data at "/test".


```cpp

FirebaseJson updateData;
FirebaseJson json;
json.add("_data2","_value2");
updateData.add("data1","value1");
updateData.add("data2", json);

if (Firebase.RTDB.updateNode(&fbdo, "/test/update", &updateData)) {

  Serial.println(fbdo.dataPath());

  Serial.println(fbdo.dataType());

  Serial.println(fbdo.jsonString()); 

} else {
  Serial.println(fbdo.errorReason());
}
```





### Delete Data


The following example showed how to delete data and its children at node "/test/append"

```cpp
Firebase.RTDB.deleteNode(&fbdo, "/test/append");
```





### Filtering Data

To filter or query the data, the following query parameters are available through the QueryFilter class.

These parameters are `orderBy`, `limitToFirst`, `limitToLast`, `startAt`, `endAt`, and `equalTo`.

To filter data, parameter `orderBy` should be assigned.

Use **"$key"** as the `orderBy` parameter if the key of child nodes was used for the query.

Use **"$value"** as the `orderBy` parameter if the value of child nodes was used for the query.

Use **key (or full path) of child nodes** as the `orderBy` parameter if all values of the specific key were used for the query.

Use **"$priority"** as `orderBy` parameter if child nodes's **"priority"** was used for query.



The above `orderBy` parameter can be combined with the following parameters for limited and ranged the queries.

`QueryFilter.limitToFirst` -  The total children (number) to filter from the first child.

`QueryFilter.limitToLast` -   The total last children (number) to filter. 

`QueryFilter.startAt` -       Starting value of range (number or string) of query upon orderBy param.

`QueryFilter.endAt` -         Ending value of range (number or string) of query upon orderBy param.

`QueryFilter.equalTo` -       Value (number or string) matches the orderBy param



The following example showed how to use queries parameter in QueryFilter class to filter the data at node "/test/data"

```cpp
//Assume that children that have key "sensor" are under "/test/data"

//Instantiate the QueryFilter class
QueryFilter query;

//Build query using specified child node key "sensor" under "/test/data"
query.orderBy("sensor");

//Query any child that its value begins with 2 (number), assumed that its data type is float or integer
query.startAt(2);

//Query any child that its value ends with 8 (number), assumed that its data type is float or integer
query.endAt(8);

//Limit the maximum query result to return only the last 5 nodes
query.limitToLast(5);


if (Firebase.RTDB.getJSON(&fbdo, "/test/data", &query))
{
  //Success, then try to read the JSON payload value
  Serial.println(fbdo.jsonString());
}
else
{
  //Failed to get JSON data at defined node, print out the error reason
  Serial.println(fbdo.errorReason());
}

//Clear all query parameters
query.clear();
```



### Server Data Changes Listener with Server-Sent Events or HTTP Streaming



This library uses HTTP GET request with `text/event-stream` header to make [**HTTP streaming**](https://en.wikipedia.org/wiki/Server-sent_events) connection.

The Firebase's functions that involved the stream operations are `beginStream`, `beginMultiPathStream`, 
`setStreamCallback`, `setMultiPathStreamCallback` and/or `readStream`.

Function `beginStream` is to subscribe to the data changes at a defined node.

Function `beginMultiPathStream` is to subscribe to the data changes at a defined parent node path with multiple child nodes value parsing and works with setMultiPathStreamCallback.

Function `setStreamCallback` is to assign the callback function that accepts the **FirebaseStream** class as parameter.

Function `setMultiPathStreamCallback` is to assign the callback function that accepts the **MultiPathStream** class as parameter.


The **FirebaseStream** contains stream's event/data payloadd and interface function calls are similar to `FirebaseData` object.

The **MultiPathStream** contains stream's event/data payload for various child nodes.


To polling the stream's event/data payload manually, use `readStream` in loop().

Function `readStream` used in the loop() task to continuously read the stream's event and data.

Since polling the stream's event/data payload with `readStream`, use `fbdo.streamAvailable` to check if stream event/data payoad is available.

Function `fbdo.streamAvailable` returned true when new stream's event/data payload was available. 

When new stream payload was available, its data and event can be accessed from `FirebaseData` object functions.

Function `endStream` ends the stream operation.


Note that, when using the shared `FirebaseData` object for stream and CRUD usages(normal operation to create,read, update and delete data), the stream connection will be interrupted (closed) to connect in other HTTP mode, the stream will be resumed (open) after the CRUD usages.

For the above case, you need to provide the idle time for `FirebaseData` object to established the streaming connection and received the stream payload. The changes on the server at the streaming node path during the stream interruption will be missed.

To avoid this sitation, don't share the usage of stream's `FirebaseData` object, another `FirebaseData` object should be used.

In addition, delay function used in the same loop of `readStream()` will defer the streaming, the server data changes may be missed.

Keep in mind that `FirebaseData` object will create the SSL client inside of HTTPS data transaction and uses large memory.



The following example showed how to subscribe to the data changes at node "/test/data" with a callback function.

```cpp

//In setup(), set the stream callback function to handle data
//streamCallback is the function that called when database data changes or updates occurred
//streamTimeoutCallback is the function that called when the connection between the server 
//and client was timeout during HTTP stream

Firebase.RTDB.setStreamCallback(&fbdo, streamCallback, streamTimeoutCallback);

//In setup(), set the streaming path to "/test/data" and begin stream connection

if (!Firebase.RTDB.beginStream(&fbdo, "/test/data"))
{
  //Could not begin stream connection, then print out the error detail
  Serial.println(fbdo.errorReason());
}

  
  //Global function that handles stream data
void streamCallback(FirebaseStream data)
{

  //Print out all information

  Serial.println("Stream Data...");
  Serial.println(data.streamPath());
  Serial.println(data.dataPath());
  Serial.println(data.dataType());

  //Print out the value
  //Stream data can be many types which can be determined from function dataType

  if (data.dataTypeEnum() == fb_esp_rtdb_data_type_integer)
      Serial.println(data.to<int>());
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_float)
      Serial.println(data.to<float>(), 5);
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_double)
      printf("%.9lf\n", data.to<double>());
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_boolean)
      Serial.println(data.to<bool>()? "true" : "false");
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_string)
      Serial.println(data.to<String>());
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_json)
  {
      FirebaseJson *json = data.to<FirebaseJson *>();
      Serial.println(json->raw());
  }
  else if (data.dataTypeEnum() == fb_esp_rtdb_data_type_array)
  {
      FirebaseJsonArray *arr = data.to<FirebaseJsonArray *>();
      Serial.println(arr->raw());
  }
     

}

//Global function that notifies when stream connection lost
//The library will resume the stream connection automatically
void streamTimeoutCallback(bool timeout)
{
  if(timeout){
    //Stream timeout occurred
    Serial.println("Stream timeout, resume streaming...");
  }  
}

```

For multiple paths streaming, see the MultiPath example.


The following example showed how to subscribe to the data changes at "/test/data" and polling the stream manually.

```cpp
//In setup(), set the streaming path to "/test/data" and begin stream connection
if (!Firebase.RTDB.beginStream(&fbdo, "/test/data"))
{
  Serial.println(fbdo.errorReason());
}

//Place this in loop()
if (!Firebase.RTDB.readStream(&fbdo))
{
  Serial.println(fbdo.errorReason());
}

if (fbdo.streamTimeout())
{
  Serial.println("Stream timeout, resume streaming...");
  Serial.println();
}

if (fbdo.streamAvailable())
{
  if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_integer)
    Serial.println(fbdo.to<int>());
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_float)
    Serial.println(fbdo.to<float>(), 5);
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_double)
    printf("%.9lf\n", fbdo.to<double>());
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_boolean)
    Serial.println(fbdo.to<bool>() ? "true" : "false");
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_string)
    Serial.println(fbdo.to<String>());
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_json)
  {
      FirebaseJson *json = fbdo.to<FirebaseJson *>();
      Serial.println(json->raw());
  }
  else if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_array)
  {
      FirebaseJsonArray *arr = fbdo.to<FirebaseJsonArray *>();
      Serial.println(arr->raw());
  }
}
```



### Backup and Restore Data


This library allows data backup and restores at a defined path.

The backup file will store in SD/SDMMC card or flash memory.

The file systems for flash and SD memory can be changed via [**FirebaseFS.h**](/src/FirebaseFS.h).

Due to SD library used, only 8.3 DOS format file name supported.

The maximum 8 characters for a file name and 3 characters for file extension.

The database restoration returned completed status only when Firebase server successfully updates the data. 

Any failed operation will not affect the database (no updates or changes).

The following example showed how to backup all database data at "/" and restore.

```cpp
 String backupFileName;

 if (!Firebase.RTDB.backup(&fbdo, mem_storage_type_sd, "/", "/backup.txt"))
 {
   Serial.println(fbdo.errorReason());
 }
 else
 {
   Serial.println(fbdo.getBackupFilename());
   Serial.println(fbdo.getBackupFileSize());
   backupFileName = fbdo.getBackupFilename();
  }


  //Begin restore backed dup data back to database
  if (!Firebase.RTDB.restore(&fbdo, mem_storage_type_sd, "/", backupFileName.c_str()))
  {
    Serial.println(fbdo.errorReason());
  }
  else
  {
    Serial.println(fbdo.getBackupFilename());
  }
```


### Database Error Handling

When read store, append and update operations were failed due to buffer overflow and network problems.

These operations can retry and queued after the retry amount was reached the maximum retry set in function `setMaxRetry`.

```cpp
//set maximum retry amount to 3
 Firebase.RTDB.setMaxRetry(&fbdo, 3);
```

The function `setMaxErrorQueue` limits the maximum queues in Error Queue collection.

The full of queue collection can be checked through function `isErrorQueueFull`.


```cpp
 //set maximum queues to 10
 Firebase.RTDB.setMaxErrorQueue(&fbdo, 10);

 //determine whether Error Queue collection is full or not
 Firebase.RTDB.isErrorQueueFull(&fbdo);
```

This library provides two approaches to run or process Error Queues with two functions. 

* `beginAutoRunErrorQueue`
* `processErrorQueue`

The function `beginAutoRunErrorQueue` will run or process queues automatically and can be called once. 

While function `processErrorQueue` will run or process queues and should call inside the loop().

With function `beginAutoRunErrorQueue`, you can assigned callback function that accept **QueueInfo** object as parameter.

Which contains all information about being processed queue, number of remaining queues and Error Queue collection status.

Otherwise, Error Queues can be tracked manually with the following functions.

Function `getErrorQueueID` will return the unsigned integer presents the id of the queue which will keep using later.

Use `getErrorQueueID` and `isErrorQueueExisted` to check whether this queue id is still existed or not. 

If Error Queue ID does not exist in Error Queues collection, that queue is already done.

The following example showed how to run Error Queues automatically and track the status with the callback function.

```cpp

//In setup()

//Set the maximum Firebase Error Queues in collection (0 - 255).
//Firebase read/store operation causes by network problems and buffer overflow will be 
//added to Firebase Error Queues collection.
Firebase.RTDB.setMaxErrorQueue(&fbdo, 10);

//Begin to run Error Queues in Error Queue collection  
Firebase.RTDB.beginAutoRunErrorQueue(&fbdo, callback);


//Use to stop the auto run queues
//Firebase.endAutoRunErrorQueue(fbdo);

void errorQueueCallback (QueueInfo queueinfo){

  if (queueinfo.isQueueFull())
  {
    Serial.println("Queue is full");
  }

  Serial.print("Remaining queues: ");
  Serial.println(queueinfo.totalQueues());

  Serial.print("Being processed queue ID: ");
  Serial.println(queueinfo.currentQueueID());  

  Serial.print("Data type:");
  Serial.println(queueinfo.dataType()); 

  Serial.print("Method: ");
  Serial.println(queueinfo.method());

  Serial.print("Path: ");
  Serial.println(queueinfo.path());

  Serial.println();
}
```

The following example showed how to run Error Queues and track its status manually.

```cpp
//In setup()

//Set the maximum Firebase Error Queues in collection (0 - 255).
//Firebase read/store operation causes by network problems and buffer overflow will be added to 
//Firebase Error Queues collection.
Firebase.RTDB.setMaxErrorQueue(&fbdo, 10);


//All of the following are in loop()

Firebase.RTDB.processErrorQueue(&fbdo);

//Detrnine the queue status
if (Firebase.RTDB.isErrorQueueFull(&fbdo))
{
  Serial.println("Queue is full");
}

//Remaining Error Queues in Error Queue collection
Serial.print("Remaining queues: ");
Serial.println(Firebase.RTDB.errorQueueCount(&fbdo));

//Assumed that queueID is unsigned integer array of queue that added to Error Queue collection 
//when error and use Firebase.getErrorQueueID to get this Error Queue id.

for (uint8_t i = 0; i < LENGTH_OF_QUEUEID_ARRAY; i++)
{
  Serial.print("Error Queue ");
  Serial.print(queueID[i]);
  if (Firebase.RTDB.isErrorQueueExisted(&fbdo, queueID[i]))
    Serial.println(" is queuing");
  else
    Serial.println(" is done");
}
Serial.println();
```


Error Queues can be saved as a file in SD/SDMMC card or flash memory with function `saveErrorQueue`.

The file systems for flash and SD memory can be changed via [**FirebaseFS.h**](/src/FirebaseFS.h).

Error Queues stored as a file can be restored to Error Queue collection with function `restoreErrorQueue`.

Two types of storage can be assigned with these functions, `mem_storage_type_flash` and `mem_storage_type_sd`.

The following example showed how to restore and save Error Queues in /test.txt file.

```cpp
//To restore Error Queues

if (Firebase.RTDB.errorQueueCount(&fbdo, "/test.txt", mem_storage_type_flash) > 0)
{
    Firebase.RTDB.restoreErrorQueue(&fbdo, "/test.txt", mem_storage_type_flash);
    Firebase.deleteStorageFile("/test.txt", mem_storage_type_flash);
}

//To save Error Queues to file
Firebase.RTDB.saveErrorQueue(&fbdo, "/test.txt", mem_storage_type_flash);

```




## Firebase Cloud Messaging (FCM)

The library acts as a app server to sends the message to registeration devices by sending request to the Google's FCM backend via the legacy HTTP and HTTPv1 APIs.

The functions available are setServerKey, send, subscibeTopic, unsubscibeTopic, appInstanceInfo and regisAPNsTokens.

Function `Firebase.FCM.setServerKey` to setup the Server Key which required by the legacy protocols.

Function `Firebase.FCM.send` to send the message with the selectable legacy and HTTPv1 messages constructors.  

Function `Firebase.FCM.subscribeTopic` to add the subscription for instance ID (IID) tokens to the defined topic.

Function `Firebase.FCM.unsubscribeTopic` to remove the subscription for instance ID (IID) tokens from the defined topic.

Function `Firebase.FCM.appInstanceInfo` to get the app instance info for a device. This also provides the subscribed topics info.

Function `Firebase.FCM.regisAPNsTokens` to create the registration tokens for iOS APNs tokens.

The library provides two message constructors that hold the data to construct the JSON object payload internally.

For legacy message, see https://firebase.google.com/docs/cloud-messaging/http-server-ref

For HTTPv1 message, see ttps://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages

The HTTPv1 APIs requires OAUth2.0 authentication using the Service Account credential.


The following example showed how to send FCM message.

```cpp
//Provide your Firebase project's server key to send messsage using the legacy protocols
Firebase.FCM.setServerKey(FIREBASE_FCM_SERVER_KEY);

//Construct the legacy message
FCM_HTTPv1_JSON_Message msg;

//Assign the device registration token
msg.token = DEVICE_REGISTRATION_ID_TOKEN;

//Assign the notification payload
msg.notification.body = "Notification body";
msg.notification.title = "Notification title";

FirebaseJson json;
String payload;

//Assign the data payload
//all data key-values should be in string
json.add("humidity", "70");
json.toString(payload);
msg.data = payload.c_str();

//Send message
if (Firebase.FCM.send(&fbdo, &msg))
{
   erial.println("Message sent to FCM backend.");
   Serial.println(Firebase.FCM.payload(&fbdo));
}
else
{
   Serial.println("Something wrong, can't send request to FCM backend.");
   Serial.println(fbdo.errorReason());
}

```


## Firebase Cloud Firestore

This library implements a REST Client for Cloud Firestore database. The RPC APIs are not implemented in this library. 

The support functions for Cloud Firestore are export, import, create, patch, get, delete the document and list the documents and collection.

See the [Firestore examples](/examples/Firestore) for the usages.

The unsecured security rules that allows the public usage of Firestore is

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /todos/{document=**} {
      allow read, write: if true;
    }
  }
}
```

For secured Firestore usages, the security rules should include the auth field similar to this.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth.uid != null;
    }
  }
}
```



## Firebase Storage

The Firebase Storage bucket file upload, download, read its meta data and listing are supported. 

See the [Firebase Storage examples](/examples/Storage/Firebase_Storage) for the usages.


## Google Cloud Storage

The Firebase or Google Cloud Storage bucket file upload, download, read its meta data and listing are supported.

This Google Cloud Storage supported allows large files upload which upload the large file via the Firebase Storage functions is not allowable.


See the [Google Cloud Storage examples](/examples/Storage/Google_Cloud_Storage) for the usages.



## Cloud Functions for Firebase

The Cloud Functions create/deployment, update, call, delete and list are supported.

The library also support the source code deployment which you can edit the functions and deploy it using the embedded device.

See [Cloud Functions examples](/examples/Cloud_Functions) for complete usages.



### IAM Permission and API Enable


Some Firestore functions and all Cloud Functions functions requires the OAuth2.0 authentication and not allow the unauthentication and Email/password or custom token authenication access.

You may still get the error permission denined error even using OAuth2.0 authen with Service Account credentials, because the client in the Service Account does not has the owner permission.

To assign the owner permission to the client.

Go to https://console.cloud.google.com/iam-admin

Choose the project, look at the member which matches the client email in service account credentials. Edit the permission, add the role Owner under the Basic

![IAM Edit Permission 1](/media/images/IAM_Permission_Role1.png)

![IAM Edit Permission 2](/media/images/IAM_Permission_Role2.png)

![IAM Edit Permission 3](/media/images/IAM_Permission_Role3.png)


For Cloud Functions Cloud Build API must be enabled for the project. To enable Cloud Build API go to https://console.developers.google.com/apis/library/cloudbuild.googleapis.com




## Create, Edit, Serializing and Deserializing the JSON Objects


This library has built-in FirebaseJson Arduino library, the easiest JSON parser, builder and editor.

FirebaseJson usages are so simple as you read, store and update(edit) the JSON node in Firebase RTDB.

It doesn't use the recursive call to parse or deserialize complex or nested JSON objects and arrays. 

This makes the library can use with a limited memory device. 


Since you declare the FirebaseJson or FirebaseJsonArray object, use the functions `setJsonData`, `setJsonArrayData`, `add`, `set` and `remove` to build or edit the JSON/Array object and use `get` to parse the node's contents. 

Defined the relative path of the specific node to `add`, `set`, `remove` and `get` functions to add, set, remove and get its contents.


Function `FirebaseJson.setJsonData` is to deserialize the JSON string to JSON object.

In addition, function `FirebaseJson.readFrom` can be used to read the streaming JSON contents from WiFi/Ethernet Client, File and Harware Serial and serialize it as the streaming content contains valid JSON data. 


Function `FirebaseJson.add` is used to add the new node with the contents e.g. String, Number (int and double), Boolean, Array and Object to the defined node.


Function `FirebaseJson.set` is used for edit, overwrite, create new (if not exist) node with contents e.g. String, Number (int and double), Boolean, Array and Object at the defined relative path and node.


Function `FirebaseJson.get` is used for parsing or deserializee the JSON object and array. The deserialized or parsed result will keep in FirebaseJsonData object which can be casted to any type of value or variable e.g string, bool, int, float, double by using `FirebaseJsonData.to<type>`. 

The casting from FirebaseJsonData to FirebaseJson and FirebaseJsonArray objects is different, by using `FirebaseJsonData.getJSON(FirebaseJson)` and `FirebaseJsonData.getArray(FirebaseJsonArray)`.


Function `FirebaseJson.search` is used for searching the elements in JSON object and array. The search function supports criterias which can be set using FirebaseJson::SearchCriteria data.

The search function returns the number of items or elements found.

The SearchCriteria data consisted of the properties e.g. path, value, depth, endDepth, and searchAll.

The path property is the key name or path to search which can use the wildcard * for path to be any key or name at that depth and so on.

The value property can be used with or without path assignment.

The depth property is begin depth to search, default value is 0.

The endDepth property is the end depth to search, default value is -1 for unlimited end depth.

The searchAll property, when set to true, is to search all occurrences of elements found and search result stores in FirebaseJsonData object will be array of all items found. The actual full path of search can be obtained by `FirebaseJsonData.searchPath` which will be serialized array string of all paths for all elements found from search.

When searchAll property is false (default), the first occurrence will be set to the result which can be any value type.

The search path from `FirebaseJsonData.searchPath` will be string of the element or item full path.


The search result will keep in FirebaseJsonData object which later can cast to any type (always be an array in case of searchAll property is true) by using `FirebaseJsonData.to<type>`.


Function `FirebaseJson.remove` is used to remove the node and all its children's contents at the defined relative path and node. 


Function `FirebaseJson.toString` is used for serializeing the JSON object to writable objects e.g. char array, Arduino String, C/C++ string, WiFi/Ethernet Client and Hardware/Software Serial.


Function `FirebaseJson.serializedBufferLength` is used for calculating the serialized buffer size that required for reserved buffer in serialization.


Function `FirebaseJson.responseCode` is used to get the http code response header while read the WiFi/Ethernet Client using `FirebaseJson.toString`.


Functions `FirebaseJson.iteratorBegin`, `FirebaseJson.iteratorGet` and `FirebaseJson.iteratorEnd` are used to parse all JSON object contents as a list which can be iterated with index.


Function `FirebaseJson.clear` is used to clear JSON object contents.


Function `FirebaseJson.setFloatDigits` is for float number precision when serialized to string.


Function `FirebaseJson.setDoubleDigits` is for double number precision when serialized to string.


Function `FirebaseJsonArray.add` is used for adding the new contents e.g. String, Number (int and double), Boolean, Array and Object to JSON array.


Function `FirebaseJsonArray.set` is for edit, overwrite, create new (if not exist) contents e.g. String, Number (int and double), Boolean, Array and Object at the defined relative path or defined index of JSON array.


Function `FirebaseJsonArray.get` and `FirebaseJsonArray.search`work in the same way as FirebaseJson objects


Function `FirebaseJsonArray.remove` is used to remove the array's contents at the defined relative path or defined index of JSON array.


Function `FirebaseJsonArray.toString` is used for serializeing the JSON array object to writable objects e.g. char array, Arduino String, C/C++ string, WiFi/Ethernet Client and Hardware/Software Serial.


Function `FirebaseJsonArray.serializedBufferLength` is used for calculating the serialized buffer size that required for reserved buffer in serialization.


Function `FirebaseJsonArray.responseCode` is used to get the http code response header while read the WiFi/Ethernet Client using `FirebaseJson.toString`.


Function `FirebaseJsonArray.clear` is used to clear JSON array object contents.


Function `FirebaseJsonArray.setFloatDigits` is for float number precision when serialized to string.


Function `FirebaseJsonArray.setDoubleDigits` is for double number precision when serialized to string.



The following example shows how to use FirebaseJson.

```cpp
//Declare FirebaseJson object (global or local)
FirebaseJson json;

//Add name with value Living Room to JSON object
json.add("name", "Living Room");

//Add temp1 with value 120 and temp1 with 40 to JSON object
//Note: temp2 is not the child of temp1 as in previous version.
json.add("temp1", 120).add("temp2", 40);

//Add nested child contents directly
json.set("unit/temp1", "Farenheit");
json.set("unit/temp2", "Celcius");

//Deserialize to serial with prettify option
json.toString(Serial, true);
Serial.println();
Serial.println();

/**
This is the result of the above code

{
    "name": "Living Room",
    "temp1": 120,
    "temp2": 40,
    "unit": {
        "temp1": "Farenheit",
        "temp2": "Celcius"
    }
}
*/

//To set array to the above JSON using FirebaseJson directly
//Set (add) array indexes 0,1,2,5,7 under temp1, the original value will be replaced with new one.
json.set("temp1/[0]", 47);
json.set("temp1/[1]", 28);
json.set("temp1/[2]", 34);
json.set("temp1/[5]", 23); //null will be created at array index 3,4 due to it's not yet assigned
json.set("temp1/[7]", 25); //null will be created at array index 6

//Print out as prettify string
json.toString(Serial, true);
Serial.println();
Serial.println();

/**
The result of the above code

{
    "name": "Living Room",
    "temp1": [
        47,
        28,
        34,
        null,
        null,
         23,
        null,
        25
     ],
    "temp2": 40,
    "unit": {
        "temp1": "Farenheit",
        "temp2": "Celcius"
    }
 }
*/

//Try to remove temp1 array at index 1
json.remove("temp1/[1]");

//Try to remove temp2
json.remove("temp2");

//Print out as prettify string
json.toString(Serial, true);
Serial.println();
Serial.println();

/**
The result of the above code

{
    "name": "Living Room",
    "temp1": [
         47,
         34,
         null,
         null,
         23,
         null,
         25
    ],
    "unit": {
        "temp1": "Farenheit",
        "temp2": "Celcius"
    }
}
*/

//Now parse/read the contents from specific node unit/temp2
//FirebaseJsonData is required to keep the parse results which can be accessed later
FirebaseJsonData result;

json.get(result, "unit/temp2");

if (result.success)
{
  //Print type of parsed data e.g string, int, double, bool, object, array, null and undefined
  Serial.println(result.type);
  //Print its content e.g.string, int, double, bool whereas object, array and null also can access as string
  Serial.println(result.to<String>());
  //Serial.println(result.to<int>());
  //Serial.println(result.to<bool>());
  //Serial.println(result.to<float>());
  //Serial.println(result.to<double>());
}

//The above code will show
/**
string
Celcius
*/

//To get the array temp from FirebaseJson

json.get(result, "temp1");

//Prepare FirebaseJsonArray to take the array from FirebaseJson
FirebaseJsonArray arr;

//Get array data
result.get<FirebaseJsonArray>(arr);

//Call get with FirebaseJsonData to parse the array at defined index i
for (size_t i = 0; i < arr.size(); i++)
{
  //result now used as temporary object to get the parse results
  arr.get(result, i);

  //Print its value
  Serial.print("Array index: ");
  Serial.print(i);
  Serial.print(", type: ");
  Serial.print(result.type);
  Serial.print(", value: ");
  Serial.println(result.to<String>());
}

/**
The result of above code
Array index: 0, type: int, value: 47
Array index: 1, type: int, value: 34
Array index: 2, type: null, value: null
Array index: 3, type: null, value: null
Array index: 4, type: int, value: 23
Array index: 5, type: null, value: null
Array index: 6, type: int, value: 25
*/
 

```


The following example shows how to use FirebaseJsonArray.

```cpp
//Declare FirebaseJsonArray object (global or local)
FirebaseJsonArray arr;

//Add some data
arr.add("banana");
arr.add("mango");
arr.add("coconut");


//Change the array contents
arr.set("[1]/food", "salad");
arr.set("[1]/sweet", "cake");
arr.set("[1]/appetizer", "snack");
arr.set("[2]", "apple"); // or arr.set(2, "apple");
arr.set("[4]/[0]/[1]/amount", 20);

//Print out array as prettify string
arr.toString(Serial, true);
Serial.println();
Serial.println();

/**
This is the result of the above code

[
    "banana",
    {
        "food": "salad",
        "sweet": "cake",
        "appetizer": "snack"
    },
    "apple",
    null,
    [
        [
            null,
            {
                "amount": 20
            }
        ]
    ]
]
*/

//Remove array content at /4/0/1/amount
arr.remove("[4]/[0]/[1]/amount");

//Print out as prettify string
arr.toString(Serial, true);
Serial.println();
Serial.println();
/**
The result of the above code

[
    "banana",
    {
        "food": "salad",
        "sweet": "cake",
        "appetizer": "snack"
    },
    "apple",
    null,
    [
        [
            null
        ]
    ]
]

*/

//Now parse/read the array contents at some index

FirebaseJsonData result;

arr.get(result, "[1]/food");

if(result.success)
{
  //Type of parsed data
  Serial.println(result.type);
  //Its value
  Serial.println(result.to<String>());
  //Serial.println(result.to<int>());
  //Serial.println(result.to<bool>());
  //Serial.println(result.to<float>());
  //Serial.println(result.to<double>());

}

//The above code will show
/**
string
salad
*/


//To get the JSON object at array index 1 from FirebaseJsonArray
arr.get(result, "[1]");// or arr.get(result, 1);

//Prepare FirebaseJson to take the JSON object from FirebaseJsonArray
FirebaseJson json;

//Get FirebaseJson data
result.get<FirebaseJson>(json);

//Parse the JSON object as list
//Get the number of items
size_t len = json.iteratorBegin();
FirebaseJson::IteratorValue value;
for (size_t i = 0; i < len; i++)
{
    value = json.valueAt(i);
    Serial.printf("%d, Type: %s, Name: %s, Value: %s\n", i, value.type == FirebaseJson::JSON_OBJECT ? "object" : "array", value.key.c_str(), value.value.c_str());
}

//Clear all list to free memory
json.iteratorEnd();


/**
The result of the above code

0, Type: object, Key: food, Value: salad
1, Type: object, Key: sweet, Value: cake
2, Type: object, Key: appetizer, Value: snack

*/


```





## License

The MIT License (MIT)

Copyright (C) 2021 K. Suwatchai (Mobizt)


Permission is hereby granted, free of charge, to any person returning a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

