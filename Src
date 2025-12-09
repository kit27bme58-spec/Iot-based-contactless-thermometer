#define BLYNK_TEMPLATE_ID "TMPL35ysfqiw6"
#define BLYNK_TEMPLATE_NAME "sansusa"
#define BLYNK_AUTH_TOKEN "1pn4lsWbacl_znWTGXs4y_IbFF9TRZj9"

#include <Wire.h>
#include <Adafruit_MLX90614.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

char ssid[] = "Sanjai@0077";      // Wi-Fi SSID
char pass[] = "7777777777";       // Wi-Fi Password

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

Adafruit_MLX90614 mlx = Adafruit_MLX90614();
BlynkTimer timer;

#define BUZZER_PIN D5     // Buzzer pin
#define TEMP_ALERT 38.0   // Alert threshold in °C

// ---- Send Object Temp to Blynk ----
void sendSensor() {
  float objectC = mlx.readObjectTempC();

  // Validate reading
  if (isnan(objectC)) {
    Serial.println("Invalid sensor reading, skipping update");
    return;
  }

  // Send to Blynk
  Blynk.virtualWrite(V5, objectC);   // Object Temp °C

  // Buzzer control
  int buzzerState = LOW;
  if (objectC >= TEMP_ALERT) {
    digitalWrite(BUZZER_PIN, HIGH);
    buzzerState = HIGH;
  } else {
    digitalWrite(BUZZER_PIN, LOW);
  }
  Blynk.virtualWrite(V6, buzzerState);  // Buzzer status 1=ON, 0=OFF

  // Serial output
  Serial.print("Object Temp: "); Serial.print(objectC, 1); Serial.print(" °C | ");
  Serial.print("Buzzer: "); Serial.println(buzzerState == HIGH ? "ON" : "OFF");
  Serial.println("----------------------------------");

  // OLED display
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.println("Temperature Monitor");

  display.setTextSize(2);
  display.setCursor(0, 25);
  display.print("Obj: ");
  display.print(objectC, 1);
  display.println("C");

  display.display();
}

void setup() {
  Serial.begin(9600);
  Wire.begin(D2, D1); // SDA, SCL

  pinMode(BUZZER_PIN, OUTPUT);
  digitalWrite(BUZZER_PIN, LOW);

  // Start Blynk
  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  // Initialize MLX90614
  if (!mlx.begin()) {
    Serial.println("Error: MLX90614 not found!");
    while (1);
  }
  Serial.println("MLX90614 ready...");

  // Initialize OLED
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("SSD1306 allocation failed");
    while (1);
  }
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.println("MLX90614 + Blynk Ready");
  display.display();
  delay(2000);

  // Schedule updates every 2 seconds
  timer.setInterval(2000L, sendSensor);
}

void loop() {
  Blynk.run();
  timer.run();
}
