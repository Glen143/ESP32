#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "Pogi";
const char* password = "12348765";
const int ledPins[] = {2, 4, 17, 18, 21};  

WebServer server(80); 

void handleRoot()
{
  String html = "<!DOCTYPE html><html><head><title>ESP32 LED Control</title>";
  html += "<style>";
  html += "body { font-family: Arial, sans-serif; background-color: #000000; color: #ffffff; text-align: center; margin: 0; padding: 0;}";
  html += "h1 { color: #ffffff; }";
  html += "button { font-size: 18px; padding: 10px 20px; margin: 10px; border: none; border-radius: 5px; background-color: #ffffff; color: black; cursor: pointer; transition: background-color 0.3s ease;}";
  html += "button:hover { background-color: #f0f0f0; }";  // Light gray hover effect
  html += "</style>";
  html += "</head><body>";
  html += "<h1>ESP32 Web Server - LED Control</h1>";
  html += "<button onclick=\"window.location.href='/toggle/on'\">Turn All LEDs ON</button><br>";
  html += "<button onclick=\"window.location.href='/toggle/off'\">Turn All LEDs OFF</button><br>";
  html += "<button onclick=\"window.location.href='/toggle/alternate'\">Button2 LEDs ON/OFF</button><br>";
  html += "<button onclick=\"window.location.href='/toggle/sequence'\">Button3 ON in Sequence</button><br>";
  html += "</body></html>";

  server.send(200, "text/html", html);  
}
void LEDsOn() {
  for (int i = 0; i < 5; i++) {
    digitalWrite(ledPins[i], HIGH);
  }
  server.send(200);
}

void LEDsOff() {
  for (int i = 0; i < 5; i++) {
    digitalWrite(ledPins[i], LOW);
  }
  server.send(200);
}

void button2() 
{
  digitalWrite(ledPins[0], HIGH);  
  delay(500);  
  digitalWrite(ledPins[0], LOW);  
  delay(100); 
  
  digitalWrite(ledPins[2], HIGH); 
  delay(500);  
  digitalWrite(ledPins[2], LOW);  
  delay(100); 
  
  digitalWrite(ledPins[4], HIGH);  
  delay(500);  
  digitalWrite(ledPins[4], LOW); 
  delay(100);  
  
  digitalWrite(ledPins[1], HIGH); 
  delay(500);  
  digitalWrite(ledPins[1], LOW);  
  delay(100);  
  
  digitalWrite(ledPins[3], HIGH); 
  delay(500);  
  digitalWrite(ledPins[3], LOW);  
  delay(100);  

  server.send(200);
}

void button3() {
  digitalWrite(ledPins[2], HIGH);  
  delay(500); 
  digitalWrite(ledPins[1], HIGH);  
  digitalWrite(ledPins[3], HIGH);  
  delay(500); 
  digitalWrite(ledPins[0], HIGH); 
  digitalWrite(ledPins[4], HIGH);  
  delay(500); 

  //reverse
  digitalWrite(ledPins[0], LOW); 
  digitalWrite(ledPins[4], LOW); 
  delay(500);
  digitalWrite(ledPins[1], LOW);  
  digitalWrite(ledPins[3], LOW);  
  delay(500); 
  digitalWrite(ledPins[2], LOW);  
  
  server.send(200);
}


void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  // Print the IP address
  Serial.println(WiFi.localIP());

  for (int i = 0; i < 5; i++) {
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW);  
  }

  server.on("/", handleRoot);
  server.on("/toggle/on", HTTP_GET, LEDsOn);
  server.on("/toggle/off", HTTP_GET, LEDsOff);
  server.on("/toggle/alternate", HTTP_GET, button2);
   server.on("/toggle/sequence", HTTP_GET, button3);
  server.begin();
  Serial.println("HTTP server started");
}

void loop() 
{
  server.handleClient();
}
