---
path: '/osa-10/4-storre-applikation'
title: 'Att utveckla en större applikation'
hidden: false
---

<text-box variant='learningObjectives' name='Inlärningsmål'>

Efter den här delen

- Känner du till några grundläggande principer för applikationsutveckling
- Kommer du att känna dig bekväm med att skilja mellan de olika delarna av en applikation (användargränssnitt, programlogik och filhantering)
- Har du övat på att skriva din egna lite större applikation

</text-box>

Hittills i detta kursmaterial har vi gått igenom ett stort antal Python-funktioner.

I Introduktion till programmering-kursen introducerades kontrollstrukturer som while och for, funktioner samt grundläggande datastrukturer som listor, tupler och ordlistor. I princip är dessa verktyg allt som behövs för att uttrycka vad som helst som en programmerare kan tänka sig vilja uttrycka med Python.

På denna avancerade kurs i programmering, med början i modul 8 av materialet, har du blivit bekant med klasser och objekt. Låt oss ta en stund att fundera på när och varför de är nödvändiga, ifall de grundläggande verktygen från modul 1 till 7 borde räcka.

## Att hantera komplexitet

Objekt och klasser är långt ifrån nödvändiga i alla programmeringssammanhang. Om du t.ex. programmerar ett litet skript för engångsbruk är objekt oftast överflödiga. Men när du ska programmera något större och mer komplicerat blir objekten mycket användbara.

När programmen blir allt mer komplexa blir mängden detaljer snabbt ohanterlig, såvida inte programmet är organiserat på något systematiskt sätt. Även några av de mer komplicerade övningarna på den här kursen hittills skulle ha haft nytta av de exempel som ges i den här delen av materialet.

