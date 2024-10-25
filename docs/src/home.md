# TI Messenger Documentation v1.1.1

## Überblick

Die folgende Dokumentation ergänzt die Spezifikation *TI-Messenger-Dienst* v1.1.1 gemäß *[gemSpec_TI-Messenger-Dienst]*. An dieser Stelle werden insbesondere die in der Spezifikation genannten Komponenten der Lösung sowie deren Schnittstellen weiter dargestellt und erläutert, um Hersteller und Anbieter eines *TI-Messenger-Dienstes* bestmöglich zu unterstützen. Der Funktionsumfang des *TI-Messenger-Dienst* v1.1.1 beinhaltet: +

* Instant-Messaging zwischen Leistungserbringern und Leistungserbringerinstitutionen und
* Instant-Messaging direkt zwischen Leistungserbringern.

```admonish note
In der Spezifikationsversion v1.1.1 können keine Versicherten an dem *TI-Messenger-Dienst* teilnehmen_.
```

```admonish important
Die Dokumentation ergänzt die Spezifikation gemäß [gemSpec_TI-Messenger-Dienst](https://fachportal.gematik.de/fachportal-import/files/gemSpec_TI-Messenger-Dienst_V1.1.1.pdf), die als Grundlage für das Verständnis vorrausgesetzt wird.
```

## Branches

Für die Dokumentation des *TI-Messenger-Dienstes* werden in GitHub die folgenden Branches verwendet:

- `main`: enthält die Dokumentation für das aktuell veröffentlichte Release gemäß *[gemSpec_TI-Messenger-Dienst]*,
- `develop`: enthält die Dokumentation der in Entwicklung befindlichen Features, die noch nicht released sind, 
- `hotfix`: enthält die Dokumentation kurzfristig notwendiger Änderungen am aktuellen Release, 
- `feature/*`: enthält eine Vorschau, Vorbereitungen und Diskussion eines neuen Themas, welches noch nicht in der Entwicklung ist. Die Inhalte können unvollständig sein und sich bis zur Fertigstellung noch ändern.

## Systemübersicht
Die folgende Abbildung gibt einen Überblick über die Systemarchitektur des *TI-Messenger-Dienst* v1.1.1 insbesondere auf die Schnittstellen zwischen den Komponenten, die in den folgenden Kapiteln weiter betrachtet werden. 

![](images/System_overview.png)

```admonish tip
Auf die Schnittstellen zur Autentisierung am *Auth-Service* des *VZD-FHIR-Directory* wird in der oben gezeigten Abbildung verzichtet. Die Informationen hierzu können in dem entsprechenden Kapitel für das *VZD-FHIR-Directory* [hier](https://github.com/gematik/api-vzd/blob/gemILF_VZD_FHIR_Directory/1.2.2/docs/FHIR_VZD_HOWTO_Authenticate.adoc#fhirdirectoryauthenticationapis) nachgelesen werden.
```

[*TI Messenger-Fachdienst*](fachdienst.md)

* [*Registrierungs-Dienst*](registrierungsdienst.md)
Der *Registrierungs-Dienst* bietet drei abstrakte Schnittstellen an. Die Schnittstelle `I_Registration` wird vom *Frontend des Registrierungs-Dienstes* aufgerufen, um eine Organisation beim *Registrierungs-Dienst* zu authentifizieren und *Messenger-Services* zu administrieren. Die Schnittstelle `I_internVerification` wird von den *Messenger-Proxies* aufgerufen, um die Föderationsliste abzurufen und dient zusätzlich der Prüfung (der beteiligten Akteure) auf existierende VZD-FHIR-Einträge. Die Schnittstelle `I_requestToken` wird vom *Org-Admin-Client* aufgerufen, um Zugang zum *FHIR-Proxy* für die Bearbeitung von FHIR-Ressourcen zu erhalten.

* [*Messenger-Service*](messengerservice.md)
Ein *Messenger-Service* besteht aus den Teilkomponenten *Messenger-Proxy* und einem *Matrix-Homeserver*. Die Teilkomponente *Matrix-Homeserver* basiert auf dem offenen Kommunikationsprotokoll Matrix und bietet die `Matrix-Client-Server API` sowie die `Matrix-Server-Server API` an. Die Kommunikation zu einem *Matrix-Homeserver* wird immer über den *Messenger-Proxy* geleitet, sofern die Berechtigungsprüfung erfolgreich war. Der *Messenger-Proxy* stellt die Schnittstelle `I_TiMessengerContactManagement` bereit, um die Administration der Freigabeliste eines Akteurs zu ermöglichen. 

