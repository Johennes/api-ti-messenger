# Registrierungs-Dienst

## Überblick

Die folgende Seite gibt einen kurzen Überblick über die Funktionalitäten des *Registrierungs-Dienstes* und beschreibt die Unterschiede für den *Registrierungs-Dienst* in unterschiedlichen Produktausprägungen des *TI-Messengers*. Details sind den Spezifikationen auf den [gemspec Pages](https://gemspec.gematik.de/) der gematik zu entnehmen.  

## Schnittstelle: I_Registration

Über die Schnittstelle `I_Registration` werden 2 Funktionen bereitgestellt. Zum einen kann die eigene Organisation (z. B. per SM\(C)-B) registriert werden, um einen Admin-Account zu erhalten. Zum anderen können anschließend über die Schnittstelle neue *Messenger-Services* bereitgestellt werden. Die Ausgestaltung des Frontends sowie der Schnittstelle `I_Registration` ist dem jeweiligen TI-Messenger-Hersteller überlassen. 

### Authentisieren einer Organisation

Die abstrakte Schnittstelle `I_Registration` muss es einer Organisation ermöglichen sich mittels der SM\(C)-B der Organisation zu authentisieren. Der *Registrierungs-Dienst* kann hierfür einen Prozess mittels OpenID-Connect oder KIM anbieten. 

### Bereitstellung eines Messenger-Service

Nach erfolgreicher Authentifizierung einer Organisation am *Registrierungs-Dienst* wird ein Admin-Account für die Organisation auf dem Registrierungs-Dienst angelegt. Unter Verwendung des bereitgestellten Admin-Kontos können *Messenger-Services* für die Organisation erstellt werden. Um einen neuen *Messenger-Service* zu erstellen, muss ein *Registrierungs-Dienst*:

. sich gegenüber dem Endpunkt `/tim-provider-services` gemäß [Authentisierung für die Anbieter-API](https://github.com/gematik/api-vzd/blob/gemILF_VZD_FHIR_Directory/1.2.2/docs/FHIR_VZD_HOWTO_Authenticate.adoc#authenticate-for-the-provider-api) authentisieren und
. die neue Domäne zur Föderationsliste [hinzufügen](https://github.com/gematik/api-vzd/blob/gemILF_VZD_FHIR_Directory/1.2.2/docs/FHIR_VZD_HOWTO_Provider.adoc#add-own-domain).

```admonish tip
Wenn für die Auflösung der Matrix Domains Redirects verwendet werden, dann müssen diese ebenfalls in der Föderationsliste hinterlegt werden. Sonst kann der Zugriff auf den `/getInfo` Endpunkt durch das *VZD-FHIR-Directory* nicht gewährleistet werden. (Die Firewall des *VZD-FHIR-Directorys* blockiert sonst den Zugriff auf die Redirect Domains, wenn diese nicht in der "Allowlist" hinterlegt werden. Das Hinterlegen für die Firewall erfolgt automatisch, wenn die Redirects in der Föderationsliste hinterlegt werden)
```

## Schnittstelle: I_Admin

Über die Schnittstelle `I_Admin` stellt der *Registrierungs-Dienst* dem Akteur in der Rolle *Org-Admin* Funktionen zur Verwaltung der eigenen *Messenger-Services* zur Verfügung.

## Schnittstelle: I_internVerification

Über die Schnittstelle `I_internVerification` stellt der *Registrierungs-Dienst* den angeschlossenen *Messenger-Proxies* Funktionen bereit um Verwaltungsaufgaben an der Schnittstelle `I_VZD_TIM_Provider_Services` des *VZD-FHIR-Directory* durchzuführen.

# TI-M Pro Besonderheiten

Dieser Abschnitt beschreibt die besonderen Eigenschaften die für einen *TI-M Pro Registrierungs-Dienst* gelten.

## Schnittstelle: I_requestToken

Über die Schnittstelle `I_requestToken` stellt der *Registrierungs-Dienst* `RegService-OpenID-Token` aus. Das Token wird für die Authentifizierung am *FHIR-Proxy* des *VZD-FHIR-Directory* benötigt, damit ein Akteur in der Rolle *Org-Admin* Organisationseinträge ändern kann. Das Token muss signiert werden, damit das *VZD-FHIR-Directory* dem Aussteller vertraut. Hierzu ist ein Zertifikat über einen *TI-ITSM Service Request* zu [beantragen](fachdienst.md#erstellung-des-signaturzertifikates-f%C3%BCr-den-anbeiter), welches im Anschluss für die Signatur genutzt werden kann. Weitere Details z.B. zum Aufbau und Inhalt des Tokens sind in [Authentisierung RegService-OpenID-Token](https://github.com/gematik/api-vzd/blob/gemILF_VZD_FHIR_Directory/1.2.2/docs/FHIR_VZD_HOWTO_Authenticate.adoc#authenticate-with-an-regservice-openid-token) beschrieben.

```admonish tip
*VZD-FHIR-Directory* Endpunkte für den Austausch von `RegService-OpenID-Token` gegen ein `owner-accesstoken`:
- TU: https://fhir-directory-test.vzd.ti-dienste.de/owner-authenticate +
- RU: https://fhir-directory-ref.vzd.ti-dienste.de/owner-authenticate +
- PU: https://fhir-directory.vzd.ti-dienste.de/owner-authenticate 
```

# TI-M ePA Besonderheiten

## Bereitstellung eines Messenger-Service für eine Organisation

Der *Registrierungs-Dienst* des *TI-M ePA Fachdienstes* darf nur Usern in der Rolle *Org-Admin* einen *Messenger-Service* bereitstellen, die sich mit einer SM\(C)-B für Kostenträger (professionOID 1.2.276.0.76.4.59) authentisiert haben. 
