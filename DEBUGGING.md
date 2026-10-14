## Felsökningsrapport – Överlappande bokningar

1. Vilken bugg valde jag?
Jag valde att felsöka problemet med överlappande bokningar i bokningssystemet i `BookingService.cs`. Problemet var att programmet inte alltid upptäckte om två bokningar överlappade varandra i samma rum.

2. Hur återskapade jag felet?
Om det redan fanns en bokning i Aspen mellan 10:00 och 12:00 kunde man försöka skapa en ny bokning mellan 09:00 och 11:00 samma dag. Den gamla koden kunde då godkänna bokningen trots att tiderna överlappade.
Det jag gjorde var att skapa en ny bokning som överlappade med en befintlig bokning och försökte spara den. Jag kunde se att den nya bokningen godkändes trots att den borde ha nekats, alltså att två kunder kunde få samma rum samtidigt.

3. Vilket resultat fick jag & vad var orsaken till felet?
Orsaken till felet var en felaktig implementering av överlappningskontrollen i metoden `Validate` i `BookingService.cs`. Koden kontrollerade att bokningarna gällde samma rum och datum, men överlappningskontrollen jämförde bara den nya bokningens starttid med den befintliga bokningens tider. Därför kunde den missa vissa överlappningar.
Användaren skulle i stället få ett felmeddelande om att rummet redan var bokat under den valda tiden. Det faktiska resultatet var att bokningen godkändes, medan det förväntade resultatet var att bokningen skulle nekas.

4. Hur löste jag felet?
Jag ändrade logiken i `Validate`-metoden för att korrekt kontrollera om den nya bokningen överlappade med någon befintlig bokning. Jag ändrade den befintliga överlappningskontrollen så att den jämför både start och sluttiderna för den nya bokningen med den befintliga bokningen.
Den tidigare såg ut:

```csharp
booking.StartTime >= existing.StartTime &&
booking.StartTime < existing.EndTime
```

Den nya:

```csharp
booking.StartTime < existing.EndTime &&
booking.EndTime > existing.StartTime
```

Den gamla överlappningskontrollen jämförde bara den nya bokningens starttid med den befintliga bokningens tider. Jag ändrade villkoret så att både starttiden och sluttiden jämförs. På så sätt upptäcker programmet även bokningar som börjar tidigare men ändå överlappar.

5. Hur kontrollerade jag att ändringen fungerade?

Efter ändringen körde jag de automatiska testerna i Test Explorer i Visual Studio. Testerna som tidigare misslyckades blev gröna och till slut gick alla 14 tester igenom.
Jag testade även manuellt genom att köra programmet och försöka skapa överlappande bokningar och kontrollera hur de hanterade olika rum och tider.

6. Vilket automatiskt test skyddar mot att buggen kommer tillbaka?

Testet `Create_WhenBookingOverlapsExistingBooking_ReturnsFailure` i `BookingServiceTests.cs`.
Testet provar olika start och sluttider och kontrollerar att resultatet blir misslyckat och att rätt felmeddelande visas.
Om någon skulle ändra överlappningskontrollen så att den inte längre fungerar kan testet upptäcka problemet.

7. Alternativ lösning och varför jag valde min

En annan lösning hade varit att använda flera olika if-satser för att kontrollera om bokningarna överlappar varandra på olika sätt.
Jag valde i stället att använda två villkor med && eftersom det blev mindre kod och var lättare att läsa. Det gör också att jag slipper skriva separata kontroller för varje typ av överlappning.