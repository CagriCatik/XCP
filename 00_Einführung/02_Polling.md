# Polling

XCP bietet zwei verschiedene Messmodi, den Polling-Modus und den Synchrone Datenerfassungsmodus - kurz DAQ-Modus.

Der CANape-Benutzer entscheidet über den Messmodus in CANape in der Messkonfiguration.

In diesem Kapitel erfahren Sie mehr über den Polling-Modus und seine Kommunikationsstruktur. Am Ende des Kapitels werden wir die Vor- und Nachteile dieses Messmodus diskutieren.

Der Polling-Modus wird mit den Befehlen SHORT_UPLOAD oder UPLOAD implementiert. Jedes angeforderte Signal wird aus dem Speicher des Steuergeräts mit einem separaten Befehl gelesen. XCP-Befehle werden immer sequenziell verarbeitet, was bedeutet, dass zuerst ein Befehl verarbeitet und bestätigt werden muss, bevor ein folgender Befehl vom Master gesendet werden kann.

Als Konsequenz ist es nicht möglich, mehrere Signale genau zur gleichen Zeit im Polling-Modus zu messen. Signale werden asynchron mit einer Zeitverzögerung zwischen ihnen gemessen.

Das Messen vieler Signale im Polling-Modus führt zu einer signifikant hohen Buslast, da mindestens zwei XCP-Nachrichten pro gemessenem Signal in jedem Zyklus benötigt werden.

Zusammenfassend ist der Polling-Modus nur für die Messung weniger dynamischer Signale bei niedrigeren Messraten geeignet, z. B. Temperaturvariablen. Wenn Sie sich fragen, in welchen anderen Szenarien der Polling-Modus nützlich sein kann, zögern Sie nicht, diese Frage dem Trainer Ihres bevorstehenden CANape-Trainings zu stellen.

Um Signale synchronisiert zu messen - also genau zur gleichen Zeit gemessen zu werden, muss der DAQ-Modus verwendet werden. Im nächsten Kapitel erfahren Sie mehr über diesen zweiten, ausgefeilteren Messmodus.
