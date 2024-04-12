# Introduction to XCP

XCP, das Universal Measurement and Calibration Protocol, ist ein standardisiertes Protokoll, das in der Automobiltechnik für die effiziente Messung und Kalibrierung von elektronischen Steuergeräten (ECUs) verwendet wird. Dieses Kapitel dient als einführender Leitfaden für die Grundlagen von XCP und erkundet seine Anwendungsfälle, Nachrichtenstruktur und praktischen Anwendungen. XCP erleichtert die Messung und Kalibrierung von Daten innerhalb von ECUs und ermöglicht Ingenieuren die Interaktion und Modifikation von Parametern für optimale Leistung. Zu den häufigen Aufgaben gehören:

* **Datenerfassung** : Erfassung von Echtzeitdaten aus ECUs zur Analyse und Überwachung.
* **Kalibrierung** : Anpassung von ECU-Parametern zur Optimierung von Leistung, Kraftstoffeffizienz, Emissionen usw.

#### Struktur von XCP-Nachrichten

Die XCP-Kommunikation dreht sich um Nachrichten, die zwischen einem Master (z. B. Kalibrierungswerkzeug) und einem Slave (ECU) ausgetauscht werden. Diese Nachrichten bestehen aus Anfragen und Antworten, die über Kommunikationsnetze wie den CAN-Bus übertragen werden. Wesentliche Bestandteile einer XCP-Nachricht sind:

* **Paketkennung (PID)** : Das erste Byte eines XCP-Pakets, das den Befehlscodiert und die auszuführende Aktion bestimmt.
* **Datenfeld** : Enthält Informationen, die für die Ausführung des Befehls erforderlich sind, wie Speicheradressen, Datenwerte usw.

Die bereitgestellte Übung veranschaulicht einen Kalibrierungsprozess, der über XCP-Befehle durchgeführt wird. Es umfasst:

| Time   | Name | Dir | Len | Data                              | Command      |
| ------ | ---- | --- | --- | --------------------------------- | ------------ |
| 35.84s | CMD  | Tx  | 8   | **f6** 01 09 00 96 00 00 00 | SET_MTA      |
| 36.01s | RES  | Rx  | 1   | **ff**                      | POS_ACK      |
| 36.01s | CMD  | Tx  | 3   | **f0** 01 0a                | DOWNLOAD     |
| 36.01s | RES  | Rx  | 1   | **ff**                      | POS_ACK      |
| 36.07s | CMD  | Tx  | 8   | **f4** 01 0a 00 96 00 00 00 | SHORT_UPLOAD |
| 36.07s | RES  | Rx  | 2   | **ff** 0a                   | POS_ACK      |

Die Sequenz von XCP-Befehlen aus der Übung stellt einen realen Kalibrierungsprozess in einem CANape-Kalibrierfenster dar. Der Kalibrierungsprozess wird mit einem SET_MTA-Befehl (PID: 0xf6) initiiert. Der Befehl setzt den Speicherzugriffspointer des XCP-Slaves auf die in dem Befehl angegebene Adresse. In unserem Fall wird der Speicherzugriffspointer auf die Adresse 0x00 00 00 96 gesetzt. Nach Erhalt der positiven Antwort auf den SET_MTA-Befehl verwendet der Master einen DOWNLOAD-Befehl (PID: 0xf0), um Daten zu schreiben (in unserem Fall den Wert 0x0A, 1 Byte) in den Speicher des Slaves. Die Daten werden in die Speicherzellen geschrieben, die durch den Speicheradresspointer markiert sind.

Interessanterweise beendet der Master den Kalibrierungsprozess, indem er den Wert der geänderten Speicherzellen mit einem SHORT_UPLOAD-Befehl (PID: 0xf4) zurückliest. Der SHORT_UPLOAD-Befehl wird die Startadresse (0x00 00 00 96) und die Länge der auszulesenden Speicherzellen (1 Byte) enthalten. Der SHORT_UPLOAD-Befehl wird auch verwendet, um kontinuierlich einzelne Werte aus dem Steuergerät auszulesen, wie wir im nächsten Kapitel besprechen werden, das sich mit den verfügbaren Messmodi über XCP beschäftigt.

