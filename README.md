# NodoIoT_RFM69
# TFG: Implementación de nodo IoT para la captura y monitorización de datos de una estación meteorológica Davis
## Equipo:
### Hardware
Sensor Transmitter Davis 6331/2  
Sensor de temperatura y humedad Davis  
Placa Adafruit Adalogger FeatherWing  
PLaca Adafruit Radio FeatherWing  
Placa Adafruit Feather ESP32 V2  
Antena ...  
### Software  
Arduino IDE versión 2.2.1  
Placa Esp32 de espressif version 2.0.4  
## Conexiones pines:

![Image text](https://github.com/DaniAntolin/NodoIoT_RFM69/blob/V1.0/imagenes/imagenes/adafruit_products_Adafruit_ESP32_Feather_V2_Pinout.png)

### Placa Adafruit Feather ESP32 V2
SCK -> Pin 5  
MISO -> Pin 21  
MOSI -> Pin 19   
CS -> Pin 32  
IRQ -> Pin 27  
### Adafruit Radio FeatherWing

## Desarrollo.
Para el desarroyo de este proyecto se divirá en tres partes que de forma independiente se podrá testear las diferentes fases del proyecto: Recibir, Guardar y Enviar.  
Estas tres fases serán las implementadas en un archivo .ino que posteriormente serán junatadas en un archivo .ino final.  
### Recibir.
Esta fase tiene como objetivo recibir paquetes de las distintas estaciones con los datos obtenidos en sus sensores.  
El Sensor Transmitter Davis tiene en su interior 4 switches que dependiendo si estan en **ON** u **OFF** representan un canal u otro.

Dependiendo del canal escogido transmitirá mas o menos lento los paquetes segun la siguiente equación:  
**(41 + ID)/16 [segundos]**  
### Guardar.
### Enviar.  




```mermaid
graph TD;
    1["initialize_radio & set_chanel(0)"]-->2("loop_start");
    2("loop_start")-->3{"packet recived?"};
    3{"packet recived?"}-- Yes -->4["packetsRecived++"];
    3{"packet recived?"}-- No -->13("end_recive_block");
    4["packetsRecived++"]-->5{"correct CRC?"};
    5{"correct CRC?"}-- Yes -->6{"correctID?"};
    5{"correct CRC?"}-- No -->7["crcErrors++"];
    6{"correctID?"}-- Yes -->9["hop_count=1"];
    6{"correctID?"}-- No -->8["IDerrors++"];
    7["crcErrors++"]-->10{"correctCRC & correctID?"};
    8["IDerrors++"]-->10{"correctCRC & correctID?"};
    9["hop_count=1"]-->10{"correctCRC & correctID?"};
    10{"correctCRC & correctID?"}-- Yes -->11["lastRxTime=millis() ./ radio.hop()"];
    10{"correctCRC & correctID?"}-- No -->12["wait same chanel"];
    11["lastRxTime=millis() ./ radio.hop()"]-->13("end_recive_block");
    12["wait same chanel"]-->13("end_recive_block");
    13("end_recive_block")-->14["recived paket on time?"];
    14{"recived paket on time?"}-- No -->15["packetsMissed++"];
    14{"recived paket on time?"}-- Yes -->20("end_loop");
    15["packetsMissed++"]-->16{"hop_count?"};
    16{"hop_count?"}-- 2-4 -->19["next_channel"];
    16{"hop_count?"}-- >4 -->17["hop_count=0"];
    16{"hop_count?"}-- 1 -->18["numResyncs++"];
    17["hop_count=0"]-->19["next_channel"];
    18["numResyncs++"]-->19["next_channel"];
    19["next_channel"]-->20("end_loop");
    20("end_loop")-->2("loop_start");
```


```mermaid
graph TD;
    A[Preprocesador] --> B[Configuración];
    B --> C[Loop];
    subgraph "Preprocesador";
    A1[#if defined(ESP32)] --> A2[#include <WiFi.h>];
    A3[#elif defined(ESP8266)] --> A4[#include <ESP8266WiFi.h>];
    end;
    subgraph "Configuración";
    B1[setup()] --> B2[Serial.begin()];
    B2 --> B3[pinMode()];
    B3 --> B4[digitalWrite()];
    B4 --> B5[ledcSetup()];
    B5 --> B6[ledcAttachPin()];
    B6 --> B7[Serial.print()];
    B7 --> B8[WiFi.begin()];
    B8 --> B9[while(WiFi.status() != WL_CONNECTED)];
    B9 --> B10[Serial.println()];
    B10 --> B11[server.begin()];
    end;
    subgraph "Loop";
    C1[loop()] --> C2[WiFiClient client = server.available()];
    C2 --> C3[if(client)];
    C3 --> C4[while(client.connected() && millis() - lastTime <= timeout)];
    C4 --> C5[if(client.available())];
    C5 --> C6[char c = client.read()];
    C6 --> C7[header += c];
    C7 --> C8[if(c == '\n')];
    C8 --> C9[if(currentLine.length() == 0)];
    C9 --> C10[client.println()];
    C10 --> C11[if (header.indexOf("GET /habon") >= 0)];
    C11 --> C12[else if (header.indexOf("GET /haboff") >= 0)];
    C12 --> C13[else if (header.indexOf("GET /sala") >= 0)];
    C13 --> C14[else if (header.indexOf("GET /comedor") >= 0)];
    C14 --> C15[else if (header.indexOf("GET /temperatura") >= 0)];
    C15 --> C16[else if (header.indexOf("GET /potenciometro") >= 0)];
    C16 --> C17[else if (header.indexOf("POST /led") >= 0)];
    end;
```

 


