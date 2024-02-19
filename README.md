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
  A[ESP32/ESP8266] -->|WiFi Library| B(WiFi);
  C(Setup) -->|Initialize WiFi| D(Initialize Server);
  E(Loop) -->|Handle Clients| F(Handle Requests);
  G(GPIO Control) -->|LED Control| H(PWM Control);
  G -->|Analog Read| I(Potentiometer);
  G -->|Temperature Read| J(Temperature Sensor);
  G -->|Proximity Sensor| K(Proximity Sensor);
  D -->|Handle HTTP Requests| F;
  F -->|Generate HTTP Response| G;
  F -->|Handle GET /habon| G;
  F -->|Handle GET /haboff| G;
  F -->|Handle GET /sala| G;
  F -->|Handle GET /comedor| G;
  F -->|Handle GET /temperatura| G;
  F -->|Handle GET /potenciometro| G;
  F -->|Handle GET /verdeON| G;
  F -->|Handle GET /verdeOFF| G;
  F -->|Handle POST /led| G;

  style A,B,C,D,E,F,G,H,I,J,K fill:#ffffff,stroke:#000000,stroke-width:2px;
  style arrowhead, link arrowhead:open;

<!doctype html>
<html>
<head>
<meta charset='UTF-8'><meta name='viewport' content='width=device-width initial-scale=1'>