Im XCP-Protokoll wird jeder Befehl durch einen spezifischen Befehlscode identifiziert, der als erstes Byte eines XCP-Pakets übertragen wird, das als Paketkennung (PID) bekannt ist. Diese Befehlscodes werden durch die ASAM-Spezifikation des XCP-Protokolls standardisiert. Im Folgenden finden Sie eine Tabelle mit einer Reihe von XCP-Befehlen sowie ihren entsprechenden Befehlscodes. Durch Analyse des ersten Bytes jedes XCP-Pakets können Sie die entsprechenden XCP-Befehle identifizieren, wie in der Tabelle aufgeführt.

| Command             | Command Code |
| :------------------ | ------------ |
| GET_STATUS          | 0xFD         |
| SYNCH               | 0xFC         |
| GET_COMM_MODE_INFO  | 0xFB         |
| GET_ID              | 0xFA         |
| SET_REQUEST         | 0xF9         |
| GET_SEED            | 0xF8         |
| UNLOCK              | 0xF7         |
| SET_MTA             | 0xF6         |
| UPLOAD              | 0xF5         |
| SHORT_UPLOAD        | 0xF4         |
| BUILD_CHECKSUM      | 0xF3         |
| TRANSPORT_LAYER_CMD | 0xF2         |
| USER_CMD            | 0xF1         |
| DOWNLOAD            | 0xF0         |
| POS_ACK (RES)       | 0xFF         |
| ERR                 | 0xFE         |

Durch Bezugnahme auf diese Tabelle können Sie leicht den verwendeten XCP-Befehl in einem bestimmten XCP-Paket bestimmen, indem Sie den Wert des ersten Bytes analysieren, der den Befehlscode darstellt. Hier sind die Erklärungen für jeden Befehl im Zusammenhang mit dem XCP-Protokoll:

1. **GET_STATUS (Status abrufen)** (Befehlscode: 0xFD):

   - Dieser Befehl wird verwendet, um den Status des ECU-Slave-Geräts abzurufen, einschließlich Informationen über seinen aktuellen Zustand und Betriebsmodus.
2. **SYNCH (Synchronisation)** (Befehlscode: 0xFC):

   - Der SYNCH-Befehl wird verwendet, um die Kommunikation zwischen dem Master und dem Slave zu synchronisieren, insbesondere nach einem möglichen Kommunikationsverlust oder nach einem Systemneustart.
3. **GET_COMM_MODE_INFO (Kommunikationsmodusinformationen abrufen)** (Befehlscode: 0xFB):

   - Mit diesem Befehl können Informationen über die verfügbaren Kommunikationsmodi zwischen dem Master und dem Slave abgerufen werden, einschließlich der unterstützten Übertragungsraten und anderer relevanter Parameter.
4. **GET_ID (ID abrufen)** (Befehlscode: 0xFA):

   - Der GET_ID-Befehl wird verwendet, um die eindeutige Kennung des Slaves abzurufen, die oft zur Identifizierung des Geräts in einem Netzwerk verwendet wird.
5. **SET_REQUEST (Anforderung einstellen)** (Befehlscode: 0xF9):

   - Dieser Befehl wird verwendet, um spezifische Anfragen oder Anforderungen an den Slave zu senden, die eine bestimmte Aktion auslösen sollen, wie z.B. das Lesen oder Schreiben von Daten.
6. **GET_SEED (Seed abrufen)** (Befehlscode: 0xF8):

   - Mit diesem Befehl wird ein Seed-Wert vom Slave abgerufen, der für Authentifizierungszwecke verwendet werden kann, insbesondere wenn Sicherheitsmechanismen wie Seed & Key implementiert sind.
7. **UNLOCK (Entsperren)** (Befehlscode: 0xF7):

   - Der UNLOCK-Befehl wird verwendet, um den Slave zu entsperren, wenn bestimmte Zugriffsbeschränkungen oder Sicherheitsmechanismen aktiviert sind.
8. **SET_MTA (Memory-Transfer-Adresse einstellen)** (Befehlscode: 0xF6):

   - SET_MTA wird verwendet, um den Speicherzugriffspointer des Slaves auf eine bestimmte Adresse zu setzen, was oft der erste Schritt bei Kalibrierungs- oder Messvorgängen ist.
9. **UPLOAD (Hochladen)** (Befehlscode: 0xF5):

   - Dieser Befehl wird verwendet, um Daten oder Informationen vom Slave zum Master hochzuladen, beispielsweise Messwerte oder Konfigurationsdaten.
