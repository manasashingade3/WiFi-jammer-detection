# WiFi-jammer-detection system
An ESP32-based security tool called the Wi-Fi Jammer Detection System is made to identify beacon faking and Wi-Fi jamming threats. It keeps an eye on nearby Wi-Fi networks and notifies users of any questionable activities. For real-time notifications, the system makes use of an LCD display, LEDs, a buzzer, and Blynk IoT connection.

When the ESP32 loses Wi-Fi connectivity, it is detected by Wi-Fi Disconnection Monitoring.
Multiple duplicate SSIDs are detected by Beacon Attack Detection, which suggests spoofing efforts.
When a jammer is detected, a buzzer and LED alert are activated.
Real-time status updates are provided by the LCD display.
Blynk Integration: Uses the Blynk IoT platform to provide notifications.
Remote Reset: Using the Blynk app, users can remotely reset the device.


Components Required

ESP32 Development Board,
LiquidCrystal I2C LCD (16x2),
Buzzer,
LEDs (Red & Green),
Wi-Fi Network,
Blynk App (for remote monitoring)

CODE

#define BLYNK_TEMPLATE_ID "TMPL3u6B-Jn33"
#define BLYNK_TEMPLATE_NAME "wifi jammer detection system"
#define BLYNK_AUTH_TOKEN "eoIb0VfdmEKitfC9bQroe85lMvo8aN8W"

#include <WiFi.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <BlynkSimpleEsp32.h>

// LCD setup: I2C address 0x27, 16 characters, 2 lines
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pin definitions
#define GREEN_LED  12  // Green LED for normal status
#define RED_LED    27  // Red LED for alerts
#define BUZZER     27  // Buzzer pin

// Wi‑Fi credentials (replace with your network details)
const char* ssid = "Realme6";
const char* password = "1234567890";

// Connection monitoring settings
unsigned long connectionTimeout = 5000; // 5 seconds timeout for connection attempts

// Threshold for duplicate SSIDs to consider it suspicious
const int duplicateThreshold = 2;

// Flag to indicate if an alert has been triggered
bool alertActive = false;

void setup() {
  Serial.begin(115200);

  pinMode(GREEN_LED, OUTPUT);
  pinMode(RED_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);

  digitalWrite(GREEN_LED, LOW);
  digitalWrite(RED_LED, LOW);
  digitalWrite(BUZZER, LOW);

  lcd.init();
  lcd.backlight();
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Wi-Fi Detector");
  lcd.setCursor(0, 1);
  lcd.print("Initializing...");
  delay(2000);
  lcd.clear();

  connectToWiFi();

  // Initialize Blynk if Wi‑Fi is connected
  if(WiFi.status() == WL_CONNECTED) {
    Blynk.begin(BLYNK_AUTH_TOKEN, ssid, password);
  }
}

void loop() {
  Blynk.run();  // Process Blynk tasks

  if (!alertActive) {
    checkWiFiStatus();
    delay(5000);  // Check every 5 seconds
  }
}

void checkWiFiStatus() {
  int networksFound = WiFi.scanNetworks();

  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("Wi-Fi connection lost.");
    
    if (networksFound == 0) {
      Serial.println("No networks detected – Possible Wi-Fi Jammer!");
      triggerAlert("Jammer Detected!");
    } else {
      Serial.println("Wi-Fi disappeared – Device turned off?");
      triggerAlert("Wi-Fi Off?");
    }
    connectToWiFi();
    return;
  }

  bool beaconAttackDetected = false;
  for (int i = 0; i < networksFound; i++) {
    String currentSSID = WiFi.SSID(i);
    int count = 0;
    for (int j = 0; j < networksFound; j++) {
      if (WiFi.SSID(j) == currentSSID) {
        count++;
      }
    }
    if (count >= duplicateThreshold) {
      beaconAttackDetected = true;
      Serial.printf("Beacon attack: Detected %d clones of SSID %s\n", count, currentSSID.c_str());
      break;
    }
  }

  if (beaconAttackDetected) {
    triggerAlert("Beacon Attack!");
  } else {
    normalStatus();
  }
}

void connectToWiFi() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Connecting...");
  Serial.print("Connecting to ");
  Serial.println(ssid);
  
  WiFi.begin(ssid, password);
  unsigned long startAttemptTime = millis();
  
  while (WiFi.status() != WL_CONNECTED && millis() - startAttemptTime < connectionTimeout) {
    delay(500);
    Serial.print(".");
  }
  
  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("\nConnected!");
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Connected!");
    digitalWrite(GREEN_LED, HIGH);
    digitalWrite(RED_LED, LOW);
    delay(2000);
  } else {
    Serial.println("\nConnection Failed");
    triggerAlert("Conn Failed!");
  }
}

void triggerAlert(String alertMessage) {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(alertMessage);
  digitalWrite(GREEN_LED, LOW);

  // Send Blynk notification if jammer is detected
  if(alertMessage == "Jammer Detected!") {
    Blynk.logEvent("jammer_detected");  // Create an event named "jammer_detected" in your Blynk dashboard
  }
  
  Serial.println("🚨 ALERT! Restart device to reset.");
  alertActive = true; // Keep the alert active until restart

  // Infinite loop: Red LED & Buzzer keep blinking
  while (true) {
    digitalWrite(RED_LED, HIGH);
    digitalWrite(BUZZER, HIGH);
    delay(300);
    
    digitalWrite(RED_LED, LOW);
    digitalWrite(BUZZER, LOW);
    delay(300);
  }
}

void normalStatus() {
  if (!alertActive) { // Only update if no alert is active
    digitalWrite(GREEN_LED, HIGH);
    digitalWrite(RED_LED, LOW);
    digitalWrite(BUZZER, LOW);

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Wi-Fi Connected");
    lcd.setCursor(0, 1);
    lcd.print(WiFi.localIP());
  }
}

// Blynk virtual switch on V1 used to reset the board.
// When switched ON in the Blynk app, the board will restart.
BLYNK_WRITE(V1) {
  int pinValue = param.asInt();
  if (pinValue == 1) {
    ESP.restart();
  }
}

How It Works

Monitors Wi-Fi Networks: Scans available SSIDs every 5 seconds.
Detects Disconnection: Triggers an alert if the ESP32 loses connection unexpectedly.
Detects Spoofing (Beacon Attack): Flags duplicate SSIDs appearing multiple times.

Future Improvements

Triggers Alerts: If a jammer or spoofing attack is detected, it triggers a buzzer, LED, and a Blynk notification.
Adding support for GSM-based alerts (SMS notifications),
Data logging for historical attack analysis,
Integration with a mobile app for enhanced monitoring.Contributing,
