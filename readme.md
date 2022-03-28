# Opstart af bibliotek:
For at bruge biblioteket skal man importere biblioteket i sin sketch, dette gøres ved at skrive følgende linjer i toppen af koden:

```c
#include <SDU_CAR.h> // Including the SDU Car library

CAR car; // Initializing the controls for the motors
DATA cardata; // Initializing the sensors
LOG carlog; // Initializing the SD Card communication
```
I din setup skal du inkludere følgende to linjer kode, disse gøre det muligt at skrive til SD Kort samt læse diverse sensore:

```c
void setup() {​​​​

  car.begin(); // Initializing the motors and latch
  cardata.begin(); // Initializing tachometer and communication with MEMS
  carlog.begin(); // Initializing communication with the SD Card
 
}​​​​​​​​​​​
```
\pagebreak
# Brug af biblioteket:

## Indstilling af hastigheden på bilen:
```c
void CAR::setCarSpeed(int left_speed, int right_speed)  // Controls the motor speed
```
> Note: Dette er funktionen i biblioteket. Ikke hvordan man bruger den (Husk dette når du læser dokumentationen!)

Tager to parametre som input. Venste- og højre hastighed. Hastigheden er i procent, det vil sige at fremad er plus, og bagud er minus. Det vil sige at du kan sætte det venstre hjul til at køre 75% og højre til -75% for at dreje rundt på stedet.

**Eksempel på brug:**
```c
car.setCarSpeed(75, -75)  // Notice that you need to call the class 'car' in order to
                          // use this function
```
\pagebreak
## Brug af accelerometer:
```c
void DATA::readAccel()  // Reads the accelerometer and stores the values for later use

float DATA::getAccel(accel_data_dir_t dir)  // Returns the value of the accelerometer
                                            // in the specified direction
```
Når du skal aflæse værdien af accelerometeret skal du først læse dataen fra MEMS sensoren, herefter kan du hente de individuelle akser ned ved efterfølgende at kalde funktionen getAccel().

**Eksempel på brug:**
```c
cardata.readAccel();  // Reads the data from the MEMS sensor, requred!
 
float xAxis = cardata.getAccel(x);  // Returns the data from the x axis, 
                                    // parameters: [x, y, z]
```
> HUSK AT KALDE LÆSEFUNKTION, ELLERS VIRKER DET IKKE!
\pagebreak
## Brug af tachometer:

```c
unsigned int DATA::getTachoLeft(void)  // Returns no. of ticks on the left tachometer
 
unsigned int DATA::getTachoRight(void)  // Returns no. of ticks on the right tachometer

float DATA::getDistLeft(void)  // Returns distance driven on left wheel in meters

float DATA::getDistRight(void)  // Returns distance driven on right wheel in meters

void DATA::resetTacho(void)  // Resets both tacho counters, including distance driven
```
Brug den eller de funktioner som er nødvendige. Skal du tracke at du køre baglæns skal dette gøres ved at nulstille tachomoteret og huske at den kørte distance er baglens, da tachometeret vil tælle op uanset retning.

**Eksempel på brug:**
```c
unsigned int leftWheelTicks = cardata.getTachoLeft(); // Set the variable to the number 
                                                      // of ticks

unsigned int rightWheelTicks = cardata.getTachoRight(); // Set the variable to the number 
                                                        // of ticks

float distanceDrivenLeft = cardata.getDistLeft();  // Sets the variable to the distance 
                                                   // driven on the left wheel

float distanceDrivenRight = cardata.getDistRight();  // Sets the variable to the distance 
                                                     // driven on the right wheel
 
cardata.resetTacho();
```
\pagebreak
## Aflæsning af batterispænding:

```c
float DATA::getBatteryVoltage(void)  // Returns the battery voltage
```
Funktionen retunerer batterispændingen.

**Eksempel på brug:**
```c
float batteryVoltage = cardata.getBatteryVoltage(); // Set the variable to the battery
                                                    // voltage
```
\pagebreak
## Aflæsning af linje sensor:

