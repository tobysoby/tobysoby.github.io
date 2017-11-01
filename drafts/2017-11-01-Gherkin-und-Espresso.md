---
title: Gherkin und Espresso
date: 2017-11-01 11:16:00
categories:
- Android
- Testautomatisierung
- Espresso
- Gherkin
- BDD
---
Als ich noch mehr mit Testautomatisierung im Web-Umfeld zu tun hatte war mein go-to-Tool natürlich [Cucumber](https://cucumber.io/) und alles was dazu gehört. Ich glaube jeder der einmal mit Anforderungen von Testfallerstellung, Management von Testfällen und anschließender Automatisierung in Berührung gekommen ist, möchte die Tools aus dem Cucumber-Umfeld nicht mehr missen.

Natürlich habe ich etwas derartiges auch im App-Umfeld gesucht und teilweise gefunden. Teilweise, weil z.B. [Calabash](http://calaba.sh/) auf Cucumber aufbaut und es erlaubt, die Testfälle in Gherkin zu verfassen, die [Zukunft dieses Frameworks aufgrund eingestellter Entwicklung durch den Haupt-Contributor Xamarin zumindest fragwürdig erscheint](https://forums.xamarin.com/discussion/93584/question-about-calabash-announcement). Abgesehen davon gibt es für Android natürlich auch den großen Konkurrenten Espresso, das offizielle UI-Testing Framework von Google, dessen Fähigkeiten von Version zu Version besser werden.

### Auftritt Green Coffee
Gefunden habe ich schließlich Green Coffee, eine Bibliothek die es erlaubt auch unter Android UI-Tests als Features in Gherkin zu verfassen und mit Espresso-Code zu verknüpfen.
Die Tests werden in den Assets gespeichert, der eigentliche Tests ist nur ein Set-Up in den die Step Definitions importiert werden.

### Einbindung in Android Projekt
Anstatt ein eigenes Projekt aufzusetzen, verweise ich an dieser Stelle lediglich auf die [hervorragende Beispiel-Implementierung von Maurizio Togneri](https://github.com/vndly/green-coffee-example).

Eingebunden ist die Bibliothek über Gradle.
```
dependencies
{
    androidTestCompile 'com.android.support.test:runner:1.0.1'
    androidTestCompile 'com.mauriciotogneri:greencoffee:2.9.0'
}
```
Dazu wird noch der folgende test instrumentation runner benötigt:
```
defaultConfig
{
    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}
```

Die App hat eine einfache Activity mit einem Login. Dieses besteht aus zwei EditTexts, einmal für unseren username, einmal für unser password. Darunter befindet sich ein Button zum Ausführen des Logins.

Unser Feature könnte folgendermaßen aussehen:
```
Feature: Login

Scenario: Login with wrong credentials
Given I am on the Login Screen
When I fill in a username "testuser"
And I fill in a password "wrongpassword"
When I tap the login button
Then I see a notice that my credentials are wrong

Scenario: Login with correct credentials
Given I am on the Login Screen
When I fill in a username "testuser"
And I fill in a password "correctpassword"
When I tap the login button
Then I see a notice that my credentials are wrong
```


