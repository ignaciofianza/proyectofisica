# Sistema de Control de Riego Automático  

### **Proyecto:** Sistema de control de riego automático  
**Autores:** Sebastián Benítez, Ignacio Fianza, Santiago Figueroa, Tomás Paz, Agustín Silva  
**Institución:** Instituto Superior Brazo Oriental  
**Materia:** Física  
**Profesor/a:** Verónica Otamendi  
**Fecha:** Noviembre, 2024  

---

## Descripción  
Este proyecto es un sistema automatizado diseñado para gestionar el riego de plantas en función de los niveles de humedad del suelo. Utiliza sensores para medir la humedad en diferentes zonas, y, según los valores obtenidos, controla bombas de agua a través de relés para optimizar el uso de recursos hídricos.

## Características principales  
- Monitoreo en tiempo real de la humedad del suelo en cuatro sectores distintos.  
- Activación automática de bombas de agua cuando la humedad cae por debajo de un umbral definido.  
- Visualización del estado de las bombas y los valores de los sensores en una pantalla LCD.  
- Configuración sencilla y adaptable a diferentes necesidades de riego.  

---

## Materiales necesarios  
- **Microcontrolador:** Arduino UNO  
- **Sensores de humedad del suelo:** 4 unidades  
- **Relés de 5V:** 4 unidades  
- **Bombas de agua:** 4 unidades  
- **Pantalla LCD con módulo I2C**  
- **Cables, protoboard y fuente de alimentación**  

---

## Instalación  
1. Descarga e instala el [Arduino IDE](https://www.arduino.cc/en/software).  
2. Conecta los componentes siguiendo el diagrama del circuito.  
3. Carga el código en el Arduino UNO a través del IDE.  
4. Ajusta el umbral de humedad (`umbralHumedad`) según sea necesario.  
5. Enciende el sistema y verifica su funcionamiento.  

---

## Código fuente  
```arduino
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// pines para los sensores de humedad
const int pinSensorHumedad1 = A0;
const int pinSensorHumedad2 = A1;
const int pinSensorHumedad3 = A2;
const int pinSensorHumedad4 = A3;

// pines para los relés que controlan las bombas
const int pinRele1 = 8;
const int pinRele2 = 9;
const int pinRele3 = 10;
const int pinRele4 = 11;

// variables para almacenar los valores de humedad
int valorHumedad1 = 0;
int valorHumedad2 = 0;
int valorHumedad3 = 0;
int valorHumedad4 = 0;

// variables para almacenar el estado de los motores
int estadoMotor1 = 0;
int estadoMotor2 = 0;
int estadoMotor3 = 0;
int estadoMotor4 = 0;

int umbralHumedad = 1000;  // umbral de humedad para activar las bombas

// crea el objeto para la pantalla LCD
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  // configura los pines de los relés como salida y los apaga inicialmente
  pinMode(pinRele1, OUTPUT);
  pinMode(pinRele2, OUTPUT);
  pinMode(pinRele3, OUTPUT);
  pinMode(pinRele4, OUTPUT);

  digitalWrite(pinRele1, HIGH); // relé apagado con lógica inversa
  digitalWrite(pinRele2, HIGH);
  digitalWrite(pinRele3, HIGH);
  digitalWrite(pinRele4, HIGH);

  Serial.begin(9600); // inicia la comunicación serial para monitorizar valores

  // inicia la pantalla LCD y muestra un mensaje de inicio
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Iniciando...");
  delay(1000); // espera un segundo para dar tiempo al arranque
}

void loop() {
  // lee los valores de los sensores de humedad
  valorHumedad1 = analogRead(pinSensorHumedad1);
  valorHumedad2 = analogRead(pinSensorHumedad2);
  valorHumedad3 = analogRead(pinSensorHumedad3);
  valorHumedad4 = analogRead(pinSensorHumedad4);

  // envía los valores de humedad a través del puerto serial
  Serial.print(valorHumedad1);
  Serial.print(" ");
  Serial.print(valorHumedad2);
  Serial.print(" ");
  Serial.print(valorHumedad3);
  Serial.print(" ");
  Serial.print(valorHumedad4);
  Serial.print(" ");
  
  // determina el estado de los motores según el umbral de humedad
  estadoMotor1 = (valorHumedad1 > umbralHumedad) ? 1 : 0;
  estadoMotor2 = (valorHumedad2 > umbralHumedad) ? 1 : 0;
  estadoMotor3 = (valorHumedad3 > umbralHumedad) ? 1 : 0;
  estadoMotor4 = (valorHumedad4 > umbralHumedad) ? 1 : 0;

  // envía el estado de los motores a través del puerto serial
  Serial.print(estadoMotor1);
  Serial.print(" ");
  Serial.print(estadoMotor2);
  Serial.print(" ");
  Serial.print(estadoMotor3);
  Serial.print(" ");
  Serial.println(estadoMotor4);

  lcd.setCursor(0, 0);

  // controla la bomba 1
  if (estadoMotor1) {
    digitalWrite(pinRele1, LOW); // enciende la bomba (lógica inversa)
    lcd.print("B1: ON  ");
  } else {
    digitalWrite(pinRele1, HIGH); // apaga la bomba
    lcd.print("B1: OFF ");
  }

  // controla la bomba 2
  if (estadoMotor2) {
    digitalWrite(pinRele2, LOW); // enciende la bomba
    lcd.print("B2: ON  ");
  } else {
    digitalWrite(pinRele2, HIGH); // apaga la bomba
    lcd.print("B2: OFF ");
  }

  lcd.setCursor(0, 1);

  // controla la bomba 3
  if (estadoMotor3) {
    digitalWrite(pinRele3, LOW); // enciende la bomba
    lcd.print("B3: ON  ");
  } else {
    digitalWrite(pinRele3, HIGH); // apaga la bomba
    lcd.print("B3: OFF ");
  }

  // controla la bomba 4
  if (estadoMotor4) {
    digitalWrite(pinRele4, LOW); // enciende la bomba
    lcd.print("B4: ON  ");
  } else {
    digitalWrite(pinRele4, HIGH); // apaga la bomba
    lcd.print("B4: OFF ");
  }

  delay(1000); // espera 1 segundo antes de la próxima lectura
}
