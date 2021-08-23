# **Österreich E-Government Leak**

Im folgenden wird erklärt, dass ca. 1Mio personenbezogene Daten von österreichischen Bürgern öffentlich zugänglich sind. 

## **Wer ist hierfür verantwortlich?**

Die personenbezogenen Daten werden von der Firma **_A-Trust Gesellschaft für Sicherheitssysteme im elektronischen Datenverkehr GmbH_** (kurz **_A-Trust_**) durch deren Produkte (_Handy-Signatur_, _Bürgerkarte_) gespeichert.

## **Wer ist davon betroffen?**

Bürger, die einer der folgende Produkte der _A-Trust_ verwenden:

- _Handy-Signatur_ (https://handy-signaturkonto.at)
- _Bürgerkarte_ (https://bürgerkarte.at)

Weiters sind MitarbeiterInnen von folgenden Einrichtungen/Firmen betroffen:
- Personendaten des VwGH, VfGH, AK, WKO, OeGK, u.v.m.
- Personendaten der Bundeslandesregierungen 
- Personendaten der Polizei 
- Personendaten von MitarbeiterInnen von mehreren tausend Firmen:
  - ÖBB 
  - Raiffeisen Gruppe
  - Bawag
  - Erste Bank Gruppe
  - UniCredit
  - OMV
  - Post
  - A1
  - ...
  - div. KMUs

## **Welche Daten sind öffentlich?**

- Vorname
- Nachname
- Geburtsdatum
- E-Mail Adresse
- Einrichtung/Firma/Branche
- Verwendete A-Trust Produkte
- Sicherheitszertifikat (Public Key) 


Geburtsdaten oder E-Mail Adressen sind freiwillige/optionale Angaben. Falls diese bei der Beantragung nicht angegeben wurden, sind diese Felder auch nicht Teil des Datenbestands.  

## **Handelt es sich hierbei um ein Leak?**

Aus der Perspektive der _A-Trust_ ist der Zugang der o.g. personenbezogenen Daten in deren IT-Architektur vorgesehen.

Jedoch wirft das Lesen der Datenschutzerklärung und Websiten der _A-Trust_ einige Fragen auf, die für die Kunden der _A-Trust_ täuschend sind und nicht der Realität entsprechen. 

Auf der _A-Trust_ Website wird auf die Sicherheit der personenbezogenen Daten verwiesen:

**Zitat aus der Website der Bürgerkarte (_A-Trust_ Produkt):**

> Die Personenbindung der Handy-Signatur beinhaltet ebenso Vorname, Nachname, Geburtsdatum und Stammzahl. **Die Daten der Handy-Signatur werden in einem Hochsicherheits-Server gespeichert und nur bei Bedarf abgerufen.**

Die Datenschutzerklärung erwähnt ausdrücklich, dass personenbezogene Daten nicht weitergegeben werden. Dennoch widerspricht der Aufbau der IT-Systeme diese Aussagen.

**Zitat aus der Datenschutzerklärung der _A-Trust_**:


> ### **Bei Verwendung des Verzeichnisdienstes**
>
>
> A-Trust bietet die Webanwendung Verzeichnisdienst an, mit dem überprüft werden kann, ob eine durch Eingabe von **Vor- und Nachnamen/Email/UniqueID/Zeritifikatsseriennummer** identifizierte Person Inhaberin eines A-Trust Zertifikates ist. 
> 
> A-Trust verarbeitet diese Daten, neben Ihrer IP-Adresse und dem Zugriffszeitpunkt, ausschließlich zum Zweck der Ausgabe etwaiger Zertifikatsinhaber. **Diese personenbezogenen Daten werden nicht weitergegeben.**
>
> **Das Resultat der Verzeichnissuche erhält ausschließlich der konkrete Nutzer des Verzeichnisdienstes.**

Fakt ist jedoch, dass die o.g. Daten ungesichert für jede Person mit Internetzugang zugänglich sind.


## **Wie gelangt man zu den Daten?**

Die personenbezogenen Daten sind auf einem _OpenLDAP Server_, der auch innerhalb der Produktdokumentationen der _A-Trust_ an mehreren Stellen erwähnt wird, gespeichert. **Dieser Server ist ohne Sicherheitsvorkehrungen** in der DMZ erreichbar.

Mit Sicherheitsvorkehrungen sind folgende Punkte gemeint:

- Keine Authentifizierung
- Keine verschlüsselte Übertragung via TLS
- Keine Rate-Limits per IP
- Keine Konfigurationen, die das Herunterladen der Daten verhindern


Es kann daher nicht davon ausgegangen werden, dass die personenbezogenen Daten auf einem **"Hochsicherheits-Server"** gespeichert werden.

Der **"Hochsicherheits-Server"** wird wahrscheinlich für die Speicherung von Private Keys der Zertifikate verwendet und nicht für die persönlichen Informationen. 

Laut Dokumentation wird folgendes erwähnt (_Anmerkung: Das ist _A-Trust_ Sicherheitskonzept für die Absicherung des o.g. LDAP Servers_):

> **Um Trivialsuchen und missbräuchliche Verwendung zu verhindern, wird der LDAP Server mit einem Timelimit und Sizelimt betrieben.** 
>
> Bitte zu beachten, **wenn eine Abfrage länger als 300 sec dauert oder die Anzahl der Treffer 200 übersteigt, wird eine entsprechende Fehlermeldung zurückgegeben.**

In anderen Worten:

> Hier ist ein Buch, bitte nur Seite für Seite lesen.

Die Limitierung der zurückgegebenen Treffer des LDAP Servers **verhindern nicht**, dass eine Person mittels mehrere Anfragen das LDAP Verzeichnis durchiterieren kann.


## **Wurde die A-Trust in Kenntnis gesetzt?**

Die Antwort der _A-Trust_ hat mich dennoch interessiert, was mit der o.g. "Verhinderung von Missbrauchen" gemeint ist und welche Sicherheitsvorkehrungen getroffen werden zwecks dieser Personendaten. Die Antwort war:

>**Bei dem Service LDAP handelt es sich um ein öffentlichen Verzeichnisdienst, in dem natürliche Personen** bei der Aktivierung ihres qualifizierten Zertifikates **die Option haben anzugeben, dass ihr öffentliches Zertifikat veröffentlicht werden soll oder nicht.**
>
> **Der öffentliche Verzeichnisdienst ist nicht gesichert, da er öffentlich ist** und die natürliche Person angibt, ob es gewünscht ist, dass das Zertifikat veröffentlicht wird, oder nicht und nachträglich die Option hat die Entscheidung der Veröffentlichung wieder rückgängig zu machen.

Gefragt, mit welchen Konsequenzen man rechnen müsse, wenn man nicht im **"öffentlichen Verzeichnisdienst"** erscheinen möchte, wird wie folgt geantwortet:

> So gut wie keinen, darum werden **neue Handy-Signaturen auch gar nicht mehr** (_Anmerkung: ins öffentliche Verzeichnisdienst_) **aufgenommen**.

D.h. man hat mit einem IT-Konzept begonnen:

1. wo die _A-Trust_ personenbezogene Daten veröffentlicht
2. herausfindet, dass dies doch nicht notwendig ist
3. und die alten Handy-Signaturen, die nach einem Opt-Out Verfahren veröffentlicht worden sind, nicht aus dem Verzeichnis entfernt.

Interessant...


## **Werden die personenbezogenen Daten von dir veröffentlicht?**

Nein, ich habe kein Interesse an der Speicherung, Veröffentlichung o.ä.

Die Daten sind seit mind. 2016 öffentlich zugänglich.


## **Schlusswort**

Die _A-Trust_ mit dem Slogan: _"Einfach Sicher"_ erweckt bei mir kein Vertrauen. 

Man könnte sagen: Nomen est Omen. _A-Trust_ (gelesen: A Minus Trust).

Die Dokumentationen, Datenschutzerklärung, AGB und die Aussagen der leitenden Angestellten der _A-Trust_ widersprechen sich zutiefst. 

Einerseits erwähnt man, dass die _A-Trust_ personenbezogene Daten nicht an Drittländer weitergibt (aktiv). Auf der anderen Seite wird dies durch ein **"öffentliches Verzeichnis"** ermöglicht (passiv). 

Die Dokumentation des **"LDAP Servers"** erwähnt Maßnahmen, die eine **"missbräuchliche Verwendung"** verhindern. Die Realtität sieht jedoch anders aus.


Die Geschichte der _A-Trust_ zeigt:

- [Panne bei Handysignatur - Namen von Dokumenten einsehbar](https://www.derstandard.at/story/2000052007651/panne-bei-handysignaturat-name-von-dokumenten-einsehbar)
- [Unprofessionelles Vorgehen bei der Eintragung in Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=252610)
- [Firefox dank A-Trust nicht für e-Government geeignet](https://martin.leyrer.priv.at/y2008/m06/Firefox_3_dank_A-Trust_nicht_fuer_e-Government_geeignet.html) 


Wieso wir einen wichtigen Bestandteil unserer E-Government an eine private Firma ausgelagert haben, ist eine andere Frage.


## **Was ist das Ziel?**

Awareness. Hier ist man mit Personendaten fahrlässig umgegangen.

- Die _A-Trust_ muss in der Datenschutzerklärung oder in den AGB explizit auf die Eintragung von personenbezogenen Daten in ein öffentliches Verzeichnisdienst hinweisen. 
- Die Aussage, dass personenbezogene Daten auf "Hochsicherheits-Servern" liegen, aber dann auf einem einfachen OpenLDAP Server öffentlich bereitgestellt werden, kann als schäbiger Marketingspruch abgetan werden.
- Die Widersprüche, dass personenbezogenene Daten an unbefugte Personen/Organisationen/Drittländer nicht (aktiv) weitergegeben werden, aber unbefugte Personen/Organisationen/Drittländer in der Lage sind diese einfach herunterzuladen, ist fahrlässig.
- Die _A-Trust_ hat zu Beginn ohne einen Grund, via Opting-Out, Personendaten ins öffentliche Verzeichnisdienst eingetragen.


## **Bin ich davon betroffen und was kann ich tun?**

Mein Professor meinte immer: _In die Hände spucken und lachen._

### Betroffen

Betroffene, die vor Mai 2018 eine Handy-Signatur beantragt haben, können einen Antrag auf Löschung aus dem Verzeichnisdienst schriftlich bei der _A-Trust_ einreichen.

### Nicht Betroffen

Handy-Signaturen, die nach Mai 2018 beantragt wurden, werden nicht ins öffentliche Verzeichnisdienst eingetragen.

___
**Für die Statistik:** Mit Stand Mai 2018 gab es ca. 1Mio eingetragene Handy-Signaturen, die automatisch ins öffentliche Verzeichnisdienst eingetragen worden sind. 

