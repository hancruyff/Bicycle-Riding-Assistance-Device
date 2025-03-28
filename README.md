# 🚴‍♂️ 자전거 주행 보조 시스템

**아두이노 + 3종 센서 + 모터 제어 + 자동 조명 + 긴급 문자 전송 시스템**  
기울기 방향에 따라 **자동 핸들 보정**,  
어두운 환경에선 **LED 조명 자동 점등**,  
충격 발생 시 **사고 감지 및 SMS 전송**을 수행하는 스마트 자전거 보조 장치입니다.

---

## 📂 목차

- [1. 개요](#1-개요)
- [2. 실행 화면](#2-실행-화면)
- [3. 시스템 구성](#3-시스템-구성)
- [4. 주요 기능](#4-주요-기능)
- [5. 코드 설명](#5-코드-설명)
- [6. 프로젝트 구조](#6-프로젝트-구조)
- [7. 기술 스택](#7-기술-스택)
- [8. 학습 포인트](#8-학습-포인트)

---

## 1. 📌 개요

이 프로젝트는 자전거 사용자의 안전을 위해 제작된 **스마트 주행 보조 시스템**입니다.

### 🛠 주요 센서 및 제어 장치:
- **기울기 센서** → 자동 핸들 방향 제어
- **조도 센서 (LDR)** → 어두울 때 자동 LED 점등
- **충격 센서** → 사고 판단 시 SMS 자동 전송

---

## 2. 🖼 실행 화면

![ezgif com-animated-gif-maker](https://github.com/user-attachments/assets/a91fd7cf-b8ea-4c6d-afcd-a07456a7aefd)

---

## 3. 🔌 시스템 구성

- 🧠 **Arduino Uno**: 전체 센서/모터 제어 및 파이썬 통신
- 🧭 **기울기 센서 (Tilt)**: 자전거 기울기 감지 → 모터를 통해 핸들을 자동 보정
- 🌘 **조도 센서 (LDR)**: 주변 밝기 감지 → 야간 시 자동 LED 점등
- 💥 **충격 감지 센서**: 낙차 또는 강한 충격 감지 시 Python으로 시리얼 전송
- 🔁 **모터 (Servo/DC)**: 기울기 방향에 따라 핸들을 해당 방향으로 회전
- 💡 **LED**: 조도가 낮으면 자동으로 점등
- 📲 **Python + Twilio API**: 사고 감지 시 긴급 연락처로 SMS 전송

---

## 4. 🧠 주요 기능

| 기능                         | 설명 |
|------------------------------|------|
| ⚖️ 자동 핸들 보정             | 기울기 센서 값에 따라 모터 작동 → 핸들을 기울어지는 방향으로 조정 |
| 🌃 자동 조명 점등             | 조도가 일정 수준 이하일 경우 LED 점등 |
| 🚨 사고 감지 후 문자 전송     | 충격이 감지되면 Python 코드로 시리얼 전송 → Twilio를 통한 긴급 문자 발송 |

---

## 5. 🔍 코드 설명

### Arduino (핸들 보정 + LED 점등 + 충격 감지)

```python
int tiltValue = analogRead(A0);
int lightValue = analogRead(A1);
int shockValue = digitalRead(2);

void loop() {
  // 기울기 감지 → 모터 제어
  if (tiltValue > 700) {
    myMotor.write(90); // 오른쪽
  } else if (tiltValue < 300) {
    myMotor.write(0);  // 왼쪽
  } else {
    myMotor.write(45); // 중앙
  }

  // 조도 감지 → LED 제어
  if (lightValue < 200) {
    digitalWrite(ledPin, HIGH);  // 어두우면 LED ON
  } else {
    digitalWrite(ledPin, LOW);
  }

  // 충격 감지 → 시리얼 메시지 전송
  if (shockValue == LOW) {
    Serial.println("ACCIDENT");
  }

  delay(300);
}
```

### Python (충격 감지 → 문자 발송)
```python
import serial
from twilio.rest import Client

ser = serial.Serial('COM3', 9600)

while True:
    if ser.in_waiting:
        line = ser.readline().decode().strip()
        if line == "ACCIDENT":
            send_sms()  # Twilio API로 문자 전송

def send_sms():
    client = Client(account_sid, auth_token)
    client.messages.create(
        to="+8210xxxxxxx",
        from_="+1xxxxxxxxx",
        body="🚨 사고 발생! 자전거 충격 감지되었습니다.")
```
---

### 6. 📁 프로젝트 구조
```
sketch_nov19a/
├── sketch_nov19a.ino        → 아두이노 센서+모터 제어 코드
├── sms.py                   → 사고 문자 발송 Python 코드
├── test.py                  → 시리얼 통신 테스트용 스크립트
├── KakaoTalk_20221125.jpg   → 실물 구성 사진
├── *.mp4                    → 시연 영상
```
---

### 7. 🛠 기술 스택
<img src="https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white"/> <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/> <img src="https://img.shields.io/badge/Twilio-ED1C24?style=for-the-badge&logo=twilio&logoColor=white"/> <img src="https://img.shields.io/badge/Tilt%2FShock%2FLDR%20Sensors-blue?style=for-the-badge"/>

Arduino Uno: 센서와 액츄에이터 제어

Tilt Sensor: 기울기 → 핸들 제어

LDR (조도 센서): 야간 인식 → LED 점등

Shock Sensor: 충격 → 사고 판단

Python + Twilio: 문자 발송 자동화

---

### 8. 📚 학습 포인트

✔️ 다양한 센서 데이터를 활용한 상황별 로직 처리

✔️ 모터를 활용한 물리적 핸들 제어 경험

✔️ 시리얼 통신 + 파이썬 자동화 구조 구성

✔️ 실시간 사고 대응 시스템 구성 경험
