# Overkoepelende-opdracht
Voor de overkoepelende opdracht werd in eerste instatie gekozen voor een opdracht die van een lager niveau was om behendiger te worden in het programmeren. Het eerste project was de pomodoro timer. Nadat het afgewerkt was, werd besloten om nog een tweeDE project te starten en het niveau te verhogen. Dit was het binaural beats and breating light project. 
## Pomodoro timer 
### Projectbeschrijving
In dit project programmeren we een product dat bijhoudt hoelang iemand aan het werk is en wanneer iemand pauze moet nemen. Dit wordt getrackt volgens de pomodoro-techniek: 20 minuten werken en 5 minuten pauze. Het toestel kan bediend worden met twee simpele knoppen: Een pauze knop en een start/stop knop. 
Aan de hand van dit toestel kunnen bijvoorbeeld studenten gemakkelijk hun werktijd bijhouden en nemen ze ook op tijd een korte pauze. 
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
