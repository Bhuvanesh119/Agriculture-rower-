// ============================
// Agriculture Row Robot
// ESP32 Version
// ============================

#define LM1 25
#define LM2 26
#define RM1 27
#define RM2 14

#define IR_LEFT 34
#define IR_RIGHT 35

#define TRIG 5
#define ECHO 18

#define TOOL_PIN 23   // relay or weeding motor

#define SPEED 180
#define DIST_LIMIT 25  // cm

// ---------- Motor Control ----------
void forward() {
  analogWrite(LM1, SPEED);
  analogWrite(LM2, 0);
  analogWrite(RM1, SPEED);
  analogWrite(RM2, 0);
}

void left() {
  analogWrite(LM1, 0);
  analogWrite(LM2, SPEED);
  analogWrite(RM1, SPEED);
  analogWrite(RM2, 0);
}

void right() {
  analogWrite(LM1, SPEED);
  analogWrite(LM2, 0);
  analogWrite(RM1, 0);
  analogWrite(RM2, SPEED);
}

void stopBot() {
  analogWrite(LM1, 0);
  analogWrite(LM2, 0);
  analogWrite(RM1, 0);
  analogWrite(RM2, 0);
}

// ---------- Distance ----------
long getDistance() {
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);

  long duration = pulseIn(ECHO, HIGH);
  return duration * 0.034 / 2;
}

// ---------- Setup ----------
void setup() {
  Serial.begin(115200);

  pinMode(LM1, OUTPUT);
  pinMode(LM2, OUTPUT);
  pinMode(RM1, OUTPUT);
  pinMode(RM2, OUTPUT);

  pinMode(IR_LEFT, INPUT);
  pinMode(IR_RIGHT, INPUT);

  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);

  pinMode(TOOL_PIN, OUTPUT);
}

// ---------- Main Loop ----------
void loop() {

  long dist = getDistance();

  if (dist < DIST_LIMIT) {
    stopBot();
    digitalWrite(TOOL_PIN, LOW);
    delay(500);
    return;
  }

  int leftIR = digitalRead(IR_LEFT);
  int rightIR = digitalRead(IR_RIGHT);

  digitalWrite(TOOL_PIN, HIGH); // weeder ON

  if (leftIR == 0 && rightIR == 0)
    forward();

  else if (leftIR == 1)
    left();

  else if (rightIR == 1)
    right();

  else
    forward();

  delay(10);
}# Agriculture-rower-
