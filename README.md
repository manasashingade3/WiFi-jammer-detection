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


How It Works

Monitors Wi-Fi Networks: Scans available SSIDs every 5 seconds.
Detects Disconnection: Triggers an alert if the ESP32 loses connection unexpectedly.
Detects Spoofing (Beacon Attack): Flags duplicate SSIDs appearing multiple times.

Future Improvements

Triggers Alerts: If a jammer or spoofing attack is detected, it triggers a buzzer, LED, and a Blynk notification.
Adding support for GSM-based alerts (SMS notifications),
Data logging for historical attack analysis,
Integration with a mobile app for enhanced monitoring.Contributing,
