# Testing Sketch
```c
#include <WiFly.h>

WiFlyClient client("build-light.com", 80);

unsigned long mark1;
unsigned long mark2;
unsigned long mark3;
unsigned long mark4;

void die() { for(;;) { ; } }

void setup()
{
  mark1 = millis();
  Serial.begin(115200);
  
  WiFly.begin();
  if (!WiFly.join("ssid", "phrase")) 
  {
    Serial.println("ERR: Could not connect to network");
    die();
  }
  
  Serial.println("  *: Connected to WiFi");
  Serial.print("  *: IP: ");
  Serial.println(WiFly.ip());
  
  mark2 = millis();
  if(client.connect())
  {
    mark3 = millis();
    Serial.println(" =>: GET /lights/1 HTTP/1.1");
    client.println("GET /lights/1 HTTP/1.1");
    client.println();
  } else {
    Serial.println("ERR: Could not connect to host");
    die();
  }
  
  mark4 = millis();
}

void loop()
{
  if(client.available())
  {
    Serial.print((char) client.read());
  }
  
  if(!client.connected()) 
  {
    Serial.println("  *: Disconnected");
    client.stop();
    
    unsigned long stopped = millis();
    
    Serial.print("  *: Total time: ");
    Serial.print(stopped - mark1);
    Serial.println("ms");
    
    Serial.print("  *: Time to connect to host: ");
    Serial.print(mark3 - mark2);
    Serial.println("ms");

    Serial.print("  *: Time to make GET request: ");
    Serial.print(mark4 - mark3);
    Serial.println("ms");
    
    die();
  } 
}
```

# Exisitng Version
```
  *: Connected to WiFi
  *: IP: 192.168.1.134
 =>: GET /lights/1 HTTP/1.1
HTTP/1.1 400 Bad Request
Server: nginx
Date: Sun, 19 Feb 2012 16:52:20 GMT
Content-Type: text/html
Content-Length: 166
Connection: close

<html>
<head><title>400 Bad Request</title></head>
<body bgcolor="white">
<center><h1>400 Bad Request</h1></center>
<hr><center>nginx</center>
</body>
</html>
  *: Disconnected
  *: Total time: 8634ms
  *: Time to connect to host: 667ms
  *: Time to make GET request: 3ms
```
