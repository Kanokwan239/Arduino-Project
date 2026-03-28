#define BLYNK_PRINT Serial
#include <BlynkSimpleStream.h> // ใช้สำหรับการเชื่อมต่อผ่านสาย USB (Serial)
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// 1. ใส่ Auth Token จาก Blynk ของคุณ
char auth[] = "UfSCQYafNC1V6DHg_g_K8qmo3J7emgd-";

// 2. ตั้งค่าจอ LC
LiquidCrystal_I2C lcd(0x27, 16, 2);

// 3. กำหนดพินอุปกรณ์
const int trigPin = 9;
const int echoPin = 10;
const int ledGreen = 2;
const int ledYellow = 3;
const int ledRed = 4;
const int buzzer = 5;

BlynkTimer timer;

// ฟังก์ชันวัดระยะทางและประมวลผล
void checkWaterLevel() {
  long duration;
  int distance;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  if (distance <= 0) distance = 0;

  // แสดงผลบน LCD
  lcd.setCursor(0, 0);
  lcd.print("Dist: ");
  lcd.print(distance);
  lcd.print(" cm   ");

  // ตรรกะการแจ้งเตือน (อิงตามเงื่อนไขเดิมของคุณ)
  if (distance <= 15) {
    digitalWrite(ledRed, HIGH);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledGreen, LOW);
    digitalWrite(buzzer, HIGH);
    lcd.setCursor(0, 1);
    lcd.print("Status: DANGER! ");
    
    // ส่งค่าไป Blynk (V1) และเปลี่ยนสี Widget เป็นสีแดง
    Blynk.virtualWrite(V1, distance);
    Blynk.setProperty(V1, "color", "#FF0000"); 
  } 
  else if (distance >= 16 && distance <= 30) {
    digitalWrite(ledRed, LOW);
    digitalWrite(ledYellow, HIGH);
    digitalWrite(ledGreen, LOW);
    
    // เสียงเตือนเป็นจังหวะ
    digitalWrite(buzzer, HIGH);
    delay(100);
    digitalWrite(buzzer, LOW);
    
    lcd.setCursor(0, 1);
    lcd.print("Status: WARNING ");
    
    // ส่งค่าไป Blynk (V1) และเปลี่ยนสี Widget เป็นสีส้ม
    Blynk.virtualWrite(V1, distance);
    Blynk.setProperty(V1, "color", "#FFA500");
  } 
  else {
    digitalWrite(ledRed, LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledGreen, HIGH);
    noTone(buzzer);
    
    lcd.setCursor(0, 1);
    lcd.print("Status: SAFE    ");
    
    // ส่งค่าไป Blynk (V1) และเปลี่ยนสี Widget เป็นสีเขียว
    Blynk.virtualWrite(V1, distance);
    Blynk.setProperty(V1, "color", "#00FF00");
  }
}

void setup() {
  // เริ่มต้น Serial สำหรับ Blynk USB
  Serial.begin(9600);
  Blynk.begin(Serial, auth);

  lcd.init();
  lcd.backlight();
  
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledGreen, OUTPUT);
  pinMode(ledYellow, OUTPUT);
  pinMode(ledRed, OUTPUT);
  pinMode(buzzer, OUTPUT);

  // ตั้งเวลาให้ฟังก์ชันทำงานทุก 0.5 วินาที
  timer.setInterval(500L, checkWaterLevel);
}

void loop() {
  Blynk.run();
  timer.run();
}
