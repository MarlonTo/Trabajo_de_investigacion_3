//UNIVERSIDAD DE LAS FUERZAS ARMADAS
//Investigacion de MQTT 
//Fecha Modficacion: 23/08/2020
//Creditos: Torres Marlon, Toapanta Elian, Zambrano Alejandro.

#include <ESP8266WiFi.h>  //Libreria del arduino(esp8266)
#include <PubSubClient.h>  //Libreria del PUB/SUB

//Variables de conexion al wifi
const char* ssid = "ptopb1";
const char* password = "2008P4P3lucho";
const char* mqtt_server = "186.4.224.175"; //Direccion IP del servidor del MQTT

WiFiClient espClient; //Variable para conexion wifi
PubSubClient client(espClient);  //Variable para la conexion PUB/SUB

//Variables de uso general
unsigned long lastMsg = 0;
char* estado ="";
int dato;
int anterior,actual;

//Funcion de conexion wifi
void setup_wifi() {
  
  delay(10);
  Serial.println();
  Serial.print("Conectando a");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi conectado");
  Serial.println("Direccion IP: ");
  Serial.println(WiFi.localIP());
}

//Funcion para reservir datos del publicador
void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Mensaje recibido [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
  //Suscripcion al topic "focoEstado"
  if ((String)topic == "focoEstado"){
    if ((char)payload[0] == '1') {
      digitalWrite(D3, HIGH); //Led encendido
    } else {
      digitalWrite(D3, LOW);  // Led apgado
    }
  }
}

//Funcion de conexion/reconexion al servidor MQTT 
void reconnect() {
  
  while (!client.connected()) {
    Serial.print("Conectando al servidor MQTT ...");
  
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
   
    if (client.connect(clientId.c_str(),"admin",password)) {
      Serial.println("conectar");
     
      client.subscribe("focoEstado");//  Suscripcion al topico "focoEstado" 
      
    } else {
      Serial.print("fallo, rc=");
      Serial.print(client.state());
      Serial.println(" reintentando en 5 segundos");
      delay(5000);
    }
  }
}

void setup() {
  pinMode(D3, OUTPUT);     //Pin del foco
   pinMode(D2, INPUT);     //Pin del reedswitch
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);  //Conexion al servidor MQTT
  client.setCallback(callback);
  anterior=LOW;
  actual=LOW;
}

void loop() {
   
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
   actual=digitalRead(D2);
   if (actual != anterior){
     if (actual == HIGH)
       client.publish("puertaAlarma","Cerrada"); //Publicacion puerta cerrada
      else
        client.publish("puertaAlarma","Abierta"); //Publicacion puerta abierta 
      anterior=actual;       
    }
  }
}