I flera decennier har begreppet [Separation of concerns](https://sv.wikipedia.org/wiki/Inkapsling_(Separation_of_Concerns)) varit en av de centrala principerna inom programmering och inom datavetenskapen i stort. Citat från Wikipedia:

_Separation of concerns is a design principle for separating a computer program into distinct sections such that each section addresses a separate concern. A concern is a set of information that affects the code of a computer program_

Genom att dela upp programmet i olika delar, så att varje del har sina egna problem att hantera, blir det lättare att hantera den oundvikliga komplexiteten i ett datorprogram.

Funktioner är ett sätt att organisera ett program i distinkta, hanterbara helheter. I stället för att skriva ett enda skript är tanken att formulera små, separat verifierbara funktioner som var och en löser en del av det större problemet.

En annan vanlig metod för att hantera större program är objekt, genom objektorienterade programmeringsprinciper. Det finns fördelar och nackdelar med båda metoderna, och varje programmerare har sin egen favorit. Som vi har sett hittills kan vi med hjälp av objekt och klasser samla alla data och den kod som bearbetar dessa data i en enda enhet, i ett objekts attribut och metoder. Dessutom ger objekten ett sätt att kapsla in de data de kontrollerar, så att andra delar av programmet inte behöver oroa sig för de interna detaljerna i ett objekt.

## Ett fungerande exempel: telefonkatalog

Hur ska ett program delas in i klasser och objekt? Det här är inte alls en enkel fråga med ett enda godtagbart svar, så vi fortsätter med ett exempel. I del fem genomförde du en [telefonkatalogsapplikation](https://rage.github.io/ohjelmointi-24-sv/osa-5/3-lexikon), och nu ska vi genomföra något liknande med hjälp av objektorienterade programmeringsprinciper.

Enligt principen om separation of concerns bör ett program delas upp i sektioner som var och en har sin egen sak att ta hand om. I objektorienterad programmering översätts detta till [principen om ett ansvar](https://sv.wikipedia.org/wiki/Single_responsibility_principle). Utan att gå in på detaljerna framgår det grundläggande syftet redan av namnet: en enda klass och de objekt som skapas utifrån den ska ha ett enda ansvar i programmet.


Objektorienterad programmering används ofta som ett sätt att modellera objekt och fenomen i den verkliga världen. Ett enskilt objekt i den verkliga världen modelleras med en enda klass i programkoden. I fallet med en telefonkatalog kan sådana objekt vara
- en person
- ett namn
- ett telefonnummer

Ett namn och ett telefonnummer kan uppfattas som data som inte förtjänar egna klasser, men en person är en distinkt fysisk enhet i den verkliga världen, och i programmeringsvärlden skulle den kunna fungera som en klass. Ett Person-objekt skulle vara ansvarigt för att knyta ihop ett namn och de telefonnummer som är kopplade till det.

En telefonkatalog i sig skulle kunna vara en bra kandidat för en klass. Dess ansvar skulle vara att hantera olika personobjekt och de data de innehåller.

Nu har vi skissat kärnan i vår applikation: telefonkatalog och person utgör programmeringslogiken i vår applikation, eller applikationslogiken i korthet. Vår applikation skulle behöva några andra klasser också.

Det är oftast en bra idé att hålla all interaktion med en användare skild från applikationslogiken. Det är ju trots allt ett helt eget ansvar. Förutom den centrala applikationslogiken bör vårt program därför innehålla en klass som hanterar användargränssnittet.

Dessutom bör vår telefonkatalog ha någon form av beständig lagring mellan exekveringar. Filhanteringen är återigen ett tydligt separat ansvar, så den förtjänar en egen klass.

Nu när vi har en översikt över de grundläggande komponenterna i vårt program uppstår frågan: var ska vi börja programmera? Inte heller här finns det något rätt eller fel svar, men det är ofta en bra idé att börja med någon del av programlogiken.

## Steg 1: en skiss för applikationslogiken

Låt oss börja med klassen Telefonkatalog. En skelettimplementering skulle kunna se ut så här:

```python
class Telefonkatalog:
    def __init__(self):
        self.__personer = []

    def tillsatt_nummer(self, namn: str, nummer: str):
        pass

    def hamta_nummer(self, namn: str):
        pass

```

Denna klass består av en lista med personer samt metoder för att både lägga till och hämta data.

Varje person kan vara kopplad till flera nummer, så låt oss implementera den interna strukturen för `personer` med en ordlista. En ordlista ger oss möjlighet att söka efter nycklar enligt namn, och värdet som är kopplat till en ordlistas nyckel kan vara en lista. Hittills ser det ut som att vi inte behöver en separat klass för att representera en person – ett inlägg i en ordlista räcker.

Låt oss implementera de metoder som listas ovan och testa vår telefonkatalog:

```python
class Telefonkatalog:
    def __init__(self):
        self.__personer = {}

    def tillsatt_nummer(self, namn: str, nummer: str):
        if not namn in self.__personer:
            # till personen är en lista med nummer anknyten
            self.__personer[namn] = []

        self.__personer[namn].append(nummer)

    def hamta_nummer(self, namn: str):
        if not namn in self.__personer:
            return None

        return self.__personer[namn]

# testkod
katalog = Telefonkatalog()
katalog.tillsatt_nummer("Erik", "02-123456")
print(katalog.hamta_nummer("Erik"))
print(katalog.hamta_nummer("Emilia"))
```

Detta borde utskriva följande:

<sample-output>

['02-123456']
None

</sample-output>

Metoden `hamta_nummer` returnerar `None` om ett namn inte finns med i telefonkatalogen. Om namnet finns returneras listan med de nummer som är kopplade till namnet.

När man gör ändringar i ett program är det alltid värt att testa att koden fungerar som förväntat innan man går vidare med andra ändringar. Den kod som används för testning är vanligtvis något som raderas strax efteråt, och därför kanske du tycker att det inte är värt besväret att skriva några tester i första hand. I de flesta fall är detta inte sant. Testning är en förutsättning för bra programmeringsresultat.

En bugg i programmet bör fångas upp och åtgärdas så snart som möjligt. Om du tar för vana att kontrollera funktionaliteten i praktiskt taget varje ny kodrad kommer du att upptäcka att buggarna oftast är lätta att hitta och åtgärda, eftersom du kan vara helt säker på att buggen orsakades av den senaste ändringen. Om du bara testar programmet efter att ha lagt till dussintals rader kod ökar de potentiella källorna till buggar också med dussintals gånger.

## Steg 2: en skiss för användargränssnittet

Med den grundläggande applikationslogiken ur vägen är det dags att implementera ett textbaserat användargränssnitt. Vi kommer att behöva en ny klass, `TelefonkatalogApplikation`, med följande inledande funktionalitet:

```python
class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()

    def hjalp(self):
        print("instruktioner: ")
        print("0 avsluta")

    def exekvera(self):
        self.hjalp()
        while True:
            print("")
            instruktion = input("instruktion: ")
            if instruktion == "0":
                break

applikation = TelefonkatalogApplikation()
applikation.exekvera()
```

Det här programmet gör inte så mycket ännu, men låt oss gå igenom innehållet. Konstruktormetoden skapar en ny Telefonkatalog, som lagras i ett privat attribut. Metoden `exekvera(self)` startar programmets textbaserade användargränssnitt, vars kärna är `while`-loopen, som fortsätter att be användaren om instruktioner tills de skriver in instruktionen för att avsluta. Det finns också en metod för instruktioner, `hjalp(self)`, som anropas innan man går in i loopen, så att instruktionerna skrivs ut.

Låt oss nu lägga till lite faktisk funktionalitet. Först implementerar vi att kunna lägga till nya data i telefonkatalogen:

```python
class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()

    def hjalp(self):
        print("instruktioner: ")
        print("0 avsluta")
        print("1 tillsätt inlägg")

    def exekvera(self):
        self.hjalp()
        while True:
            print("")
            instruktion = input("instruktion: ")
            if instruktion == "0":
                break
            elif instruktion == "1":
                namn = input("namn: ")
                nummer = input("nummer: ")
                self.__katalog.tillsatt_nummer(namn, nummer)

applikation = TelefonkatalogApplikation()
applikation.exekvera()
```

Om användaren skriver in 1 för att lägga till ett nytt nummer, frågar användargränssnittet efter ett namn och ett nummer och lägger till dessa i telefonkatalogen med hjälp av den lämpliga metod som definieras i klassen.

Användargränssnittets enda ansvar är att kommunicera med användaren. All annan funktionalitet, t.ex. att lagra ett nytt namn- och nummerpar, är Telefonkatalog-objektets ansvar.

Det finns utrymme för förbättringar i strukturen i vår användargränssnittsklass. Låt oss skapa en metod `inlagg(self)` som hanterar instruktionen för att lägga till ett nytt inlägg:

```python
class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()

    def hjalp(self):
        print("instruktioner: ")
        print("0 avsluta")
        print("1 tillsätt inlägg")

    # separation of concerns i praktiken: en ny metod för att lägga till ett inlägg
    def inlagg(self):
        namn = input("namn: ")
        nummer = input("nummer: ")
        self.__katalog.tillsatt_nummer(namn, nummer)

    def exekvera(self):
        self.hjalp()
        while True:
            print("")
            instruktion = input("instruktion: ")
            if instruktion == "0":
                break
            elif instruktion == "1":
                self.inlagg()

applikation = TelefonkatalogApplikation()
applikation.exekvera()
```

Separation of concerns-principen sträcker sig även till metodnivå. Vi skulle kunna ha hela användargränssnittets funktionalitet i en enda komplicerad `while`-loop, men det är bättre att separera varje funktionalitet i en egen metod. Ansvaret för `exekvera()`-metoden är bara att delegera de instruktioner som användaren skriver in till relevanta metoder. Detta hjälper till att hantera den växande komplexiteten i vårt program. Om vi till exempel senare vill ändra hur det fungerar att lägga till inlägg, är det omedelbart klart att vi då måste fokusera våra ansträngningar på `inlagg()`-metoden.

Låt oss inkludera funktionalitet för att söka efter inlägg i vårt användargränssnitt. Detta bör också ha sin egen metod:

```python

class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()

    def hjalp(self):
        print("instruktioner: ")
        print("0 avsluta")
        print("1 tillsätt inlägg")
        print("2 sök")

    def inlagg(self):
        namn = input("namn: ")
        nummer = input("nummer: ")
        self.__katalog.tillsatt_nummer(namn, nummer)

    def sok(self):
        namn = input("namn: ")
        numren = self.__katalog.hamta_nummer(namn)
        if numren == None:
            print("nummer okänd")
            return
        for nummer in numren:
            print(nummer)

    def exekvera(self):
        self.hjalp()
        while True:
            print("")
            instruktion = input("instruktion: ")
            if instruktion == "0":
                break
            elif instruktion == "1":
                self.inlagg()
            elif instruktion == "2":
                self.sok()
            else:
                self.hjalp()

applikation = TelefonkatalogApplikation()
applikation.exekvera()
```

Vi har nu en enkel fungerande telefonkatalogsapplikation som är redo för testning. Följande är ett exempel på en exekvering:

<sample-output>

instruktioner:
0 avsluta
1 tillsätt inlägg
2 sök

instruktion: **1**
namn: **Erik**
nummer: **02-123456**

instruktion: **1**
namn: **Erik**
nummer: **045-4356713**

instruktion: **2**
namn: **Erik**
02-123456
045-4356713

instruktion: **2**
namn: Emilia
nummer okänd

instruktion: **0**

</sample-output>

För en så enkel applikation har vi skrivit ganska mycket kod. Om vi hade skrivit allt i den enda `while`-loopen hade vi förmodligen kunnat komma undan med mycket mindre kod. Det är dock ganska lätt att läsa koden, strukturen är tydlig och vi borde inte ha några problem med att lägga till nya funktioner.

## Steg 3: Importera data från en fil

Låt oss anta att vi redan har några telefonnummer lagrade i en fil, och att vi vill läsa detta när programmet startar. Datafilen är i följande CSV-format:

```csv
Erik;02-1234567;045-4356713
Emilia;040-324344
```

Hantering av filer är helt klart ett eget ansvarsområde, så det förtjänar en klass för sig:

```python
class FilHanterare():
    def __init__(self, fil):
        self.__fil = fil

    def ladda(self):
        namnen = {}
        with open(self.__fil) as f:
            for rad in f:
                delar = rad.strip().split(';')
                namn, *numren = delar
                namnen[namn] = numren

        return namnen
```

Konstruktormetoden tar namnet på filen som sitt argument. Metoden `ladda(self)` läser innehållet i filen. Varje rad delas upp i två delar: ett namn och en lista med siffror. Sedan läggs dessa till i en ordbok, med namnet som nyckel och listan som värde.

Metoden använder en smidig Python-funktion: det är möjligt att först välja några objekt från en lista separat och sedan ta resten av objekten i en ny lista. Du kan se ett exempel på detta nedan. Du kanske minns från [modul 6](https://rage.github.io/ohjelmointi-24-sv/osa-6/1-lasa-filer) att strängmetoden `split` returnerar en lista.

```python
lista = [1, 2, 3, 4, 5]
forsta, andra, *resten = lista
print(forsta)
print(andra)
print(resten)
```

<sample-output>

1
2
[3, 4, 5]

</sample-output>

Tecknet `*` framför variabelnamnet `resten` i ovanstående exempel betyder att den sista variabeln ska innehålla alla återstående poster i listan, från den tredje och framåt.

Vi bör absolut testa filhanteraren separat innan vi inkluderar den i vår applikation:

```python
t = FilHanterare("katalog.txt")
print(t.ladda())
```

<sample-output>

{'Erik': ['02-1234567', '045-4356713'], 'Emilia': ['040-324344']}

</sample-output>

Eftersom filhanteraren verkar fungera bra kan vi lägga till den i vår applikation. Låt oss anta att vi vill läsa filen som det första varje gång programmet körs. Den logiska platsen för att läsa filen skulle vara konstruktören för klassen `TelefonkatalogApplikation`:

```python
class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()
        self.__fil = FilHanterare("katalog.txt")

        # lägg till namn och nummer från filen till telefonkatalogen
        for namn, numren in self.__fil.ladda().items():
            for nummer in numren:
                self.__katalog.tillsatt_nummer(namn, nummer)

    # annan kod
```

Denna funktionalitet bör också testas. När vi har försäkrat oss om att filens innehåll är tillgängligt via användargränssnittet i vår applikation kan vi gå vidare till nästa steg.

## Steg 4: exportera data till en fil

Den sista funktionen i vår grundversion av applikationen är att spara innehållet i telefonkatalogen tillbaka i samma fil som data lästes från.

Detta innebär en förändring av `Telefonkatalog`-klassen. Vi måste kunna exportera innehållet i telefonkatalogen:

```python
class Telefonkatalog:
    def __init__(self):
        self.__personer = {}

    # ...

    # returnera alla inlägg (i ordlistsformat)
    def alla_uppgifter(self):
        return self.__personer
```

Själva sparandet till filen bör hanteras av `FilHanterare`-klassen. Låt oss lägga till metoden `spara` som tar en ordlistsrepresentation av telefonkatalogen som argument:

```python
class FilHanterare:
    def __init__(self, fil):
        self.__fil = fil

    def ladda(self):
        # ...

    def spara(self, katalog: dict):
        with open(self.__fil, "w") as f:
            for namn, numren in katalog.items():
                rad = [namn] + numren
                f.write(";".join(rad) + "\n")
```

Sparandet bör ske när programmet avslutas. Låt oss lägga till en metod för detta ändamål i användargränssnittet och anropa den innan vi bryter ut ur `while`-loopen:

```python

class TelefonkatalogApplikation:
    # resten av koden för användargränssnittet

    # metod som exekveras till programmet avslutas
    def avsluta(self):
        self.__fil.spara(self.__katalog.alla_uppgifter())

    def exekvera(self):
        self.hjalp()
        while True:
            print("")
            instruktion = input("instruktion: ")
            if instruktion == "0":

                self.avsluta()
                break
            elif instruktion == "1":
                self.inlagg()
            elif instruktion == "2":
                self.sok()
            else:
                self.hjalp()
```

<programming-exercise name='Utveckling av telefonkatalogen, del 1' tmcname='osa10-10_telefonkatalog_del1'>

I den här övningen kommer du att skapa en liten utvidgning av telefonkatalogapplikationen. Koden från exemplet ovan finns i övningsmallen. Lägg till ett kommando som låter användaren söka  efter nummer i telefonkatalogen. Efter tillägget ska applikationen fungera på följande sätt:

<sample-output>

instruktioner:
0 avsluta
1 tillsätt inlägg
2 sök
3 sök enligt nummer

instruktion: **1**
namn: **Erik**
nummer: **02-123456**

instruktion: **1**
namn: **Erik**
nummer: **045-4356713**

instruktion: **3**
nummer: **02-123456**
Erik

instruktion: **3**
nummer: **0100100**
okänt nummer

instruktion: **0**

</sample-output>

Implementera detta tillägg med hänsyn till den nuvarande strukturen i programmet. Detta innebär att du i klassen `TelefonkatalogApplikation` bör lägga till en lämplig hjälpmetod för att möjliggöra den nya funktionaliteten, och även lägga till en ny gren i while-loopen. I `Telefonkatalog`-klassen bör du lägga till en metod som gör det möjligt att söka med ett nummer.



</programming-exercise>

## Objekt i en ordlista

I nästa övning ombeds du att ändra din telefonkatalog så att värdena i ordlistan är objekt, inte listor.

Det är inget konstigt med det i sig, men det är första gången på den här kursen som något sådant föreslås, så låt oss gå igenom ett enklare exempel innan vi dyker ner i övningen.

Här har vi en applikation som håller reda på hur många övningar som studenterna har gjort på en kurs. Varje elevs antal övningar lagras i ett enkelt objekt:

```python
class Uppgiftsraknare:
    def __init__(self):
        self.__uppgifter = 0

    def klar(self):
        self.__uppgifter += 1

    def gjorda(self):
        return self.__uppgifter
```

Följande huvudfunktion använder klassen ovan:

```python
studeranden = {}

print("markerar uppgifter")
while True:
    namn = input("studerande: ")
    if len(namn) == 0:
        break

    # skapa ett nytt objekt ifall det inte finns ännu
    if not namn in studeranden:
        studeranden[namn] = Uppgiftsraknare()

    # lägg till en ny utförd uppgift i räknaren
    studeranden[namn].klar()

print()
print("gjorda uppgifter:")

for studerande, uppgifter in studeranden.items():
    print(f"{studerande} uppgifter {uppgifter.gjorda()} st")
```

Att exekvera koden ovan kunde se ut enligt följande:

<sample-output>

markerar uppgifter
studerande: **peter**
studerande: **sara**
studerande: **anton**
studerande: **sara**
studerande: **jonas**
studerande: **jonas**
studerande: **anton**
studerande: **sara**
studerande:

gjorda uppgifter:
peter uppgifter 1 st
anton uppgifter 2 st
sara uppgifter 3 st
jonas uppgifter 2 st

</sample-output>

Det finns ett par saker att tänka på i exemplet ovan. När användaren matar in ett namn kontrollerar programmet först om namnet redan är en nyckel i ordlistan. Om namnet inte finns skapas ett nytt objekt som läggs till som en post i ordlistan:

```python
if not namn in studeranden:
    studeranden[namn] = Uppgiftsraknare()
```

Efter detta kan vi vara säkra på att objektet existerar, kopplat till namnet på den student som används som nyckel. Antingen skapades det precis, eller så fanns det redan från en tidigare iteration av loopen. I vilket fall som helst kan vi nu hämta objektet med nyckeln och kalla metoden `klar`:

```python
studeranden[namn].klar()
```

Raden ovan innehåller egentligen två separata händelser. Vi kan lika gärna använda en hjälpvariabel och skriva den på två separata kodrader:

```python
studerandes_raknare = studeranden[namn]
studerandes_raknare.klar()
```

OBS: Även om objektet här tilldelas en hjälpvariabel, så finns objektet kvar i ordlistan precis som tidigare. Hjälparvariabeln innehåller en referens till objektet i ordlistan.

Om du inte är helt säker på vad som egentligen händer i koden ovan kan du prova med [visualiseringsverktyget](http://www.pythontutor.com/visualize.html#mode=edit).

<programming-exercise name='Utveckling av telefonkatalogen, del 2' tmcname='osa10-11_telefonkatalog_del2'>

I denna övning kommer du att skapa en annan version av `TelefonkatalogApplikation`. Du kommer att lägga till adresser i datan som kan kopplas till ett namn. För enkelhetens skull har funktionaliteten för att spara till fil tagits bort, och vissa andra metoder har bytt namn för att bättre passa in i förändringen.

## En separat klass för en persons data

Ändra hur data om en person hanteras. Implementera en klass med namnet `Person`, som tar hand om personers telefonnummer och adresser. Klassen ska fungera på följande sätt:


```python
person = Person("Erik")
print(person.namn())
print(person.numren())
print(person.adress())
person.tillsatt_nummer("040-123456")
person.tillsatt_adress("Mannerheimintie 10 Helsinki")
print(person.numren())
print(person.adress())
```

<sample-output>

Erik
[]
None
['040-123456']
Mannerheimintie 10 Helsinki

</sample-output>

## Telefonkatalogs användning av klassen Person

Ändra den interna implementeringen av din applikation så att din `Telefonkatalog`-klass använder objekt av klassen `Person` för att lagra data i telefonkatalogen. Det vill säga, attributet `__personer` ska fortfarande innehålla en ordlista, men värdena ska vara Person-objekt och inte listor. Användaren av din applikation ska inte märka någon skillnad; ändringarna får inte påverka användargränssnittet.

**VARNING:** när du gör strukturella ändringar i din kod, som beskrivs i den här övningen, ska du alltid ta små steg och testa i varje möjligt skede. Försök inte att göra alla ändringar på en gång. Det är ett säkert sätt att **stöta på allvarliga problem med din kod**.

Ett lämpligt första steg kan vara att skriva lite kod för att kontrollera funktionaliteten i `Telefonkatalog`-klassen direkt. Till exempel bör följande åtminstone inte orsaka några fel:

```python
katalog = Telefonkatalog()
katalog.tillsatt_nummer("Erik", "02-123456")
print(katalog.hamta_uppgifter("Erik"))
print(katalog.hamta_uppgifter("Emilia"))
```

Lägg märke till det nya namnet på metoden för att hämta ett inlägg från telefonkatalogen. De automatiska testerna kontrollerar inte vad utskriften från din `hamta_uppgifter`-metod är, utan ser till att inga fel uppstår av ovanstående kod och att resultatet är logiskt inom din implementation.

När du har gjort de nödvändiga ändringarna i ditt program och absolut har verifierat funktionaliteten i `Telefonkatalog`-klassen kan du gå vidare till användargränssnittet och se om allt fortfarande fungerar som förväntat.

## Tillägg av adress

Vänligen implementera funktionaliteten för att lägga till en adress till ett inlägg i din telefonkatalog. Programmet ska fungera på följande sätt:

<sample-output>

instruktioner:
0 avsluta
1 tillsätt nummer
2 sök
3 tillsätt adress

instruktion: **1**
namn: **Erik**
nummer: **02-123456**

instruktion: **3**
namn: **Emilia**
adress: **Viherlaaksontie 7, Espoo**

instruktion: **2**
namn: **Erik**
02-123456
adress okänd

instruktion: **2**
namn: **Emilia**
nummer okänd
Viherlaaksontie 7, Espoo

instruktion: **3**
namn: **Erik**
adress: **Linnankatu 75, Turku**

instruktion: 2
namn: **Erik**
02-123456
Linnankatu 75, Turku

instruktion: **2**
namn: **Wilhelm**
adress okänd
nummer okänd

instruktion: **0**

</sample-output>

**VARNING och tips:** som nämndes ovan i den föregående övningen, försök inte att göra alla ändringar på en gång. Det är ett säkert sätt att **stöta på allvarliga problem med din kod**.

Kontrollera först att du på ett tillförlitligt sätt kan lägga till adresser med hjälp av `Telefonkatalog`-klassen direkt. När du har verifierat detta kan du gå vidare till de nödvändiga ändringarna i användargränssnittet.

</programming-exercise>

## Några avslutande anmärkningar

Strukturen i Telefonkatalog-exemplet ovan följer de grundläggande principerna för objektorienterad programmering ganska väl. Den centrala principen är att identifiera de olika ansvarsområdena i programmet och fördela dessa logiskt mellan de olika klasserna och metoderna. Ett av de viktigaste motiven för denna uppdelning är att hantera komplexiteten. Ett annat viktigt motiv är att en logisk uppdelning av ansvarsområden - modularitet i facktermer - ofta gör koden lättare att underhålla och bygga vidare på.

I de programvarupaket som utvecklas och används ute i världen är underhåll och utbyggnad, dvs felsökning av befintlig programvara och implementering av nya funktioner, den absolut dyraste delen av utvecklingen. Korrekt implementerad modularitet är ekonomiskt sett en mycket viktig funktion i programvaruutvecklingen.

Det finns några fler objektorienterade programmeringsprinciper som är värda att lyfta fram här. Telefonkatalog är ett bra exempel på hur den centrala programlogiken kan (och bör) separeras från både användargränssnitt och datalagring. Detta är viktigt på grund av ett par olika skäl. För det första gör denna separation det möjligt att testa koden i mindre enheter, en klass och metod i taget. För det andra, eftersom kärnlogiken nu är oberoende av gränssnitten som kommer till omvärlden, är det möjligt att i viss utsträckning ändra implementeringen av antingen kärnlogiken eller gränssnitten utan att hela applikationen går sönder.

Filhanteringen i Telefonkatalog-programmet går till på följande sätt: Programmet läser filen en enda gång, när det startar. Efter detta lagras all data i variabler i programmet. När programmet avslutas lagras alla data igen, vilket i praktiken innebär att filen skrivs över. I de flesta fall är detta det rekommenderade sättet att hantera externa filer, eftersom det ofta är mycket mer komplicerat att redigera data på plats.

Det finns många bra guideböcker för att lära sig om god programmeringspraxis. En sådan är [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) av Robert Martin. Kodexemplen i boken är dock implementerade i Java, så att arbeta igenom exemplen kan vara ganska besvärligt vid denna tidpunkt i din programmeringskarriär, även om boken i sig rekommenderas mycket av kurspersonalen. Teman som lätt underhållen, utbyggbar kod av god kvalitet kommer att utforskas ytterligare på kurserna [Software Development Methods](https://studies.helsinki.fi/opintotarjonta/cu/hy-CU-118024742-2020-08-01) och [Software Engineering](https://studies.helsinki.fi/opintotarjonta/cu/hy-CU-118024909-2020-08-01).

Att skriva kod enligt etablerade principer för objektorienterad programmering har ett pris. Du kommer sannolikt att få skriva mer kod än om du skulle skriva din implementation i en enda kontinuerlig stöt av spaghettikod. En av de viktigaste färdigheterna hos en programmerare är att bestämma det bästa tillvägagångssättet för varje situation. Ibland är det nödvändigt att bara hacka ihop något snabbt för omedelbar användning. Å andra sidan, om det inom överskådlig framtid kan förväntas att koden kommer att återanvändas, underhållas eller vidareutvecklas, antingen av dig eller, mer kritiskt, av någon helt annan, blir programkodens läsbarhet och logiska modularitet väsentlig. Oftast är det så att om det är värt att göra, så är det värt att göra det bra, även i de allra tidigaste utvecklingsstadierna.

För att avsluta denna del av materialet kommer du att implementera ytterligare en större applikation.

<programming-exercise name='Studieregister' tmcname='osa10-12_studieregister'>

Skriv en interaktiv applikation för att hålla koll på dina studier. Den interna strukturen är upp till dig, men det här skulle vara ett bra tillfälle att öva på att skapa en liknande struktur som i Telefonkatalog-exemplet ovan.

Ditt program bör fungera på följande sätt:

<sample-output>

1 tillsätt prestation
2 hämta prestation
3 statistik
0 avsluta

instruktion: **1**
kurs: **ItP**
vitsord: **3**
studiepoäng: **5**

instruktion: **2**
kurs: **ItP**
ItP (5 sp) vitsord 3

instruktion: **1**
kurs: **ItP**
vitsord: **5**
studiepoäng: **5**

instruktion: **2**
kurs: **ItP**
ItP (5 sp) vitsord 5

instruktion: **1**
kurs: **ItP**
vitsord: **1**
studiepoäng: **5**

instruktion: **2**
kurs: **ItP**
ItP (5 sp) vitsord 5

instruktion: **2**
kurs: **Introduktion till Java**
inga prestationer

instruktion: **1**
kurs: **Tira**
vitsord: **1**
studiepoäng: **10**

instruktion: **1**
kurs: **Tilpe**
vitsord: **2**
studiepoäng: **5**

instruktion: **1**
kurs: **Lapio**
vitsord: **4**
studiepoäng: **1**

instruktion: **1**
kurs: **Lama**
vitsord: **5**
studiepoäng: **8**

instruktion: **3**
5 avklarade kurser, totalt 29 studiepoäng
medeltal 3.4
vitsordsdistribution
5: xx
4: x
3:
2: x
1: x

instruktion: **0**

</sample-output>

Varje kursnamn ska resultera i ett enda tillägg i registret. Ett betyg kan höjas genom att kursuppgifterna skrivs in på nytt, men betyget får aldrig sänkas.

Denna övning är värd två övningspoäng. Den första ges om kommandona 1, 2 och 0 fungerar korrekt i ditt program. Det andra ges om även kommando 3 fungerar som förväntat.

</programming-exercise>

## Epilog

För att avsluta den här delen av materialet återvänder vi till användargränssnittet i telefonkatalogsexemplet för en stund.

```python
class TelefonkatalogApplikation:
    def __init__(self):
        self.__katalog = Telefonkatalog()
        self.__fil = FilHanterare("katalog.txt")

    # annan kod

applikation = TelefonkatalogApplikation()
applikation.exekvera()
```

Ett `TelefonkatalogApplikation`-objekt innehåller både ett `Telefonkatalog`-objekt och ett `FilHanterare`-objekt. Namnet på den fil som skickas till FilHanterare är för närvarande hårdkodat i `TelefonkatalogApplikation`-klassen. Detta är en helt irrelevant detalj när det gäller applikationens användargränssnitt. Faktum är att den bryter mot principen om separation of concerns: var ett `Telefonkatalog`-objekt sparar sitt innehåll ska inte vara en angelägenhet för en `TelefonkatalogApplikation`, men om vi vill ändra platsen måste vi ändra koden för `TelefonkatalogApplikation`.

Det skulle vara en bättre idé att skapa ett FilHanterare-objekt någonstans utanför `TelefonkatalogApplikation`-klassen och skicka det som ett argument till applikationen:

```python
class TelefonkatalogApplikation:
    def __init__(self, fil):
        self.__katalog = Telefonkatalog()
        self.__fil = fil

    # annan kod

# skapar ett objekt för att hantera sparning av filen
forvaringstjanst = FilHanterare("katalog.txt")
# och passerar det som argument till TelefonkatalogApplikation-objektets konstruktor
applikation = TelefonkatalogApplikation(forvaringstjanst)
applikation.exekvera()
```

Detta tar bort ett onödigt beroende från `TelefonkatalogApplikation`-klassen. Om namnet på filen ändras behöver användargränssnittet inte längre ändras. Vi behöver bara skicka ett annat argument till konstruktören:


```python
class TelefonkatalogApplikation:
    def __init__(self, fil):
        self.__katalog = Telefonkatalog()
        self.__fil = fil

    # annan kod

# använd ett annat filnamn
forvaringstjanst = FilHanterare("ny_katalog.txt")
applikation = TelefonkatalogApplikation(forvaringstjanst)
applikation.exekvera()
```

Denna förändring gör det också möjligt för oss att överväga mer exotiska lagringsplatser, till exempel en molntjänst på internet. Vi behöver bara implementera en klass som använder molntjänsten och erbjuder `TelefonkatalogApplikation` exakt samma metoder som `FilHanterare`.

En instans av denna nya "moln hanterar"-klass kan skickas som ett argument till konstruktören och inte en enda rad kod behöver ändras i användargränssnittet:

```python
class MolnHanterare:
    # kod som sparar innehållet av telefonkatalogen i en molntjänst på internet

forvaringstjanst = MolnHanterare("amazon-cloud", "anvandarnamn", "losenord")
applikation = TelefonkatalogApplikation(forvaringstjanst)
applikation.exekvera()
```

Som du har sett tidigare har den här typen av tekniker ett pris, eftersom det blir mer kod att skriva, så en programmerare måste överväga om det är en acceptabel avvägning.

Den teknik som beskrivs ovan kallas beroendeinjektion. Som namnet antyder är tanken att alla beroenden som krävs av ett objekt ska tillhandahållas utanför objektet. Det är ett mycket användbart verktyg i en programmerares verktygslåda, eftersom det gör det lättare att implementera nya funktioner i program och underlättar automatisk testning. Detta tema kommer att utforskas ytterligare på de tidigare nämnda kurserna [Software Development](https://studies.helsinki.fi/opintotarjonta/cu/hy-CU-118024742-2020-08-01) och [Software Engineering](https://studies.helsinki.fi/opintotarjonta/cu/hy-CU-118024909-2020-08-01).


Svara vänligen på en snabb enkät om denna del av kursen.

<quiz id="4d7c8be7-105b-5bd2-87dd-7da65ff664e7"></quiz>
