
# Synchronisierte Datenerfassung

In diesem Kapitel erfahren Sie mehr über das wichtigste Merkmal des XCP-Protokolls - das synchrone Messen von Daten mit dem DAQ-Modus.

Wir werden uns die verschiedenen Phasen sowie die Kommunikationsstruktur des DAQ-Modus ansehen. Wir werden den DAQ-Modus mit dem bereits bekannten Polling-Modus vergleichen. Kurz gesagt werden wir besprechen, wie verschiedene DAQ-Messmodi mit unterschiedlichen Messraten implementiert werden und wie entschieden wird, welche Raten zur Messung unserer Signale verwendet werden sollen.

Der DAQ-Modus wird durch XCP-Ereignisse implementiert, die der Software eines Steuergeräts hinzugefügt werden. Jedes XCP-Ereignis implementiert einen einzelnen DAQ-Messmodus. Da das XCP-Ereignis zyklisch mit einer festen Rate in der Software des Steuergeräts aufgerufen wird, wird auch der entsprechende DAQ-Messmodus eine feste Rate haben.

Sobald ein XCP-Ereignis aufgerufen wird, werden Daten - synchron - gemessen und an den XCP-Master gesendet. Während der Messung muss der XCP-Master daher keine Daten mehr anfordern. Nur der XCP-Slave sendet XCP-Nachrichten. Dies führt zu einer deutlich geringeren Buslast im Vergleich zum Polling-Modus.

Signale sollten immer mit einem DAQ-Modus gemessen werden, der mit der gleichen Rate misst, mit der die angeforderten Signale im Steuergerät aktualisiert werden. Nicht mit einer langsameren Rate (wir verpassen Daten), noch mit einer schnelleren Rate (wir erzeugen zusätzliche Buslast, ohne weitere Informationen zu erhalten). Daher ist es wichtig, den perfekten DAQ-Messmodus Ihrer Signale vor Ihrer Messung herauszufinden.

Neben zyklischen XCP-Ereignissen (Ereignisse, die z. B. alle 20 ms aufgerufen werden) können auch azyklische XCP-Ereignisse implementiert werden. Diese XCP-Ereignisse werden ausgelöst, sobald eine bestimmte Bedingung erfüllt ist (z. B. erreicht die Kurbelwelle eine bestimmte Position). Sie können für das Messen von Signalen verwendet werden, die im Steuergerät unter denselben Bedingungen aktualisiert werden.

Wenn Sie sich fragen, wie die Informationen darüber, welches Signal mit welchem DAQ-Messmodus gemessen werden soll, am besten gespeichert werden können, wird der Trainer Ihres bevorstehenden CANape-Trainings die Antwort kennen.