10. **SHORT_UPLOAD (Kurzes Hochladen)** (Befehlscode: 0xF4):

    - SHORT_UPLOAD ermöglicht das Hochladen einer begrenzten Anzahl von Daten vom Slave zum Master, was besonders nützlich ist, wenn nur kleine Datenmengen benötigt werden.
11. **BUILD_CHECKSUM (Prüfsumme erstellen)** (Befehlscode: 0xF3):

    - Mit diesem Befehl kann eine Prüfsumme oder ein Prüfwert für bestimmte Daten im Slave-Gerät erstellt werden, um die Integrität der übertragenen Daten zu überprüfen.
12. **TRANSPORT_LAYER_CMD (Transportebenen-Befehl)** (Befehlscode: 0xF2):

    - Dieser Befehl wird verwendet, um spezifische Befehle oder Aktionen auf der Transportebene durchzuführen, beispielsweise das Öffnen oder Schließen einer Kommunikationsverbindung.
13. **USER_CMD (Benutzerbefehl)** (Befehlscode: 0xF1):

    - USER_CMD ermöglicht die Übertragung benutzerdefinierter Befehle oder Aktionen zwischen Master und Slave, die nicht durch Standard-XCP-Befehle abgedeckt sind.
14. **DOWNLOAD (Herunterladen)** (Befehlscode: 0xF0):

    - Dieser Befehl wird verwendet, um Daten oder Konfigurationsinformationen vom Master zum Slave zu übertragen, beispielsweise Kalibrierungsparameter oder Software-Updates.
15. **POS_ACK (Positive Bestätigung) (RES)** (Befehlscode: 0xFF):

    - POS_ACK ist eine positive Bestätigungsnachricht vom Slave an den Master, um den Empfang und die Verarbeitung eines Befehls zu bestätigen.
16. **ERR (Fehler)** (Befehlscode: 0xFE):

    - ERR ist eine Fehlermeldung vom Slave an den Master, die darauf hinweist, dass ein Befehl nicht erfolgreich verarbeitet werden konnte oder ein Problem aufgetreten ist.

XCP-Anfragen, wie der SHORT_UPLOAD-Befehl, können verwendet werden, um bestimmte Zellen des internen Speichers eines Steuergeräts zu lesen. Diese Befehle werden verwendet, um Signale direkt aus dem internen Speicher zu messen, wie Sie im zweiten Kapitel erfahren werden. Andererseits können Daten auch in den internen Speicher zurückgeschrieben werden, z. B. durch Verwendung eines DOWNLOAD-Befehls.

XCP-Verbindungen sind Punkt-zu-Punkt-Verbindungen zwischen einem Master (z. B. einem Mess- und Kalibrierungswerkzeug wie CANape) und einem Slave (dem XCP-Treiber eines Steuergeräts). Die Kommunikation wird immer vom Master hergestellt. Der Master sendet einen XCP-Befehl im Datenfeld eines Transportebenenrahmens des vorhandenen Netzwerks. Der Typ des Befehls wird als erstes Byte der XCP-Nachricht als Paketkennung (PID) übertragen. Die folgenden Bytes der Nachricht enthalten Informationen, die vom Slave zur Ausführung des Befehls benötigt werden. Z. B. eine Adresse und die Anzahl der vom Slave auszulesenden Speicherzellen. Der XCP-Slave antwortet entweder mit einer positiven Antwort oder einer negativen Antwort. Eine positive Antwort kann zusätzliche Daten enthalten, z. B. die angeforderten Informationen. Eine negative Antwort trägt einen Fehlercode.

Die PID und Struktur von XCP-Befehlen und deren Antworten sind in der ASAM-Spezifikation des XCP-Protokolls beschrieben. Das Protokoll beschreibt auch die Struktur von XCP-Rahmen in einem CAN-, FlexRay-, Ethernet-Netzwerk usw. Mess- und Kalibrierungsaufgaben werden unter Verwendung derselben XCP-Befehle in verschiedenen Transportebenen implementiert. Lediglich die Transportebenenrahmen, die die entsprechenden XCP-Befehle tragen, werden sich ändern.

Im Fall von XCP über CAN kommuniziert jedes Master-Slave-Paar mit einem festen Satz von IDs, z. B. verwendet CANape die CAN-ID 200 und das Steuergerät die CAN-ID 201 für ihre XCP-Kommunikation. Falls ein zweites Master-Slave-Paar existiert, werden eine andere Reihe von festen CAN-IDs verwendet.