* [*Push-Gateway*](https://spec.matrix.org/v1.3/push-gateway-api/)
Das *Push-Gateway* stellt die `Matrix-Push-Gateway API` gemäß der Matrix Spezifikation bereit. Dieses ermöglicht die Weiterleitung von Benachrichtigungen an Akteure des *TI-Messenger-Dienstes*.

[*TI Messenger-Client*](client.md)

* Der *TI-Messenger-Client* basiert auf der `Matrix-Client-Server API`. Er wird durch weitere Funktionsmerkmale erweitert und ruft die Schnittstellen am *TI-Messenger-Fachdienst* sowie am *VZD-FHIR-Directory* auf.

[*VZD-FHIR-Directory*](https://github.com/gematik/api-vzd/blob/main/docs/Fachkonzept_FHIR-Directory.adoc)

* Beim *VZD-FHIR-Directory* handelt es sich um einen zentralen Verzeichnisdient der TI, der die deutschlandweite Suche von Organisationen und Akteuren des *TI-Messenger-Dienstes* ermöglicht. Das *VZD-FHIR-Directory* basiert auf dem FHIR-Standard und bietet neben der Suche (`FHIRDirectorySearchAPI`) für den *TI-Messenger-Dienst* Schnittstellen zur Administration der [*Föderationsliste*](föderationsliste.md) an (`FHIRDirectoryTIMProviderAPI`). Zusätzlich existiert für Organisaitonen und Practitioner eine Schnittstelle (`FHIRDirectoryOwnerAPI`), über die die Verwaltung des eigenen Eintrages im *VZD-FHIR-Directory* möglich ist. Details sind dem [*Implementierungsleitfaden*](https://github.com/gematik/api-vzd/blob/gemILF_VZD_FHIR_Directory/1.2.2/docs/gemILF_VZD_FHIR_Directory.adoc) des *VZD-FHIR-Directory* zu entnehmen.

[*Zentraler IDP-Dienst*](idp.md)

* Der *Zentrale IDP-Dienst* der gematik übernimmt die Aufgabe der smartcard-basierten Authentisierung eines Akteures. Hierbei fasst der *IDP-Dienst* aus der Smartcard notwendige Attribute (z. B. `TelematikID`, `ProfessionOID`) in ein signiertes JSON Web Token (`ID_TOKEN`) zusammen, damit sich ein Client gegenüber Fachanwendungen (*Registrierungs-Dienst* und *VZD-FHIR-Directory*) identifizieren kann. 

[*gematik Authenticator*](authenticator.md)

* Der *Authenticator* der gematik erhält vom *zentralen IDP-Dienst* einen `AUTHORIZATION_CODE` zurück, welcher durch Vorlage vom *Registrierungs-Dienst* oder vom *Auth-Service* des *VZD-FHIR-Directory* am *IDP-Dienst* durch ein `ID_TOKEN` ausgetauscht wird.

## Quellen

Die nachfolgende Tabelle enthält die in der vorliegenden Online Dokumentation referenzierten Dokumente der gematik. 

| [Quelle] | Herausgeber: Titel |
| [gemSpec_TI-Messenger-Dienst_v1.1.1](https://fachportal.gematik.de/fachportal-import/files/gemSpec_TI-Messenger-Dienst_V1.1.1.pdf) | gematik: Spezifikation TI-Messenger-Dienst |
| [gemSpec_TI-Messenger-FD_v1.1.1](https://fachportal.gematik.de/fachportal-import/files/gemSpec_TI-Messenger-FD_V1.1.1.pdf) | gematik: Spezifikation TI-Messenger-Fachdienst |
| [gemSpec_TI-Messenger-Client_v1.1.1](https://fachportal.gematik.de/fachportal-import/files/gemSpec_TI-Messenger-Client_V1.1.1.pdf) | gematik: Spezifikation TI-Messenger-Client |
| [gemSpec_VZD_FHIR_Directory_v1.3.0](https://fachportal.gematik.de/fachportal-import/files/gemSpec_VZD_FHIR_Directory_V1.3.0.pdf) | gematik: Spezifikation Verzeichnisdienst FHIR-Directory |
| [zentraler IDP_Dienst](https://fachportal.gematik.de/hersteller-anbieter/komponenten-dienste/identity-provider-idp) | gematik: zentraler IDP-Dienst der gematik |
| [Authenticator](https://fachportal.gematik.de/hersteller-anbieter/komponenten-dienste/authenticator) | gematik: Authenticator der gematik |


## 💡 Onboarding

Hersteller und Anbieter eines *TI-Messenger-Dienstes* können das von der gematik im [Fachportal](https://fachportal.gematik.de/anwendungen/ti-messenger) bereitgestellte Welcome Package zum Onboarding nutzen. Dieses Welcome Package ist als "Schritt-für-Schritt"-Anleitung gedacht, um Hersteller und Anbieter beim Onboarding des *TI-Messenger-Dienstes* zu unterstützen.

## Weiterführende Seiten

*Produkttypen* +
- [TI-Messenger-Fachdienst](fachdienst.md)
- [TI-Messenger-Client](client.md)
- [VZD-FHIR-Directory](https://github.com/gematik/api-vzd/blob/main/docs/Fachkonzept_FHIR-Directory.adoc)
- [Zentraler IDP-Dienst](idp.md)

*Leitfaden für Primärsystemhersteller*
- [Primärsystem](primärsystem.md)

*Diverses*
- [TI-Messenger im gematik Fachportal](https://fachportal.gematik.de/anwendungen/ti-messenger)
- [Authenticator im gematik Fachportal](https://fachportal.gematik.de/hersteller-anbieter/komponenten-dienste/authenticator)
- [TI-Messenger-Testsuite](https://github.com/gematik/TI-Messenger-Testsuite)
- [Fragen und Antworten zur aktuellen Spezifikation &#91;FAQ&#93;](faq.md)

*Referenz-Implementierungen* +
- coming soon

## Lizenzbedingungen

Copyright (c) 2023 gematik GmbH

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
