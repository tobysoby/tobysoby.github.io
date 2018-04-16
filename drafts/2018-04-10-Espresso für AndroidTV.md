---
title: Espresso für AndroidTV
date: 2018-04-10 08:34:54
categories:
- Android
- AndroidTV
- Espresso
---
Bei der Automatisierung von Espresso-Tests treten besonders folgende zwei Probleme auf:
* Durch Leanback funktioniert kein direktes Klicken auf Menüpunkte
* Espresso Test Recorder funktioniert nicht

#### Durch Leanback funktioniert kein direktes Klicken auf Menüpunkte
Normalerweise ist die Element selection und das Interagieren mit Elementen gut und einfach gelöst.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}