<link href='https://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,700,400&subset=latin,latin-ext' rel='stylesheet' type='text/css' /><style type='text/css'>html {overflow-x: initial !important;}:root { --mermaid-theme: default; --mermaid-sequence-numbers: off; --mermaid-flowchart-curve: linear; --mermaid--gantt-left-padding: 75; --sequence-theme: simple; }
.tooltip[data-v-70836592] { cursor: pointer; position: relative; display: block; width: 100%; text-align: center; z-index: 10; }
.tooltip span[data-v-70836592] { border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: currentcolor; }
.tooltip[data-v-70836592]::after { display: none; content: attr(data-tooltip); background: rgb(232, 233, 233); max-width: 500px; width: 200px; position: absolute; left: -200px; right: 0px; margin: auto; opacity: 0; height: auto; font-size: 14px; padding: 10px; border-radius: 4px; color: rgb(17, 17, 17); text-align: left; }
.tooltip.bottom[data-v-70836592]::after { top: 80%; transition: opacity 0.3s 0.3s, top 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) 0.3s; }
.tooltip.bottom[data-v-70836592]:hover::after { display: block; top: 130%; opacity: 1; }
.lifeline .line[data-v-6efc771f] { background-size: 1px 20px; }
.zenuml .interaction { border: 0px dashed transparent; }
.zenuml .interaction.sync { border-right-width: 7px; }
.zenuml .interaction.inited-from-occurrence, .zenuml .interaction.self-invocation { border-left-width: 7px; }
.zenuml .interaction.return { border-left-width: 7px; border-right-width: 7px; }
.zenuml .interaction.return-to-start { border-left-width: 0px; }
.zenuml .interaction:hover { cursor: pointer; }
.zenuml .message { position: relative; }
.zenuml .message > .name { text-align: center; }
.zenuml .interaction.right-to-left > .occurrence { left: -14px; }
.zenuml .interaction.self > .occurrence { left: -8px; margin-top: -10px; }
.zenuml .fragment { border-width: 1px; margin: 8px 0px 0px; padding-bottom: 10px; }
.zenuml .sequence-diagram * { box-sizing: inherit; }
.zenuml .sequence-diagram { line-height: normal; }
.zenuml .participant { border-width: 2px; padding: 8px 4px; min-width: 88px; max-width: 250px; text-align: center; pointer-events: all; }
.no-fill svg.arrow polyline[data-v-ca07199a] { fill: none !important; }
.async > .message > .point > svg.arrow > polyline[data-v-ca07199a] { fill: none; }
.right-to-left.point[data-v-ca07199a] { left: 0px; right: auto; }
.right-to-left.point > svg > polyline.right[data-v-ca07199a] { display: none; }
.right-to-left.point > svg > polyline.left[data-v-ca07199a] { display: inline; }
.point > svg > polyline.left[data-v-ca07199a] { display: none; }
:root { --color-bg-base: #fff; --color-text-base: #000; --color-text-secondary: #333; --color-border-base: #000; }
.theme-default { --color-text-base: #222; --color-border-base: #666; --color-message-arrow: #000; --color-bg-occurrence: #dedede; }
.theme-mermaid { --color-bg-base: #fff; --color-text-base: #222; --color-border-base: #666; --color-bg-occurrence: #dedede; }
.theme-mermaid .footer { visibility: hidden; }
.theme-darcula { --color-bg-canvas: #ffffff; --color-bg-frame: #2b2b2b; --color-border-frame: #cccccc; --color-bg-title: #2b2b2b; --color-text-title: #f8f8f2; --color-bg-participant: #44475a; --color-text-participant: #f8f8f2; --color-border-participant: #cccccc; --color-text-message: #ffb86c; --color-message-arrow: #cccccc; --color-bg-message-hover: #a6d2ff; --color-text-message-hover: #174ad4; --color-text-comment: #666666; --color-bg-fragment-header: #44475a; --color-text-fragment: #8be9fd; --color-border-fragment: #cccccc; --color-bg-occurrence: #44475a; --color-border-occurrence: #555555; --color-text-link: #a6d2ff; --color-text-control: #e2ba88; }
.theme-sky { --color-bg-canvas: #ffffff; --color-bg-frame: #ffffff; --color-border-frame: #cccccc; --color-bg-title: #deecfb; --color-text-title: #032c72; --color-bg-participant: #deecfb; --color-text-participant: #032c72; --color-border-participant: #032c72; --color-text-message: #032c72; --color-message-arrow: #032c72; --color-bg-message-hover: #a6d2ff; --color-text-message-hover: #174ad4; --color-text-comment: #666666; --color-bg-fragment-header: #f0f0f0; --color-text-fragment: #032c72; --color-border-fragment: #032c72; --color-bg-occurrence: #deecfb; --color-border-occurrence: #555555; --color-text-link: #a6d2ff; --color-text-control: #a6d2ff; }
.theme-idle-afternoon { --color-bg-canvas: #d8dad9; --color-bg-frame: #d8dad9; --color-border-frame: #324939; --color-bg-title: #d8dad9; --color-text-title: #182e27; --color-bg-participant: #f3f5f7; --color-text-participant: #182e27; --color-border-participant: #182e27; --color-text-message: #030809; --color-message-arrow: #324939; --color-bg-message-hover: #aea690; --color-text-message-hover: #000000; --color-text-comment: #030809; --color-bg-fragment-header: #f0f0f0; --color-text-fragment: #182e27; --color-border-fragment: #344337; --color-bg-occurrence: #f3f5f7; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #97a49b; }
.theme-coles { --color-bg-canvas: #ffffff; --color-bg-frame: #ffde00; --color-border-frame: #ee141f; --color-bg-title: #ffffff; --color-text-title: #182e27; --color-bg-participant: #f3f5f7; --color-text-participant: #000000; --color-border-participant: #344337; --color-text-message: #000000; --color-message-arrow: #000000; --color-bg-message-hover: #ee141f; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #ee141f; --color-text-fragment: #ffffff; --color-border-fragment: #ee141f; --color-bg-occurrence: #f3f5f7; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #97a49b; }
.theme-coles .footer { background-color: rgb(255, 255, 255); }
.theme-woolworths { --color-bg-canvas: #e6eaf3; --color-bg-frame: #e6eaf3; --color-border-frame: #049e50; --color-bg-title: #ffffff; --color-text-title: #12522f; --color-bg-participant: #aacb51; --color-text-participant: #000000; --color-border-participant: #344337; --color-text-message: #12522f; --color-message-arrow: #049e50; --color-bg-message-hover: #fffefb; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #aacb51; --color-text-fragment: #12522f; --color-border-fragment: #049e50; --color-bg-occurrence: #aacb51; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #335c80; }
.theme-anz { --color-bg-canvas: #ffffff; --color-bg-frame: #ffffff; --color-border-frame: #089fd8; --color-bg-title: #ffffff; --color-text-title: #006e9c; --color-bg-participant: #fcfdf8; --color-text-participant: #00abd8; --color-border-participant: #00abd8; --color-text-message: #42a4e0; --color-message-arrow: #089fd8; --color-bg-message-hover: #006e9c; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #42a4e0; --color-text-fragment: #006e9c; --color-border-fragment: #089fd8; --color-bg-occurrence: #fcfdf8; --color-border-occurrence: #006e9c; --color-text-link: #c9d8e7; --color-text-control: #335c80; }
.theme-anz .fragment .header { color: rgb(255, 255, 255); }
.theme-nab { --color-bg-canvas: #f2f4f6; --color-bg-participant: #c30000; --color-text-participant: #ffffff; --color-border-participant: #c30000; --color-text-message: #c30000; --color-bg-fragment-header: #c30000; --color-text-fragment-header: #ffffff; }
.theme-google { --color-white: #ffffff; --color-red-google: #db4437; --color-blue-google: #4285f4; --color-green-google: #0f9d58; --color-yellow-google: #f4b400; --color-bg-canvas: var(--color-white); --color-bg-frame: #f2f4f6; --color-border-frame: #4285f4; --color-bg-title: #4285f4; --color-text-title: #ffffff; --color-bg-participant: var(--color-red-google); --color-text-participant: #ffffff; --color-border-participant: #4285f4; --color-text-message: var(--color-red-google); --color-message-arrow: var(--color-red-google); --color-bg-message-hover: #fffefb; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: var(--color-green-google); --color-text-fragment: #ffffff; --color-border-fragment: #4285f4; --color-bg-occurrence: #f4b400; --color-border-occurrence: #4285f4; --color-text-link: #c9d8e7; --color-text-control: #335c80; }
*, ::before, ::after { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
::-webkit-backdrop { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
::backdrop { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
.container { width: 100%; }
@media (min-width: 640px) {
  .container { max-width: 640px; }
}
@media (min-width: 768px) {
  .container { max-width: 768px; }
}
@media (min-width: 1024px) {
  .container { max-width: 1024px; }
}
@media (min-width: 1280px) {
  .container { max-width: 1280px; }
}
@media (min-width: 1536px) {
  .container { max-width: 1536px; }
}
.zenuml .sr-only { position: absolute; width: 1px; height: 1px; padding: 0px; margin: -1px; overflow: hidden; clip: rect(0px, 0px, 0px, 0px); white-space: nowrap; border-width: 0px; }
.zenuml .pointer-events-none { pointer-events: none; }
.zenuml .pointer-events-auto { pointer-events: auto; }
.zenuml .invisible { visibility: hidden; }
.zenuml .fixed { position: fixed; }
.zenuml .absolute { position: absolute; }
.zenuml .relative { position: relative; }
.zenuml .sticky { position: sticky; }
.zenuml .inset-0 { inset: 0px; }
.zenuml .bottom-1 { bottom: 0.25rem; }
.zenuml .left-1 { left: 0.25rem; }
.zenuml .right-1 { right: 0.25rem; }
.zenuml .top-0 { top: 0px; }
.zenuml .top-8 { top: 2rem; }
.zenuml .left-1\/2 { left: 50%; }
.zenuml .right-0 { right: 0px; }
.zenuml .right-\[100\%\] { right: 100%; }
.zenuml .left-full { left: 100%; }
.zenuml .z-40 { z-index: 40; }
.zenuml .z-10 { z-index: 10; }
.zenuml .col-span-2 { grid-column-start: span 2; grid-column-end: span 2; }
.zenuml .col-span-1 { grid-column-start: span 1; grid-column-end: span 1; }
.zenuml .col-span-4 { grid-column-start: span 4; grid-column-end: span 4; }
.zenuml .float-right { float: right; }
.zenuml .m-1 { margin: 0.25rem; }
.zenuml .m-2 { margin: 0.5rem; }
.zenuml .m-auto { margin: auto; }
.zenuml .mx-auto { margin-left: auto; margin-right: auto; }
.zenuml .mx-2 { margin-left: 0.5rem; margin-right: 0.5rem; }
.zenuml .-my-px { margin-top: -1px; margin-bottom: -1px; }
.zenuml .ml-3 { margin-left: 0.75rem; }
.zenuml .mr-1 { margin-right: 0.25rem; }
.zenuml .ml-4 { margin-left: 1rem; }
.zenuml .mt-3 { margin-top: 0.75rem; }
.zenuml .mt-4 { margin-top: 1rem; }
.zenuml .mb-4 { margin-bottom: 1rem; }
.zenuml .mt-8 { margin-top: 2rem; }
.zenuml .-mt-12 { margin-top: -3rem; }
.zenuml .mt-1 { margin-top: 0.25rem; }
.zenuml .mb-0 { margin-bottom: 0px; }
.zenuml .mt-2 { margin-top: 0.5rem; }
.zenuml .box-border { box-sizing: border-box; }
.zenuml .block { display: block; }
.zenuml .inline-block { display: inline-block; }
.zenuml .inline { display: inline; }
.zenuml .flex { display: flex; }
.zenuml .inline-flex { display: inline-flex; }
.zenuml .table { display: table; }
.zenuml .grid { display: grid; }
.zenuml .contents { display: contents; }
.zenuml .hidden { display: none; }
.zenuml .h-10 { height: 2.5rem; }
.zenuml .h-5 { height: 1.25rem; }
.zenuml .h-0 { height: 0px; }
.zenuml .h-screen { height: 100vh; }
.zenuml .h-full { height: 100%; }
.zenuml .h-4 { height: 1rem; }
.zenuml .h-6 { height: 1.5rem; }
.zenuml .h-12 { height: 3rem; }
.zenuml .h-8 { height: 2rem; }
.zenuml .h-14 { height: 3.5rem; }
.zenuml .h-3 { height: 0.75rem; }
.zenuml .min-h-screen { min-height: 100vh; }
.zenuml .min-h-\[1em\] { min-height: 1em; }
.zenuml .w-full { width: 100%; }
.zenuml .w-96 { width: 24rem; }
.zenuml .w-28 { width: 7rem; }
.zenuml .w-4 { width: 1rem; }
.zenuml .w-6 { width: 1.5rem; }
.zenuml .w-8 { width: 2rem; }
.zenuml .w-11\/12 { width: 91.666667%; }
.zenuml .w-px { width: 1px; }
.zenuml .w-3 { width: 0.75rem; }
.zenuml .min-w-\[100px\] { min-width: 100px; }
.zenuml .max-w-full { max-width: 100%; }
.zenuml .max-w-7xl { max-width: 80rem; }
.zenuml .flex-shrink-0, .zenuml .shrink-0 { flex-shrink: 0; }
.zenuml .flex-grow, .zenuml .grow { flex-grow: 1; }
.zenuml .origin-top-left { transform-origin: left top; }
.zenuml .-translate-x-1\/2 { --tw-translate-x: -50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-full { --tw-translate-y: -100%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-8 { --tw-translate-y: -2rem; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-x-full { --tw-translate-x: -100%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-1\/2 { --tw-translate-y: -50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .translate-y-1\/2 { --tw-translate-y: 50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .transform { transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .cursor-pointer { cursor: pointer; }
.zenuml .select-none { -webkit-user-select: none; }
.zenuml .resize { resize: both; }
.zenuml .grid-cols-6 { grid-template-columns: repeat(6, minmax(0px, 1fr)); }
.zenuml .grid-cols-4 { grid-template-columns: repeat(4, minmax(0px, 1fr)); }
.zenuml .grid-cols-1 { grid-template-columns: repeat(1, minmax(0px, 1fr)); }
.zenuml .flex-row-reverse { flex-direction: row-reverse; }
.zenuml .flex-col { flex-direction: column; }
.zenuml .flex-nowrap { flex-wrap: nowrap; }
.zenuml .items-start { align-items: flex-start; }
.zenuml .items-end { align-items: flex-end; }
.zenuml .items-center { align-items: center; }
.zenuml .justify-center { justify-content: center; }
.zenuml .justify-between { justify-content: space-between; }
.zenuml .justify-around { justify-content: space-around; }
.zenuml .gap-5 { row-gap: 1.25rem; column-gap: 1.25rem; }
.zenuml .overflow-hidden { overflow: hidden; }
.zenuml .overflow-visible { overflow: visible; }
.zenuml .overflow-y-auto { overflow-y: auto; }
.zenuml .whitespace-normal { white-space: normal; }
.zenuml .whitespace-nowrap { white-space: nowrap; }
.zenuml .rounded { border-radius: 0.25rem; }
.zenuml .rounded-sm { border-radius: 0.125rem; }
.zenuml .rounded-md { border-radius: 0.375rem; }
.zenuml .rounded-lg { border-radius: 0.5rem; }
.zenuml .rounded-t { border-top-left-radius: 0.25rem; border-top-right-radius: 0.25rem; }
.zenuml .rounded-t-md { border-top-left-radius: 0.375rem; border-top-right-radius: 0.375rem; }
.zenuml .rounded-b-md { border-bottom-right-radius: 0.375rem; border-bottom-left-radius: 0.375rem; }
.zenuml .border-2 { border-width: 2px; }
.zenuml .border { border-width: 1px; }
.zenuml .border-b-2 { border-bottom-width: 2px; }
.zenuml .border-b { border-bottom-width: 1px; }
.zenuml .border-t { border-top-width: 1px; }
.zenuml .border-r { border-right-width: 1px; }
.zenuml .border-solid { border-style: solid; }
.zenuml .border-dashed { border-style: dashed; }
.zenuml .border-red-900 { --tw-border-opacity: 1; border-color: rgb(127 29 29 / var(--tw-border-opacity)); }
.zenuml .border-skin-frame { border-color: var(--color-border-frame, var(--color-border-base, #000)); }
.zenuml .border-gray-200 { --tw-border-opacity: 1; border-color: rgb(229 231 235 / var(--tw-border-opacity)); }
.zenuml .border-skin-participant { border-color: var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-fragment { border-color: var(--color-border-fragment, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-message-arrow { border-color: var(--color-message-arrow, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-occurrence { border-color: var(--color-border-occurrence, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .bg-gray-50 { --tw-bg-opacity: 1; background-color: rgb(249 250 251 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-canvas { background-color: var(--color-bg-canvas, var(--color-bg-base, #fff)); }
.zenuml .bg-skin-frame { background-color: var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff))); }
.zenuml .bg-skin-title { background-color: var(--color-bg-title, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff)))); }
.zenuml .bg-skin-base { background-color: var(--color-bg-base); }
.zenuml .bg-green-200 { --tw-bg-opacity: 1; background-color: rgb(187 247 208 / var(--tw-bg-opacity)); }
.zenuml .bg-white { --tw-bg-opacity: 1; background-color: rgb(255 255 255 / var(--tw-bg-opacity)); }
.zenuml .bg-gray-500 { --tw-bg-opacity: 1; background-color: rgb(107 114 128 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-lifeline { background-color: var(--color-border-participant, var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000)))); }
.zenuml .bg-skin-participant { background-color: var(--color-bg-participant, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff)))); }
.zenuml .bg-gray-400 { --tw-bg-opacity: 1; background-color: rgb(156 163 175 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-divider { background-color: var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .bg-skin-fragment-header { background-color: var(--color-bg-fragment-header, transparent); }
.zenuml .bg-skin-occurrence { background-color: var(--color-bg-occurrence, var(--color-bg-participant, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff))))); }
.zenuml .bg-opacity-75 { --tw-bg-opacity: 0.75; }
.zenuml .fill-current { fill: currentcolor; }
.zenuml .fill-none { fill: none; }
.zenuml .stroke-current { stroke: currentcolor; }
.zenuml .stroke-2 { stroke-width: 2; }
.zenuml .object-contain { object-fit: contain; }
.zenuml .p-1 { padding: 0.25rem; }
.zenuml .p-2 { padding: 0.5rem; }
.zenuml .p-0 { padding: 0px; }
.zenuml .px-1 { padding-left: 0.25rem; padding-right: 0.25rem; }
.zenuml .px-3 { padding-left: 0.75rem; padding-right: 0.75rem; }
.zenuml .py-1 { padding-top: 0.25rem; padding-bottom: 0.25rem; }
.zenuml .px-2 { padding-left: 0.5rem; padding-right: 0.5rem; }
.zenuml .py-2 { padding-top: 0.5rem; padding-bottom: 0.5rem; }
.zenuml .px-4 { padding-left: 1rem; padding-right: 1rem; }
.zenuml .py-5 { padding-top: 1.25rem; padding-bottom: 1.25rem; }
.zenuml .px-px { padding-left: 1px; padding-right: 1px; }
.zenuml .pb-8 { padding-bottom: 2rem; }
.zenuml .pt-8 { padding-top: 2rem; }
.zenuml .pt-4 { padding-top: 1rem; }
.zenuml .pb-20 { padding-bottom: 5rem; }
.zenuml .pb-4 { padding-bottom: 1rem; }
.zenuml .pb-32 { padding-bottom: 8rem; }
.zenuml .pb-2 { padding-bottom: 0.5rem; }
.zenuml .pr-24 { padding-right: 6rem; }
.zenuml .pt-24 { padding-top: 6rem; }
.zenuml .pb-10 { padding-bottom: 2.5rem; }
.zenuml .pr-1 { padding-right: 0.25rem; }
.zenuml .text-left { text-align: left; }
.zenuml .text-center { text-align: center; }
.zenuml .align-bottom { vertical-align: bottom; }
.zenuml .text-xs { font-size: 0.75rem; line-height: 1rem; }
.zenuml .text-sm { font-size: 0.875rem; line-height: 1.25rem; }
.zenuml .text-base { font-size: 1rem; line-height: 1.5rem; }
.zenuml .text-xl { font-size: 1.25rem; line-height: 1.75rem; }
.zenuml .text-lg { font-size: 1.125rem; line-height: 1.75rem; }
.zenuml .font-bold { font-weight: 700; }
.zenuml .font-semibold { font-weight: 600; }
.zenuml .font-medium { font-weight: 500; }
.zenuml .font-thin { font-weight: 100; }
.zenuml .italic { font-style: italic; }
.zenuml .leading-6 { line-height: 1.5rem; }
.zenuml .leading-4 { line-height: 1rem; }
.zenuml .text-skin-title { color: var(--color-text-title, var(--color-text-message, var(--color-text-base, #000))); }
.zenuml .text-skin-control { color: var(--color-text-control, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-skin-link { color: var(--color-text-link, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-green-700 { --tw-text-opacity: 1; color: rgb(21 128 61 / var(--tw-text-opacity)); }
.zenuml .text-white { --tw-text-opacity: 1; color: rgb(255 255 255 / var(--tw-text-opacity)); }
.zenuml .text-gray-900 { --tw-text-opacity: 1; color: rgb(17 24 39 / var(--tw-text-opacity)); }
.zenuml .text-gray-400 { --tw-text-opacity: 1; color: rgb(156 163 175 / var(--tw-text-opacity)); }
.zenuml .text-gray-600 { --tw-text-opacity: 1; color: rgb(75 85 99 / var(--tw-text-opacity)); }
.zenuml .text-gray-500 { --tw-text-opacity: 1; color: rgb(107 114 128 / var(--tw-text-opacity)); }
.zenuml .text-skin-participant { color: var(--color-text-participant, var(--color-text-message, var(--color-text-base, #000))); }
.zenuml .text-skin-base { color: var(--color-text-base); }
.zenuml .text-skin-message { color: var(--color-text-message, var(--color-text-base, #000)); }
.zenuml .text-skin-comment { color: var(--color-text-comment, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-skin-fragment-header { color: var(--color-text-fragment-header, var(--color-text-message, #000)); }
.zenuml .text-skin-fragment { color: var(--color-text-fragment, var(--color-text-message, #000)); }
.zenuml .text-skin-message-arrow { color: var(--color-message-arrow, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .opacity-50 { opacity: 0.5; }
.zenuml .shadow-sm { --tw-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05); --tw-shadow-colored: 0 1px 2px 0 var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow-xl { --tw-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1); --tw-shadow-colored: 0 20px 25px -5px var(--tw-shadow-color), 0 8px 10px -6px var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow { --tw-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1); --tw-shadow-colored: 0 1px 3px 0 var(--tw-shadow-color), 0 1px 2px -1px var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow-slate-500\/50 { --tw-shadow-color: rgb(100 116 139 / 0.5); --tw-shadow: var(--tw-shadow-colored); }
.zenuml .blur { --tw-blur: blur(8px); filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .grayscale { --tw-grayscale: grayscale(100%); filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .filter { filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .transition { transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter, -webkit-backdrop-filter; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .transition-opacity { transition-property: opacity; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .transition-all { transition-property: all; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .hover\:whitespace-normal:hover { white-space: normal; }
.zenuml .hover\:bg-yellow-300:hover { --tw-bg-opacity: 1; background-color: rgb(253 224 71 / var(--tw-bg-opacity)); }
.zenuml .hover\:bg-gray-100:hover { --tw-bg-opacity: 1; background-color: rgb(243 244 246 / var(--tw-bg-opacity)); }
.zenuml .hover\:bg-skin-message-hover:hover { background-color: var(--color-bg-message-hover, var(--color-text-base, #000)); }
.zenuml .hover\:text-gray-600:hover { --tw-text-opacity: 1; color: rgb(75 85 99 / var(--tw-text-opacity)); }
.zenuml .hover\:text-gray-500:hover { --tw-text-opacity: 1; color: rgb(107 114 128 / var(--tw-text-opacity)); }
.zenuml .hover\:text-skin-message-hover:hover { color: var(--color-text-message-hover, var(--color-bg-base, #fff)); }
.zenuml .hover\:opacity-100:hover { opacity: 1; }
.zenuml .focus\:outline-none:focus { outline: transparent solid 2px; outline-offset: 2px; }
.zenuml .focus\:ring-2:focus { --tw-ring-offset-shadow: var(--tw-ring-inset) 0 0 0 var(--tw-ring-offset-width) var(--tw-ring-offset-color); --tw-ring-shadow: var(--tw-ring-inset) 0 0 0 calc(2px + var(--tw-ring-offset-width)) var(--tw-ring-color); box-shadow: var(--tw-ring-offset-shadow),var(--tw-ring-shadow),var(--tw-shadow, 0 0 #0000); }
.zenuml .focus\:ring-inset:focus { --tw-ring-inset: inset; }
.zenuml .focus\:ring-indigo-500:focus { --tw-ring-opacity: 1; --tw-ring-color: rgb(99 102 241 / var(--tw-ring-opacity)); }
.zenuml .group:hover .group-hover\:flex { display: flex; }
.zenuml .group:hover .group-hover\:hidden { display: none; }
@media (min-width: 640px) {
  .zenuml .sm\:my-8 { margin-top: 2rem; margin-bottom: 2rem; }
  .zenuml .sm\:block { display: block; }
  .zenuml .sm\:inline-block { display: inline-block; }
  .zenuml .sm\:h-screen { height: 100vh; }
  .zenuml .sm\:grid-cols-6 { grid-template-columns: repeat(6, minmax(0px, 1fr)); }
  .zenuml .sm\:grid-cols-2 { grid-template-columns: repeat(2, minmax(0px, 1fr)); }
  .zenuml .sm\:gap-6 { row-gap: 1.5rem; column-gap: 1.5rem; }
  .zenuml .sm\:p-2 { padding: 0.5rem; }
  .zenuml .sm\:px-6 { padding-left: 1.5rem; padding-right: 1.5rem; }
  .zenuml .sm\:align-middle { vertical-align: middle; }
}
@media (min-width: 1024px) {
  .zenuml .lg\:mx-auto { margin-left: auto; margin-right: auto; }
  .zenuml .lg\:mx-0 { margin-left: 0px; margin-right: 0px; }
  .zenuml .lg\:max-w-none { max-width: none; }
  .zenuml .lg\:grid-cols-8 { grid-template-columns: repeat(8, minmax(0px, 1fr)); }
  .zenuml .lg\:grid-cols-3 { grid-template-columns: repeat(3, minmax(0px, 1fr)); }
  .zenuml .lg\:px-8 { padding-left: 2rem; padding-right: 2rem; }
}
.zenuml .\[\&\>svg\]\:h-full > svg { height: 100%; }
.zenuml .\[\&\>svg\]\:w-full > svg { width: 100%; }
.zenuml *, .zenuml ::before, .zenuml ::after { box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgb(229, 231, 235); }
.zenuml ::before, .zenuml ::after { --tw-content: ""; }
.zenuml html { line-height: 1.5; tab-size: 4; font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji"; font-feature-settings: normal; }
.zenuml body { margin: 0px; line-height: inherit; }
.zenuml hr { height: 0px; color: inherit; border-top-width: 1px; }
.zenuml abbr:where([title]) { text-decoration: underline; text-decoration-style: dotted; text-decoration-color: currentcolor; }
.zenuml h1, .zenuml h2, .zenuml h3, .zenuml h4, .zenuml h5, .zenuml h6 { font-size: inherit; font-weight: inherit; }
.zenuml a { color: inherit; text-decoration: inherit; }
.zenuml b, .zenuml strong { font-weight: bolder; }
.zenuml code, .zenuml kbd, .zenuml samp, .zenuml pre { font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace; font-size: 1em; }
.zenuml small { font-size: 80%; }
.zenuml sub, .zenuml sup { font-size: 75%; line-height: 0; position: relative; vertical-align: baseline; }
.zenuml sub { bottom: -0.25em; }
.zenuml sup { top: -0.5em; }
.zenuml table { text-indent: 0px; border-color: inherit; border-collapse: collapse; }
.zenuml button, .zenuml input, .zenuml optgroup, .zenuml select, .zenuml textarea { font-family: inherit; font-size: 100%; font-weight: inherit; line-height: inherit; color: inherit; margin: 0px; padding: 0px; }
.zenuml button, .zenuml select { text-transform: none; }
.zenuml button, .zenuml [type="button"], .zenuml [type="reset"], .zenuml [type="submit"] { appearance: button; background-color: transparent; background-image: none; }
.zenuml progress { vertical-align: baseline; }
.zenuml ::-webkit-inner-spin-button, .zenuml ::-webkit-outer-spin-button { height: auto; }
.zenuml [type="search"] { appearance: textfield; outline-offset: -2px; }
.zenuml ::-webkit-search-decoration { appearance: none; }
.zenuml ::-webkit-file-upload-button { appearance: button; font-style: inherit; font-variant-caps: inherit; font-weight: inherit; font-stretch: inherit; font-size: inherit; line-height: inherit; font-family: inherit; font-size-adjust: inherit; font-kerning: inherit; font-variant-alternates: inherit; font-variant-ligatures: inherit; font-variant-numeric: inherit; font-variant-east-asian: inherit; font-variant-position: inherit; font-feature-settings: inherit; font-optical-sizing: inherit; font-variation-settings: inherit; }
.zenuml summary { display: list-item; }
.zenuml blockquote, .zenuml dl, .zenuml dd, .zenuml h1, .zenuml h2, .zenuml h3, .zenuml h4, .zenuml h5, .zenuml h6, .zenuml hr, .zenuml figure, .zenuml p, .zenuml pre { margin: 0px; }
.zenuml fieldset { margin: 0px; padding: 0px; }
.zenuml legend { padding: 0px; }
.zenuml ol, .zenuml ul, .zenuml menu { list-style: none; margin: 0px; padding: 0px; }
.zenuml textarea { resize: vertical; }
.zenuml input::placeholder, .zenuml textarea::placeholder { opacity: 1; color: rgb(156, 163, 175); }
.zenuml button, .zenuml [role="button"] { cursor: pointer; }
.zenuml :disabled { cursor: default; }
.zenuml img, .zenuml svg, .zenuml video, .zenuml canvas, .zenuml audio, .zenuml iframe, .zenuml embed, .zenuml object { display: block; vertical-align: middle; }
.zenuml img, .zenuml video { max-width: 100%; height: auto; }
.zenuml [hidden] { display: none; }
pre.zenuml { margin: 0px; }
.zenuml { font-family: Helvetica, Verdana, serif; font-size: 16px; }
.zenuml .frame .sequence-diagram .comments code { background-color: rgb(249, 242, 244); padding: 2px; margin: 1px 0px; border-radius: 2px; }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-post { color: rgb(13, 75, 59); }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-get { color: rgb(47, 61, 137); }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-delete { color: rgb(126, 30, 35); }
.zenuml .frame .sequence-diagram .comments ol, .zenuml .frame .sequence-diagram .comments ul { margin: 0px; padding-left: 20px; }
.zenuml .frame .sequence-diagram .comments ul li { list-style-type: none; }
.zenuml .frame .sequence-diagram .comments ul li input[type="checkbox"] { margin-left: -1em; }
.zenuml .frame .sequence-diagram .comments table { border-collapse: collapse; border-spacing: 0px; empty-cells: show; border-color: rgb(203, 203, 203); }
.zenuml .frame .sequence-diagram .comments table td, .zenuml .frame .sequence-diagram .comments table th { padding: 0.5em; }
.zenuml .frame .sequence-diagram .comments table thead { background-color: rgb(224, 224, 224); }
.zenuml .frame .sequence-diagram .comments table td { background-color: transparent; }
.zenuml .theme-blue .sequence-diagram .divider .name { border-radius: 4px; border: 1px solid rgb(226, 133, 83); color: rgb(226, 133, 83); }
.zenuml .theme-blue .sequence-diagram .divider .left, .zenuml .theme-blue .sequence-diagram .divider .right { background: rgb(226, 133, 83); }
.zenuml .theme-blue .sequence-diagram .lifeline .participant { border-color: rgb(3, 44, 114); background: rgba(146, 192, 240, 0.3); }
.zenuml .theme-blue .sequence-diagram .lifeline .participant label { color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .lifeline .line { border-left-color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message { border-bottom-color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message .name { color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message svg polyline { stroke: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message svg polyline.head { fill: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .occurrence { border-color: rgb(3, 44, 114); background-color: rgb(255, 255, 255); }
.zenuml .theme-blue .sequence-diagram .fragment { border-radius: 4px; border-color: rgba(4, 46, 110, 0.3); }
.zenuml .theme-blue .sequence-diagram .fragment .header .name { background: rgba(4, 46, 110, 0.1); }
.zenuml .theme-blue .sequence-diagram .fragment .header label { color: rgb(3, 44, 114); }
.zenuml .theme-black-white .sequence-diagram .divider .name { border-radius: 0px; border-color: rgb(0, 0, 0); box-shadow: rgb(0, 0, 0) 2px 2px; }
.zenuml .theme-black-white .sequence-diagram .divider .left, .zenuml .theme-black-white .sequence-diagram .divider .right { background: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .lifeline .participant { border-color: rgb(0, 0, 0); background: rgb(255, 255, 255); box-shadow: rgb(0, 0, 0) 2px 2px; }
.zenuml .theme-black-white .sequence-diagram .lifeline .line { border-left-color: rgba(0, 0, 0, 0.4); }
.zenuml .theme-black-white .sequence-diagram .message { border-bottom-color: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message .name { color: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message svg polyline { stroke: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message svg polyline.head { fill: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .occurrence { border-color: rgb(0, 0, 0); background-color: rgb(245, 245, 245); }
.zenuml .theme-black-white .sequence-diagram .fragment { border-color: rgba(0, 0, 0, 0.3); }
.zenuml .theme-black-white .sequence-diagram .fragment .header .name { background: rgba(0, 0, 0, 0.07); }
.zenuml .theme-star-uml .sequence-diagram .lifeline .participant { border-color: rgb(185, 64, 101); background-color: rgb(255, 254, 200); }
.zenuml .theme-star-uml .sequence-diagram .lifeline .line { border-left-color: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message { border-bottom-color: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message svg polyline { stroke: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message svg polyline.head { fill: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .occurrence { background-color: rgb(255, 254, 200); border-color: rgb(185, 64, 101); }
.zenuml .blue-river .sequence-diagram { color: rgb(84, 82, 246); }
.zenuml .blue-river .sequence-diagram .life-line-layer .lifeline.actor .participant::before { filter: invert(100%) sepia(0%) saturate(0%) hue-rotate(93deg) brightness(103%) contrast(103%); }
.zenuml .blue-river .sequence-diagram .life-line-layer .participant { background: rgb(32, 151, 247); box-shadow: rgb(131, 196, 248) 4px 4px; border-radius: 9px; color: rgb(255, 255, 255); }
.zenuml .blue-river .sequence-diagram .life-line-layer .participant .interface { font-size: 0.8em; }
.zenuml .blue-river .sequence-diagram .life-line-layer .line { border-left-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message { border-bottom-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message svg.arrow polyline { stroke: rgb(32, 151, 247); fill: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message.self svg.arrow polyline { fill: none; }
.zenuml .blue-river .sequence-diagram .message-layer .message.self svg.arrow polyline.head { fill: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .occurrence { background: rgb(229, 253, 232); border-color: rgb(101, 191, 115); box-shadow: rgb(147, 198, 155) 4px 4px; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment { border-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header .name label { display: inline-block; min-width: 50px; background: rgb(84, 82, 246); color: rgb(255, 255, 255); padding: 2px 0px 2px 10px; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header .name::after { content: "SO"; display: inline-block; background: rgb(84, 82, 246); margin-left: -23px; width: 34px; transform: translateY(-2px); height: 22px; clip-path: polygon(66% 0px, 100% 0px, 100% 66%, 66% 100%); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header > label { display: inline-block; min-width: 50px; font-weight: 700; color: rgb(255, 255, 255); background: rgb(101, 191, 115); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .comments { border-color: inherit; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .divider { border-bottom-color: inherit; }
:root { --background: #282a36; --hover: #2b2b2b; --occurance-border: #6e7191; --border: #585b74; --white: #f8f8f2; --current: #44475a; --comment: #6272a4; --cyan: #8be9fd; --green: #50fa7b; --orange: #ffb86c; --pink: #ff79c6; --purple: #bd93f9; --red: #ff5555; --yellow: #f1fa8c; --crayola: #a9b7c6; }
body .zenuml.theme-dark { background-color: var(--background); }
.zenuml.theme-dark .header { color: var(--crayola); font-weight: 700; }
.zenuml.theme-dark .sequence-diagram .lifeline-group:hover { background-color: rgb(34, 48, 73); }
.zenuml.theme-dark .sequence-diagram .lifeline .participant { border-color: var(--border); border-radius: 2px; background: var(--background); }
.zenuml.theme-dark .sequence-diagram .lifeline .participant label { color: var(--cyan); }
.zenuml.theme-dark .sequence-diagram .lifeline .line { background: var(--border); }
.zenuml.theme-dark .fragment > .header > .condition { color: var(--crayola); padding: 6px; }
.zenuml.theme-dark .sequence-diagram .fragment .header label { color: var(--crayola); padding: 6px; }
.zenuml.theme-dark .sequence-diagram .fragment .header .name { border-bottom-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .fragment .header .name label { color: var(--pink); font-weight: 400; }
.zenuml.theme-dark .sequence-diagram .fragment { border-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .comments { border: medium; background: none; color: var(--comment); }
.zenuml.theme-dark .sequence-diagram .fragment .segment:not(:first-child) { border-top-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .interaction { color: var(--orange); }
.zenuml.theme-dark .sequence-diagram .message { border-bottom-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .message .name:hover { color: var(--crayola); }
.zenuml.theme-dark .sequence-diagram .message svg polyline { fill: var(--border); stroke: var(--border); }
.zenuml.theme-dark .sequence-diagram .message.self svg > polyline:not(.head) { fill: none; }
.zenuml.theme-dark .sequence-diagram .occurrence { background-color: var(--current); box-shadow: 0 0 0 1px var(--occurance-border); border-radius: 2px; }
.zenuml.theme-dark .sequence-diagram .divider .left, .zenuml.theme-dark .sequence-diagram .divider .right { background: var(--border); }
.zenuml.theme-dark .sequence-diagram .divider .name { color: var(--comment); }
.zenuml.theme-dark .interaction .message > .name:hover { background-color: rgb(34, 139, 34); color: rgb(255, 255, 255); }
p[data-v-15224042] { margin: 0px; line-height: 1.25em; }
.occurrence[data-v-4e715276] { width: 15px; padding: 16px 0px 16px 5px; }
[data-v-4e715276] > .statement-container:last-child > .interaction.return:last-of-type { margin-bottom: 0px; border-bottom-width: 0px; border-bottom-style: none; border-bottom-color: currentcolor; transform: translateY(1px); }
[data-v-4e715276] > .statement-container:last-child > .interaction.return:last-of-type > .message { bottom: -17px; height: 0px; }
.right-to-left.occurrence[data-v-4e715276] { left: -14px; }
.occurrence { margin-top: -2px; }
.interaction .occurrence.source[data-v-703306ae] { position: absolute; height: calc(100% + 14px); left: -12px; display: none; }
.interaction .occurrence.source.right-to-left[data-v-703306ae] { left: unset; right: -13px; }
.message.self[data-v-fbc5fa7e] { transform: translate(-5px); }
.interaction .invisible-occurrence[data-v-40bff5d3] { height: 20px; }
.interaction.async[data-v-40bff5d3] .message { width: 100%; }
.collapsible-header[data-v-b41acdfc] { display: flex; width: 100%; justify-content: space-between; }
.collapse-button[data-v-b41acdfc] { display: none; }
.fragment:hover .collapsible-header .collapse-button.expanded[data-v-b41acdfc] { display: inline-block !important; }
[data-v-0a896a9f], [data-v-0f640fba] { border-color: inherit; }
.fragment.par > .block > .statement-container:not(:first-child) { border-top-color: inherit; border-top-width: 1px; border-top-style: solid; }
[data-v-ce259a11], [data-v-a846d536], [data-v-7b7c43b8] { border-color: inherit; }
.divider[data-v-9e567e69] { display: flex; align-items: center; }
.name[data-v-9e567e69] { margin: 0px; padding: 2px 6px; }
.left[data-v-9e567e69], .right[data-v-9e567e69] { height: 1px; flex-grow: 1; }


:root { --bg-color: #ffffff; --text-color: #333333; --select-text-bg-color: #B5D6FC; --select-text-font-color: auto; --monospace: "Lucida Console",Consolas,"Courier",monospace; --title-bar-height: 20px; }
.mac-os-11 { --title-bar-height: 28px; }
html { font-size: 14px; background-color: var(--bg-color); color: var(--text-color); font-family: "Helvetica Neue", Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; }
h1, h2, h3, h4, h5 { white-space: pre-wrap; }
body { margin: 0px; padding: 0px; height: auto; inset: 0px; font-size: 1rem; line-height: 1.428571; overflow-x: hidden; background: inherit; }
iframe { margin: auto; }
a.url { word-break: break-all; }
a:active, a:hover { outline: 0px; }
.in-text-selection, ::selection { text-shadow: none; background: var(--select-text-bg-color); color: var(--select-text-font-color); }
#write { margin: 0px auto; height: auto; width: inherit; word-break: normal; overflow-wrap: break-word; position: relative; white-space: normal; overflow-x: visible; padding-top: 36px; }
#write.first-line-indent p { text-indent: 2em; }
#write.first-line-indent li p, #write.first-line-indent p * { text-indent: 0px; }
#write.first-line-indent li { margin-left: 2em; }
.for-image #write { padding-left: 8px; padding-right: 8px; }
body.typora-export { padding-left: 30px; padding-right: 30px; }
.typora-export .footnote-line, .typora-export li, .typora-export p { white-space: pre-wrap; }
.typora-export .task-list-item input { pointer-events: none; }
@media screen and (max-width: 500px) {
  body.typora-export { padding-left: 0px; padding-right: 0px; }
  #write { padding-left: 20px; padding-right: 20px; }
}
#write li > figure:last-child { margin-bottom: 0.5rem; }
#write ol, #write ul { position: relative; }
img { max-width: 100%; vertical-align: middle; image-orientation: from-image; }
button, input, select, textarea { color: inherit; font-style: inherit; font-variant-caps: inherit; font-weight: inherit; font-stretch: inherit; font-size: inherit; line-height: inherit; font-family: inherit; font-size-adjust: inherit; font-kerning: inherit; font-variant-alternates: inherit; font-variant-ligatures: inherit; font-variant-numeric: inherit; font-variant-east-asian: inherit; font-variant-position: inherit; font-feature-settings: inherit; font-optical-sizing: inherit; font-variation-settings: inherit; }
input[type="checkbox"], input[type="radio"] { line-height: normal; padding: 0px; }
*, ::after, ::before { box-sizing: border-box; }
#write h1, #write h2, #write h3, #write h4, #write h5, #write h6, #write p, #write pre { width: inherit; }
#write h1, #write h2, #write h3, #write h4, #write h5, #write h6, #write p { position: relative; }
p { line-height: inherit; }
h1, h2, h3, h4, h5, h6 { break-after: avoid-page; break-inside: avoid; orphans: 4; }
p { orphans: 4; }
h1 { font-size: 2rem; }
h2 { font-size: 1.8rem; }
h3 { font-size: 1.6rem; }
h4 { font-size: 1.4rem; }
h5 { font-size: 1.2rem; }
h6 { font-size: 1rem; }
.md-math-block, .md-rawblock, h1, h2, h3, h4, h5, h6, p { margin-top: 1rem; margin-bottom: 1rem; }
.hidden { display: none; }
.md-blockmeta { color: rgb(204, 204, 204); font-weight: 700; font-style: italic; }
a { cursor: pointer; }
sup.md-footnote { padding: 2px 4px; background-color: rgba(238, 238, 238, 0.7); color: rgb(85, 85, 85); border-radius: 4px; cursor: pointer; }
sup.md-footnote a, sup.md-footnote a:hover { color: inherit; text-transform: inherit; text-decoration: inherit; }
#write input[type="checkbox"] { cursor: pointer; width: inherit; height: inherit; }
figure { overflow-x: auto; margin: 1.2em 0px; max-width: calc(100% + 16px); padding: 0px; }
figure > table { margin: 0px; }
thead, tr { break-inside: avoid; break-after: auto; }
thead { display: table-header-group; }
table { border-collapse: collapse; border-spacing: 0px; width: 100%; overflow: auto; break-inside: auto; text-align: left; }
table.md-table td { min-width: 32px; }
.CodeMirror-gutters { border-right-width: 0px; border-right-style: none; border-right-color: currentcolor; background-color: inherit; }
.CodeMirror-linenumber { -webkit-user-select: none; }
.CodeMirror { text-align: left; }
.CodeMirror-placeholder { opacity: 0.3; }
.CodeMirror pre { padding: 0px 4px; }
.CodeMirror-lines { padding: 0px; }
div.hr:focus { cursor: none; }
#write pre { white-space: pre-wrap; }
#write.fences-no-line-wrapping pre { white-space: pre; }
#write pre.ty-contain-cm { white-space: normal; }
.CodeMirror-gutters { margin-right: 4px; }
.md-fences { font-size: 0.9rem; display: block; break-inside: avoid; text-align: left; overflow: visible; white-space: pre; background: inherit; position: relative !important; }
.md-fences-adv-panel { width: 100%; margin-top: 10px; text-align: center; padding-top: 0px; padding-bottom: 8px; overflow-x: auto; }
#write .md-fences.mock-cm { white-space: pre-wrap; }
.md-fences.md-fences-with-lineno { padding-left: 0px; }
#write.fences-no-line-wrapping .md-fences.mock-cm { white-space: pre; overflow-x: auto; }
.md-fences.mock-cm.md-fences-with-lineno { padding-left: 8px; }
.CodeMirror-line, twitterwidget { break-inside: avoid; }
svg { break-inside: avoid; }
.footnotes { opacity: 0.8; font-size: 0.9rem; margin-top: 1em; margin-bottom: 1em; }
.footnotes + .footnotes { margin-top: 0px; }
.md-reset { margin: 0px; padding: 0px; border: 0px; outline: 0px; vertical-align: top; background: 0px 0px; text-decoration: none; text-shadow: none; float: none; position: static; width: auto; height: auto; white-space: nowrap; cursor: inherit; line-height: normal; font-weight: 400; text-align: left; box-sizing: content-box; direction: ltr; }
li div { padding-top: 0px; }
blockquote { margin: 1rem 0px; }
li .mathjax-block, li p { margin: 0.5rem 0px; }
li blockquote { margin: 1rem 0px; }
li { margin: 0px; position: relative; }
blockquote > :last-child { margin-bottom: 0px; }
blockquote > :first-child, li > :first-child { margin-top: 0px; }
.footnotes-area { color: rgb(136, 136, 136); margin-top: 0.714rem; padding-bottom: 0.143rem; white-space: normal; }
#write .footnote-line { white-space: pre-wrap; }
@media print {
  body, html { border: 1px solid transparent; height: 99%; break-after: avoid; break-before: avoid; font-variant-ligatures: no-common-ligatures; }
  #write { margin-top: 0px; border-color: transparent !important; padding-top: 0px !important; padding-bottom: 0px !important; }
  .typora-export * { print-color-adjust: exact; }
  .typora-export #write { break-after: avoid; }
  .typora-export #write::after { height: 0px; }
  .is-mac table { break-inside: avoid; }
  #write > p:nth-child(1) { margin-top: 0px; }
  .typora-export-show-outline .typora-export-sidebar { display: none; }
  figure { overflow-x: visible; }
}
.footnote-line { margin-top: 0.714em; font-size: 0.7em; }
a img, img a { cursor: pointer; }
pre.md-meta-block { font-size: 0.8rem; min-height: 0.8rem; white-space: pre-wrap; background: rgb(204, 204, 204); display: block; overflow-x: hidden; }
p > .md-image:only-child:not(.md-img-error) img, p > img:only-child { display: block; margin: auto; }
#write.first-line-indent p > .md-image:only-child:not(.md-img-error) img { left: -2em; position: relative; }
p > .md-image:only-child { display: inline-block; width: 100%; }
#write .MathJax_Display { margin: 0.8em 0px 0px; }
.md-math-block { width: 100%; }
.md-math-block:not(:empty)::after { display: none; }
.MathJax_ref { fill: currentcolor; }
[contenteditable="true"]:active, [contenteditable="true"]:focus, [contenteditable="false"]:active, [contenteditable="false"]:focus { outline: 0px; box-shadow: none; }
.md-task-list-item { position: relative; list-style-type: none; }
.task-list-item.md-task-list-item { padding-left: 0px; }
.md-task-list-item > input { position: absolute; top: 0px; left: 0px; margin-left: -1.2em; margin-top: calc(1em - 10px); border: medium; }
.math { font-size: 1rem; }
.md-toc { min-height: 3.58rem; position: relative; font-size: 0.9rem; border-radius: 10px; }
.md-toc-content { position: relative; margin-left: 0px; }
.md-toc-content::after, .md-toc::after { display: none; }
.md-toc-item { display: block; color: rgb(65, 131, 196); }
.md-toc-item a { text-decoration: none; }
.md-toc-inner:hover { text-decoration: underline; }
.md-toc-inner { display: inline-block; cursor: pointer; }
.md-toc-h1 .md-toc-inner { margin-left: 0px; font-weight: 700; }
.md-toc-h2 .md-toc-inner { margin-left: 2em; }
.md-toc-h3 .md-toc-inner { margin-left: 4em; }
.md-toc-h4 .md-toc-inner { margin-left: 6em; }
.md-toc-h5 .md-toc-inner { margin-left: 8em; }
.md-toc-h6 .md-toc-inner { margin-left: 10em; }
@media screen and (max-width: 48em) {
  .md-toc-h3 .md-toc-inner { margin-left: 3.5em; }
  .md-toc-h4 .md-toc-inner { margin-left: 5em; }
  .md-toc-h5 .md-toc-inner { margin-left: 6.5em; }
  .md-toc-h6 .md-toc-inner { margin-left: 8em; }
}
a.md-toc-inner { font-size: inherit; font-style: inherit; font-weight: inherit; line-height: inherit; }
.footnote-line a:not(.reversefootnote) { color: inherit; }
.reversefootnote { font-family: ui-monospace, sans-serif; }
.md-attr { display: none; }
.md-fn-count::after { content: "."; }
code, pre, samp, tt { font-family: var(--monospace); }
kbd { margin: 0px 0.1em; padding: 0.1em 0.6em; font-size: 0.8em; color: rgb(36, 39, 41); background: rgb(255, 255, 255); border: 1px solid rgb(173, 179, 185); border-radius: 3px; box-shadow: rgba(12, 13, 14, 0.2) 0px 1px 0px, rgb(255, 255, 255) 0px 0px 0px 2px inset; white-space: nowrap; vertical-align: middle; }
.md-comment { color: rgb(162, 127, 3); opacity: 0.6; font-family: var(--monospace); }
code { text-align: left; vertical-align: initial; }
a.md-print-anchor { white-space: pre !important; border-width: medium !important; border-style: none !important; border-color: currentcolor !important; display: inline-block !important; position: absolute !important; width: 1px !important; right: 0px !important; outline: 0px !important; background: 0px 0px !important; text-decoration: initial !important; text-shadow: initial !important; }
.os-windows.monocolor-emoji .md-emoji { font-family: "Segoe UI Symbol", sans-serif; }
.md-diagram-panel > svg { max-width: 100%; }
[lang="flow"] svg, [lang="mermaid"] svg { max-width: 100%; height: auto; }
[lang="mermaid"] .node text { font-size: 1rem; }
table tr th { border-bottom-width: 0px; border-bottom-style: none; border-bottom-color: currentcolor; }
video { max-width: 100%; display: block; margin: 0px auto; }
iframe { max-width: 100%; width: 100%; border: medium; }
.highlight td, .highlight tr { border: 0px; }
mark { background: rgb(255, 255, 0); color: rgb(0, 0, 0); }
.md-html-inline .md-plain, .md-html-inline strong, mark .md-inline-math, mark strong { color: inherit; }
.md-expand mark .md-meta { opacity: 0.3 !important; }
mark .md-meta { color: rgb(0, 0, 0); }
@media print {
  .typora-export h1, .typora-export h2, .typora-export h3, .typora-export h4, .typora-export h5, .typora-export h6 { break-inside: avoid; }
}
.md-diagram-panel .messageText { stroke: none !important; }
.md-diagram-panel .start-state { fill: var(--node-fill); }
.md-diagram-panel .edgeLabel rect { opacity: 1 !important; }
.md-fences.md-fences-math { font-size: 1em; }
.md-fences-advanced:not(.md-focus) { padding: 0px; white-space: nowrap; border: 0px; }
.md-fences-advanced:not(.md-focus) { background: inherit; }
.typora-export-show-outline .typora-export-content { max-width: 1440px; margin: auto; display: flex; flex-direction: row; }
.typora-export-sidebar { width: 300px; font-size: 0.8rem; margin-top: 80px; margin-right: 18px; }
.typora-export-show-outline #write { --webkit-flex: 2; flex: 2 1 0%; }
.typora-export-sidebar .outline-content { position: fixed; top: 0px; max-height: 100%; overflow: hidden auto; padding-bottom: 30px; padding-top: 60px; width: 300px; }
@media screen and (max-width: 1024px) {
  .typora-export-sidebar, .typora-export-sidebar .outline-content { width: 240px; }
}
@media screen and (max-width: 800px) {
  .typora-export-sidebar { display: none; }
}
.outline-content li, .outline-content ul { margin-left: 0px; margin-right: 0px; padding-left: 0px; padding-right: 0px; list-style: none; overflow-wrap: anywhere; }
.outline-content ul { margin-top: 0px; margin-bottom: 0px; }
.outline-content strong { font-weight: 400; }
.outline-expander { width: 1rem; height: 1.428571rem; position: relative; display: table-cell; vertical-align: middle; cursor: pointer; padding-left: 4px; }
.outline-expander::before { content: ""; position: relative; font-family: Ionicons; display: inline-block; font-size: 8px; vertical-align: middle; }
.outline-item { padding-top: 3px; padding-bottom: 3px; cursor: pointer; }
.outline-expander:hover::before { content: ""; }
.outline-h1 > .outline-item { padding-left: 0px; }
.outline-h2 > .outline-item { padding-left: 1em; }
.outline-h3 > .outline-item { padding-left: 2em; }
.outline-h4 > .outline-item { padding-left: 3em; }
.outline-h5 > .outline-item { padding-left: 4em; }
.outline-h6 > .outline-item { padding-left: 5em; }
.outline-label { cursor: pointer; display: table-cell; vertical-align: middle; text-decoration: none; color: inherit; }
.outline-label:hover { text-decoration: underline; }
.outline-item:hover { border-color: rgb(245, 245, 245); background-color: var(--item-hover-bg-color); }
.outline-item:hover { margin-left: -28px; margin-right: -28px; border-left-width: 28px; border-left-style: solid; border-left-color: transparent; border-right-width: 28px; border-right-style: solid; border-right-color: transparent; }
.outline-item-single .outline-expander::before, .outline-item-single .outline-expander:hover::before { display: none; }
.outline-item-open > .outline-item > .outline-expander::before { content: ""; }
.outline-children { display: none; }
.info-panel-tab-wrapper { display: none; }
.outline-item-open > .outline-children { display: block; }
.typora-export .outline-item { padding-top: 1px; padding-bottom: 1px; }
.typora-export .outline-item:hover { margin-right: -8px; border-right-width: 8px; border-right-style: solid; border-right-color: transparent; }
.typora-export .outline-expander::before { content: "+"; font-family: inherit; top: -1px; }
.typora-export .outline-expander:hover::before, .typora-export .outline-item-open > .outline-item > .outline-expander::before { content: "−"; }
.typora-export-collapse-outline .outline-children { display: none; }
.typora-export-collapse-outline .outline-item-open > .outline-children, .typora-export-no-collapse-outline .outline-children { display: block; }
.typora-export-no-collapse-outline .outline-expander::before { content: "" !important; }
.typora-export-show-outline .outline-item-active > .outline-item .outline-label { font-weight: 700; }
.md-inline-math-container mjx-container { zoom: 0.95; }
mjx-container { break-inside: avoid; }


.CodeMirror { height: auto; }
.CodeMirror.cm-s-inner { background: inherit; }
.CodeMirror-scroll { overflow: auto hidden; z-index: 3; }
.CodeMirror-gutter-filler, .CodeMirror-scrollbar-filler { background-color: rgb(255, 255, 255); }
.CodeMirror-gutters { border-right-width: 1px; border-right-style: solid; border-right-color: rgb(221, 221, 221); background: inherit; white-space: nowrap; }
.CodeMirror-linenumber { padding: 0px 3px 0px 5px; text-align: right; color: rgb(153, 153, 153); }
.cm-s-inner .cm-keyword { color: rgb(119, 0, 136); }
.cm-s-inner .cm-atom, .cm-s-inner.cm-atom { color: rgb(34, 17, 153); }
.cm-s-inner .cm-number { color: rgb(17, 102, 68); }
.cm-s-inner .cm-def { color: rgb(0, 0, 255); }
.cm-s-inner .cm-variable { color: rgb(0, 0, 0); }
.cm-s-inner .cm-variable-2 { color: rgb(0, 85, 170); }
.cm-s-inner .cm-variable-3 { color: rgb(0, 136, 85); }
.cm-s-inner .cm-string { color: rgb(170, 17, 17); }
.cm-s-inner .cm-property { color: rgb(0, 0, 0); }
.cm-s-inner .cm-operator { color: rgb(152, 26, 26); }
.cm-s-inner .cm-comment, .cm-s-inner.cm-comment { color: rgb(170, 85, 0); }
.cm-s-inner .cm-string-2 { color: rgb(255, 85, 0); }
.cm-s-inner .cm-meta { color: rgb(85, 85, 85); }
.cm-s-inner .cm-qualifier { color: rgb(85, 85, 85); }
.cm-s-inner .cm-builtin { color: rgb(51, 0, 170); }
.cm-s-inner .cm-bracket { color: rgb(153, 153, 119); }
.cm-s-inner .cm-tag { color: rgb(17, 119, 0); }
.cm-s-inner .cm-attribute { color: rgb(0, 0, 204); }
.cm-s-inner .cm-header, .cm-s-inner.cm-header { color: rgb(0, 0, 255); }
.cm-s-inner .cm-quote, .cm-s-inner.cm-quote { color: rgb(0, 153, 0); }
.cm-s-inner .cm-hr, .cm-s-inner.cm-hr { color: rgb(153, 153, 153); }
.cm-s-inner .cm-link, .cm-s-inner.cm-link { color: rgb(0, 0, 204); }
.cm-negative { color: rgb(221, 68, 68); }
.cm-positive { color: rgb(34, 153, 34); }
.cm-header, .cm-strong { font-weight: 700; }
.cm-del { text-decoration: line-through; }
.cm-em { font-style: italic; }
.cm-link { text-decoration: underline; }
.cm-error { color: red; }
.cm-invalidchar { color: red; }
.cm-constant { color: rgb(38, 139, 210); }
.cm-defined { color: rgb(181, 137, 0); }
div.CodeMirror span.CodeMirror-matchingbracket { color: rgb(0, 255, 0); }
div.CodeMirror span.CodeMirror-nonmatchingbracket { color: rgb(255, 34, 34); }
.cm-s-inner .CodeMirror-activeline-background { background: inherit; }
.CodeMirror { position: relative; overflow: hidden; }
.CodeMirror-scroll { height: 100%; outline: 0px; position: relative; box-sizing: content-box; background: inherit; }
.CodeMirror-sizer { position: relative; }
.CodeMirror-gutter-filler, .CodeMirror-hscrollbar, .CodeMirror-scrollbar-filler, .CodeMirror-vscrollbar { position: absolute; z-index: 6; display: none; outline: 0px; }
.CodeMirror-vscrollbar { right: 0px; top: 0px; overflow: hidden; }
.CodeMirror-hscrollbar { bottom: 0px; left: 0px; overflow: auto hidden; }
.CodeMirror-scrollbar-filler { right: 0px; bottom: 0px; }
.CodeMirror-gutter-filler { left: 0px; bottom: 0px; }
.CodeMirror-gutters { position: absolute; left: 0px; top: 0px; padding-bottom: 10px; z-index: 3; overflow-y: hidden; }
.CodeMirror-gutter { white-space: normal; height: 100%; box-sizing: content-box; padding-bottom: 30px; margin-bottom: -32px; display: inline-block; }
.CodeMirror-gutter-wrapper { position: absolute; z-index: 4; background: 0px 0px !important; border: medium !important; }
.CodeMirror-gutter-background { position: absolute; top: 0px; bottom: 0px; z-index: 4; }
.CodeMirror-gutter-elt { position: absolute; cursor: default; z-index: 4; }
.CodeMirror-lines { cursor: text; }
.CodeMirror pre { border-radius: 0px; border-width: 0px; background: 0px 0px; font-family: inherit; font-size: inherit; margin: 0px; white-space: pre; overflow-wrap: normal; color: inherit; z-index: 2; position: relative; overflow: visible; }
.CodeMirror-wrap pre { overflow-wrap: break-word; white-space: pre-wrap; word-break: normal; }
.CodeMirror-code pre { border-right-width: 30px; border-right-style: solid; border-right-color: transparent; width: fit-content; }
.CodeMirror-wrap .CodeMirror-code pre { border-right-width: medium; border-right-style: none; border-right-color: currentcolor; width: auto; }
.CodeMirror-linebackground { position: absolute; inset: 0px; z-index: 0; }
.CodeMirror-linewidget { position: relative; z-index: 2; overflow: auto; }
.CodeMirror-wrap .CodeMirror-scroll { overflow-x: hidden; }
.CodeMirror-measure { position: absolute; width: 100%; height: 0px; overflow: hidden; visibility: hidden; }
.CodeMirror-measure pre { position: static; }
.CodeMirror div.CodeMirror-cursor { position: absolute; visibility: hidden; border-right-width: medium; border-right-style: none; border-right-color: currentcolor; width: 0px; }
.CodeMirror div.CodeMirror-cursor { visibility: hidden; }
.CodeMirror-focused div.CodeMirror-cursor { visibility: inherit; }
.cm-searching { background: rgba(255, 255, 0, 0.4); }
span.cm-underlined { text-decoration: underline; }
span.cm-strikethrough { text-decoration: line-through; }
.cm-tw-syntaxerror { color: rgb(255, 255, 255); background-color: rgb(153, 0, 0); }
.cm-tw-deleted { text-decoration: line-through; }
.cm-tw-header5 { font-weight: 700; }
.cm-tw-listitem:first-child { padding-left: 10px; }
.cm-tw-box { border-style: solid; border-right-width: 1px; border-bottom-width: 1px; border-left-width: 1px; border-color: inherit; border-top-width: 0px !important; }
.cm-tw-underline { text-decoration: underline; }
@media print {
  .CodeMirror div.CodeMirror-cursor { visibility: hidden; }
}


:root {
    --side-bar-bg-color: #fafafa;
    --control-text-color: #777;
}

@include-when-export url(https://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,700,400&subset=latin,latin-ext);

/* open-sans-regular - latin-ext_latin */
  /* open-sans-italic - latin-ext_latin */
    /* open-sans-700 - latin-ext_latin */
    /* open-sans-700italic - latin-ext_latin */
  html {
    font-size: 16px;
    -webkit-font-smoothing: antialiased;
}

body {
    font-family: "Open Sans","Clear Sans", "Helvetica Neue", Helvetica, Arial, 'Segoe UI Emoji', sans-serif;
    color: rgb(51, 51, 51);
    line-height: 1.6;
}

#write {
    max-width: 860px;
  	margin: 0 auto;
  	padding: 30px;
    padding-bottom: 100px;
}

@media only screen and (min-width: 1400px) {
	#write {
		max-width: 1024px;
	}
}

@media only screen and (min-width: 1800px) {
	#write {
		max-width: 1200px;
	}
}

#write > ul:first-child,
#write > ol:first-child{
    margin-top: 30px;
}

a {
    color: #4183C4;
}
h1,
h2,
h3,
h4,
h5,
h6 {
    position: relative;
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-weight: bold;
    line-height: 1.4;
    cursor: text;
}
h1:hover a.anchor,
h2:hover a.anchor,
h3:hover a.anchor,
h4:hover a.anchor,
h5:hover a.anchor,
h6:hover a.anchor {
    text-decoration: none;
}
h1 tt,
h1 code {
    font-size: inherit;
}
h2 tt,
h2 code {
    font-size: inherit;
}
h3 tt,
h3 code {
    font-size: inherit;
}
h4 tt,
h4 code {
    font-size: inherit;
}
h5 tt,
h5 code {
    font-size: inherit;
}
h6 tt,
h6 code {
    font-size: inherit;
}
h1 {
    font-size: 2.25em;
    line-height: 1.2;
    border-bottom: 1px solid #eee;
}
h2 {
    font-size: 1.75em;
    line-height: 1.225;
    border-bottom: 1px solid #eee;
}

/*@media print {
    .typora-export h1,
    .typora-export h2 {
        border-bottom: none;
        padding-bottom: initial;
    }

    .typora-export h1::after,
    .typora-export h2::after {
        content: "";
        display: block;
        height: 100px;
        margin-top: -96px;
        border-top: 1px solid #eee;
    }
}*/

h3 {
    font-size: 1.5em;
    line-height: 1.43;
}
h4 {
    font-size: 1.25em;
}
h5 {
    font-size: 1em;
}
h6 {
   font-size: 1em;
    color: #777;
}
p,
blockquote,
ul,
ol,
dl,
table{
    margin: 0.8em 0;
}
li>ol,
li>ul {
    margin: 0 0;
}
hr {
    height: 2px;
    padding: 0;
    margin: 16px 0;
    background-color: #e7e7e7;
    border: 0 none;
    overflow: hidden;
    box-sizing: content-box;
}

li p.first {
    display: inline-block;
}
ul,
ol {
    padding-left: 30px;
}
ul:first-child,
ol:first-child {
    margin-top: 0;
}
ul:last-child,
ol:last-child {
    margin-bottom: 0;
}
blockquote {
    border-left: 4px solid #dfe2e5;
    padding: 0 15px;
    color: #777777;
}
blockquote blockquote {
    padding-right: 0;
}
table {
    padding: 0;
    word-break: initial;
}
table tr {
    border: 1px solid #dfe2e5;
    margin: 0;
    padding: 0;
}
table tr:nth-child(2n),
thead {
    background-color: #f8f8f8;
}
table th {
    font-weight: bold;
    border: 1px solid #dfe2e5;
    border-bottom: 0;
    margin: 0;
    padding: 6px 13px;
}
table td {
    border: 1px solid #dfe2e5;
    margin: 0;
    padding: 6px 13px;
}
table th:first-child,
table td:first-child {
    margin-top: 0;
}
table th:last-child,
table td:last-child {
    margin-bottom: 0;
}

.CodeMirror-lines {
    padding-left: 4px;
}

.code-tooltip {
    box-shadow: 0 1px 1px 0 rgba(0,28,36,.3);
    border-top: 1px solid #eef2f2;
}

.md-fences,
code,
tt {
    border: 1px solid #e7eaed;
    background-color: #f8f8f8;
    border-radius: 3px;
    padding: 0;
    padding: 2px 4px 0px 4px;
    font-size: 0.9em;
}

code {
    background-color: #f3f4f4;
    padding: 0 2px 0 2px;
}

.md-fences {
    margin-bottom: 15px;
    margin-top: 15px;
    padding-top: 8px;
    padding-bottom: 6px;
}


.md-task-list-item > input {
  margin-left: -1.3em;
}

@media print {
    html {
        font-size: 13px;
    }
    pre {
        page-break-inside: avoid;
        word-wrap: break-word;
    }
}

.md-fences {
	background-color: #f8f8f8;
}
#write pre.md-meta-block {
	padding: 1rem;
    font-size: 85%;
    line-height: 1.45;
    background-color: #f7f7f7;
    border: 0;
    border-radius: 3px;
    color: #777777;
    margin-top: 0 !important;
}

.mathjax-block>.code-tooltip {
	bottom: .375rem;
}

.md-mathjax-midline {
    background: #fafafa;
}

#write>h3.md-focus:before{
	left: -1.5625rem;
	top: .375rem;
}
#write>h4.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
#write>h5.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
#write>h6.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
.md-image>.md-meta {
    /*border: 1px solid #ddd;*/
    border-radius: 3px;
    padding: 2px 0px 0px 4px;
    font-size: 0.9em;
    color: inherit;
}

.md-tag {
    color: #a7a7a7;
    opacity: 1;
}

.md-toc { 
    margin-top:20px;
    padding-bottom:20px;
}

.sidebar-tabs {
    border-bottom: none;
}

#typora-quick-open {
    border: 1px solid #ddd;
    background-color: #f8f8f8;
}

#typora-quick-open-item {
    background-color: #FAFAFA;
    border-color: #FEFEFE #e5e5e5 #e5e5e5 #eee;
    border-style: solid;
    border-width: 1px;
}

/** focus mode */
.on-focus-mode blockquote {
    border-left-color: rgba(85, 85, 85, 0.12);
}

header, .context-menu, .megamenu-content, footer{
    font-family: "Segoe UI", "Arial", sans-serif;
}

.file-node-content:hover .file-node-icon,
.file-node-content:hover .file-node-open-state{
    visibility: visible;
}

.mac-seamless-mode #typora-sidebar {
    background-color: #fafafa;
    background-color: var(--side-bar-bg-color);
}

.md-lang {
    color: #b4654d;
}

/*.html-for-mac {
    --item-hover-bg-color: #E6F0FE;
}*/

#md-notification .btn {
    border: 0;
}

.dropdown-menu .divider {
    border-color: #e5e5e5;
    opacity: 0.4;
}

.ty-preferences .window-content {
    background-color: #fafafa;
}

.ty-preferences .nav-group-item.active {
    color: white;
    background: #999;
}

.menu-item-container a.menu-style-btn {
    background-color: #f5f8fa;
    background-image: linear-gradient( 180deg , hsla(0, 0%, 100%, 0.8), hsla(0, 0%, 100%, 0)); 
}


 @media print { @page {margin: 0 0 0 0;} body.typora-export {padding-left: 0; padding-right: 0;} #write {padding:0;}}
</style><title>Datalogging-Diagram</title>
</head>
<body class='typora-export'><div class='typora-export-content'>
<div id='write'  class=''><h1 id='pruebas-23-24-de-noviembre'><span>Pruebas 23-24 de noviembre</span></h1><p>&nbsp;</p><h2 id='código-issr-modificado'><span>Código ISSR modificado</span></h2><div class="md-diagram-panel md-fences-adv-panel" lang="mermaid"><svg id="mermaidChart978" width="100%" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="max-width: 933.498047px; margin: auto;" viewBox="-8 -8 933.498046875 2625.796875" role="graphics-document document" aria-roledescription="flowchart-v2"><style>#mermaidChart978{font-family:sans-serif;font-size:16px;fill:#333;}#mermaidChart978 .error-icon{fill:#552222;}#mermaidChart978 .error-text{fill:#552222;stroke:#552222;}#mermaidChart978 .edge-thickness-normal{stroke-width:2px;}#mermaidChart978 .edge-thickness-thick{stroke-width:3.5px;}#mermaidChart978 .edge-pattern-solid{stroke-dasharray:0;}#mermaidChart978 .edge-pattern-dashed{stroke-dasharray:3;}#mermaidChart978 .edge-pattern-dotted{stroke-dasharray:2;}#mermaidChart978 .marker{fill:#333333;stroke:#333333;}#mermaidChart978 .marker.cross{stroke:#333333;}#mermaidChart978 svg{font-family:sans-serif;font-size:16px;}#mermaidChart978 .label{font-family:sans-serif;color:#333;}#mermaidChart978 .cluster-label text{fill:#333;}#mermaidChart978 .cluster-label span,#mermaidChart978 p{color:#333;}#mermaidChart978 .label text,#mermaidChart978 span,#mermaidChart978 p{fill:#333;color:#333;}#mermaidChart978 .node rect,#mermaidChart978 .node circle,#mermaidChart978 .node ellipse,#mermaidChart978 .node polygon,#mermaidChart978 .node path{fill:#ECECFF;stroke:#9370DB;stroke-width:1px;}#mermaidChart978 .flowchart-label text{text-anchor:middle;}#mermaidChart978 .node .label{text-align:center;}#mermaidChart978 .node.clickable{cursor:pointer;}#mermaidChart978 .arrowheadPath{fill:#333333;}#mermaidChart978 .edgePath .path{stroke:#333333;stroke-width:2.0px;}#mermaidChart978 .flowchart-link{stroke:#333333;fill:none;}#mermaidChart978 .edgeLabel{background-color:#e8e8e8;text-align:center;}#mermaidChart978 .edgeLabel rect{opacity:0.5;background-color:#e8e8e8;fill:#e8e8e8;}#mermaidChart978 .labelBkg{background-color:rgba(232, 232, 232, 0.5);}#mermaidChart978 .cluster rect{fill:#ffffde;stroke:#aaaa33;stroke-width:1px;}#mermaidChart978 .cluster text{fill:#333;}#mermaidChart978 .cluster span,#mermaidChart978 p{color:#333;}#mermaidChart978 div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:sans-serif;font-size:12px;background:hsl(80, 100%, 96.2745098039%);border:1px solid #aaaa33;border-radius:2px;pointer-events:none;z-index:100;}#mermaidChart978 .flowchartTitleText{text-anchor:middle;font-size:18px;fill:#333;}#mermaidChart978 :root{--mermaid-alt-font-family:sans-serif;}</style><g><marker id="flowchart-pointEnd" class="marker flowchart" viewBox="0 0 10 10" refX="10" refY="5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 0 0 L 10 5 L 0 10 z" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-pointStart" class="marker flowchart" viewBox="0 0 10 10" refX="0" refY="5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 0 5 L 10 10 L 10 0 z" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-circleEnd" class="marker flowchart" viewBox="0 0 10 10" refX="11" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="flowchart-circleStart" class="marker flowchart" viewBox="0 0 10 10" refX="-1" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="flowchart-crossEnd" class="marker cross flowchart" viewBox="0 0 11 11" refX="12" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" class="arrowMarkerPath" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-crossStart" class="marker cross flowchart" viewBox="0 0 11 11" refX="-1" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" class="arrowMarkerPath" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><g class="root"><g class="clusters"></g><g class="edgePaths"><path d="M534.119140625,53L533.619140625,77.5L533.619140625,102.5" id="L-setup-loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-setup LE-loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M474.4826124237805,155L418.162109375,180L418.662109375,205.5" id="L-loop-receive.done-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-loop LE-receive.done" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M357.89806592829274,393.68908155329274L257.720703125,497.703125L257.720703125,541.453125" id="L-receive.done-packet_received-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-receive.done LE-packet_received" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M257.720703125,593.953125L257.720703125,618.953125L258.220703125,644.453125" id="L-packet_received-crc_check-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-packet_received LE-crc_check" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M298.792305375002,816.740897749998L350.23828125,900.5625L350.73828125,944.8125" id="L-crc_check-correctID-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crc_check LE-correctID" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M313.58498320287055,1085.8545144528705L255.21875,1166.2578125L255.21875,1247.5078125" id="L-correctID-IDerror-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-correctID LE-IDerror" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M390.0769682506856,1083.6691254993143L455.265625,1166.2578125L455.265625,1210.0078125" id="L-correctID-radio_hop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-correctID LE-radio_hop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M199.11936102378897,798.211157898789L70.18359375,900.5625L70.18359375,1033.41015625L70.18359375,1166.2578125L70.18359375,1247.5078125" id="L-crc_check-crcErrors-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crc_check LE-crcErrors" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M70.18359375,1300.0078125L70.18359375,1362.5078125L112.87318978658536,1387.5078125" id="L-crcErrors-wait_here-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crcErrors LE-wait_here" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M255.21875,1300.0078125L255.21875,1362.5078125L207.6472942073171,1387.5078125" id="L-IDerror-wait_here-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-IDerror LE-wait_here" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M157.697265625,1440.0078125L157.697265625,1465.0078125L157.697265625,1685.921875L157.697265625,1925.5859375L157.697265625,1995.5859375L157.697265625,2046.8359375L157.697265625,2168.31640625L157.697265625,2308.546875L157.697265625,2378.546875L157.697265625,2429.796875L157.697265625,2481.046875L157.697265625,2532.296875L612.048828125,2577.2672460130057" id="L-wait_here-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-wait_here LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M455.265625,1337.5078125L455.265625,1362.5078125L531.0887957317074,1387.5078125" id="L-radio_hop-end_receive_block-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-radio_hop LE-end_receive_block" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M504.0295445235641,369.0856898514359L785.3671875,497.703125L785.3671875,567.703125L785.3671875,618.953125L785.3671875,750.3828125L785.3671875,900.5625L785.3671875,1033.41015625L785.3671875,1166.2578125L785.3671875,1273.7578125L785.3671875,1362.5078125L700.165205792683,1387.5078125" id="L-receive.done-end_receive_block-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-receive.done LE-end_receive_block" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M610.703125,1440.0078125L610.703125,1465.0078125L611.203125,1490.5078125" id="L-end_receive_block-delay-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-end_receive_block LE-delay" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M564.0905701922363,1835.2233826922363L534.822265625,1925.5859375L534.822265625,1969.3359375" id="L-delay-packet_missed-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-delay LE-packet_missed" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M534.822265625,2021.8359375L534.822265625,2046.8359375L535.322265625,2072.3359375" id="L-packet_missed-hop_retry-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-packet_missed LE-hop_retry" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M580.2088009067626,2220.4103397182375L656.822265625,2308.546875L656.822265625,2352.296875" id="L-hop_retry-resync-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-resync" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M475.8147504783674,2205.7893598533674L309.123046875,2308.546875L309.123046875,2378.546875L309.123046875,2429.796875L404.08203125,2457.790311204514" id="L-hop_retry-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M496.0858650950851,2226.060474470085L438.705078125,2308.546875L438.705078125,2352.296875" id="L-hop_retry-reset_hop_count-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-reset_hop_count" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M438.705078125,2404.796875L438.705078125,2429.796875L460.2990186737805,2454.796875" id="L-reset_hop_count-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-reset_hop_count LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M656.822265625,2404.796875L656.822265625,2429.796875L561.86328125,2457.790311204514" id="L-resync-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-resync LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M738.939453125,2570.110842846593L917.498046875,2532.296875L917.498046875,2481.046875L917.498046875,2429.796875L917.498046875,2378.546875L917.498046875,2308.546875L917.498046875,2168.31640625L917.498046875,2046.8359375L917.498046875,1995.5859375L917.498046875,1925.5859375L917.498046875,1685.921875L917.498046875,1465.0078125L917.498046875,1413.7578125L917.498046875,1362.5078125L917.498046875,1273.7578125L917.498046875,1166.2578125L917.498046875,1033.41015625L917.498046875,900.5625L917.498046875,750.3828125L917.498046875,618.953125L917.498046875,567.703125L917.498046875,497.703125L917.498046875,329.4765625L917.498046875,180L600.380859375,137.66306615245287" id="L-end_loop-loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-end_loop LE-loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M482.97265625,2507.296875L482.97265625,2532.296875L612.048828125,2566.6574754808717" id="L-radio_hop_again-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-radio_hop_again LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M693.0586937733963,1800.4803687266037L782.701171875,1925.5859375L782.701171875,1995.5859375L782.701171875,2046.8359375L782.701171875,2168.31640625L782.701171875,2308.546875L782.701171875,2378.546875L782.701171875,2429.796875L782.701171875,2481.046875L782.701171875,2532.296875L730.405059070122,2557.296875" id="L-delay-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-delay LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path></g><g class="edgeLabels"><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(257.720703125, 497.703125)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(350.23828125, 900.5625)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(255.21875, 1166.2578125)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(455.265625, 1166.2578125)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(70.18359375, 1033.41015625)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(785.3671875, 900.5625)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(534.822265625, 1925.5859375)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(656.822265625, 2308.546875)"><g class="label" transform="translate(-6.6796875, -18.75)"><foreignObject width="13.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">1</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(309.123046875, 2378.546875)"><g class="label" transform="translate(-17.34375, -18.75)"><foreignObject width="34.6875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">2-4</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(438.705078125, 2308.546875)"><g class="label" transform="translate(-13.6875, -18.75)"><foreignObject width="27.375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">&gt;4</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(782.701171875, 2308.546875)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g></g><g class="nodes"><g class="node default default flowchart-label" id="flowchart-setup-29212" transform="translate(533.619140625, 26.25)"><polygon points="0,0 355.1953125,0 355.1953125,-52.5 0,-52.5 0,0 -8,0 363.1953125,0 363.1953125,-52.5 -8,-52.5 -8,0" class="label-container" transform="translate(-177.59765625,26.25)" style=""></polygon><g class="label" style="" transform="translate(-170.09765625, -18.75)"><rect></rect><foreignObject width="340.1953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">initialize_radio &amp; set_channel(0)</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-loop-29214" transform="translate(533.619140625, 128.75)"><rect style="" rx="26.25" ry="26.25" x="-66.76171875" y="-26.25" width="133.5234375" height="52.5"></rect><g class="label" style="" transform="translate(-52.69921875, -18.75)"><rect></rect><foreignObject width="105.3984375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">loop_start</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-receive.done-29216" transform="translate(418.162109375, 329.4765625)"><polygon points="124.4765625,0 248.953125,-124.4765625 124.4765625,-248.953125 0,-124.4765625" class="label-container" transform="translate(-124.4765625,124.4765625)" style=""></polygon><g class="label" style="" transform="translate(-90.7265625, -18.75)"><rect></rect><foreignObject width="181.453125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packet received?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-packet_received-29219" transform="translate(257.720703125, 567.703125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-112.8984375" y="-26.25" width="225.796875" height="52.5"></rect><g class="label" style="" transform="translate(-105.3984375, -18.75)"><rect></rect><foreignObject width="210.796875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packetsReceived++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-crc_check-29220" transform="translate(257.720703125, 750.3828125)"><polygon points="106.4296875,0 212.859375,-106.4296875 106.4296875,-212.859375 0,-106.4296875" class="label-container" transform="translate(-106.4296875,106.4296875)" style=""></polygon><g class="label" style="" transform="translate(-72.6796875, -18.75)"><rect></rect><foreignObject width="145.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">correct CRC?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-correctID-29226" transform="translate(350.23828125, 1033.41015625)"><polygon points="89.09765625,0 178.1953125,-89.09765625 89.09765625,-178.1953125 0,-89.09765625" class="label-container" transform="translate(-89.09765625,89.09765625)" style=""></polygon><g class="label" style="" transform="translate(-55.34765625, -18.75)"><rect></rect><foreignObject width="110.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">correctID?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-IDerror-29228" transform="translate(255.21875, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-64.8515625" y="-26.25" width="129.703125" height="52.5"></rect><g class="label" style="" transform="translate(-57.3515625, -18.75)"><rect></rect><foreignObject width="114.703125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">IDerrors++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-radio_hop-29232" transform="translate(455.265625, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-85.1953125" y="-63.75" width="170.390625" height="127.5"></rect><g class="label" style="" transform="translate(-77.6953125, -56.25)"><rect></rect><foreignObject width="155.390625" height="112.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">next_channel<br/>    					hop_count=1<br/>    					last_time=time</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-crcErrors-29233" transform="translate(70.18359375, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-70.18359375" y="-26.25" width="140.3671875" height="52.5"></rect><g class="label" style="" transform="translate(-62.68359375, -18.75)"><rect></rect><foreignObject width="125.3671875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">crcErrors++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-wait_here-29237" transform="translate(157.697265625, 1413.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-143.58984375" y="-26.25" width="287.1796875" height="52.5"></rect><g class="label" style="" transform="translate(-136.08984375, -18.75)"><rect></rect><foreignObject width="272.1796875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">wait on the same channel</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-end_receive_block-29242" transform="translate(610.703125, 1413.7578125)"><rect style="" rx="26.25" ry="26.25" x="-114.12890625" y="-26.25" width="228.2578125" height="52.5"></rect><g class="label" style="" transform="translate(-100.06640625, -18.75)"><rect></rect><foreignObject width="200.1328125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">end_receive_block</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-end_loop-29244" transform="translate(675.494140625, 2583.546875)"><rect style="" rx="26.25" ry="26.25" x="-63.4453125" y="-26.25" width="126.890625" height="52.5"></rect><g class="label" style="" transform="translate(-49.3828125, -18.75)"><rect></rect><foreignObject width="98.765625" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">end_loop</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-delay-29250" transform="translate(610.703125, 1685.921875)"><polygon points="195.9140625,0 391.828125,-195.9140625 195.9140625,-391.828125 0,-195.9140625" class="label-container" transform="translate(-195.9140625,195.9140625)" style=""></polygon><g class="label" style="" transform="translate(-143.4140625, -37.5)"><rect></rect><foreignObject width="286.828125" height="75"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">received pakect on time?<br/>    			(last_time-time&gt;2555+200)</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-hop_retry-29252" transform="translate(534.822265625, 2168.31640625)"><polygon points="96.48046875,0 192.9609375,-96.48046875 96.48046875,-192.9609375 0,-96.48046875" class="label-container" transform="translate(-96.48046875,96.48046875)" style=""></polygon><g class="label" style="" transform="translate(-62.73046875, -18.75)"><rect></rect><foreignObject width="125.4609375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">hop_count?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-radio_hop_again-29253" transform="translate(482.97265625, 2481.046875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-78.890625" y="-26.25" width="157.78125" height="52.5"></rect><g class="label" style="" transform="translate(-71.390625, -18.75)"><rect></rect><foreignObject width="142.78125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">next_channel</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-resync-29254" transform="translate(656.822265625, 2378.546875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-90.87890625" y="-26.25" width="181.7578125" height="52.5"></rect><g class="label" style="" transform="translate(-83.37890625, -18.75)"><rect></rect><foreignObject width="166.7578125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">numResyncs++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-packet_missed-29255" transform="translate(534.822265625, 1995.5859375)"><rect class="basic label-container" style="" rx="0" ry="0" x="-100.88671875" y="-26.25" width="201.7734375" height="52.5"></rect><g class="label" style="" transform="translate(-93.38671875, -18.75)"><rect></rect><foreignObject width="186.7734375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packetsMissed++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-reset_hop_count-29264" transform="translate(438.705078125, 2378.546875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-77.23828125" y="-26.25" width="154.4765625" height="52.5"></rect><g class="label" style="" transform="translate(-69.73828125, -18.75)"><rect></rect><foreignObject width="139.4765625" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">hop_count=0</span></div></foreignObject></g></g></g></g></g></svg></div><h2 id='pruebas-2-estaciones'><span>Pruebas 2 estaciones</span></h2><ul><li><p><span>Midiendo de estación ID=1 y otra estación ID=2</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666667px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation" style=""><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">14725225:  Hopped channel and ready to receive.</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 4840 packetsMissed: 519 numResyncs: 205 crcErrors: 302 idErrors: 512</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 14725225 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 14725225/(2563+(1/16)) = 5745</span></p></li><li><p><span>Paquetes recibidos: 4840</span></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 4840+519=5.359</span></p></li><li><p><span>Resincronizaciones: 205 (cada resincronización ha requerido en promedio 2,53 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 302</span></p></li><li><p><span>Errores de ID: 512</span></p></li><li><p><span>Paquetes correctos: 4840 - 302 - 512 = 4026</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 4026/5.359=75%</span></p></li></ul></li><li><p><span>Midiendo de estación ID=1 y la otra estación con ID=6</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666664px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 1301 packetsMissed: 106 numResyncs: 45 receivedStreak: 66 crcErrors: 68 idErrors: 156</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">3894281:  0 - Data: E1 0 0 80 5 0 A 37 FF FF &nbsp; RSSI: -53 CRC: OK</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 3894281 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 3894281/(2563+(1/16)) = 1519</span></p></li><li><p><span>Paquetes recibidos: 1301</span></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 1301+106=1407</span></p></li><li><p><span>Resincronizaciones: 45 (cada resincronización ha requerido en promedio 2,35 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 68</span></p></li><li><p><span>Errores de ID:156</span></p></li><li><p><span>Paquetes correctos:1301 - 68 - 156 = 1077</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 1077/1407=76,5%</span></p></li></ul></li><li><p><span>Repitiendo y dejando toda la noche</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666668px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 21205 packetsMissed: 2501 numResyncs: 975 receivedStreak: 66 crcErrors: 1483 idErrors: 2564</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">65581295</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 65581295 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 65581295/(2563+(1/16)) = 25587</span></p></li><li><p><span>Paquetes recibidos: 21205 </span><strong><span>(82%)</span></strong></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 21205+2501=23.706</span></p></li><li><p><span>Resincronizaciones: 975 (cada resincronización ha requerido en promedio 2,56 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 1483 </span><strong><span>(6,9%)</span></strong></p></li><li><p><span>Errores de ID: 2564 </span><strong><span>(12%)</span></strong></p></li><li><p><span>Paquetes correctos: 21205 - 1483 - 2564 = 17158</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 17158/23706=</span><strong><span>72,3%</span></strong></p></li></ul></li></ul><h2 id='pruebas-3-estaciones'><span>Pruebas 3 estaciones </span></h2><ul><li><p><span>ID=1 + ID=0, ID=6</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666667px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre><span>xxxxxxxxxx</span></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">3706815:  3 - Data: E6 0 0 80 5 0 C2 76 FF FF &nbsp; RSSI: -54 CRC: OK</span></pre></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">:  Wrong Station ID or CRC waiting in the same channel</span></pre><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 1194 packetsMissed: 139 numResyncs: 57 receivedStreak: 13 crcErrors: 160</span></pre><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;"> idErrors: 317</span></pre></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 117px;"></div><div class="CodeMirror-gutters" style="display: none; height: 117px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 3706815 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 3706815/(2563+(1/16)) = 1446</span></p></li><li><p><span>Paquetes recibidos: 1194 </span><strong><span>(82%)</span></strong></p></li><li><p><span>Paquetes que se deberían haber recibido según el código (recibidos + missed) :1194+139=1333</span></p></li><li><p><span>Resincronizaciones: 57 (cada resincronización ha requerido en promedio 2,43 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 160 </span><strong><span>(13,4%)</span></strong></p></li><li><p><span>Errores de ID: 317 </span><strong><span>(26,5%)</span></strong></p></li><li><p><span>Paquetes correctos: 1194 -160 - 317=717</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 717/1333 = </span><strong><span>53%</span></strong></p></li></ul></li><li><p><span>ID=1 + ID=0, ID=6 (toda la noche) --&gt; pendiente</span></p></li></ul></div></div>
</body>
</html>







```
<!doctype html>
<html>
<head>
<meta charset='UTF-8'><meta name='viewport' content='width=device-width initial-scale=1'>

<link href='https://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,700,400&subset=latin,latin-ext' rel='stylesheet' type='text/css' /><style type='text/css'>html {overflow-x: initial !important;}:root { --mermaid-theme: default; --mermaid-sequence-numbers: off; --mermaid-flowchart-curve: linear; --mermaid--gantt-left-padding: 75; --sequence-theme: simple; }
.tooltip[data-v-70836592] { cursor: pointer; position: relative; display: block; width: 100%; text-align: center; z-index: 10; }
.tooltip span[data-v-70836592] { border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: currentcolor; }
.tooltip[data-v-70836592]::after { display: none; content: attr(data-tooltip); background: rgb(232, 233, 233); max-width: 500px; width: 200px; position: absolute; left: -200px; right: 0px; margin: auto; opacity: 0; height: auto; font-size: 14px; padding: 10px; border-radius: 4px; color: rgb(17, 17, 17); text-align: left; }
.tooltip.bottom[data-v-70836592]::after { top: 80%; transition: opacity 0.3s 0.3s, top 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) 0.3s; }
.tooltip.bottom[data-v-70836592]:hover::after { display: block; top: 130%; opacity: 1; }
.lifeline .line[data-v-6efc771f] { background-size: 1px 20px; }
.zenuml .interaction { border: 0px dashed transparent; }
.zenuml .interaction.sync { border-right-width: 7px; }
.zenuml .interaction.inited-from-occurrence, .zenuml .interaction.self-invocation { border-left-width: 7px; }
.zenuml .interaction.return { border-left-width: 7px; border-right-width: 7px; }
.zenuml .interaction.return-to-start { border-left-width: 0px; }
.zenuml .interaction:hover { cursor: pointer; }
.zenuml .message { position: relative; }
.zenuml .message > .name { text-align: center; }
.zenuml .interaction.right-to-left > .occurrence { left: -14px; }
.zenuml .interaction.self > .occurrence { left: -8px; margin-top: -10px; }
.zenuml .fragment { border-width: 1px; margin: 8px 0px 0px; padding-bottom: 10px; }
.zenuml .sequence-diagram * { box-sizing: inherit; }
.zenuml .sequence-diagram { line-height: normal; }
.zenuml .participant { border-width: 2px; padding: 8px 4px; min-width: 88px; max-width: 250px; text-align: center; pointer-events: all; }
.no-fill svg.arrow polyline[data-v-ca07199a] { fill: none !important; }
.async > .message > .point > svg.arrow > polyline[data-v-ca07199a] { fill: none; }
.right-to-left.point[data-v-ca07199a] { left: 0px; right: auto; }
.right-to-left.point > svg > polyline.right[data-v-ca07199a] { display: none; }
.right-to-left.point > svg > polyline.left[data-v-ca07199a] { display: inline; }
.point > svg > polyline.left[data-v-ca07199a] { display: none; }
:root { --color-bg-base: #fff; --color-text-base: #000; --color-text-secondary: #333; --color-border-base: #000; }
.theme-default { --color-text-base: #222; --color-border-base: #666; --color-message-arrow: #000; --color-bg-occurrence: #dedede; }
.theme-mermaid { --color-bg-base: #fff; --color-text-base: #222; --color-border-base: #666; --color-bg-occurrence: #dedede; }
.theme-mermaid .footer { visibility: hidden; }
.theme-darcula { --color-bg-canvas: #ffffff; --color-bg-frame: #2b2b2b; --color-border-frame: #cccccc; --color-bg-title: #2b2b2b; --color-text-title: #f8f8f2; --color-bg-participant: #44475a; --color-text-participant: #f8f8f2; --color-border-participant: #cccccc; --color-text-message: #ffb86c; --color-message-arrow: #cccccc; --color-bg-message-hover: #a6d2ff; --color-text-message-hover: #174ad4; --color-text-comment: #666666; --color-bg-fragment-header: #44475a; --color-text-fragment: #8be9fd; --color-border-fragment: #cccccc; --color-bg-occurrence: #44475a; --color-border-occurrence: #555555; --color-text-link: #a6d2ff; --color-text-control: #e2ba88; }
.theme-sky { --color-bg-canvas: #ffffff; --color-bg-frame: #ffffff; --color-border-frame: #cccccc; --color-bg-title: #deecfb; --color-text-title: #032c72; --color-bg-participant: #deecfb; --color-text-participant: #032c72; --color-border-participant: #032c72; --color-text-message: #032c72; --color-message-arrow: #032c72; --color-bg-message-hover: #a6d2ff; --color-text-message-hover: #174ad4; --color-text-comment: #666666; --color-bg-fragment-header: #f0f0f0; --color-text-fragment: #032c72; --color-border-fragment: #032c72; --color-bg-occurrence: #deecfb; --color-border-occurrence: #555555; --color-text-link: #a6d2ff; --color-text-control: #a6d2ff; }
.theme-idle-afternoon { --color-bg-canvas: #d8dad9; --color-bg-frame: #d8dad9; --color-border-frame: #324939; --color-bg-title: #d8dad9; --color-text-title: #182e27; --color-bg-participant: #f3f5f7; --color-text-participant: #182e27; --color-border-participant: #182e27; --color-text-message: #030809; --color-message-arrow: #324939; --color-bg-message-hover: #aea690; --color-text-message-hover: #000000; --color-text-comment: #030809; --color-bg-fragment-header: #f0f0f0; --color-text-fragment: #182e27; --color-border-fragment: #344337; --color-bg-occurrence: #f3f5f7; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #97a49b; }
.theme-coles { --color-bg-canvas: #ffffff; --color-bg-frame: #ffde00; --color-border-frame: #ee141f; --color-bg-title: #ffffff; --color-text-title: #182e27; --color-bg-participant: #f3f5f7; --color-text-participant: #000000; --color-border-participant: #344337; --color-text-message: #000000; --color-message-arrow: #000000; --color-bg-message-hover: #ee141f; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #ee141f; --color-text-fragment: #ffffff; --color-border-fragment: #ee141f; --color-bg-occurrence: #f3f5f7; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #97a49b; }
.theme-coles .footer { background-color: rgb(255, 255, 255); }
.theme-woolworths { --color-bg-canvas: #e6eaf3; --color-bg-frame: #e6eaf3; --color-border-frame: #049e50; --color-bg-title: #ffffff; --color-text-title: #12522f; --color-bg-participant: #aacb51; --color-text-participant: #000000; --color-border-participant: #344337; --color-text-message: #12522f; --color-message-arrow: #049e50; --color-bg-message-hover: #fffefb; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #aacb51; --color-text-fragment: #12522f; --color-border-fragment: #049e50; --color-bg-occurrence: #aacb51; --color-border-occurrence: #344337; --color-text-link: #344337; --color-text-control: #335c80; }
.theme-anz { --color-bg-canvas: #ffffff; --color-bg-frame: #ffffff; --color-border-frame: #089fd8; --color-bg-title: #ffffff; --color-text-title: #006e9c; --color-bg-participant: #fcfdf8; --color-text-participant: #00abd8; --color-border-participant: #00abd8; --color-text-message: #42a4e0; --color-message-arrow: #089fd8; --color-bg-message-hover: #006e9c; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: #42a4e0; --color-text-fragment: #006e9c; --color-border-fragment: #089fd8; --color-bg-occurrence: #fcfdf8; --color-border-occurrence: #006e9c; --color-text-link: #c9d8e7; --color-text-control: #335c80; }
.theme-anz .fragment .header { color: rgb(255, 255, 255); }
.theme-nab { --color-bg-canvas: #f2f4f6; --color-bg-participant: #c30000; --color-text-participant: #ffffff; --color-border-participant: #c30000; --color-text-message: #c30000; --color-bg-fragment-header: #c30000; --color-text-fragment-header: #ffffff; }
.theme-google { --color-white: #ffffff; --color-red-google: #db4437; --color-blue-google: #4285f4; --color-green-google: #0f9d58; --color-yellow-google: #f4b400; --color-bg-canvas: var(--color-white); --color-bg-frame: #f2f4f6; --color-border-frame: #4285f4; --color-bg-title: #4285f4; --color-text-title: #ffffff; --color-bg-participant: var(--color-red-google); --color-text-participant: #ffffff; --color-border-participant: #4285f4; --color-text-message: var(--color-red-google); --color-message-arrow: var(--color-red-google); --color-bg-message-hover: #fffefb; --color-text-message-hover: #ffffff; --color-text-comment: #000000; --color-bg-fragment-header: var(--color-green-google); --color-text-fragment: #ffffff; --color-border-fragment: #4285f4; --color-bg-occurrence: #f4b400; --color-border-occurrence: #4285f4; --color-text-link: #c9d8e7; --color-text-control: #335c80; }
*, ::before, ::after { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
::-webkit-backdrop { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
::backdrop { --tw-border-spacing-x: 0; --tw-border-spacing-y: 0; --tw-translate-x: 0; --tw-translate-y: 0; --tw-rotate: 0; --tw-skew-x: 0; --tw-skew-y: 0; --tw-scale-x: 1; --tw-scale-y: 1; --tw-pan-x: ; --tw-pan-y: ; --tw-pinch-zoom: ; --tw-scroll-snap-strictness: proximity; --tw-ordinal: ; --tw-slashed-zero: ; --tw-numeric-figure: ; --tw-numeric-spacing: ; --tw-numeric-fraction: ; --tw-ring-inset: ; --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgb(59 130 246 / 0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; --tw-shadow: 0 0 #0000; --tw-shadow-colored: 0 0 #0000; --tw-blur: ; --tw-brightness: ; --tw-contrast: ; --tw-grayscale: ; --tw-hue-rotate: ; --tw-invert: ; --tw-saturate: ; --tw-sepia: ; --tw-drop-shadow: ; --tw-backdrop-blur: ; --tw-backdrop-brightness: ; --tw-backdrop-contrast: ; --tw-backdrop-grayscale: ; --tw-backdrop-hue-rotate: ; --tw-backdrop-invert: ; --tw-backdrop-opacity: ; --tw-backdrop-saturate: ; --tw-backdrop-sepia: ; }
.container { width: 100%; }
@media (min-width: 640px) {
  .container { max-width: 640px; }
}
@media (min-width: 768px) {
  .container { max-width: 768px; }
}
@media (min-width: 1024px) {
  .container { max-width: 1024px; }
}
@media (min-width: 1280px) {
  .container { max-width: 1280px; }
}
@media (min-width: 1536px) {
  .container { max-width: 1536px; }
}
.zenuml .sr-only { position: absolute; width: 1px; height: 1px; padding: 0px; margin: -1px; overflow: hidden; clip: rect(0px, 0px, 0px, 0px); white-space: nowrap; border-width: 0px; }
.zenuml .pointer-events-none { pointer-events: none; }
.zenuml .pointer-events-auto { pointer-events: auto; }
.zenuml .invisible { visibility: hidden; }
.zenuml .fixed { position: fixed; }
.zenuml .absolute { position: absolute; }
.zenuml .relative { position: relative; }
.zenuml .sticky { position: sticky; }
.zenuml .inset-0 { inset: 0px; }
.zenuml .bottom-1 { bottom: 0.25rem; }
.zenuml .left-1 { left: 0.25rem; }
.zenuml .right-1 { right: 0.25rem; }
.zenuml .top-0 { top: 0px; }
.zenuml .top-8 { top: 2rem; }
.zenuml .left-1\/2 { left: 50%; }
.zenuml .right-0 { right: 0px; }
.zenuml .right-\[100\%\] { right: 100%; }
.zenuml .left-full { left: 100%; }
.zenuml .z-40 { z-index: 40; }
.zenuml .z-10 { z-index: 10; }
.zenuml .col-span-2 { grid-column-start: span 2; grid-column-end: span 2; }
.zenuml .col-span-1 { grid-column-start: span 1; grid-column-end: span 1; }
.zenuml .col-span-4 { grid-column-start: span 4; grid-column-end: span 4; }
.zenuml .float-right { float: right; }
.zenuml .m-1 { margin: 0.25rem; }
.zenuml .m-2 { margin: 0.5rem; }
.zenuml .m-auto { margin: auto; }
.zenuml .mx-auto { margin-left: auto; margin-right: auto; }
.zenuml .mx-2 { margin-left: 0.5rem; margin-right: 0.5rem; }
.zenuml .-my-px { margin-top: -1px; margin-bottom: -1px; }
.zenuml .ml-3 { margin-left: 0.75rem; }
.zenuml .mr-1 { margin-right: 0.25rem; }
.zenuml .ml-4 { margin-left: 1rem; }
.zenuml .mt-3 { margin-top: 0.75rem; }
.zenuml .mt-4 { margin-top: 1rem; }
.zenuml .mb-4 { margin-bottom: 1rem; }
.zenuml .mt-8 { margin-top: 2rem; }
.zenuml .-mt-12 { margin-top: -3rem; }
.zenuml .mt-1 { margin-top: 0.25rem; }
.zenuml .mb-0 { margin-bottom: 0px; }
.zenuml .mt-2 { margin-top: 0.5rem; }
.zenuml .box-border { box-sizing: border-box; }
.zenuml .block { display: block; }
.zenuml .inline-block { display: inline-block; }
.zenuml .inline { display: inline; }
.zenuml .flex { display: flex; }
.zenuml .inline-flex { display: inline-flex; }
.zenuml .table { display: table; }
.zenuml .grid { display: grid; }
.zenuml .contents { display: contents; }
.zenuml .hidden { display: none; }
.zenuml .h-10 { height: 2.5rem; }
.zenuml .h-5 { height: 1.25rem; }
.zenuml .h-0 { height: 0px; }
.zenuml .h-screen { height: 100vh; }
.zenuml .h-full { height: 100%; }
.zenuml .h-4 { height: 1rem; }
.zenuml .h-6 { height: 1.5rem; }
.zenuml .h-12 { height: 3rem; }
.zenuml .h-8 { height: 2rem; }
.zenuml .h-14 { height: 3.5rem; }
.zenuml .h-3 { height: 0.75rem; }
.zenuml .min-h-screen { min-height: 100vh; }
.zenuml .min-h-\[1em\] { min-height: 1em; }
.zenuml .w-full { width: 100%; }
.zenuml .w-96 { width: 24rem; }
.zenuml .w-28 { width: 7rem; }
.zenuml .w-4 { width: 1rem; }
.zenuml .w-6 { width: 1.5rem; }
.zenuml .w-8 { width: 2rem; }
.zenuml .w-11\/12 { width: 91.666667%; }
.zenuml .w-px { width: 1px; }
.zenuml .w-3 { width: 0.75rem; }
.zenuml .min-w-\[100px\] { min-width: 100px; }
.zenuml .max-w-full { max-width: 100%; }
.zenuml .max-w-7xl { max-width: 80rem; }
.zenuml .flex-shrink-0, .zenuml .shrink-0 { flex-shrink: 0; }
.zenuml .flex-grow, .zenuml .grow { flex-grow: 1; }
.zenuml .origin-top-left { transform-origin: left top; }
.zenuml .-translate-x-1\/2 { --tw-translate-x: -50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-full { --tw-translate-y: -100%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-8 { --tw-translate-y: -2rem; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-x-full { --tw-translate-x: -100%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .-translate-y-1\/2 { --tw-translate-y: -50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .translate-y-1\/2 { --tw-translate-y: 50%; transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .transform { transform: translate(var(--tw-translate-x),var(--tw-translate-y)) rotate(var(--tw-rotate)) skew(var(--tw-skew-x)) skewY(var(--tw-skew-y)) scaleX(var(--tw-scale-x)) scaleY(var(--tw-scale-y)); }
.zenuml .cursor-pointer { cursor: pointer; }
.zenuml .select-none { -webkit-user-select: none; }
.zenuml .resize { resize: both; }
.zenuml .grid-cols-6 { grid-template-columns: repeat(6, minmax(0px, 1fr)); }
.zenuml .grid-cols-4 { grid-template-columns: repeat(4, minmax(0px, 1fr)); }
.zenuml .grid-cols-1 { grid-template-columns: repeat(1, minmax(0px, 1fr)); }
.zenuml .flex-row-reverse { flex-direction: row-reverse; }
.zenuml .flex-col { flex-direction: column; }
.zenuml .flex-nowrap { flex-wrap: nowrap; }
.zenuml .items-start { align-items: flex-start; }
.zenuml .items-end { align-items: flex-end; }
.zenuml .items-center { align-items: center; }
.zenuml .justify-center { justify-content: center; }
.zenuml .justify-between { justify-content: space-between; }
.zenuml .justify-around { justify-content: space-around; }
.zenuml .gap-5 { row-gap: 1.25rem; column-gap: 1.25rem; }
.zenuml .overflow-hidden { overflow: hidden; }
.zenuml .overflow-visible { overflow: visible; }
.zenuml .overflow-y-auto { overflow-y: auto; }
.zenuml .whitespace-normal { white-space: normal; }
.zenuml .whitespace-nowrap { white-space: nowrap; }
.zenuml .rounded { border-radius: 0.25rem; }
.zenuml .rounded-sm { border-radius: 0.125rem; }
.zenuml .rounded-md { border-radius: 0.375rem; }
.zenuml .rounded-lg { border-radius: 0.5rem; }
.zenuml .rounded-t { border-top-left-radius: 0.25rem; border-top-right-radius: 0.25rem; }
.zenuml .rounded-t-md { border-top-left-radius: 0.375rem; border-top-right-radius: 0.375rem; }
.zenuml .rounded-b-md { border-bottom-right-radius: 0.375rem; border-bottom-left-radius: 0.375rem; }
.zenuml .border-2 { border-width: 2px; }
.zenuml .border { border-width: 1px; }
.zenuml .border-b-2 { border-bottom-width: 2px; }
.zenuml .border-b { border-bottom-width: 1px; }
.zenuml .border-t { border-top-width: 1px; }
.zenuml .border-r { border-right-width: 1px; }
.zenuml .border-solid { border-style: solid; }
.zenuml .border-dashed { border-style: dashed; }
.zenuml .border-red-900 { --tw-border-opacity: 1; border-color: rgb(127 29 29 / var(--tw-border-opacity)); }
.zenuml .border-skin-frame { border-color: var(--color-border-frame, var(--color-border-base, #000)); }
.zenuml .border-gray-200 { --tw-border-opacity: 1; border-color: rgb(229 231 235 / var(--tw-border-opacity)); }
.zenuml .border-skin-participant { border-color: var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-fragment { border-color: var(--color-border-fragment, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-message-arrow { border-color: var(--color-message-arrow, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .border-skin-occurrence { border-color: var(--color-border-occurrence, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .bg-gray-50 { --tw-bg-opacity: 1; background-color: rgb(249 250 251 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-canvas { background-color: var(--color-bg-canvas, var(--color-bg-base, #fff)); }
.zenuml .bg-skin-frame { background-color: var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff))); }
.zenuml .bg-skin-title { background-color: var(--color-bg-title, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff)))); }
.zenuml .bg-skin-base { background-color: var(--color-bg-base); }
.zenuml .bg-green-200 { --tw-bg-opacity: 1; background-color: rgb(187 247 208 / var(--tw-bg-opacity)); }
.zenuml .bg-white { --tw-bg-opacity: 1; background-color: rgb(255 255 255 / var(--tw-bg-opacity)); }
.zenuml .bg-gray-500 { --tw-bg-opacity: 1; background-color: rgb(107 114 128 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-lifeline { background-color: var(--color-border-participant, var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000)))); }
.zenuml .bg-skin-participant { background-color: var(--color-bg-participant, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff)))); }
.zenuml .bg-gray-400 { --tw-bg-opacity: 1; background-color: rgb(156 163 175 / var(--tw-bg-opacity)); }
.zenuml .bg-skin-divider { background-color: var(--color-border-participant, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .bg-skin-fragment-header { background-color: var(--color-bg-fragment-header, transparent); }
.zenuml .bg-skin-occurrence { background-color: var(--color-bg-occurrence, var(--color-bg-participant, var(--color-bg-frame, var(--color-bg-canvas, var(--color-bg-base, #fff))))); }
.zenuml .bg-opacity-75 { --tw-bg-opacity: 0.75; }
.zenuml .fill-current { fill: currentcolor; }
.zenuml .fill-none { fill: none; }
.zenuml .stroke-current { stroke: currentcolor; }
.zenuml .stroke-2 { stroke-width: 2; }
.zenuml .object-contain { object-fit: contain; }
.zenuml .p-1 { padding: 0.25rem; }
.zenuml .p-2 { padding: 0.5rem; }
.zenuml .p-0 { padding: 0px; }
.zenuml .px-1 { padding-left: 0.25rem; padding-right: 0.25rem; }
.zenuml .px-3 { padding-left: 0.75rem; padding-right: 0.75rem; }
.zenuml .py-1 { padding-top: 0.25rem; padding-bottom: 0.25rem; }
.zenuml .px-2 { padding-left: 0.5rem; padding-right: 0.5rem; }
.zenuml .py-2 { padding-top: 0.5rem; padding-bottom: 0.5rem; }
.zenuml .px-4 { padding-left: 1rem; padding-right: 1rem; }
.zenuml .py-5 { padding-top: 1.25rem; padding-bottom: 1.25rem; }
.zenuml .px-px { padding-left: 1px; padding-right: 1px; }
.zenuml .pb-8 { padding-bottom: 2rem; }
.zenuml .pt-8 { padding-top: 2rem; }
.zenuml .pt-4 { padding-top: 1rem; }
.zenuml .pb-20 { padding-bottom: 5rem; }
.zenuml .pb-4 { padding-bottom: 1rem; }
.zenuml .pb-32 { padding-bottom: 8rem; }
.zenuml .pb-2 { padding-bottom: 0.5rem; }
.zenuml .pr-24 { padding-right: 6rem; }
.zenuml .pt-24 { padding-top: 6rem; }
.zenuml .pb-10 { padding-bottom: 2.5rem; }
.zenuml .pr-1 { padding-right: 0.25rem; }
.zenuml .text-left { text-align: left; }
.zenuml .text-center { text-align: center; }
.zenuml .align-bottom { vertical-align: bottom; }
.zenuml .text-xs { font-size: 0.75rem; line-height: 1rem; }
.zenuml .text-sm { font-size: 0.875rem; line-height: 1.25rem; }
.zenuml .text-base { font-size: 1rem; line-height: 1.5rem; }
.zenuml .text-xl { font-size: 1.25rem; line-height: 1.75rem; }
.zenuml .text-lg { font-size: 1.125rem; line-height: 1.75rem; }
.zenuml .font-bold { font-weight: 700; }
.zenuml .font-semibold { font-weight: 600; }
.zenuml .font-medium { font-weight: 500; }
.zenuml .font-thin { font-weight: 100; }
.zenuml .italic { font-style: italic; }
.zenuml .leading-6 { line-height: 1.5rem; }
.zenuml .leading-4 { line-height: 1rem; }
.zenuml .text-skin-title { color: var(--color-text-title, var(--color-text-message, var(--color-text-base, #000))); }
.zenuml .text-skin-control { color: var(--color-text-control, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-skin-link { color: var(--color-text-link, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-green-700 { --tw-text-opacity: 1; color: rgb(21 128 61 / var(--tw-text-opacity)); }
.zenuml .text-white { --tw-text-opacity: 1; color: rgb(255 255 255 / var(--tw-text-opacity)); }
.zenuml .text-gray-900 { --tw-text-opacity: 1; color: rgb(17 24 39 / var(--tw-text-opacity)); }
.zenuml .text-gray-400 { --tw-text-opacity: 1; color: rgb(156 163 175 / var(--tw-text-opacity)); }
.zenuml .text-gray-600 { --tw-text-opacity: 1; color: rgb(75 85 99 / var(--tw-text-opacity)); }
.zenuml .text-gray-500 { --tw-text-opacity: 1; color: rgb(107 114 128 / var(--tw-text-opacity)); }
.zenuml .text-skin-participant { color: var(--color-text-participant, var(--color-text-message, var(--color-text-base, #000))); }
.zenuml .text-skin-base { color: var(--color-text-base); }
.zenuml .text-skin-message { color: var(--color-text-message, var(--color-text-base, #000)); }
.zenuml .text-skin-comment { color: var(--color-text-comment, var(--color-text-secondary, var(--color-text-base, #000))); }
.zenuml .text-skin-fragment-header { color: var(--color-text-fragment-header, var(--color-text-message, #000)); }
.zenuml .text-skin-fragment { color: var(--color-text-fragment, var(--color-text-message, #000)); }
.zenuml .text-skin-message-arrow { color: var(--color-message-arrow, var(--color-border-frame, var(--color-border-base, #000))); }
.zenuml .opacity-50 { opacity: 0.5; }
.zenuml .shadow-sm { --tw-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05); --tw-shadow-colored: 0 1px 2px 0 var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow-xl { --tw-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1); --tw-shadow-colored: 0 20px 25px -5px var(--tw-shadow-color), 0 8px 10px -6px var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow { --tw-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1); --tw-shadow-colored: 0 1px 3px 0 var(--tw-shadow-color), 0 1px 2px -1px var(--tw-shadow-color); box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000),var(--tw-ring-shadow, 0 0 #0000),var(--tw-shadow); }
.zenuml .shadow-slate-500\/50 { --tw-shadow-color: rgb(100 116 139 / 0.5); --tw-shadow: var(--tw-shadow-colored); }
.zenuml .blur { --tw-blur: blur(8px); filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .grayscale { --tw-grayscale: grayscale(100%); filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .filter { filter: var(--tw-blur) var(--tw-brightness) var(--tw-contrast) var(--tw-grayscale) var(--tw-hue-rotate) var(--tw-invert) var(--tw-saturate) var(--tw-sepia) var(--tw-drop-shadow); }
.zenuml .transition { transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter, -webkit-backdrop-filter; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .transition-opacity { transition-property: opacity; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .transition-all { transition-property: all; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1); transition-duration: 0.15s; }
.zenuml .hover\:whitespace-normal:hover { white-space: normal; }
.zenuml .hover\:bg-yellow-300:hover { --tw-bg-opacity: 1; background-color: rgb(253 224 71 / var(--tw-bg-opacity)); }
.zenuml .hover\:bg-gray-100:hover { --tw-bg-opacity: 1; background-color: rgb(243 244 246 / var(--tw-bg-opacity)); }
.zenuml .hover\:bg-skin-message-hover:hover { background-color: var(--color-bg-message-hover, var(--color-text-base, #000)); }
.zenuml .hover\:text-gray-600:hover { --tw-text-opacity: 1; color: rgb(75 85 99 / var(--tw-text-opacity)); }
.zenuml .hover\:text-gray-500:hover { --tw-text-opacity: 1; color: rgb(107 114 128 / var(--tw-text-opacity)); }
.zenuml .hover\:text-skin-message-hover:hover { color: var(--color-text-message-hover, var(--color-bg-base, #fff)); }
.zenuml .hover\:opacity-100:hover { opacity: 1; }
.zenuml .focus\:outline-none:focus { outline: transparent solid 2px; outline-offset: 2px; }
.zenuml .focus\:ring-2:focus { --tw-ring-offset-shadow: var(--tw-ring-inset) 0 0 0 var(--tw-ring-offset-width) var(--tw-ring-offset-color); --tw-ring-shadow: var(--tw-ring-inset) 0 0 0 calc(2px + var(--tw-ring-offset-width)) var(--tw-ring-color); box-shadow: var(--tw-ring-offset-shadow),var(--tw-ring-shadow),var(--tw-shadow, 0 0 #0000); }
.zenuml .focus\:ring-inset:focus { --tw-ring-inset: inset; }
.zenuml .focus\:ring-indigo-500:focus { --tw-ring-opacity: 1; --tw-ring-color: rgb(99 102 241 / var(--tw-ring-opacity)); }
.zenuml .group:hover .group-hover\:flex { display: flex; }
.zenuml .group:hover .group-hover\:hidden { display: none; }
@media (min-width: 640px) {
  .zenuml .sm\:my-8 { margin-top: 2rem; margin-bottom: 2rem; }
  .zenuml .sm\:block { display: block; }
  .zenuml .sm\:inline-block { display: inline-block; }
  .zenuml .sm\:h-screen { height: 100vh; }
  .zenuml .sm\:grid-cols-6 { grid-template-columns: repeat(6, minmax(0px, 1fr)); }
  .zenuml .sm\:grid-cols-2 { grid-template-columns: repeat(2, minmax(0px, 1fr)); }
  .zenuml .sm\:gap-6 { row-gap: 1.5rem; column-gap: 1.5rem; }
  .zenuml .sm\:p-2 { padding: 0.5rem; }
  .zenuml .sm\:px-6 { padding-left: 1.5rem; padding-right: 1.5rem; }
  .zenuml .sm\:align-middle { vertical-align: middle; }
}
@media (min-width: 1024px) {
  .zenuml .lg\:mx-auto { margin-left: auto; margin-right: auto; }
  .zenuml .lg\:mx-0 { margin-left: 0px; margin-right: 0px; }
  .zenuml .lg\:max-w-none { max-width: none; }
  .zenuml .lg\:grid-cols-8 { grid-template-columns: repeat(8, minmax(0px, 1fr)); }
  .zenuml .lg\:grid-cols-3 { grid-template-columns: repeat(3, minmax(0px, 1fr)); }
  .zenuml .lg\:px-8 { padding-left: 2rem; padding-right: 2rem; }
}
.zenuml .\[\&\>svg\]\:h-full > svg { height: 100%; }
.zenuml .\[\&\>svg\]\:w-full > svg { width: 100%; }
.zenuml *, .zenuml ::before, .zenuml ::after { box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgb(229, 231, 235); }
.zenuml ::before, .zenuml ::after { --tw-content: ""; }
.zenuml html { line-height: 1.5; tab-size: 4; font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji"; font-feature-settings: normal; }
.zenuml body { margin: 0px; line-height: inherit; }
.zenuml hr { height: 0px; color: inherit; border-top-width: 1px; }
.zenuml abbr:where([title]) { text-decoration: underline; text-decoration-style: dotted; text-decoration-color: currentcolor; }
.zenuml h1, .zenuml h2, .zenuml h3, .zenuml h4, .zenuml h5, .zenuml h6 { font-size: inherit; font-weight: inherit; }
.zenuml a { color: inherit; text-decoration: inherit; }
.zenuml b, .zenuml strong { font-weight: bolder; }
.zenuml code, .zenuml kbd, .zenuml samp, .zenuml pre { font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace; font-size: 1em; }
.zenuml small { font-size: 80%; }
.zenuml sub, .zenuml sup { font-size: 75%; line-height: 0; position: relative; vertical-align: baseline; }
.zenuml sub { bottom: -0.25em; }
.zenuml sup { top: -0.5em; }
.zenuml table { text-indent: 0px; border-color: inherit; border-collapse: collapse; }
.zenuml button, .zenuml input, .zenuml optgroup, .zenuml select, .zenuml textarea { font-family: inherit; font-size: 100%; font-weight: inherit; line-height: inherit; color: inherit; margin: 0px; padding: 0px; }
.zenuml button, .zenuml select { text-transform: none; }
.zenuml button, .zenuml [type="button"], .zenuml [type="reset"], .zenuml [type="submit"] { appearance: button; background-color: transparent; background-image: none; }
.zenuml progress { vertical-align: baseline; }
.zenuml ::-webkit-inner-spin-button, .zenuml ::-webkit-outer-spin-button { height: auto; }
.zenuml [type="search"] { appearance: textfield; outline-offset: -2px; }
.zenuml ::-webkit-search-decoration { appearance: none; }
.zenuml ::-webkit-file-upload-button { appearance: button; font-style: inherit; font-variant-caps: inherit; font-weight: inherit; font-stretch: inherit; font-size: inherit; line-height: inherit; font-family: inherit; font-size-adjust: inherit; font-kerning: inherit; font-variant-alternates: inherit; font-variant-ligatures: inherit; font-variant-numeric: inherit; font-variant-east-asian: inherit; font-variant-position: inherit; font-feature-settings: inherit; font-optical-sizing: inherit; font-variation-settings: inherit; }
.zenuml summary { display: list-item; }
.zenuml blockquote, .zenuml dl, .zenuml dd, .zenuml h1, .zenuml h2, .zenuml h3, .zenuml h4, .zenuml h5, .zenuml h6, .zenuml hr, .zenuml figure, .zenuml p, .zenuml pre { margin: 0px; }
.zenuml fieldset { margin: 0px; padding: 0px; }
.zenuml legend { padding: 0px; }
.zenuml ol, .zenuml ul, .zenuml menu { list-style: none; margin: 0px; padding: 0px; }
.zenuml textarea { resize: vertical; }
.zenuml input::placeholder, .zenuml textarea::placeholder { opacity: 1; color: rgb(156, 163, 175); }
.zenuml button, .zenuml [role="button"] { cursor: pointer; }
.zenuml :disabled { cursor: default; }
.zenuml img, .zenuml svg, .zenuml video, .zenuml canvas, .zenuml audio, .zenuml iframe, .zenuml embed, .zenuml object { display: block; vertical-align: middle; }
.zenuml img, .zenuml video { max-width: 100%; height: auto; }
.zenuml [hidden] { display: none; }
pre.zenuml { margin: 0px; }
.zenuml { font-family: Helvetica, Verdana, serif; font-size: 16px; }
.zenuml .frame .sequence-diagram .comments code { background-color: rgb(249, 242, 244); padding: 2px; margin: 1px 0px; border-radius: 2px; }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-post { color: rgb(13, 75, 59); }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-get { color: rgb(47, 61, 137); }
.zenuml .frame .sequence-diagram .comments .rest-api .http-method-delete { color: rgb(126, 30, 35); }
.zenuml .frame .sequence-diagram .comments ol, .zenuml .frame .sequence-diagram .comments ul { margin: 0px; padding-left: 20px; }
.zenuml .frame .sequence-diagram .comments ul li { list-style-type: none; }
.zenuml .frame .sequence-diagram .comments ul li input[type="checkbox"] { margin-left: -1em; }
.zenuml .frame .sequence-diagram .comments table { border-collapse: collapse; border-spacing: 0px; empty-cells: show; border-color: rgb(203, 203, 203); }
.zenuml .frame .sequence-diagram .comments table td, .zenuml .frame .sequence-diagram .comments table th { padding: 0.5em; }
.zenuml .frame .sequence-diagram .comments table thead { background-color: rgb(224, 224, 224); }
.zenuml .frame .sequence-diagram .comments table td { background-color: transparent; }
.zenuml .theme-blue .sequence-diagram .divider .name { border-radius: 4px; border: 1px solid rgb(226, 133, 83); color: rgb(226, 133, 83); }
.zenuml .theme-blue .sequence-diagram .divider .left, .zenuml .theme-blue .sequence-diagram .divider .right { background: rgb(226, 133, 83); }
.zenuml .theme-blue .sequence-diagram .lifeline .participant { border-color: rgb(3, 44, 114); background: rgba(146, 192, 240, 0.3); }
.zenuml .theme-blue .sequence-diagram .lifeline .participant label { color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .lifeline .line { border-left-color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message { border-bottom-color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message .name { color: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message svg polyline { stroke: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .message svg polyline.head { fill: rgb(3, 44, 114); }
.zenuml .theme-blue .sequence-diagram .occurrence { border-color: rgb(3, 44, 114); background-color: rgb(255, 255, 255); }
.zenuml .theme-blue .sequence-diagram .fragment { border-radius: 4px; border-color: rgba(4, 46, 110, 0.3); }
.zenuml .theme-blue .sequence-diagram .fragment .header .name { background: rgba(4, 46, 110, 0.1); }
.zenuml .theme-blue .sequence-diagram .fragment .header label { color: rgb(3, 44, 114); }
.zenuml .theme-black-white .sequence-diagram .divider .name { border-radius: 0px; border-color: rgb(0, 0, 0); box-shadow: rgb(0, 0, 0) 2px 2px; }
.zenuml .theme-black-white .sequence-diagram .divider .left, .zenuml .theme-black-white .sequence-diagram .divider .right { background: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .lifeline .participant { border-color: rgb(0, 0, 0); background: rgb(255, 255, 255); box-shadow: rgb(0, 0, 0) 2px 2px; }
.zenuml .theme-black-white .sequence-diagram .lifeline .line { border-left-color: rgba(0, 0, 0, 0.4); }
.zenuml .theme-black-white .sequence-diagram .message { border-bottom-color: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message .name { color: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message svg polyline { stroke: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .message svg polyline.head { fill: rgb(0, 0, 0); }
.zenuml .theme-black-white .sequence-diagram .occurrence { border-color: rgb(0, 0, 0); background-color: rgb(245, 245, 245); }
.zenuml .theme-black-white .sequence-diagram .fragment { border-color: rgba(0, 0, 0, 0.3); }
.zenuml .theme-black-white .sequence-diagram .fragment .header .name { background: rgba(0, 0, 0, 0.07); }
.zenuml .theme-star-uml .sequence-diagram .lifeline .participant { border-color: rgb(185, 64, 101); background-color: rgb(255, 254, 200); }
.zenuml .theme-star-uml .sequence-diagram .lifeline .line { border-left-color: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message { border-bottom-color: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message svg polyline { stroke: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .message svg polyline.head { fill: rgb(185, 64, 101); }
.zenuml .theme-star-uml .sequence-diagram .occurrence { background-color: rgb(255, 254, 200); border-color: rgb(185, 64, 101); }
.zenuml .blue-river .sequence-diagram { color: rgb(84, 82, 246); }
.zenuml .blue-river .sequence-diagram .life-line-layer .lifeline.actor .participant::before { filter: invert(100%) sepia(0%) saturate(0%) hue-rotate(93deg) brightness(103%) contrast(103%); }
.zenuml .blue-river .sequence-diagram .life-line-layer .participant { background: rgb(32, 151, 247); box-shadow: rgb(131, 196, 248) 4px 4px; border-radius: 9px; color: rgb(255, 255, 255); }
.zenuml .blue-river .sequence-diagram .life-line-layer .participant .interface { font-size: 0.8em; }
.zenuml .blue-river .sequence-diagram .life-line-layer .line { border-left-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message { border-bottom-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message svg.arrow polyline { stroke: rgb(32, 151, 247); fill: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .message.self svg.arrow polyline { fill: none; }
.zenuml .blue-river .sequence-diagram .message-layer .message.self svg.arrow polyline.head { fill: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .occurrence { background: rgb(229, 253, 232); border-color: rgb(101, 191, 115); box-shadow: rgb(147, 198, 155) 4px 4px; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment { border-color: rgb(32, 151, 247); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header .name label { display: inline-block; min-width: 50px; background: rgb(84, 82, 246); color: rgb(255, 255, 255); padding: 2px 0px 2px 10px; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header .name::after { content: "SO"; display: inline-block; background: rgb(84, 82, 246); margin-left: -23px; width: 34px; transform: translateY(-2px); height: 22px; clip-path: polygon(66% 0px, 100% 0px, 100% 66%, 66% 100%); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .header > label { display: inline-block; min-width: 50px; font-weight: 700; color: rgb(255, 255, 255); background: rgb(101, 191, 115); }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .comments { border-color: inherit; }
.zenuml .blue-river .sequence-diagram .message-layer .fragment .divider { border-bottom-color: inherit; }
:root { --background: #282a36; --hover: #2b2b2b; --occurance-border: #6e7191; --border: #585b74; --white: #f8f8f2; --current: #44475a; --comment: #6272a4; --cyan: #8be9fd; --green: #50fa7b; --orange: #ffb86c; --pink: #ff79c6; --purple: #bd93f9; --red: #ff5555; --yellow: #f1fa8c; --crayola: #a9b7c6; }
body .zenuml.theme-dark { background-color: var(--background); }
.zenuml.theme-dark .header { color: var(--crayola); font-weight: 700; }
.zenuml.theme-dark .sequence-diagram .lifeline-group:hover { background-color: rgb(34, 48, 73); }
.zenuml.theme-dark .sequence-diagram .lifeline .participant { border-color: var(--border); border-radius: 2px; background: var(--background); }
.zenuml.theme-dark .sequence-diagram .lifeline .participant label { color: var(--cyan); }
.zenuml.theme-dark .sequence-diagram .lifeline .line { background: var(--border); }
.zenuml.theme-dark .fragment > .header > .condition { color: var(--crayola); padding: 6px; }
.zenuml.theme-dark .sequence-diagram .fragment .header label { color: var(--crayola); padding: 6px; }
.zenuml.theme-dark .sequence-diagram .fragment .header .name { border-bottom-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .fragment .header .name label { color: var(--pink); font-weight: 400; }
.zenuml.theme-dark .sequence-diagram .fragment { border-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .comments { border: medium; background: none; color: var(--comment); }
.zenuml.theme-dark .sequence-diagram .fragment .segment:not(:first-child) { border-top-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .interaction { color: var(--orange); }
.zenuml.theme-dark .sequence-diagram .message { border-bottom-color: var(--border); }
.zenuml.theme-dark .sequence-diagram .message .name:hover { color: var(--crayola); }
.zenuml.theme-dark .sequence-diagram .message svg polyline { fill: var(--border); stroke: var(--border); }
.zenuml.theme-dark .sequence-diagram .message.self svg > polyline:not(.head) { fill: none; }
.zenuml.theme-dark .sequence-diagram .occurrence { background-color: var(--current); box-shadow: 0 0 0 1px var(--occurance-border); border-radius: 2px; }
.zenuml.theme-dark .sequence-diagram .divider .left, .zenuml.theme-dark .sequence-diagram .divider .right { background: var(--border); }
.zenuml.theme-dark .sequence-diagram .divider .name { color: var(--comment); }
.zenuml.theme-dark .interaction .message > .name:hover { background-color: rgb(34, 139, 34); color: rgb(255, 255, 255); }
p[data-v-15224042] { margin: 0px; line-height: 1.25em; }
.occurrence[data-v-4e715276] { width: 15px; padding: 16px 0px 16px 5px; }
[data-v-4e715276] > .statement-container:last-child > .interaction.return:last-of-type { margin-bottom: 0px; border-bottom-width: 0px; border-bottom-style: none; border-bottom-color: currentcolor; transform: translateY(1px); }
[data-v-4e715276] > .statement-container:last-child > .interaction.return:last-of-type > .message { bottom: -17px; height: 0px; }
.right-to-left.occurrence[data-v-4e715276] { left: -14px; }
.occurrence { margin-top: -2px; }
.interaction .occurrence.source[data-v-703306ae] { position: absolute; height: calc(100% + 14px); left: -12px; display: none; }
.interaction .occurrence.source.right-to-left[data-v-703306ae] { left: unset; right: -13px; }
.message.self[data-v-fbc5fa7e] { transform: translate(-5px); }
.interaction .invisible-occurrence[data-v-40bff5d3] { height: 20px; }
.interaction.async[data-v-40bff5d3] .message { width: 100%; }
.collapsible-header[data-v-b41acdfc] { display: flex; width: 100%; justify-content: space-between; }
.collapse-button[data-v-b41acdfc] { display: none; }
.fragment:hover .collapsible-header .collapse-button.expanded[data-v-b41acdfc] { display: inline-block !important; }
[data-v-0a896a9f], [data-v-0f640fba] { border-color: inherit; }
.fragment.par > .block > .statement-container:not(:first-child) { border-top-color: inherit; border-top-width: 1px; border-top-style: solid; }
[data-v-ce259a11], [data-v-a846d536], [data-v-7b7c43b8] { border-color: inherit; }
.divider[data-v-9e567e69] { display: flex; align-items: center; }
.name[data-v-9e567e69] { margin: 0px; padding: 2px 6px; }
.left[data-v-9e567e69], .right[data-v-9e567e69] { height: 1px; flex-grow: 1; }


:root { --bg-color: #ffffff; --text-color: #333333; --select-text-bg-color: #B5D6FC; --select-text-font-color: auto; --monospace: "Lucida Console",Consolas,"Courier",monospace; --title-bar-height: 20px; }
.mac-os-11 { --title-bar-height: 28px; }
html { font-size: 14px; background-color: var(--bg-color); color: var(--text-color); font-family: "Helvetica Neue", Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; }
h1, h2, h3, h4, h5 { white-space: pre-wrap; }
body { margin: 0px; padding: 0px; height: auto; inset: 0px; font-size: 1rem; line-height: 1.428571; overflow-x: hidden; background: inherit; }
iframe { margin: auto; }
a.url { word-break: break-all; }
a:active, a:hover { outline: 0px; }
.in-text-selection, ::selection { text-shadow: none; background: var(--select-text-bg-color); color: var(--select-text-font-color); }
#write { margin: 0px auto; height: auto; width: inherit; word-break: normal; overflow-wrap: break-word; position: relative; white-space: normal; overflow-x: visible; padding-top: 36px; }
#write.first-line-indent p { text-indent: 2em; }
#write.first-line-indent li p, #write.first-line-indent p * { text-indent: 0px; }
#write.first-line-indent li { margin-left: 2em; }
.for-image #write { padding-left: 8px; padding-right: 8px; }
body.typora-export { padding-left: 30px; padding-right: 30px; }
.typora-export .footnote-line, .typora-export li, .typora-export p { white-space: pre-wrap; }
.typora-export .task-list-item input { pointer-events: none; }
@media screen and (max-width: 500px) {
  body.typora-export { padding-left: 0px; padding-right: 0px; }
  #write { padding-left: 20px; padding-right: 20px; }
}
#write li > figure:last-child { margin-bottom: 0.5rem; }
#write ol, #write ul { position: relative; }
img { max-width: 100%; vertical-align: middle; image-orientation: from-image; }
button, input, select, textarea { color: inherit; font-style: inherit; font-variant-caps: inherit; font-weight: inherit; font-stretch: inherit; font-size: inherit; line-height: inherit; font-family: inherit; font-size-adjust: inherit; font-kerning: inherit; font-variant-alternates: inherit; font-variant-ligatures: inherit; font-variant-numeric: inherit; font-variant-east-asian: inherit; font-variant-position: inherit; font-feature-settings: inherit; font-optical-sizing: inherit; font-variation-settings: inherit; }
input[type="checkbox"], input[type="radio"] { line-height: normal; padding: 0px; }
*, ::after, ::before { box-sizing: border-box; }
#write h1, #write h2, #write h3, #write h4, #write h5, #write h6, #write p, #write pre { width: inherit; }
#write h1, #write h2, #write h3, #write h4, #write h5, #write h6, #write p { position: relative; }
p { line-height: inherit; }
h1, h2, h3, h4, h5, h6 { break-after: avoid-page; break-inside: avoid; orphans: 4; }
p { orphans: 4; }
h1 { font-size: 2rem; }
h2 { font-size: 1.8rem; }
h3 { font-size: 1.6rem; }
h4 { font-size: 1.4rem; }
h5 { font-size: 1.2rem; }
h6 { font-size: 1rem; }
.md-math-block, .md-rawblock, h1, h2, h3, h4, h5, h6, p { margin-top: 1rem; margin-bottom: 1rem; }
.hidden { display: none; }
.md-blockmeta { color: rgb(204, 204, 204); font-weight: 700; font-style: italic; }
a { cursor: pointer; }
sup.md-footnote { padding: 2px 4px; background-color: rgba(238, 238, 238, 0.7); color: rgb(85, 85, 85); border-radius: 4px; cursor: pointer; }
sup.md-footnote a, sup.md-footnote a:hover { color: inherit; text-transform: inherit; text-decoration: inherit; }
#write input[type="checkbox"] { cursor: pointer; width: inherit; height: inherit; }
figure { overflow-x: auto; margin: 1.2em 0px; max-width: calc(100% + 16px); padding: 0px; }
figure > table { margin: 0px; }
thead, tr { break-inside: avoid; break-after: auto; }
thead { display: table-header-group; }
table { border-collapse: collapse; border-spacing: 0px; width: 100%; overflow: auto; break-inside: auto; text-align: left; }
table.md-table td { min-width: 32px; }
.CodeMirror-gutters { border-right-width: 0px; border-right-style: none; border-right-color: currentcolor; background-color: inherit; }
.CodeMirror-linenumber { -webkit-user-select: none; }
.CodeMirror { text-align: left; }
.CodeMirror-placeholder { opacity: 0.3; }
.CodeMirror pre { padding: 0px 4px; }
.CodeMirror-lines { padding: 0px; }
div.hr:focus { cursor: none; }
#write pre { white-space: pre-wrap; }
#write.fences-no-line-wrapping pre { white-space: pre; }
#write pre.ty-contain-cm { white-space: normal; }
.CodeMirror-gutters { margin-right: 4px; }
.md-fences { font-size: 0.9rem; display: block; break-inside: avoid; text-align: left; overflow: visible; white-space: pre; background: inherit; position: relative !important; }
.md-fences-adv-panel { width: 100%; margin-top: 10px; text-align: center; padding-top: 0px; padding-bottom: 8px; overflow-x: auto; }
#write .md-fences.mock-cm { white-space: pre-wrap; }
.md-fences.md-fences-with-lineno { padding-left: 0px; }
#write.fences-no-line-wrapping .md-fences.mock-cm { white-space: pre; overflow-x: auto; }
.md-fences.mock-cm.md-fences-with-lineno { padding-left: 8px; }
.CodeMirror-line, twitterwidget { break-inside: avoid; }
svg { break-inside: avoid; }
.footnotes { opacity: 0.8; font-size: 0.9rem; margin-top: 1em; margin-bottom: 1em; }
.footnotes + .footnotes { margin-top: 0px; }
.md-reset { margin: 0px; padding: 0px; border: 0px; outline: 0px; vertical-align: top; background: 0px 0px; text-decoration: none; text-shadow: none; float: none; position: static; width: auto; height: auto; white-space: nowrap; cursor: inherit; line-height: normal; font-weight: 400; text-align: left; box-sizing: content-box; direction: ltr; }
li div { padding-top: 0px; }
blockquote { margin: 1rem 0px; }
li .mathjax-block, li p { margin: 0.5rem 0px; }
li blockquote { margin: 1rem 0px; }
li { margin: 0px; position: relative; }
blockquote > :last-child { margin-bottom: 0px; }
blockquote > :first-child, li > :first-child { margin-top: 0px; }
.footnotes-area { color: rgb(136, 136, 136); margin-top: 0.714rem; padding-bottom: 0.143rem; white-space: normal; }
#write .footnote-line { white-space: pre-wrap; }
@media print {
  body, html { border: 1px solid transparent; height: 99%; break-after: avoid; break-before: avoid; font-variant-ligatures: no-common-ligatures; }
  #write { margin-top: 0px; border-color: transparent !important; padding-top: 0px !important; padding-bottom: 0px !important; }
  .typora-export * { print-color-adjust: exact; }
  .typora-export #write { break-after: avoid; }
  .typora-export #write::after { height: 0px; }
  .is-mac table { break-inside: avoid; }
  #write > p:nth-child(1) { margin-top: 0px; }
  .typora-export-show-outline .typora-export-sidebar { display: none; }
  figure { overflow-x: visible; }
}
.footnote-line { margin-top: 0.714em; font-size: 0.7em; }
a img, img a { cursor: pointer; }
pre.md-meta-block { font-size: 0.8rem; min-height: 0.8rem; white-space: pre-wrap; background: rgb(204, 204, 204); display: block; overflow-x: hidden; }
p > .md-image:only-child:not(.md-img-error) img, p > img:only-child { display: block; margin: auto; }
#write.first-line-indent p > .md-image:only-child:not(.md-img-error) img { left: -2em; position: relative; }
p > .md-image:only-child { display: inline-block; width: 100%; }
#write .MathJax_Display { margin: 0.8em 0px 0px; }
.md-math-block { width: 100%; }
.md-math-block:not(:empty)::after { display: none; }
.MathJax_ref { fill: currentcolor; }
[contenteditable="true"]:active, [contenteditable="true"]:focus, [contenteditable="false"]:active, [contenteditable="false"]:focus { outline: 0px; box-shadow: none; }
.md-task-list-item { position: relative; list-style-type: none; }
.task-list-item.md-task-list-item { padding-left: 0px; }
.md-task-list-item > input { position: absolute; top: 0px; left: 0px; margin-left: -1.2em; margin-top: calc(1em - 10px); border: medium; }
.math { font-size: 1rem; }
.md-toc { min-height: 3.58rem; position: relative; font-size: 0.9rem; border-radius: 10px; }
.md-toc-content { position: relative; margin-left: 0px; }
.md-toc-content::after, .md-toc::after { display: none; }
.md-toc-item { display: block; color: rgb(65, 131, 196); }
.md-toc-item a { text-decoration: none; }
.md-toc-inner:hover { text-decoration: underline; }
.md-toc-inner { display: inline-block; cursor: pointer; }
.md-toc-h1 .md-toc-inner { margin-left: 0px; font-weight: 700; }
.md-toc-h2 .md-toc-inner { margin-left: 2em; }
.md-toc-h3 .md-toc-inner { margin-left: 4em; }
.md-toc-h4 .md-toc-inner { margin-left: 6em; }
.md-toc-h5 .md-toc-inner { margin-left: 8em; }
.md-toc-h6 .md-toc-inner { margin-left: 10em; }
@media screen and (max-width: 48em) {
  .md-toc-h3 .md-toc-inner { margin-left: 3.5em; }
  .md-toc-h4 .md-toc-inner { margin-left: 5em; }
  .md-toc-h5 .md-toc-inner { margin-left: 6.5em; }
  .md-toc-h6 .md-toc-inner { margin-left: 8em; }
}
a.md-toc-inner { font-size: inherit; font-style: inherit; font-weight: inherit; line-height: inherit; }
.footnote-line a:not(.reversefootnote) { color: inherit; }
.reversefootnote { font-family: ui-monospace, sans-serif; }
.md-attr { display: none; }
.md-fn-count::after { content: "."; }
code, pre, samp, tt { font-family: var(--monospace); }
kbd { margin: 0px 0.1em; padding: 0.1em 0.6em; font-size: 0.8em; color: rgb(36, 39, 41); background: rgb(255, 255, 255); border: 1px solid rgb(173, 179, 185); border-radius: 3px; box-shadow: rgba(12, 13, 14, 0.2) 0px 1px 0px, rgb(255, 255, 255) 0px 0px 0px 2px inset; white-space: nowrap; vertical-align: middle; }
.md-comment { color: rgb(162, 127, 3); opacity: 0.6; font-family: var(--monospace); }
code { text-align: left; vertical-align: initial; }
a.md-print-anchor { white-space: pre !important; border-width: medium !important; border-style: none !important; border-color: currentcolor !important; display: inline-block !important; position: absolute !important; width: 1px !important; right: 0px !important; outline: 0px !important; background: 0px 0px !important; text-decoration: initial !important; text-shadow: initial !important; }
.os-windows.monocolor-emoji .md-emoji { font-family: "Segoe UI Symbol", sans-serif; }
.md-diagram-panel > svg { max-width: 100%; }
[lang="flow"] svg, [lang="mermaid"] svg { max-width: 100%; height: auto; }
[lang="mermaid"] .node text { font-size: 1rem; }
table tr th { border-bottom-width: 0px; border-bottom-style: none; border-bottom-color: currentcolor; }
video { max-width: 100%; display: block; margin: 0px auto; }
iframe { max-width: 100%; width: 100%; border: medium; }
.highlight td, .highlight tr { border: 0px; }
mark { background: rgb(255, 255, 0); color: rgb(0, 0, 0); }
.md-html-inline .md-plain, .md-html-inline strong, mark .md-inline-math, mark strong { color: inherit; }
.md-expand mark .md-meta { opacity: 0.3 !important; }
mark .md-meta { color: rgb(0, 0, 0); }
@media print {
  .typora-export h1, .typora-export h2, .typora-export h3, .typora-export h4, .typora-export h5, .typora-export h6 { break-inside: avoid; }
}
.md-diagram-panel .messageText { stroke: none !important; }
.md-diagram-panel .start-state { fill: var(--node-fill); }
.md-diagram-panel .edgeLabel rect { opacity: 1 !important; }
.md-fences.md-fences-math { font-size: 1em; }
.md-fences-advanced:not(.md-focus) { padding: 0px; white-space: nowrap; border: 0px; }
.md-fences-advanced:not(.md-focus) { background: inherit; }
.typora-export-show-outline .typora-export-content { max-width: 1440px; margin: auto; display: flex; flex-direction: row; }
.typora-export-sidebar { width: 300px; font-size: 0.8rem; margin-top: 80px; margin-right: 18px; }
.typora-export-show-outline #write { --webkit-flex: 2; flex: 2 1 0%; }
.typora-export-sidebar .outline-content { position: fixed; top: 0px; max-height: 100%; overflow: hidden auto; padding-bottom: 30px; padding-top: 60px; width: 300px; }
@media screen and (max-width: 1024px) {
  .typora-export-sidebar, .typora-export-sidebar .outline-content { width: 240px; }
}
@media screen and (max-width: 800px) {
  .typora-export-sidebar { display: none; }
}
.outline-content li, .outline-content ul { margin-left: 0px; margin-right: 0px; padding-left: 0px; padding-right: 0px; list-style: none; overflow-wrap: anywhere; }
.outline-content ul { margin-top: 0px; margin-bottom: 0px; }
.outline-content strong { font-weight: 400; }
.outline-expander { width: 1rem; height: 1.428571rem; position: relative; display: table-cell; vertical-align: middle; cursor: pointer; padding-left: 4px; }
.outline-expander::before { content: ""; position: relative; font-family: Ionicons; display: inline-block; font-size: 8px; vertical-align: middle; }
.outline-item { padding-top: 3px; padding-bottom: 3px; cursor: pointer; }
.outline-expander:hover::before { content: ""; }
.outline-h1 > .outline-item { padding-left: 0px; }
.outline-h2 > .outline-item { padding-left: 1em; }
.outline-h3 > .outline-item { padding-left: 2em; }
.outline-h4 > .outline-item { padding-left: 3em; }
.outline-h5 > .outline-item { padding-left: 4em; }
.outline-h6 > .outline-item { padding-left: 5em; }
.outline-label { cursor: pointer; display: table-cell; vertical-align: middle; text-decoration: none; color: inherit; }
.outline-label:hover { text-decoration: underline; }
.outline-item:hover { border-color: rgb(245, 245, 245); background-color: var(--item-hover-bg-color); }
.outline-item:hover { margin-left: -28px; margin-right: -28px; border-left-width: 28px; border-left-style: solid; border-left-color: transparent; border-right-width: 28px; border-right-style: solid; border-right-color: transparent; }
.outline-item-single .outline-expander::before, .outline-item-single .outline-expander:hover::before { display: none; }
.outline-item-open > .outline-item > .outline-expander::before { content: ""; }
.outline-children { display: none; }
.info-panel-tab-wrapper { display: none; }
.outline-item-open > .outline-children { display: block; }
.typora-export .outline-item { padding-top: 1px; padding-bottom: 1px; }
.typora-export .outline-item:hover { margin-right: -8px; border-right-width: 8px; border-right-style: solid; border-right-color: transparent; }
.typora-export .outline-expander::before { content: "+"; font-family: inherit; top: -1px; }
.typora-export .outline-expander:hover::before, .typora-export .outline-item-open > .outline-item > .outline-expander::before { content: "−"; }
.typora-export-collapse-outline .outline-children { display: none; }
.typora-export-collapse-outline .outline-item-open > .outline-children, .typora-export-no-collapse-outline .outline-children { display: block; }
.typora-export-no-collapse-outline .outline-expander::before { content: "" !important; }
.typora-export-show-outline .outline-item-active > .outline-item .outline-label { font-weight: 700; }
.md-inline-math-container mjx-container { zoom: 0.95; }
mjx-container { break-inside: avoid; }


.CodeMirror { height: auto; }
.CodeMirror.cm-s-inner { background: inherit; }
.CodeMirror-scroll { overflow: auto hidden; z-index: 3; }
.CodeMirror-gutter-filler, .CodeMirror-scrollbar-filler { background-color: rgb(255, 255, 255); }
.CodeMirror-gutters { border-right-width: 1px; border-right-style: solid; border-right-color: rgb(221, 221, 221); background: inherit; white-space: nowrap; }
.CodeMirror-linenumber { padding: 0px 3px 0px 5px; text-align: right; color: rgb(153, 153, 153); }
.cm-s-inner .cm-keyword { color: rgb(119, 0, 136); }
.cm-s-inner .cm-atom, .cm-s-inner.cm-atom { color: rgb(34, 17, 153); }
.cm-s-inner .cm-number { color: rgb(17, 102, 68); }
.cm-s-inner .cm-def { color: rgb(0, 0, 255); }
.cm-s-inner .cm-variable { color: rgb(0, 0, 0); }
.cm-s-inner .cm-variable-2 { color: rgb(0, 85, 170); }
.cm-s-inner .cm-variable-3 { color: rgb(0, 136, 85); }
.cm-s-inner .cm-string { color: rgb(170, 17, 17); }
.cm-s-inner .cm-property { color: rgb(0, 0, 0); }
.cm-s-inner .cm-operator { color: rgb(152, 26, 26); }
.cm-s-inner .cm-comment, .cm-s-inner.cm-comment { color: rgb(170, 85, 0); }
.cm-s-inner .cm-string-2 { color: rgb(255, 85, 0); }
.cm-s-inner .cm-meta { color: rgb(85, 85, 85); }
.cm-s-inner .cm-qualifier { color: rgb(85, 85, 85); }
.cm-s-inner .cm-builtin { color: rgb(51, 0, 170); }
.cm-s-inner .cm-bracket { color: rgb(153, 153, 119); }
.cm-s-inner .cm-tag { color: rgb(17, 119, 0); }
.cm-s-inner .cm-attribute { color: rgb(0, 0, 204); }
.cm-s-inner .cm-header, .cm-s-inner.cm-header { color: rgb(0, 0, 255); }
.cm-s-inner .cm-quote, .cm-s-inner.cm-quote { color: rgb(0, 153, 0); }
.cm-s-inner .cm-hr, .cm-s-inner.cm-hr { color: rgb(153, 153, 153); }
.cm-s-inner .cm-link, .cm-s-inner.cm-link { color: rgb(0, 0, 204); }
.cm-negative { color: rgb(221, 68, 68); }
.cm-positive { color: rgb(34, 153, 34); }
.cm-header, .cm-strong { font-weight: 700; }
.cm-del { text-decoration: line-through; }
.cm-em { font-style: italic; }
.cm-link { text-decoration: underline; }
.cm-error { color: red; }
.cm-invalidchar { color: red; }
.cm-constant { color: rgb(38, 139, 210); }
.cm-defined { color: rgb(181, 137, 0); }
div.CodeMirror span.CodeMirror-matchingbracket { color: rgb(0, 255, 0); }
div.CodeMirror span.CodeMirror-nonmatchingbracket { color: rgb(255, 34, 34); }
.cm-s-inner .CodeMirror-activeline-background { background: inherit; }
.CodeMirror { position: relative; overflow: hidden; }
.CodeMirror-scroll { height: 100%; outline: 0px; position: relative; box-sizing: content-box; background: inherit; }
.CodeMirror-sizer { position: relative; }
.CodeMirror-gutter-filler, .CodeMirror-hscrollbar, .CodeMirror-scrollbar-filler, .CodeMirror-vscrollbar { position: absolute; z-index: 6; display: none; outline: 0px; }
.CodeMirror-vscrollbar { right: 0px; top: 0px; overflow: hidden; }
.CodeMirror-hscrollbar { bottom: 0px; left: 0px; overflow: auto hidden; }
.CodeMirror-scrollbar-filler { right: 0px; bottom: 0px; }
.CodeMirror-gutter-filler { left: 0px; bottom: 0px; }
.CodeMirror-gutters { position: absolute; left: 0px; top: 0px; padding-bottom: 10px; z-index: 3; overflow-y: hidden; }
.CodeMirror-gutter { white-space: normal; height: 100%; box-sizing: content-box; padding-bottom: 30px; margin-bottom: -32px; display: inline-block; }
.CodeMirror-gutter-wrapper { position: absolute; z-index: 4; background: 0px 0px !important; border: medium !important; }
.CodeMirror-gutter-background { position: absolute; top: 0px; bottom: 0px; z-index: 4; }
.CodeMirror-gutter-elt { position: absolute; cursor: default; z-index: 4; }
.CodeMirror-lines { cursor: text; }
.CodeMirror pre { border-radius: 0px; border-width: 0px; background: 0px 0px; font-family: inherit; font-size: inherit; margin: 0px; white-space: pre; overflow-wrap: normal; color: inherit; z-index: 2; position: relative; overflow: visible; }
.CodeMirror-wrap pre { overflow-wrap: break-word; white-space: pre-wrap; word-break: normal; }
.CodeMirror-code pre { border-right-width: 30px; border-right-style: solid; border-right-color: transparent; width: fit-content; }
.CodeMirror-wrap .CodeMirror-code pre { border-right-width: medium; border-right-style: none; border-right-color: currentcolor; width: auto; }
.CodeMirror-linebackground { position: absolute; inset: 0px; z-index: 0; }
.CodeMirror-linewidget { position: relative; z-index: 2; overflow: auto; }
.CodeMirror-wrap .CodeMirror-scroll { overflow-x: hidden; }
.CodeMirror-measure { position: absolute; width: 100%; height: 0px; overflow: hidden; visibility: hidden; }
.CodeMirror-measure pre { position: static; }
.CodeMirror div.CodeMirror-cursor { position: absolute; visibility: hidden; border-right-width: medium; border-right-style: none; border-right-color: currentcolor; width: 0px; }
.CodeMirror div.CodeMirror-cursor { visibility: hidden; }
.CodeMirror-focused div.CodeMirror-cursor { visibility: inherit; }
.cm-searching { background: rgba(255, 255, 0, 0.4); }
span.cm-underlined { text-decoration: underline; }
span.cm-strikethrough { text-decoration: line-through; }
.cm-tw-syntaxerror { color: rgb(255, 255, 255); background-color: rgb(153, 0, 0); }
.cm-tw-deleted { text-decoration: line-through; }
.cm-tw-header5 { font-weight: 700; }
.cm-tw-listitem:first-child { padding-left: 10px; }
.cm-tw-box { border-style: solid; border-right-width: 1px; border-bottom-width: 1px; border-left-width: 1px; border-color: inherit; border-top-width: 0px !important; }
.cm-tw-underline { text-decoration: underline; }
@media print {
  .CodeMirror div.CodeMirror-cursor { visibility: hidden; }
}


:root {
    --side-bar-bg-color: #fafafa;
    --control-text-color: #777;
}

@include-when-export url(https://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,700,400&subset=latin,latin-ext);

/* open-sans-regular - latin-ext_latin */
  /* open-sans-italic - latin-ext_latin */
    /* open-sans-700 - latin-ext_latin */
    /* open-sans-700italic - latin-ext_latin */
  html {
    font-size: 16px;
    -webkit-font-smoothing: antialiased;
}

body {
    font-family: "Open Sans","Clear Sans", "Helvetica Neue", Helvetica, Arial, 'Segoe UI Emoji', sans-serif;
    color: rgb(51, 51, 51);
    line-height: 1.6;
}

#write {
    max-width: 860px;
  	margin: 0 auto;
  	padding: 30px;
    padding-bottom: 100px;
}

@media only screen and (min-width: 1400px) {
	#write {
		max-width: 1024px;
	}
}

@media only screen and (min-width: 1800px) {
	#write {
		max-width: 1200px;
	}
}

#write > ul:first-child,
#write > ol:first-child{
    margin-top: 30px;
}

a {
    color: #4183C4;
}
h1,
h2,
h3,
h4,
h5,
h6 {
    position: relative;
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-weight: bold;
    line-height: 1.4;
    cursor: text;
}
h1:hover a.anchor,
h2:hover a.anchor,
h3:hover a.anchor,
h4:hover a.anchor,
h5:hover a.anchor,
h6:hover a.anchor {
    text-decoration: none;
}
h1 tt,
h1 code {
    font-size: inherit;
}
h2 tt,
h2 code {
    font-size: inherit;
}
h3 tt,
h3 code {
    font-size: inherit;
}
h4 tt,
h4 code {
    font-size: inherit;
}
h5 tt,
h5 code {
    font-size: inherit;
}
h6 tt,
h6 code {
    font-size: inherit;
}
h1 {
    font-size: 2.25em;
    line-height: 1.2;
    border-bottom: 1px solid #eee;
}
h2 {
    font-size: 1.75em;
    line-height: 1.225;
    border-bottom: 1px solid #eee;
}

/*@media print {
    .typora-export h1,
    .typora-export h2 {
        border-bottom: none;
        padding-bottom: initial;
    }

    .typora-export h1::after,
    .typora-export h2::after {
        content: "";
        display: block;
        height: 100px;
        margin-top: -96px;
        border-top: 1px solid #eee;
    }
}*/

h3 {
    font-size: 1.5em;
    line-height: 1.43;
}
h4 {
    font-size: 1.25em;
}
h5 {
    font-size: 1em;
}
h6 {
   font-size: 1em;
    color: #777;
}
p,
blockquote,
ul,
ol,
dl,
table{
    margin: 0.8em 0;
}
li>ol,
li>ul {
    margin: 0 0;
}
hr {
    height: 2px;
    padding: 0;
    margin: 16px 0;
    background-color: #e7e7e7;
    border: 0 none;
    overflow: hidden;
    box-sizing: content-box;
}

li p.first {
    display: inline-block;
}
ul,
ol {
    padding-left: 30px;
}
ul:first-child,
ol:first-child {
    margin-top: 0;
}
ul:last-child,
ol:last-child {
    margin-bottom: 0;
}
blockquote {
    border-left: 4px solid #dfe2e5;
    padding: 0 15px;
    color: #777777;
}
blockquote blockquote {
    padding-right: 0;
}
table {
    padding: 0;
    word-break: initial;
}
table tr {
    border: 1px solid #dfe2e5;
    margin: 0;
    padding: 0;
}
table tr:nth-child(2n),
thead {
    background-color: #f8f8f8;
}
table th {
    font-weight: bold;
    border: 1px solid #dfe2e5;
    border-bottom: 0;
    margin: 0;
    padding: 6px 13px;
}
table td {
    border: 1px solid #dfe2e5;
    margin: 0;
    padding: 6px 13px;
}
table th:first-child,
table td:first-child {
    margin-top: 0;
}
table th:last-child,
table td:last-child {
    margin-bottom: 0;
}

.CodeMirror-lines {
    padding-left: 4px;
}

.code-tooltip {
    box-shadow: 0 1px 1px 0 rgba(0,28,36,.3);
    border-top: 1px solid #eef2f2;
}

.md-fences,
code,
tt {
    border: 1px solid #e7eaed;
    background-color: #f8f8f8;
    border-radius: 3px;
    padding: 0;
    padding: 2px 4px 0px 4px;
    font-size: 0.9em;
}

code {
    background-color: #f3f4f4;
    padding: 0 2px 0 2px;
}

.md-fences {
    margin-bottom: 15px;
    margin-top: 15px;
    padding-top: 8px;
    padding-bottom: 6px;
}


.md-task-list-item > input {
  margin-left: -1.3em;
}

@media print {
    html {
        font-size: 13px;
    }
    pre {
        page-break-inside: avoid;
        word-wrap: break-word;
    }
}

.md-fences {
	background-color: #f8f8f8;
}
#write pre.md-meta-block {
	padding: 1rem;
    font-size: 85%;
    line-height: 1.45;
    background-color: #f7f7f7;
    border: 0;
    border-radius: 3px;
    color: #777777;
    margin-top: 0 !important;
}

.mathjax-block>.code-tooltip {
	bottom: .375rem;
}

.md-mathjax-midline {
    background: #fafafa;
}

#write>h3.md-focus:before{
	left: -1.5625rem;
	top: .375rem;
}
#write>h4.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
#write>h5.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
#write>h6.md-focus:before{
	left: -1.5625rem;
	top: .285714286rem;
}
.md-image>.md-meta {
    /*border: 1px solid #ddd;*/
    border-radius: 3px;
    padding: 2px 0px 0px 4px;
    font-size: 0.9em;
    color: inherit;
}

.md-tag {
    color: #a7a7a7;
    opacity: 1;
}

.md-toc { 
    margin-top:20px;
    padding-bottom:20px;
}

.sidebar-tabs {
    border-bottom: none;
}

#typora-quick-open {
    border: 1px solid #ddd;
    background-color: #f8f8f8;
}

#typora-quick-open-item {
    background-color: #FAFAFA;
    border-color: #FEFEFE #e5e5e5 #e5e5e5 #eee;
    border-style: solid;
    border-width: 1px;
}

/** focus mode */
.on-focus-mode blockquote {
    border-left-color: rgba(85, 85, 85, 0.12);
}

header, .context-menu, .megamenu-content, footer{
    font-family: "Segoe UI", "Arial", sans-serif;
}

.file-node-content:hover .file-node-icon,
.file-node-content:hover .file-node-open-state{
    visibility: visible;
}

.mac-seamless-mode #typora-sidebar {
    background-color: #fafafa;
    background-color: var(--side-bar-bg-color);
}

.md-lang {
    color: #b4654d;
}

/*.html-for-mac {
    --item-hover-bg-color: #E6F0FE;
}*/

#md-notification .btn {
    border: 0;
}

.dropdown-menu .divider {
    border-color: #e5e5e5;
    opacity: 0.4;
}

.ty-preferences .window-content {
    background-color: #fafafa;
}

.ty-preferences .nav-group-item.active {
    color: white;
    background: #999;
}

.menu-item-container a.menu-style-btn {
    background-color: #f5f8fa;
    background-image: linear-gradient( 180deg , hsla(0, 0%, 100%, 0.8), hsla(0, 0%, 100%, 0)); 
}


 @media print { @page {margin: 0 0 0 0;} body.typora-export {padding-left: 0; padding-right: 0;} #write {padding:0;}}
</style><title>Datalogging-Diagram</title>
</head>
<body class='typora-export'><div class='typora-export-content'>
<div id='write'  class=''><h1 id='pruebas-23-24-de-noviembre'><span>Pruebas 23-24 de noviembre</span></h1><p>&nbsp;</p><h2 id='código-issr-modificado'><span>Código ISSR modificado</span></h2><div class="md-diagram-panel md-fences-adv-panel" lang="mermaid"><svg id="mermaidChart978" width="100%" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="max-width: 933.498047px; margin: auto;" viewBox="-8 -8 933.498046875 2625.796875" role="graphics-document document" aria-roledescription="flowchart-v2"><style>#mermaidChart978{font-family:sans-serif;font-size:16px;fill:#333;}#mermaidChart978 .error-icon{fill:#552222;}#mermaidChart978 .error-text{fill:#552222;stroke:#552222;}#mermaidChart978 .edge-thickness-normal{stroke-width:2px;}#mermaidChart978 .edge-thickness-thick{stroke-width:3.5px;}#mermaidChart978 .edge-pattern-solid{stroke-dasharray:0;}#mermaidChart978 .edge-pattern-dashed{stroke-dasharray:3;}#mermaidChart978 .edge-pattern-dotted{stroke-dasharray:2;}#mermaidChart978 .marker{fill:#333333;stroke:#333333;}#mermaidChart978 .marker.cross{stroke:#333333;}#mermaidChart978 svg{font-family:sans-serif;font-size:16px;}#mermaidChart978 .label{font-family:sans-serif;color:#333;}#mermaidChart978 .cluster-label text{fill:#333;}#mermaidChart978 .cluster-label span,#mermaidChart978 p{color:#333;}#mermaidChart978 .label text,#mermaidChart978 span,#mermaidChart978 p{fill:#333;color:#333;}#mermaidChart978 .node rect,#mermaidChart978 .node circle,#mermaidChart978 .node ellipse,#mermaidChart978 .node polygon,#mermaidChart978 .node path{fill:#ECECFF;stroke:#9370DB;stroke-width:1px;}#mermaidChart978 .flowchart-label text{text-anchor:middle;}#mermaidChart978 .node .label{text-align:center;}#mermaidChart978 .node.clickable{cursor:pointer;}#mermaidChart978 .arrowheadPath{fill:#333333;}#mermaidChart978 .edgePath .path{stroke:#333333;stroke-width:2.0px;}#mermaidChart978 .flowchart-link{stroke:#333333;fill:none;}#mermaidChart978 .edgeLabel{background-color:#e8e8e8;text-align:center;}#mermaidChart978 .edgeLabel rect{opacity:0.5;background-color:#e8e8e8;fill:#e8e8e8;}#mermaidChart978 .labelBkg{background-color:rgba(232, 232, 232, 0.5);}#mermaidChart978 .cluster rect{fill:#ffffde;stroke:#aaaa33;stroke-width:1px;}#mermaidChart978 .cluster text{fill:#333;}#mermaidChart978 .cluster span,#mermaidChart978 p{color:#333;}#mermaidChart978 div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:sans-serif;font-size:12px;background:hsl(80, 100%, 96.2745098039%);border:1px solid #aaaa33;border-radius:2px;pointer-events:none;z-index:100;}#mermaidChart978 .flowchartTitleText{text-anchor:middle;font-size:18px;fill:#333;}#mermaidChart978 :root{--mermaid-alt-font-family:sans-serif;}</style><g><marker id="flowchart-pointEnd" class="marker flowchart" viewBox="0 0 10 10" refX="10" refY="5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 0 0 L 10 5 L 0 10 z" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-pointStart" class="marker flowchart" viewBox="0 0 10 10" refX="0" refY="5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 0 5 L 10 10 L 10 0 z" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-circleEnd" class="marker flowchart" viewBox="0 0 10 10" refX="11" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="flowchart-circleStart" class="marker flowchart" viewBox="0 0 10 10" refX="-1" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" class="arrowMarkerPath" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="flowchart-crossEnd" class="marker cross flowchart" viewBox="0 0 11 11" refX="12" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" class="arrowMarkerPath" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="flowchart-crossStart" class="marker cross flowchart" viewBox="0 0 11 11" refX="-1" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" class="arrowMarkerPath" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><g class="root"><g class="clusters"></g><g class="edgePaths"><path d="M534.119140625,53L533.619140625,77.5L533.619140625,102.5" id="L-setup-loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-setup LE-loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M474.4826124237805,155L418.162109375,180L418.662109375,205.5" id="L-loop-receive.done-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-loop LE-receive.done" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M357.89806592829274,393.68908155329274L257.720703125,497.703125L257.720703125,541.453125" id="L-receive.done-packet_received-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-receive.done LE-packet_received" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M257.720703125,593.953125L257.720703125,618.953125L258.220703125,644.453125" id="L-packet_received-crc_check-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-packet_received LE-crc_check" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M298.792305375002,816.740897749998L350.23828125,900.5625L350.73828125,944.8125" id="L-crc_check-correctID-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crc_check LE-correctID" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M313.58498320287055,1085.8545144528705L255.21875,1166.2578125L255.21875,1247.5078125" id="L-correctID-IDerror-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-correctID LE-IDerror" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M390.0769682506856,1083.6691254993143L455.265625,1166.2578125L455.265625,1210.0078125" id="L-correctID-radio_hop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-correctID LE-radio_hop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M199.11936102378897,798.211157898789L70.18359375,900.5625L70.18359375,1033.41015625L70.18359375,1166.2578125L70.18359375,1247.5078125" id="L-crc_check-crcErrors-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crc_check LE-crcErrors" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M70.18359375,1300.0078125L70.18359375,1362.5078125L112.87318978658536,1387.5078125" id="L-crcErrors-wait_here-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-crcErrors LE-wait_here" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M255.21875,1300.0078125L255.21875,1362.5078125L207.6472942073171,1387.5078125" id="L-IDerror-wait_here-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-IDerror LE-wait_here" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M157.697265625,1440.0078125L157.697265625,1465.0078125L157.697265625,1685.921875L157.697265625,1925.5859375L157.697265625,1995.5859375L157.697265625,2046.8359375L157.697265625,2168.31640625L157.697265625,2308.546875L157.697265625,2378.546875L157.697265625,2429.796875L157.697265625,2481.046875L157.697265625,2532.296875L612.048828125,2577.2672460130057" id="L-wait_here-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-wait_here LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M455.265625,1337.5078125L455.265625,1362.5078125L531.0887957317074,1387.5078125" id="L-radio_hop-end_receive_block-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-radio_hop LE-end_receive_block" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M504.0295445235641,369.0856898514359L785.3671875,497.703125L785.3671875,567.703125L785.3671875,618.953125L785.3671875,750.3828125L785.3671875,900.5625L785.3671875,1033.41015625L785.3671875,1166.2578125L785.3671875,1273.7578125L785.3671875,1362.5078125L700.165205792683,1387.5078125" id="L-receive.done-end_receive_block-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-receive.done LE-end_receive_block" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M610.703125,1440.0078125L610.703125,1465.0078125L611.203125,1490.5078125" id="L-end_receive_block-delay-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-end_receive_block LE-delay" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M564.0905701922363,1835.2233826922363L534.822265625,1925.5859375L534.822265625,1969.3359375" id="L-delay-packet_missed-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-delay LE-packet_missed" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M534.822265625,2021.8359375L534.822265625,2046.8359375L535.322265625,2072.3359375" id="L-packet_missed-hop_retry-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-packet_missed LE-hop_retry" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M580.2088009067626,2220.4103397182375L656.822265625,2308.546875L656.822265625,2352.296875" id="L-hop_retry-resync-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-resync" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M475.8147504783674,2205.7893598533674L309.123046875,2308.546875L309.123046875,2378.546875L309.123046875,2429.796875L404.08203125,2457.790311204514" id="L-hop_retry-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M496.0858650950851,2226.060474470085L438.705078125,2308.546875L438.705078125,2352.296875" id="L-hop_retry-reset_hop_count-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-hop_retry LE-reset_hop_count" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M438.705078125,2404.796875L438.705078125,2429.796875L460.2990186737805,2454.796875" id="L-reset_hop_count-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-reset_hop_count LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M656.822265625,2404.796875L656.822265625,2429.796875L561.86328125,2457.790311204514" id="L-resync-radio_hop_again-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-resync LE-radio_hop_again" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M738.939453125,2570.110842846593L917.498046875,2532.296875L917.498046875,2481.046875L917.498046875,2429.796875L917.498046875,2378.546875L917.498046875,2308.546875L917.498046875,2168.31640625L917.498046875,2046.8359375L917.498046875,1995.5859375L917.498046875,1925.5859375L917.498046875,1685.921875L917.498046875,1465.0078125L917.498046875,1413.7578125L917.498046875,1362.5078125L917.498046875,1273.7578125L917.498046875,1166.2578125L917.498046875,1033.41015625L917.498046875,900.5625L917.498046875,750.3828125L917.498046875,618.953125L917.498046875,567.703125L917.498046875,497.703125L917.498046875,329.4765625L917.498046875,180L600.380859375,137.66306615245287" id="L-end_loop-loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-end_loop LE-loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M482.97265625,2507.296875L482.97265625,2532.296875L612.048828125,2566.6574754808717" id="L-radio_hop_again-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-radio_hop_again LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path><path d="M693.0586937733963,1800.4803687266037L782.701171875,1925.5859375L782.701171875,1995.5859375L782.701171875,2046.8359375L782.701171875,2168.31640625L782.701171875,2308.546875L782.701171875,2378.546875L782.701171875,2429.796875L782.701171875,2481.046875L782.701171875,2532.296875L730.405059070122,2557.296875" id="L-delay-end_loop-0" class=" edge-thickness-normal edge-pattern-solid flowchart-link LS-delay LE-end_loop" style="fill:none;" marker-end="url(#flowchart-pointEnd)"></path></g><g class="edgeLabels"><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(257.720703125, 497.703125)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(350.23828125, 900.5625)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(255.21875, 1166.2578125)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(455.265625, 1166.2578125)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(70.18359375, 1033.41015625)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(785.3671875, 900.5625)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(534.822265625, 1925.5859375)"><g class="label" transform="translate(-13.34765625, -18.75)"><foreignObject width="26.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">no</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(656.822265625, 2308.546875)"><g class="label" transform="translate(-6.6796875, -18.75)"><foreignObject width="13.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">1</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(309.123046875, 2378.546875)"><g class="label" transform="translate(-17.34375, -18.75)"><foreignObject width="34.6875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">2-4</span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(438.705078125, 2308.546875)"><g class="label" transform="translate(-13.6875, -18.75)"><foreignObject width="27.375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">&gt;4</span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel"><g class="label" transform="translate(0, 0)"><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel"></span></div></foreignObject></g></g><g class="edgeLabel" transform="translate(782.701171875, 2308.546875)"><g class="label" transform="translate(-18.6796875, -18.75)"><foreignObject width="37.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="edgeLabel">yes</span></div></foreignObject></g></g></g><g class="nodes"><g class="node default default flowchart-label" id="flowchart-setup-29212" transform="translate(533.619140625, 26.25)"><polygon points="0,0 355.1953125,0 355.1953125,-52.5 0,-52.5 0,0 -8,0 363.1953125,0 363.1953125,-52.5 -8,-52.5 -8,0" class="label-container" transform="translate(-177.59765625,26.25)" style=""></polygon><g class="label" style="" transform="translate(-170.09765625, -18.75)"><rect></rect><foreignObject width="340.1953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">initialize_radio &amp; set_channel(0)</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-loop-29214" transform="translate(533.619140625, 128.75)"><rect style="" rx="26.25" ry="26.25" x="-66.76171875" y="-26.25" width="133.5234375" height="52.5"></rect><g class="label" style="" transform="translate(-52.69921875, -18.75)"><rect></rect><foreignObject width="105.3984375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">loop_start</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-receive.done-29216" transform="translate(418.162109375, 329.4765625)"><polygon points="124.4765625,0 248.953125,-124.4765625 124.4765625,-248.953125 0,-124.4765625" class="label-container" transform="translate(-124.4765625,124.4765625)" style=""></polygon><g class="label" style="" transform="translate(-90.7265625, -18.75)"><rect></rect><foreignObject width="181.453125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packet received?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-packet_received-29219" transform="translate(257.720703125, 567.703125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-112.8984375" y="-26.25" width="225.796875" height="52.5"></rect><g class="label" style="" transform="translate(-105.3984375, -18.75)"><rect></rect><foreignObject width="210.796875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packetsReceived++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-crc_check-29220" transform="translate(257.720703125, 750.3828125)"><polygon points="106.4296875,0 212.859375,-106.4296875 106.4296875,-212.859375 0,-106.4296875" class="label-container" transform="translate(-106.4296875,106.4296875)" style=""></polygon><g class="label" style="" transform="translate(-72.6796875, -18.75)"><rect></rect><foreignObject width="145.359375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">correct CRC?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-correctID-29226" transform="translate(350.23828125, 1033.41015625)"><polygon points="89.09765625,0 178.1953125,-89.09765625 89.09765625,-178.1953125 0,-89.09765625" class="label-container" transform="translate(-89.09765625,89.09765625)" style=""></polygon><g class="label" style="" transform="translate(-55.34765625, -18.75)"><rect></rect><foreignObject width="110.6953125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">correctID?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-IDerror-29228" transform="translate(255.21875, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-64.8515625" y="-26.25" width="129.703125" height="52.5"></rect><g class="label" style="" transform="translate(-57.3515625, -18.75)"><rect></rect><foreignObject width="114.703125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">IDerrors++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-radio_hop-29232" transform="translate(455.265625, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-85.1953125" y="-63.75" width="170.390625" height="127.5"></rect><g class="label" style="" transform="translate(-77.6953125, -56.25)"><rect></rect><foreignObject width="155.390625" height="112.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">next_channel<br/>    					hop_count=1<br/>    					last_time=time</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-crcErrors-29233" transform="translate(70.18359375, 1273.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-70.18359375" y="-26.25" width="140.3671875" height="52.5"></rect><g class="label" style="" transform="translate(-62.68359375, -18.75)"><rect></rect><foreignObject width="125.3671875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">crcErrors++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-wait_here-29237" transform="translate(157.697265625, 1413.7578125)"><rect class="basic label-container" style="" rx="0" ry="0" x="-143.58984375" y="-26.25" width="287.1796875" height="52.5"></rect><g class="label" style="" transform="translate(-136.08984375, -18.75)"><rect></rect><foreignObject width="272.1796875" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">wait on the same channel</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-end_receive_block-29242" transform="translate(610.703125, 1413.7578125)"><rect style="" rx="26.25" ry="26.25" x="-114.12890625" y="-26.25" width="228.2578125" height="52.5"></rect><g class="label" style="" transform="translate(-100.06640625, -18.75)"><rect></rect><foreignObject width="200.1328125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">end_receive_block</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-end_loop-29244" transform="translate(675.494140625, 2583.546875)"><rect style="" rx="26.25" ry="26.25" x="-63.4453125" y="-26.25" width="126.890625" height="52.5"></rect><g class="label" style="" transform="translate(-49.3828125, -18.75)"><rect></rect><foreignObject width="98.765625" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">end_loop</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-delay-29250" transform="translate(610.703125, 1685.921875)"><polygon points="195.9140625,0 391.828125,-195.9140625 195.9140625,-391.828125 0,-195.9140625" class="label-container" transform="translate(-195.9140625,195.9140625)" style=""></polygon><g class="label" style="" transform="translate(-143.4140625, -37.5)"><rect></rect><foreignObject width="286.828125" height="75"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">received pakect on time?<br/>    			(last_time-time&gt;2555+200)</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-hop_retry-29252" transform="translate(534.822265625, 2168.31640625)"><polygon points="96.48046875,0 192.9609375,-96.48046875 96.48046875,-192.9609375 0,-96.48046875" class="label-container" transform="translate(-96.48046875,96.48046875)" style=""></polygon><g class="label" style="" transform="translate(-62.73046875, -18.75)"><rect></rect><foreignObject width="125.4609375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">hop_count?</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-radio_hop_again-29253" transform="translate(482.97265625, 2481.046875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-78.890625" y="-26.25" width="157.78125" height="52.5"></rect><g class="label" style="" transform="translate(-71.390625, -18.75)"><rect></rect><foreignObject width="142.78125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">next_channel</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-resync-29254" transform="translate(656.822265625, 2378.546875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-90.87890625" y="-26.25" width="181.7578125" height="52.5"></rect><g class="label" style="" transform="translate(-83.37890625, -18.75)"><rect></rect><foreignObject width="166.7578125" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">numResyncs++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-packet_missed-29255" transform="translate(534.822265625, 1995.5859375)"><rect class="basic label-container" style="" rx="0" ry="0" x="-100.88671875" y="-26.25" width="201.7734375" height="52.5"></rect><g class="label" style="" transform="translate(-93.38671875, -18.75)"><rect></rect><foreignObject width="186.7734375" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">packetsMissed++</span></div></foreignObject></g></g><g class="node default default flowchart-label" id="flowchart-reset_hop_count-29264" transform="translate(438.705078125, 2378.546875)"><rect class="basic label-container" style="" rx="0" ry="0" x="-77.23828125" y="-26.25" width="154.4765625" height="52.5"></rect><g class="label" style="" transform="translate(-69.73828125, -18.75)"><rect></rect><foreignObject width="139.4765625" height="37.5"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; white-space: nowrap;"><span class="nodeLabel">hop_count=0</span></div></foreignObject></g></g></g></g></g></svg></div><h2 id='pruebas-2-estaciones'><span>Pruebas 2 estaciones</span></h2><ul><li><p><span>Midiendo de estación ID=1 y otra estación ID=2</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666667px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation" style=""><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">14725225:  Hopped channel and ready to receive.</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 4840 packetsMissed: 519 numResyncs: 205 crcErrors: 302 idErrors: 512</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 14725225 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 14725225/(2563+(1/16)) = 5745</span></p></li><li><p><span>Paquetes recibidos: 4840</span></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 4840+519=5.359</span></p></li><li><p><span>Resincronizaciones: 205 (cada resincronización ha requerido en promedio 2,53 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 302</span></p></li><li><p><span>Errores de ID: 512</span></p></li><li><p><span>Paquetes correctos: 4840 - 302 - 512 = 4026</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 4026/5.359=75%</span></p></li></ul></li><li><p><span>Midiendo de estación ID=1 y la otra estación con ID=6</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666664px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 1301 packetsMissed: 106 numResyncs: 45 receivedStreak: 66 crcErrors: 68 idErrors: 156</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">3894281:  0 - Data: E1 0 0 80 5 0 A 37 FF FF &nbsp; RSSI: -53 CRC: OK</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 3894281 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 3894281/(2563+(1/16)) = 1519</span></p></li><li><p><span>Paquetes recibidos: 1301</span></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 1301+106=1407</span></p></li><li><p><span>Resincronizaciones: 45 (cada resincronización ha requerido en promedio 2,35 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 68</span></p></li><li><p><span>Errores de ID:156</span></p></li><li><p><span>Paquetes correctos:1301 - 68 - 156 = 1077</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 1077/1407=76,5%</span></p></li></ul></li><li><p><span>Repitiendo y dejando toda la noche</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666668px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 21205 packetsMissed: 2501 numResyncs: 975 receivedStreak: 66 crcErrors: 1483 idErrors: 2564</span></pre></div><div class="" style="position: relative;"><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">65581295</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 70px;"></div><div class="CodeMirror-gutters" style="display: none; height: 70px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 65581295 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 65581295/(2563+(1/16)) = 25587</span></p></li><li><p><span>Paquetes recibidos: 21205 </span><strong><span>(82%)</span></strong></p></li><li><p><span>Paquetes que se deberían haber recibido (recibidos + missed) : 21205+2501=23.706</span></p></li><li><p><span>Resincronizaciones: 975 (cada resincronización ha requerido en promedio 2,56 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 1483 </span><strong><span>(6,9%)</span></strong></p></li><li><p><span>Errores de ID: 2564 </span><strong><span>(12%)</span></strong></p></li><li><p><span>Paquetes correctos: 21205 - 1483 - 2564 = 17158</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 17158/23706=</span><strong><span>72,3%</span></strong></p></li></ul></li></ul><h2 id='pruebas-3-estaciones'><span>Pruebas 3 estaciones </span></h2><ul><li><p><span>ID=1 + ID=0, ID=6</span></p><pre class="md-fences md-end-block ty-contain-cm modeLoaded" spellcheck="false" lang=""><div class="CodeMirror cm-s-inner cm-s-null-scroll CodeMirror-wrap" lang=""><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 9.666667px; left: 8px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="position: absolute; bottom: -1em; padding: 0px; width: 1000px; height: 1em; outline: currentcolor;"></textarea></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 0px; margin-bottom: 0px; border-right-width: 0px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines" role="presentation"><div role="presentation" style="position: relative; outline: currentcolor;"><div class="CodeMirror-measure"><pre><span>xxxxxxxxxx</span></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-code" role="presentation"><div class="CodeMirror-activeline" style="position: relative;"><div class="CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: 0px; width: 0px;"></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">3706815:  3 - Data: E6 0 0 80 5 0 C2 76 FF FF &nbsp; RSSI: -54 CRC: OK</span></pre></div><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">:  Wrong Station ID or CRC waiting in the same channel</span></pre><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;">packetsReceived: 1194 packetsMissed: 139 numResyncs: 57 receivedStreak: 13 crcErrors: 160</span></pre><pre class=" CodeMirror-line " role="presentation"><span role="presentation" style="padding-right: 0.1px;"> idErrors: 317</span></pre></div></div></div></div></div><div style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 117px;"></div><div class="CodeMirror-gutters" style="display: none; height: 117px;"></div></div></div></pre><ul><li><p><span>Tiempo transcurrido: 3706815 ms</span></p></li><li><p><span>Paquetes posibles (teóricos): 3706815/(2563+(1/16)) = 1446</span></p></li><li><p><span>Paquetes recibidos: 1194 </span><strong><span>(82%)</span></strong></p></li><li><p><span>Paquetes que se deberían haber recibido según el código (recibidos + missed) :1194+139=1333</span></p></li><li><p><span>Resincronizaciones: 57 (cada resincronización ha requerido en promedio 2,43 hops con sus correspondientes esperas)</span></p></li><li><p><span>Errores de CRC: 160 </span><strong><span>(13,4%)</span></strong></p></li><li><p><span>Errores de ID: 317 </span><strong><span>(26,5%)</span></strong></p></li><li><p><span>Paquetes correctos: 1194 -160 - 317=717</span></p></li><li><p><span>Tasa (paquetes correctos / paquete que se deberían haber recibido): 717/1333 = </span><strong><span>53%</span></strong></p></li></ul></li><li><p><span>ID=1 + ID=0, ID=6 (toda la noche) --&gt; pendiente</span></p></li></ul></div></div>
</body>
</html>
 


