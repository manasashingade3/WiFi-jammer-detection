# WiFi-jammer-detection system
An ESP32-based security tool called the Wi-Fi Jammer Detection System is made to identify beacon faking and Wi-Fi jamming threats. It keeps an eye on nearby Wi-Fi networks and notifies users of any questionable activities. For real-time notifications, the system makes use of an LCD display, LEDs, a buzzer, and Blynk IoT connection.

When the ESP32 loses Wi-Fi connectivity, it is detected by Wi-Fi Disconnection Monitoring.
Multiple duplicate SSIDs are detected by Beacon Attack Detection, which suggests spoofing efforts.
When a jammer is detected, a buzzer and LED alert are activated.
Real-time status updates are provided by the LCD display.
Blynk Integration: Uses the Blynk IoT platform to provide notifications.
Remote Reset: Using the Blynk app, users can remotely reset the device.
