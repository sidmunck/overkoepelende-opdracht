//bibliotheken definiëren
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128 // OLED display breedte, in pixels
#define SCREEN_HEIGHT 32 // OLED display hoogte, in pixels

// Op een arduino UNO:       A4(SDA), A5(SCL)
#define OLED_RESET     -1 // Reset pin 
#define SCREEN_ADDRESS 0x3C // 0x3D voor 128x64, 0x3C voor 128x32
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int ledPin = 4, ledPin2 = 5, buttonPin = 3, pauseButtonPin = 2, Buzzerpin = 9; // Pinnen definiëren

unsigned long startTime = 0, pauseTime = 0, lastUpdate = 0, buzzerStart = 0, pauzeIngangTijd = 0; //timers definiëren
int Timer = 0;
bool buzzerActive = false; // beginstatus buzzer
bool tijdelijkGepauzeerd = false; // beginstatus  pauzeknop

int IDLE = 0, WORK = 1, PAUSE = 2; // verschillende fases
int phase = IDLE, vorigePhase = IDLE; //niets doen tot systeem gestart wordt

void setup() {
  // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
  display.display();
  delay(2000); // Pauze voor 2 seconden

  // buffer leegmaken
  display.clearDisplay();
  // bepaal alle uitgangen en ingangen
  pinMode(ledPin, OUTPUT); pinMode(ledPin2, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(pauseButtonPin, INPUT_PULLUP);
  pinMode(Buzzerpin, OUTPUT);
}

// loop start buzzer
void startBuzzer() {
  buzzerStart = millis();
  buzzerActive = true; // status buzzer aanpassen
  digitalWrite(Buzzerpin, HIGH); // buzzer laten afgaan
}
// loop stop buzzer
void stopBuzzer() {
  buzzerActive = false; // status buzzer aanpassen
  digitalWrite(Buzzerpin, LOW); //buzzer stoppen
}

// loop voorwaarde buzzer 
void checkBuzzer() {
  if (buzzerActive && millis() - buzzerStart >= 500) { //buzzertijd duurt een halve seconde
    stopBuzzer();
  }
}
// loop display 
void updateDisplay(const char* text) {
  display.clearDisplay();                      // Buffer leegmaken
  display.setTextSize(1);                       // Tekstgrootte instellen
  display.setTextColor(SSD1306_WHITE);          // Tekstkleur wit maken
  display.setCursor(0, 0);                      // Cursor bovenaan
  display.println(text);                        // Print de hoofdtekst

  display.setCursor(0, 16);                     // Nieuwe regel, iets lager (16px)
  display.print("Tijd: ");                      //"tijd" printen
  display.print(Timer);                         // de pauzetijd/werktijd printen
  display.print(" sec");                        // "sec" printen

  display.display();                            // alles naar scherm sturen
}

// loop leds updaten
void updateLEDs() {
  digitalWrite(ledPin, (phase == WORK)); // in werkfase rode led aanleggen
  digitalWrite(ledPin2, (phase == PAUSE)); //in pauze groene led aanlegge
}

void loop() {
  unsigned long currentTime = millis();
  int startStopBtn = digitalRead(buttonPin); //status start/stop button lezen
  int pauzeBtn = digitalRead(pauseButtonPin); //status pauze button lezen

  checkBuzzer(); //status buzzer lezen

  // Start/stop knop
  if (startStopBtn == LOW) // is knop ingeduwd
  {
    if (phase == IDLE) // is het systeem niets aan het doen
    { 
      startTime = lastUpdate = currentTime;
      Timer = 0;
      phase = WORK; // ga dan naar de werkfase
      updateLEDs(); // leds aanleggen 
    } else {
      phase = IDLE; //als het systeem niets doet terug naar IDLE
      Timer = 0;
      tijdelijkGepauzeerd = false;
      updateDisplay("Gestopt."); //tekst printen op display "gestopt"
      updateLEDs(); //leds updaten
    }
    delay(300);
  }

  
  if (pauzeBtn == LOW && phase != IDLE) //als pauzeknop ingeduwd is en fase is verschillend van IDLE
  {
    if (!tijdelijkGepauzeerd) { // als er geen pauze bezig is
      tijdelijkGepauzeerd = true; //verander status naar pauze
      vorigePhase = phase; //bewaren in welke fase we waren
      pauzeIngangTijd = currentTime; //tijd bijhouden wanneer we in pauze gingen
      updateDisplay("Gepauzeerd"); //display updaten naar pauze
    } else {
      tijdelijkGepauzeerd = false; // pauze was al bezig dus status veranderen
      unsigned long pauzeDuur = currentTime - pauzeIngangTijd; //pauzeduur berekenen
      if (vorigePhase == WORK) startTime += pauzeDuur; //timer juist laten verder tellen
      if (vorigePhase == PAUSE) pauseTime += pauzeDuur; //timer juist laten verder tellen
      lastUpdate += pauzeDuur;
      phase = vorigePhase; //fase terug juist zetten
      updateDisplay(phase == WORK ? "Verder werken..." : "Verder pauze..."); //display updaten
      updateLEDs(); //leds updaten
    }
    delay(300);
  }

  if (tijdelijkGepauzeerd) return; //in pauze niets doen

  
  if (phase == WORK) //als we in de werkfase zitten
  { 
    if (currentTime - lastUpdate >= 1000) {
      Timer++; // per seconde timer 1 omhoog
      lastUpdate = currentTime; //lastupdate gelijkstellen aan de lopende tijd
      updateDisplay("Nieuwe werkperiode!"); //"nieuwe werkperiode" op display"
    }
    if (currentTime - startTime >= 12000){
      phase = PAUSE; //als er 12 seconden verstreken zijn gaan we over naar pauze
      pauseTime = currentTime; // de pauzetijd is gelijk aan de lopende tijd 
      lastUpdate = currentTime; 
      Timer = 0; //zet timer terug naar nul om terug pauzeperiode bij te houden
      updateDisplay("Pauze gestart!"); // display updaten met "pauze gestart"
      updateLEDs(); //leds updaten: groene led aan
      startBuzzer(); //buzze laten afgaan
    }
  }
  else if (phase == PAUSE) {
    if (currentTime - lastUpdate >= 1000) {
      Timer++; //als de pauze bezig is, timer verhogen met 1 per seconde
      lastUpdate = currentTime;
      updateDisplay("Pauze..."); //display updaten
    }
    if (currentTime - pauseTime >= 5000) {
      phase = WORK; //als pauze 5 seconden bezig is terug naar werkfase
      startTime = lastUpdate = currentTime;
      Timer = 0; //timer terug naar nul om werktijd bij te houden
      updateDisplay("Nieuwe werkperiode!"); //display updaten 
      updateLEDs(); //rode led gaat terug aan
      startBuzzer(); //buzzer gaat af
    }
  }
}

