---
title: Espresso für AndroidTV
date: 2018-04-10 08:34:54
categories:
- Android
- AndroidTV
- Espresso
---
Für unsere AndroidTV-App sehen wir natürlich ein Set an automatisierten Tests vor. Bei der Automatisierung von Espresso-Tests sind mir dabei allerdings folgende zwei Probleme aufgefallen, deren Lösung ich hier notieren möchte:
* Durch Leanback funktioniert kein direktes Klicken auf Menüpunkte
* Espresso Test Recorder funktioniert nicht

#### Durch Leanback funktioniert kein direktes Klicken auf Menüpunkte
Normalerweise ist die Element selection und das Interagieren mit Elementen bei Espresso gut und einfach gelöst.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}
