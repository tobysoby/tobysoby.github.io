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
Als ich noch mehr mit Testautomatisierung im Web-Umfeld zu tun hatte war mein primäres Werkzeug natürlich [Cucumber](https://cucumber.io/) und alle Tools die im weitesten Sinne dazugehören. Ich glaube jeder, der einmal mit Anforderungen von Testfallerstellung, Management von Testfällen und anschließender Automatisierung in Berührung gekommen ist, möchte das Cucumber-Umfeld nicht mehr missen.

Natürlich habe ich etwas derartiges auch im App-Umfeld gesucht und teilweise gefunden. Teilweise, weil z.B. [Calabash](http://calaba.sh/) auf Cucumber aufbaut und es erlaubt, die Testfälle in Gherkin zu verfassen, die [Zukunft dieses Frameworks aufgrund eingestellter Entwicklung durch den Haupt-Contributor Xamarin allerdings zumindest fragwürdig erscheint](https://forums.xamarin.com/discussion/93584/question-about-calabash-announcement). Abgesehen davon gibt es für Android natürlich auch den großen Konkurrenten Espresso, das offizielle UI-Testing Framework von Google, dessen Möglichkeiten von Version zu Version größer werden.

### Auftritt Green Coffee
Gefunden habe ich schließlich GreenCoffee, eine Bibliothek die es erlaubt auch unter Android UI-Tests als Features in Gherkin zu verfassen und mit Espresso-Code zu verknüpfen.

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

Die App hat u.a. eine einfache Activity mit einem Login. Dieses besteht aus zwei EditTexts, einmal für unseren username, einmal für unser password. Darunter befindet sich ein Button zum Ausführen des Logins.

### Aufbau der Features
Unser Feature könnte nun folgendermaßen aussehen:
```
Feature: Login screen to authenticate users

...

Scenario: Invalid password
	When I introduce a valid username
	And  I introduce an invalid password
	And  I press the login button
	Then I see an error message saying 'Invalid credentials'

...

```

Wir sehen direkt Steps die mit angepassten Argumenten wiederverwendet werden können.

Bewährt hat es sich für mich, die Features in den Assets abzulegen.

### Aufbau der Tests
Die Tests werden zunächst als normale Espresso-Tests angelegt, die von GreenCoffeeTest erben. Hier wäre etwa die entsprechende Test-Datei für unser Login Feature.

```
@RunWith(Parameterized.class)
public class LoginFeatureTest extends GreenCoffeeTest // 1
{
    @Rule
    public ActivityTestRule<LoginActivity> activity = new ActivityTestRule<>(LoginActivity.class); // 2

    public LoginFeatureTest(ScenarioConfig scenarioConfig)
    {
        super(scenarioConfig);
    }

    @Parameters(name = "{0}")
    public static Iterable<ScenarioConfig> scenarios() throws IOException
    {
        return new GreenCoffeeConfig("testapp")
                .withFeatureFromAssets("assets/login.feature") // 3
                .scenarios(ENGLISH, SPANISH);
    }

    @Test
    public void test()
    {
        start(new LoginSteps(), new ContactListSteps(), new ScreenshotSteps()); // 4
    }

    @Override
    protected void beforeScenarioStarts(Scenario scenario, Locale locale)
    {
        // do something // 5
    }
```

Zur Erklärung:
1) Der Name der Klassen. Sie erbt von GreenCoffeeTest.
2) Es wird eine ActivityTestRule der Activity benötigt, die gestartet werden soll.
3) Die benötigte Pfadangabe zu der Feature-Datei.
4) Als Argumente benötigt GreenCoffee die Klassen mit den Steps.
5) Hier bestünde die Möglichkeit vor der Ausführung der Tests etwa ein spezielles SetUp durchzuführen.


```
public class LoginSteps extends GreenCoffeeSteps
{
    private static final String INVALID_USERNAME = "guest";
    private static final String INVALID_PASSWORD = "5678";

    ...

    @When("^I press the login button$")
    public void iPressTheLoginButton()
    {
        onViewWithId(R.id.login_button_doLogin).click();
    }

    @When("^I introduce a valid username$")
    public void iIntroduceAValidUsername()
    {
        onViewWithId(R.id.login_input_username).type(UserDatabase.USER_1.username());
    }

    @When("^I introduce a valid password$")
    public void iIntroduceAValidPassword()
    {
        onViewWithId(R.id.login_input_password).type(UserDatabase.USER_1.password());
    }

    @When("^I introduce an invalid username$")
    public void iIntroduceAnInvalidUsername()
    {
        onViewWithId(R.id.login_input_username).type(INVALID_USERNAME);
    }

    @When("^I introduce an invalid password$")
    public void iIntroduceAnInvalidPassword()
    {
        onViewWithId(R.id.login_input_password).type(INVALID_PASSWORD);
    }

    ...

}
```

Auch die Step-Dateien müssen von einer GreenCoffee-Klasse erben, in diesem Fall von GreenCoffeeSteps. Die Verbindung zwischen den Steps in den Feature-Dateien und den auszuführenden Methoden passiert über Annotationen.

Sind alle diese Dateien angelegt, kann der Test über ein Ausführen der Tests etwa in diesem Fall in LoginFeatureTest gestartet werden. Er verhält sich dabei wie ein regulärer Espresso-Test.

Ich hoffe ich konnte euch GreenCoffee etwa näher bringen :-)