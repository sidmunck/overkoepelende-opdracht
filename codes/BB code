#include <Adafruit_NeoPixel.h>
#include <SoftwareSerial.h>
#include <DFRobotDFPlayerMini.h>

// LED-configuratie
#define NUM_LEDS 20
#define DATA_PIN 11
Adafruit_NeoPixel ring = Adafruit_NeoPixel(NUM_LEDS, DATA_PIN, NEO_GRB + NEO_KHZ800);
bool animationRunning = false;

float position = 0;        // Huidige positie van het licht
int direction = 1;         // Richting van de beweging (1 = omhoog, -1 = omlaag)
float speed = 0.065;         // Hoe snel het licht beweegt
unsigned long lastUpdate = 0;
unsigned long updateInterval = 30;  // Snelheid animatie

// Audio-configuratie
SoftwareSerial mySerial(8, 9); // RX, TX
DFRobotDFPlayerMini myDFPlayer;

// Knoppen
const int nextButtonPin = 2;
const int prevButtonPin = 3;
const int stopButtonPin = 4;
const int BUTTON_PIN = 5;    // Start/stop LED animatie
const int BUTTON_PIN_3 = 7;  // Stop LED animatie

// Debouncing instellingen
unsigned long lastButtonTime = 0;      // Tijdstip van laatste knopactie, voor debouncing
unsigned long debounceDelay = 200;     // Minimale tijd tussen knopacties (ms) om ruis te voorkomen

// Houdt vorige knoptoestanden bij om edge-detectie te maken (detecteren wanneer knop net wordt ingedrukt)
int lastNextButtonState = HIGH;        // Vorige status van "volgende track" knop (HIGH = niet ingedrukt door INPUT_PULLUP)
int lastPrevButtonState = HIGH;        // Vorige status van "vorige track" knop
int lastStopButtonState = HIGH;        // Vorige status van "stop" knop
int lastToggleButtonState = HIGH;      // Vorige status van toggle knop (indien gebruikt)

// Audio tracking variabelen
int currentTrack = 1;                  // Huidige af te spelen tracknummer
const int totalTracks = 6;             // Totaal aantal beschikbare tracks

// --- Audio Knoppen Afhandeling --- //
// Deze functie leest de knoppen en bepaalt welke actie uitgevoerd moet worden.
// Debouncing wordt gebruikt om te voorkomen dat een knop meerdere keren wordt geregistreerd door ruis.

void handleAudioButtons() {
  // Lees huidige status van de knoppen (LOW betekent ingedrukt door INPUT_PULLUP-configuratie)
  int nextButtonState = digitalRead(nextButtonPin);
  int prevButtonState = digitalRead(prevButtonPin);
  int stopButtonState = digitalRead(stopButtonPin);

  // Check of de minimale tijd tussen knopacties is verstreken (debounce)
  if (millis() - lastButtonTime > debounceDelay) {

    // Volgende track knop is net ingedrukt (overgang van HIGH naar LOW)
    if (nextButtonState == LOW && lastNextButtonState == HIGH) {
      currentTrack++;                       // Verhoog tracknummer met 1
      if (currentTrack > totalTracks)      // Als nummer groter is dan totaal aantal tracks,
        currentTrack = 1;                   // reset naar eerste track (rondlopen)
      myDFPlayer.play(currentTrack);       // Speel geselecteerde track af
      Serial.print("Volgende track: "); Serial.println(currentTrack);  // Debug output in seriële monitor
      lastButtonTime = millis();            // Update tijdstip voor debounce
    }

    // Vorige track knop is net ingedrukt (overgang van HIGH naar LOW)
    if (prevButtonState == LOW && lastPrevButtonState == HIGH) {
      currentTrack--;                       // Verlaag tracknummer met 1
      if (currentTrack < 1)                 // Als tracknummer lager is dan 1,
        currentTrack = totalTracks;         // reset naar laatste track (rondlopen)
      myDFPlayer.play(currentTrack);       // Speel geselecteerde track af
      Serial.print("Vorige track: "); Serial.println(currentTrack);    // Debug output
      lastButtonTime = millis();            // Update debounce tijd
    }

    // Stop knop is net ingedrukt (overgang van HIGH naar LOW)
    if (stopButtonState == LOW && lastStopButtonState == HIGH) {
      myDFPlayer.stop();                    // Stop muziekweergave
      Serial.println("Muziek gestopt");    // Debug output naar seriële monitor
      lastButtonTime = millis();            // Update debounce tijd
    }
  }

  // Sla de huidige knopstatussen op om overgangen te detecteren in de volgende loop
  lastNextButtonState = nextButtonState;
  lastPrevButtonState = prevButtonState;
  lastStopButtonState = stopButtonState;
}


// --- LED Knoppen Afhandeling --- //
// Deze functie regelt de bediening van de LED-animatie via knoppen:
// - Een toggle knop om de animatie aan of uit te zetten
// - Een stopknop die de animatie direct uitzet en de lamp uitschakelt

void handleLEDButtons() {
  int toggleButtonState = digitalRead(BUTTON_PIN);    // Lees status van toggle knop (aan/uit)
  int stopButtonState = digitalRead(BUTTON_PIN_3);   // Lees status van stop knop

  // Debounce check: zorg dat er minstens 'debounceDelay' ms tussen knopacties zit
  if (millis() - lastButtonTime > debounceDelay) {

    // Als toggle knop net ingedrukt is (van HIGH naar LOW)
    if (toggleButtonState == LOW && lastToggleButtonState == HIGH) {
      animationRunning = !animationRunning;            // Schakel animatie aan/uit (toggle)
      Serial.println(animationRunning ? "Lamp aangezet" : "Lamp uitgezet");
      if (!animationRunning) turnOffLamp();            // Als animatie uit gaat, zet LED uit
      lastButtonTime = millis();                        // Update tijd voor debounce
    }

    // Als stop knop is ingedrukt (LOW), ongeacht vorige status
    if (stopButtonState == LOW) {
      animationRunning = false;                         // Zet animatie uit
      turnOffLamp();                                    // Zet LED uit
      Serial.println("Lamp handmatig uitgezet");
      lastButtonTime = millis();                        // Update debounce tijd
    }
  }

  // Update vorige toggle knopstatus om edge-detectie mogelijk te maken
  lastToggleButtonState = toggleButtonState;
}

