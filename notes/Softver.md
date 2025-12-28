---
title: Softvér
---

* TOC
{:toc}

Pre účely tohto predmetu budeme potrebovať nasledujúci softvér:

  - [Java SE Development
    Kit 23](https://www.oracle.com/java/technologies/downloads/) (stačí
    ľubovoľná verzia od 11 vyššie); pod Linuxom možno použiť aj balíky
    `openjdk`.
  - [Platformu JavaFX 23](https://gluonhq.com/products/javafx/) SDK
    (opäť by mala stačiť aj verzia 11) pre vývoj aplikácií s grafickým
    používateľským rozhraním.
  - Vývojové prostredie [IntelliJ IDEA Community
    Edition 2024.3.3](https://www.jetbrains.com/idea/download/),
    prípadne jeho vhodnú alternatívu.

Odporúčame pracovať vo vývojovom prostredí, avšak je dôležité naučiť sa
kompilovať a spúšťať programy aj z príkazového riadku (minimálne za
účelom riešenia rôznych problémov).

## Inštalácia softvéru

  - Stiahnite si
    [odtiaľto](https://www.oracle.com/java/technologies/downloads/)
    Java SE Development Kit 23 a postupujte podľa inštalačných pokynov.
  - Zistite, či inštalácia správne pridala adresár obsahujúci javovský
    kompilátor a interpreter do systémovej premennej `PATH`. V prípade
    úspechu by napríklad nasledujúce príkazy zadané do príkazového
    riadku mali vypísať verziu javovského kompilátora resp. interpretra
    zhodnú s nainštalovanou verziou Javy.

<!-- end list -->

    javac --version
    java --version

  - V prípade, že tieto príkazy nefungujú, alebo sa vypísala stará
    verzia Javy, pridajte adresár obsahujúci programy `javac` a `java`
    do systémovej premennej `PATH` manuálne (a prípadný adresár starej
    verzie Javy odtiaľ odoberte).
  - Stiahnite si [odtiaľto](https://gluonhq.com/products/javafx/) ZIP
    archív s JavaFX. Zvoľte verziu 23 a inštaláciu typu SDK pre vašu
    architektúru a operačný systém.
  - Rozbaľte stiahnutý archív do vhodného priečinka. Žiadne ďalšie úkony
    nie sú v rámci inštalácie JavaFX nutné, ale zapamätajte si adresu
    podadresára `lib`, keďže ju neskôr budete potrebovať. Nižšie budeme
    na túto cestu odkazovať ako na <cesta k lib>.
  - Stiahnite si [odtiaľto](https://www.jetbrains.com/idea/download/)
    vývojové prostredie IntelliJ IDEA Community Edition 2024.3.3 a
    postupujte podľa inštalačných pokynov.
  - IntelliJ by mal automaticky rozoznať nainštalovanú verziu Javy. V
    prípade, že sa tak neudeje, pridajte ju manuálne podľa pokynov
    [tu](https://www.jetbrains.com/help/idea/sdk.html).

## Skompilovanie a spustenie programu v Jave

Tento oddiel opisuje proces skompilovania a spustenia javovskej triedy
`Hello` vypisujúcej na konzolu text `Hello, World!` v dvoch variantoch:

  - V prvom je trieda `Hello` súčasťou nepomenovaného balíka.
  - V druhom je trieda `Hello` súčasťou balíka `somepackage`.

### Z príkazového riadku

Uvažujme najprv prípad, keď je trieda `Hello` súčasťou nepomenovaného
balíka, t.j. jej zdrojový kód vyzerá nasledovne.

``` java
public class Hello {

    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }

}
```

  - Trieda `Hello` musí byť uložená v súbore `Hello.java` (názov súboru
    bez prípony `.java` sa musí zhodovať s názvom triedy).

Postup skompilovania a spustenia triedy `Hello` z príkazového riadku je
potom nasledovný:

1.  V príkazovom riadku sa nastavíme do priečinka obsahujúceho súbor
    `Hello.java`.
2.  Triedu skompilujeme príkazom `javac Hello.java`, v dôsledku čoho by
    mal v rovnakom priečinku vzniknúť súbor `Hello.class` spustiteľný na
    JVM.
3.  Triedu `Hello` spustíme príkazom `java Hello` (prípadné argumenty
    môžeme triede `Hello` zadávať obvyklým spôsobom).

<!-- end list -->

  -   
    *Pozor*: kým pri volaní kompilátora `javac` je potrebné uviesť aj
    príponu kompilovaného súboru `.java`, pri volaní interpretra `java`
    príponu spúšťanej triedy `.class` nepíšeme (kým argumentom
    kompilátora je bežná cesta k súboru, argumentom interpretra je
    názov spúšťanej triedy).

Ďalej uvažujme prípad, keď je trieda `Hello` súčasťou balíka
`somepackage` a jej kód tak vyzerá nasledovne.

``` java
package somepackage;

public class Hello {

    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }

}
```

  - V tomto prípade by mala byť trieda `Hello` opäť uložená v súbore
    `Hello.java`, ktorý by mal navyše byť uložený v priečinku
    `somepackage`.

Skompilovanie a spustenie triedy `Hello` – alebo presnejšie
`somepackage.Hello` – z príkazového riadku realizujeme nasledovne:

1.  Nastavíme sa do priečinka obsahujúceho priečinok `somepackage`.
2.  Triedu skompilujeme príkazom `javac somepackage/Hello.java` (s
    príponou).
3.  Triedu spustíme príkazom `java somepackage.Hello` (argumentom je
    názov spúšťanej triedy vrátane balíka).

### V IntelliJ IDEA

1.  Vytvoríme nový projekt pomocou voľby *New Project*.
      - V najnovšej verzii IntelliJ IDEA Community Edition 2024.3.3
        *odškrtneme* (ak je zvolená) možnosť „*Add sample code*”, aby
        sa po vytvorení projektu automaticky nevygeneroval žiaden
        zbytočný kód; inak zvolíme východzie nastavenia (jazyk Java,
        Build system IntelliJ) a zadáme ľubovoľný názov projektu.
      - V o niečo starších verziách (napr. v počítačovej učebni) najprv
        v prvých dvoch krokoch použijeme východzie nastavenia (jazyk
        Java, bez použitia šablóny ani ďalších voliteľných možností), v
        treťom kroku zadáme ľubovoľný názov projektu.
2.  Ak sa nezobrazuje, zobrazíme panel so štruktúrou projektu cez `View
    --> Tool Windows --> Project` (alebo s použitím klávesovej skratky
    `Alt + 1`).
3.  Rozbalíme modul pomenovaný rovnako ako projekt (tučným) a zvolíme
    priečinok `src`.
4.  V ponuke `File --> New` (prípadne v podponuke `New` kontextovej
    ponuky, ktorá sa zobrazí po kliknutí na `src` pravou myšou) by sa
    teraz mala objavovať možnosť `Java Class`.
5.  Po zvolení tejto možnosti zadáme názov triedy (vrátane balíkov),
    t.j. `Hello` pre triedu v nepomenovanom balíku a `somepackage.Hello`
    pre triedu v balíku `somepackage`.
6.  V závislosti od vybranej alternatívy skopírujeme do editora vhodnú
    variantu triedy `Hello` z predchádzajúceho oddielu.
7.  Triedu skompilujeme a spustíme napríklad kliknutím na zelenú šípku
    vedľa hlavičky metódy `main` a zvolením možnosti `Run
    'trieda.main()'`. Následné ďalšie spustenia možno realizovať
    skratkou `Shift + F10`.

Dôvod, prečo skratka `Shift + F10` (ekvivalentná možnosti `Run --> Run
'Hello'`) nefunguje hneď od začiatku je ten, že sme zatiaľ nevytvorili
tzv. konfiguráciu behu, v ktorej sa napríklad zadáva verzia Javy použitá
pri kompilovaní, hlavná trieda projektu (t.j. trieda, ktorá sa má
spustiť po spustení projektu), prípadné argumenty z príkazového riadku,
parametre pre kompilátor a interpreter, atď. V prípade kliknutia na
zelenú šípku vedľa metódy main a zvolenia príslušnej možnosti sa
automaticky vytvorí tzv. dočasná konfigurácia, pri ktorej sa trieda
spustí s východzími nastaveniami.

Pre neskoršie účely je dôležité vedieť vytvárať aj vlastné (a
permanentné) konfigurácie behu (v jednom projekte možno definovať aj
viacero konfigurácií, medzi ktorými sa potom možno prepínať pomocou
prepínača v pravej časti nástrojovej lišty). Postup vytvorenia novej
konfigurácie behu je nasledujúci:

1.  Zvolíme `Run --> Edit Configurations...`.
2.  Klikneme na tlačidlo `+` a zvolíme možnosť `Application`.
3.  Následne môžeme v hlavnej časti okna napravo zadať názov
    konfigurácie, napríklad `Konfiguracia`.
4.  Zadáme potrebné údaje: predvolená by mala byť správna verzia Javy
    (napr. `java 23`), do políčka `Main class` zadáme názov hlavnej
    triedy projektu (napr. `Hello`), do políčka `Program arguments`
    môžeme zadať prípadné argumenty pre spúšťanú triedu z príkazového
    riadku (políčko môžeme ponechať prázdne). Prípadné ďalšie možnosti
    môžeme pridať kliknutím na `Modify options`.
5.  Ukončíme dialóg tlačidlom `OK`.
6.  Na nástrojovej lište sa uistíme, že je vybraná práve vytvorená
    konfigurácia a projekt spustíme skratkou `Shift + F10`.

Viac sa o konfiguráciách behu možno dočítať
[tu](https://www.jetbrains.com/help/idea/run-debug-configuration.html).

## Skompilovanie a spustenie programu v JavaFX

Ukážeme si teraz, ako z príkazového riadku a v IntelliJ IDEA skompilovať
a spustiť nasledujúcu triedu pracujúcu s JavaFX, ktorá by mala zobraziť
okno s titulkom `Hello, World!`. Táto trieda musí byť uložená v súbore
`HelloFX.java`.

``` java
import javafx.application.*;
import javafx.stage.*;
import javafx.scene.*;
import javafx.scene.layout.*;

public class HelloFX extends Application {
    @Override
    public void start(Stage primaryStage) {
        Pane pane = new Pane();
        Scene scene = new Scene(pane, 300, 250);
        
        primaryStage.setTitle("Hello, World!");
        primaryStage.setScene(scene);
        primaryStage.show();
    }
    
    public static void main(String[] args) {
        launch(args);
    }
}
```

V nasledujúcom <cesta k lib> odkazuje na cestu k adresáru `lib`
rozbaleného ZIP archívu JavaFX, o ktorej sme sa už zmienili vyššie.
Viac detailov možno nájsť [tu](https://openjfx.io/openjfx-docs/).

### Z príkazového riadku

1.  Nastavíme sa do priečinku obsahujúceho súbor `HelloFX.java`.
2.  Triedu skompilujeme príkazom

<!-- end list -->

    javac --module-path <cesta-k-lib> --add-modules javafx.controls,javafx.fxml HelloFX.java

3.  Triedu spustíme príkazom

<!-- end list -->

    java --module-path <cesta-k-lib> --add-modules javafx.controls,javafx.fxml HelloFX

### V IntelliJ IDEA

1.  Vytvoríme projekt a v ňom triedu `HelloFX` rovnakým spôsobom ako
    vyššie (dá sa prípadne vytvoriť aj projekt typu `JavaFX`).
2.  Otvoríme dialóg `File --> Project Structure...`. V ňom otvoríme
    záložku `Libraries`, klikneme na tlačidlo `+` a zvolíme možnosť
    `Java`. Otvorí sa dialógové okno umožňujúce vytvoriť knižnicu z
    priečinka. Za tento priečinok vyberieme priečinok <cesta k lib>.
3.  Vytvoríme konfiguráciu behu podobným spôsobom ako vyššie. Za hlavnú
    triedu vyberieme `HelloFX`.
4.  Klikneme na `Modify options` a zaškrtneme možnosť `Add VM options`.
5.  Následne by sa malo objaviť políčko `VM options`, do ktorého zadáme

<!-- end list -->

    --module-path <cesta-k-lib> --add-modules javafx.controls,javafx.fxml

6.  Dialóg potvrdíme kliknutím na `OK`.
7.  Projekt spustíme klávesovou skratkou `Shift + F10`.

