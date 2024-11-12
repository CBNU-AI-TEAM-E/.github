// 핀 설정
const int trigPin = 9;       // 초음파 센서 Trig 핀
const int echoPin = 10;      // 초음파 센서 Echo 핀
const int pirPin = 7;        // PIR 센서 핀
const int ledPin = 11;       // LED 핀
const int buzzerPin = 12;    // 부저 핀

// 변수 설정
long duration;
int distance;
int pirState = LOW;          // PIR 센서 초기 상태 (감지 안 됨)

// 거리 임계값 (초음파 거리 임계값)
const int thresholdDistance = 50; // 50cm

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(pirPin, INPUT);
  pinMode(ledPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);

  Serial.begin(9600);
}

void loop() {
  // 초음파 센서로 거리 측정
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  // 거리 정보 출력
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  // 사람이 임계 거리 이내에 있는지 확인
  if (distance < thresholdDistance) {
    int pirValue = digitalRead(pirPin);  // PIR 센서로 사람 감지 확인

    if (pirValue == HIGH && pirState == LOW) {
      // 사람 감지 시 LED와 부저 활성화
      digitalWrite(ledPin, HIGH);
      digitalWrite(buzzerPin, HIGH);
      Serial.println("Intruder detected!");
      pirState = HIGH;
      delay(500);  // 경고음 지속 시간 (조정 가능)
    }
    else if (pirValue == LOW && pirState == HIGH) {
      // 사람 감지 해제 시 LED와 부저 비활성화
      digitalWrite(ledPin, LOW);
      digitalWrite(buzzerPin, LOW);
      pirState = LOW;
    }
  } else {
    // 거리가 임계값 이상이면 LED와 부저 비활성화
    digitalWrite(ledPin, LOW);
    digitalWrite(buzzerPin, LOW);
  }

  delay(200);  // 짧은 지연을 추가하여 센서의 과도한 활성화를 방지
}
