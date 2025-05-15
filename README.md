# Overkoepelende-opdracht
Voor de overkoepelende opdracht werd in eerste instatie gekozen voor een opdracht die van een lager niveau was om behendiger te worden in het programmeren. Het eerste project was de pomodoro timer. Nadat het afgewerkt was, werd besloten om nog een tweede project te starten en het niveau te verhogen. Dit was het binaural beats and breathing light project. 
## Pomodoro timer 
### Projectbeschrijving
In dit project programmeren we een product dat bijhoudt hoelang iemand aan het werk is en wanneer iemand pauze moet nemen. Dit wordt getrackt volgens de pomodoro-techniek: 20 minuten werken en 5 minuten pauze. Het toestel kan bediend worden met twee simpele knoppen: Een pauze knop en een start/stop knop. 
Aan de hand van dit toestel kunnen bijvoorbeeld studenten gemakkelijk hun werktijd bijhouden en nemen ze ook op tijd een korte pauze. 

Er werd gekozen om in het prototype te werken met seconden in plaats van minuten aangezien het anders zeer moeilijk te testen is tijdens de presentatie. 
### Technische uitwerking
Het toestel bevat:
- een display: houdt bij hoeveel minuten de pauze of de werkperiode bezig is.
- een start/stop knop: start of stopt de pomodoro timer
- een pauze knop: pauzeert de timer en begint opnieuw te lopen van op het punt waar de pauze werd ingeschakeld
- groene + rode led: rode led brandt tijdens de werkperiode en groene led brandt tijdens de pauze
- breadboard + wires: connecties maken
- 2 x 1K weerstanden: voorschakelweerstand van de LED's
- buzzer: geeft een kort signaal wanneer de werkperiode begint en opnieuw start na de pauze. 

Al deze componenten worden geschakeld aan een microcontroller, de arduino nano. Er kan ook gemakkelijk een andere microcontroller gebruikt worden voor dit project mits aanpassen van de bedrading. 

In dit prototype wordt slechts 12 seconden een werkperiode getrackt en 5 seconden pauze. Dit werd gedaan om makkelijk te kunnen debuggen en snel de werking van het systeem te tonen. Dit kan echter makkelijk aangepast worden in de code. 

### Arduino code
De code die voor deze schakeling gebruikt werd is terug te vinden via deze link:

### Behuizing
De behuizing wordt gelasercut uit multiplex. Er worden openingen gehouden voor het scherm, de twee knoppen en de ledlampjes. De buzzer zit in de behuizing omdat er geen behoefte is aan deze bloot te stellen. Zo krijgen we een overzichtelijk en toegankelijk geheel. 

## Binaural beats and breathing light
### Projectbeschrijving
In dit project werd een ontspanningstoestel ontwikkeld dat twee technieken combineert: binaural beats en een breathing light. De gebruiker bedient het systeem met in totaal 4 drukknoppen:
- Één om het nummer door te spoelen
- Één om het nummer terug te spoelen
- Één om het nummer te stoppen
- Één om de breathing light te starten of te stoppen
Het doel van dit project is om een eenvoudige, fysieke interface te creëren die gebruikers ondersteunt bij mindfulness, focusmomenten of stressreductie.

FOTO EINDPRODUCT

### Technische uitwerking
De schakeling is opgebouwd uit een Arduino Nano, met een DFPlayer mini voor audioafspeling vanaf een MicroSD-kaart. De audio wordt weergegeven via een luidspreker, en de visuele ademhaling wordt gesimuleerd met een NeoPixel ring. De componenten zijn verbonden via jump wires op twee breadboards.

Gebruikte componenten:
- Arduino Nano
- DFPlayer Mini
- MicroSD-kaart (met 6 mp3-bestanden)
- 4 drukknoppen
- NeoPixel ring
- 1kΩ weerstand (voor spanningsdeling op DFPlayer RX)
- speaker
- breadboards + jump wires

FOTO SCHAKELING
De DFPlayer mini werkt in een loop van zes verschillende geluiden. Zodra het zesde nummer is bereikt begin het systeem opnieuw bij nummer één wanneer er op de "volgende"-knop wordt gedrukt.

Oorspronkelijk was het de bedoeling om de breathing light automatisch te activeren tijdens het afspelen van de muziek. Ondanks de vele pogingingen bleek het technisch moeilijk om deze functies betrouwbaar te combineren met dezelfde knopstructuur. Na meerdere iteraties is ervoor gekozen om de lichtbediening los te koppelen en een aparate vierde knop toe te voegen.
Dit bleek achteraf ook een gebruiksvriendelijke keuze: de gebruiker kan nu zelf bepalen of het licht nodig is, wat het systeem flexibeler maakt.

### Arduino code
De code die voor deze schakeling gebruikt werd is hier weergegeven: 

### Behuizing
De behuizing werd bewust eenvoudig en functioneel gehouden. De knoppen zijn duidelijk zichtbaar en goed toegankelijk geplaatst. De breathing light werd centraal gepositioneerd voor optimale visuele impact.
Aanvankelijk was het plan om de speaker extern te laten, maar uiteindelijk werd er beslist om deze mee in de behuizing te integreren. Deze beslissing werd genomen op basis van het feit dat geluid goed doorklinkt door materialen zoals karton of PLA. Voor een toekomstigere versie met een stevigere behuizing (bijv. kunststof of metaal) zou integratie van de speaker noodzakelijk zijn.

FOTO NX BESTAND BEHUIZING
