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
graph TD
  subgraph cluster_Board
    A[ESP32/ESP8266] -->|WiFi Library| B(WiFi)
    style A fill:#78c7c7,stroke:#006666,stroke-width:2px;
    style B fill:#78c7c7,stroke:#006666,stroke-width:2px;
  end

  subgraph cluster_Server
    C(Server) -->|Handling Requests| E{Request Handling}
    style C fill:#78c7c7,stroke:#006666,stroke-width:2px;
    style E fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
  end

  subgraph cluster_Client
    D(Client) -->|HTTP Requests| C
    style D fill:#78c7c7,stroke:#006666,stroke-width:2px;
  end

  subgraph cluster_GPIO
    E -->|GPIO Control| F[LED Control]
    E -->|Analog Read| G[Potentiometer]
    E -->|Temperature Read| H[Temperature Sensor]
    F -->|PWM Control| I[PWM Pin]
    G -->|Analog Input| J[Potentiometer Pin]
    H -->|Temperature Output| K[Temperature Pin]
    style F fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
    style G fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
    style H fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
    style I fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
    style J fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
    style K fill:#c7e2ff,stroke:#3366cc,stroke-width:2px;
  end

  style subgraph fill:#e6f7ff,stroke:#3366cc,stroke-width:2px;

  subgraph cluster_Code
    L[setup()]
    M[loop()]
    L -->|Initial Setup| A
    M -->|Handling Requests| E
    M -->|GPIO Control| F
    M -->|Analog Read| G
    M -->|Temperature Read| H
  end

  style L fill:#ffd699,stroke:#cc6600,stroke-width:2px;
  style M fill:#ffd699,stroke:#cc6600,stroke-width:2px;

  L -->|LED Control| F
  L -->|Potentiometer Reading| G
  L -->|Temperature Reading| H
```

 


