---
title: Retrofit 2 und XML
date: 2017-10-21 19:23:00
categories:
- Android
- Retrofit
- XML
---

Für ein privates Projekt musste ich zuletzt mithilfe von Retrofit auf eine XML-Api zugreifen. Hierzu geht man wie folgt vor.

Die Einbindung von Retrofit in ein Android-Projekt wird auf der [Retrofit Homepage](http://square.github.io/retrofit/) sehr gut erklärt. Da Retrofit standardmäßig den HTTP-Body nur in OkHttps ResponseBody serialisieren kann, ist für das weitere Mapping des Inhalt ein sog. Converter nötig. Allerdings sind die meisten der prominent im Web zu findenden Beispiele für die Serialisierung von Json-Responses, etwa mithilfe von Gson oder Jackson.

Für die Verarbeitung von XML-Responses bietet sich die Nutzung von [Simple XML](http://simple.sourceforge.net/) an.

Eingebunden werden sie im Builder für Retrofit mit einem .addConverterFactory, etwa wie folgt:

```
// Building retrofit for HTTP-Calls & Callbacks
Retrofit retrofit = new Retrofit.Builder()
	.client(okHttpClient)
	.addConverterFactory(SimpleXmlConverterFactory.create())
	// execute callback methods on background thread
	.callbackExecutor(Executors.newCachedThreadPool())
	.build();

restInterface = retrofit.create(RestInterface.class);
```

Haben wir eine XML-Struktur wie die folgende:
```
<parent>
	<child-one attribute-one="foo">
		<child-two></child-two>
		<child-two attribute-two="bar"></child-two>
		<child-two></child-two>
	</child-one>
</parent>
```

dann kann diese durch drei Java-Klassen abgebildet werden:
1. Root.java
```
@Root(name = "parent", strict = false)
public class Parent {
	Element(name="child-one", required = false)
	private ChildOne childOne;
}
```

2. ChildOne.java
```
@Root(name = "child-one", strict = false)
public class ChildOne {
	@ElementList(name="child-two", inline = true)
	private List<ChildTwo> item;

	@Attribute(name= "attribute-two")
    private String attributeTwo;
}
```

3. ChildTwo.java
```
@Root(name = "child-two", strict = false)
public class ChildTwo {
	@Attribute(name= "attribute-one")
    private String attributeOne;
}
```

Hiermit sollte dann die Serialisierung nach der Rückgabe der Response über das Retrofit-Interface gelingen.