```c
void DATA::getLineSensor()  // Reads the line sensor and stores the values for later use


int DATA::getLineSensor(char sensor_number)  // Returns the value of the sensor based
                                              // on the sensor number: [1, 2, 3, 4, 5]
```
Når du skal aflæse værdien af fra linje sensoren, skal du først læse dataen fra sensoren, dette gør du med kaldet, readLineSensor(), herefter kan du hente de individuelle sensorværdier ned, ved at kalde getLineSensor(x).

**Eksempel på brug:**
```c
cardata.readLineSensor();   // Read the line sensor, REQUIRED!

int sensor4 = cardata.getLineSensor(4); // Set the var to the value of line sensor 4 
                                        // to the variable
```
> HUSK AT KALDE LÆSEFUNKTION, ELLERS VIRKER DET IKKE!

![Line Sensor Posistion](https://raw.githubusercontent.com/sdutek/sducar_files/main/line_sensor_posistion.png)
\pagebreak
## Aflæsning af tid:

```c
float DATA::t(void)  // Returns the time in seconds
```
Funktionen retunerer tiden der er gået siden arduinoen blev tændt i sekunder

**Eksempel på brug:**
```c
float secsSinceBoot = cardata.t(); // Set the var time in seconds since Arduino boot
```
\pagebreak
## Indstilling af lys (Shift Register)

```c
void CAR::setShiftReg(uint8_t lightByte) // Set the output of each of the Shift Register to either 1 or 0.
```
Denne funktion kan bruges til at styre lyset på bilerne. Hver byte kan ses i tabellen nedenfor:

| Bit # | 7 | 6| 5| 4 | 3 | 2 | 1 | 0 |
|    :----:   |    :----:   |    :----:   |    :----:   |    :----:   |    :----:   |    :----:   |    :----:   |    :----:   |
| Name | Brake left | Brake right| Indicator left | Indicator right | Low beam right | High beam right | Low beam left | High beam left |

**Eksempel på brug:**
```c
car.setShiftReg(0b00001111); // Turn on half of the lights, leave the rest off.
delay(500);
car.setShiftReg(0b11110000); // Turn on the other half of the lights, and turn the first ones off again.
```
\pagebreak
## Logning af data til SD Kort


```c
void LOG::log(const String& logdata)  // Logs data parsed in the function to the SD Card
```

Når der skal logges data til SD Kort skal det gøres på en meget bestemt måde. Det foregår via en String(tekststreng).
Fuldstændig som hvis du ville lave en serial print:

```c
Serial.println("Hello World!");
```
Hvad der står mellem paranteserne er hvad der bliver skrevet i konsollen. På samme måde vil der blive logget til SD Kortet:

```c
carlog.log("Hello World!");
```
Ønsker du at logge værdier fra en sensor på sd kortet skal disse konverteres til en streng, dette kan gøres således:

```c
String(value, numberOfDigits) // Converts a numerical value to a string
```

Hvor value er den værdi du ønsker at lave til en streng, og numberOfDigits er antallet af chifre du ønsker efter kommaet. Nu kan værdien af sensoren bruges sammen med tekst i din log:

```c
float distanceDriven = cardata.getDistRight(); // Reading the distance driven
 
carlog.log("This is distance driven: " + String(distanceDriven, 2) + "cm");
 
### Output to SD Card: This is distance driven: 48.26cm
```
Skal dataen bruges til databehandling kan man bruge "\t" mellem forskellige data, så kan computeren selv opdele data efterfølgende:

```c
carlog.log(String(data1, 2) + "\t" + String(data2, 2) + "\t" + String(data3, 2));
```
Eksempel på hvordan en logfil ser ud lavet på denne måde:

![Text Log](https://raw.githubusercontent.com/sdutek/sducar_files/main/text_log.png)

Og efterfølgende kopieret in i Excel:

![Excel Log](https://raw.githubusercontent.com/sdutek/sducar_files/main/excel_log.png)

Skal tiden der er gået logges kan du bruge følgende:

```c
carlog.log(String(cardata.t(), 4) + "\t" + String(data, 2)); // Logs the first column as time
                                                             // in seconds, followed by data
```



