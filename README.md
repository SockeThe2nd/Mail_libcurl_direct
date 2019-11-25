# Mail_libcurl_direct

Versendet Mails mit Anhang aus einer C++-Anwendung unter Verwendung von libcurl.


## libcurl

Libcurl ist die C-Bibliothek des bekannten [CURL-Kommandozeilen-Tools](https://curl.haxx.se/). Eine vorkompilierte Version der Bibliothek ist in diesem Repository enthalten. Diese wurde mit Hilfe des von Microsoft bereitgestellten [VCPKG-Tools](https://github.com/microsoft/vcpkg) erstellt.

Wichtig ist dabei das zum Erstellen der Lib die Version mit Unterstützung für nicht-http-Protokolle (wie etwa SMTP) verwendet wird. Dazu kann bei eingerichtetem VCPKG der Befehl `vcpkg install curl[non-http]` verwendet werden.


## MailSender-Klasse

Die MailSender-Klasse kapselt die Aufrufe an `libcurl` in einer einzigen Klasse mit simplem Interface. Die einzelnen Aufrufe an `libcurl` sind im Quellcode der Klasse kommentiert. Durch Aufrufen der Send-Funktion wird eine E-Mail generiert und an den SMTP-Server übermittelt. Der Aublauf ist dabei allgemein wie folgt:

1. Bereitgestellte Headerdaten erfassen und in Curl-Format bringen
2. Einstellungen der späteren Verbindung setzen (SSL/TLS, login, url)
3. MIME-Body der Nachricht erstellen und füllen (Text, HTML und Anhang)
4. MIME-Body anhängen und Anfrage starten

### Verwendung

```cpp
//MailSender-Klasse importieren
#include "MailSender.h"


// Zu verwendender SMTP-Server im Format `smtp://SERVER:PORT`
static const string MAIL_SERVER = "smtp://smtpauth.htwsaar.de:587";
// Zu verwendender Benuzter im Format `USER:PASSWORD`
static const string USER = "user:password";

// Sender und Empfänger-Adresse
static const string FROM = "sender-addr";
static const string TO = "receiver-addr";

/*
 * Instanz der MailSender-Klasse erstellen.
 * Die Adresse des Mailservers mit Port sowie der zu verwendende Benutzer sind als Argumente angegeben.
 */
MailSender sender = MailSender(MAIL_SERVER, USER);

//Senden einer Mail. Der MailSender kann mehrfach verwendet werden sollen mehrere Mails versandt werden.
sender.send(FROM, TO, "Test-Betreff", "Inhalt als text", "<b>Inhalt als HTML<\b>", "Image.png");
```

### Integration

Um die MailSender-Klasse in ein anderes Projekt zu integrieren muss sowohl die Klasse selbst (`MailSender.h` und `MailSender.cpp`) als auch libcurl/zlib hinzugefügt werden. 

Die im Verzeichnis `include` enthaltenen Header-Dateien müssen als zusätzliche Include-Verzeichnisse hinzugefügt werden. Die im `lib`-Verzeichnis enthaltenen Dateien müssen als zusätzlicher Linker-Input hinzugefügt werden. Die im `bin` Verzeichnis enthaltenen `.dll`-Dateien müssen nach Erstellen des Projekts im Verzeichnis der `.exe` liegen (hierzu kann z.B. das Post-Build-Event von Visual Studio verwendet werden). 

Eine allgemeine Anleitung zum hinzufügen von Bibliotheken ist im folgenden Post enthalten: [HIER](https://stackoverflow.com/questions/4445418/how-to-add-additional-libraries-to-visual-studio-project)
