Proyecto ESP32 con Pantalla OLED: Predice tu "Destino" con Humor

Este proyecto utiliza un ESP32 y una pantalla OLED para mostrar mensajes humorísticos sobre la
edad y la causa de tu "muerte" al presionar un botón. Al iniciar, se muestra el mensaje "¿CUANDO
MORIRÁS?". Al presionar el botón por primera vez, aparece un mensaje específico. En pulsaciones
subsecuentes, se selecciona aleatoriamente un mensaje de una lista. Cada mensaje se muestra
durante unos segundos antes de volver al mensaje inicial. ¡Una divertida combinación de electrónica
y creatividad para arrancar una sonrisa!

#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
#define SCREEN_ADDRESS 0x3C

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

const int buttonPin = 18;
int buttonState = 0;
int lastButtonState = 0;

String options[] = {
  "Edad 48: Un resorte de la cama se rebelo contra ti.",
  "Edad 55: Una avalancha de almohadas te sepulto.",
  "Edad 72: Te dieron un abrazo tan fuerte que no pudiste respirar.",
  "Edad 87: Una ola de gelatina te atrapo en el mar."
};

bool firstPress = true; // Variable para controlar si es la primera pulsación

void setup() {
  Wire.begin();

  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  
  display.clearDisplay(); // Limpia la pantalla después de inicializar
  display.display();
  delay(2000);

  pinMode(buttonPin, INPUT_PULLUP);
  
  // Mostrar el mensaje de inicio
  display.setTextSize(2.5); // Tamaño entero
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.println("CUANDO    MORIRAS?");
  display.display();
}

void loop() {
  buttonState = digitalRead(buttonPin);

  if (buttonState == LOW && lastButtonState == HIGH) {
    delay(1500); // Esperar 1.5 segundos
    
    display.clearDisplay();
    
    if (firstPress) {
      // Mostrar la primera opción en la primera pulsación
      display.setTextSize(1.7);
      display.setCursor(0, 0);
      display.println("Edad 27: Lo atropello el Gusanito en las  Poli fiestas, jamas  pudo ver a la Tri    alzar la copa mundial de   futbol ...");
      firstPress = false; // Cambiar el estado para no volver a mostrar esta opción
    } else {
      // Mostrar una opción aleatoria en las siguientes pulsaciones
      int randomIndex = random(0, sizeof(options) / sizeof(options[0]));
      display.setTextSize(1.7);
      display.setCursor(0, 0);
      display.println(options[randomIndex]);
    }
    
    display.display();
    
    delay(9000); // Mostrar el mensaje durante 9 segundos
    
    display.clearDisplay();
    // Regresar al mensaje de inicio
    display.setTextSize(2.5); // Tamaño entero
    display.setCursor(0, 0);
    display.println("CUANDO    MORIRAS?");
    display.display();
  }

  lastButtonState = buttonState;
  delay(50); // Pequeño retraso para evitar rebotes
}