// --- LED Animatie Functie --- //
// Deze functie verzorgt het aansturen van de LED-ring met een soepel ademend (breathing) effect in paars tinten.
// De animatie beweegt heen en weer over de ring, met vloeiende kleur-overgangen tussen LEDs.

void updateLEDAnimation() {
  if (!animationRunning) return;                        // Stop als animatie niet actief is

  if (millis() - lastUpdate >= updateInterval) {       // Update alleen na interval verstreken

    ring.clear();                                       // Wis alle LEDs

    // Offset zorgt ervoor dat het 'hoogste punt' van de animatie bovenaan de ring ligt
    float offset = 10.0;
    float realPos = fmod(position + offset, NUM_LEDS); // Bereken huidige positie in ring, met offset

    int lowerLED = floor(realPos);                      // Onderliggende LED index
    int upperLED = (lowerLED + 1) % NUM_LEDS;          // Volgende LED index (wrap-around met modulo)
    float frac = realPos - lowerLED;                    // Fractie tussen lower en upper LED (voor fade)

    // Paars tinten variëren met positie:
    // Rood en blauw hoog, groen laag voor paars effect
    // 'intensity' zorgt voor ademend effect: van 0.4 (zwak) tot 1.0 (helder)

    float intensity = 0.4 + 0.6 * (sin(position * 3.1415 / (NUM_LEDS - 1)) * 0.5 + 0.5);

    // Bereken de intensiteit voor beide LEDs op basis van de fractie (voor zachte overgang)
    float intensityLower = intensity * (1.0 - frac);
    float intensityUpper = intensity * frac;

    // RGB waarden voor lower LED (rood, groen, blauw)
    uint8_t rLower = (uint8_t)(128 * intensityLower);
    uint8_t gLower = (uint8_t)(30 * intensityLower);
    uint8_t bLower = (uint8_t)(200 * intensityLower);

    // RGB waarden voor upper LED
    uint8_t rUpper = (uint8_t)(128 * intensityUpper);
    uint8_t gUpper = (uint8_t)(30 * intensityUpper);
    uint8_t bUpper = (uint8_t)(200 * intensityUpper);

    // Zet de kleuren in de ring voor beide LEDs
    ring.setPixelColor(lowerLED, rLower, gLower, bLower);
    ring.setPixelColor(upperLED, rUpper, gUpper, bUpper);
    ring.show();                                       // Update de LED-ring met nieuwe kleuren

    // Positie bijwerken voor volgende animatie stap (heen en weer beweging)
    position += speed * direction;

    // Als positie de rand bereikt, verander richting
    if (position >= NUM_LEDS - 1) {
      position = NUM_LEDS - 1;
      direction = -1;
    } else if (position <= 0) {
      position = 0;
      direction = 1;
    }

    lastUpdate = millis();                             // Update tijdstip laatste animatie update
  }
}


// --- Lamp Uitzetten --- //
// Zet alle LEDs uit door de ring te wissen en de update te tonen
// Dit zorgt ervoor dat de lamp helemaal uitgaat en geen licht meer geeft
void turnOffLamp() {
  ring.clear();   // Wis alle pixels (zet kleur naar zwart)
  ring.show();    // Update LED-ring zodat het uit is
}

// --- Setup --- //
// Wordt één keer uitgevoerd bij opstarten van de Arduino
void setup() {
  Serial.begin(9600);        // Start seriële communicatie voor debuggen op 9600 baud
  mySerial.begin(9600);      // Start software-serial communicatie voor DFPlayer

  // Stel alle knoppen in als input met interne pull-up weerstand
  // Hierdoor zijn pinnen standaard HIGH en gaan LOW als knop gedrukt wordt
  pinMode(nextButtonPin, INPUT_PULLUP);
  pinMode(prevButtonPin, INPUT_PULLUP);
  pinMode(stopButtonPin, INPUT_PULLUP);
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(BUTTON_PIN_3, INPUT_PULLUP);

  ring.begin();              // Initialiseer de LED-ring
  ring.setBrightness(80);    // Zet helderheid op 80 (van max 255) voor zachter licht
  ring.show();               // Update LED-ring zodat eventuele wijzigingen zichtbaar zijn

  // Probeer DFPlayer te initialiseren
  if (!myDFPlayer.begin(mySerial)) {
    Serial.println("Kan DFPlayer niet starten!");  
    while (true);            // Stop programma als DFPlayer niet gevonden wordt
  }

  Serial.println("DFPlayer succesvol gestart!"); 
  myDFPlayer.volume(20);     // Zet volume van DFPlayer op 20 (schaal 0-30)
  myDFPlayer.play(currentTrack); // Speel direct de eerste track af
}

// --- Loop --- //
// Wordt continu uitgevoerd zolang Arduino aanstaat
void loop() {
  handleAudioButtons();      // Controleer en verwerk audio knoppen (volgende, vorige, stop)
  handleLEDButtons();        // Controleer en verwerk LED animatie knoppen (aan/uit, stop)
  updateLEDAnimation();      // Update de LED animatie als deze actief is
}
