# Stammdaten-Struktur (aus Mini_ERP_1.0_Stammdaten_Deutsch.xlsx)

Maschinenlesbare Beschreibung der Excel-Vorlage. Diese Datei ist die Referenz für die
Django-Modelle und den Excel-Import. Die Excel-Datei selbst bleibt als Original in `docs/`,
kann aber vom Chat nicht gelesen werden (Binärformat).

Stand der Vorlage: 19.10.2026. Sie enthält bisher nur Beispieldatensätze (je eine Zeile),
die vor produktiver Nutzung ersetzt oder gelöscht werden.

## Hinweise aus dem Blatt „README“

- Zweck: Vorlage für Produkt- und Kundendaten sowie zentrale Einstellungen, keine lauffähige Anwendung.
- Produktdaten: eine Zeile je Produkt. `Produkt_ID` und `SKU_intern` dauerhaft stabil halten.
- Lagerbestand: in der Anwendung aus Lagerbewegungen berechnen, **nicht** als manuell gepflegtes Feld.
- Währungen: Einkaufspreise in EUR, Verkaufspreise standardmäßig in VND. Kursrichtung und
  Kursdatum immer eindeutig speichern.
- Historie: Der bei einer Rechnung verwendete Wechselkurs und die gespeicherten Preise müssen
  in der Rechnung fixiert werden.
- Sprachen: Deutsch und Vietnamesisch für Produktnamen und Dokumente; UI-Sprache und
  Dokumentensprache getrennt behandeln.
- Kunden: Rechnungs- und Lieferadresse sind getrennte Felder; Zahlungsziel standardmäßig 14 Tage.
- Steuern: Steuerfelder sind Konfiguration, keine steuerliche Beratung. Steuerbehandlung und
  Pflichtangaben vor Produktivbetrieb bestätigen.
- Noch ergänzen als eigene Transaktionstabellen: Lieferungen, Lieferpositionen, Packstücke,
  Rechnungen, Rechnungspositionen, Zahlungen.

## Blatt „Listen“ – Auswahlwerte (Kandidaten für Django-Choices)

| Liste | Werte |
|---|---|
| Produktkategorie | Haushalt, Kosmetik, Spielwaren, Lebensmittel, Elektronik, Kleidung, Sonstiges |
| Einheit | Stück, Packung, Karton, kg, g, Liter, ml, Set |
| JaNein | Ja, Nein |
| Sprache | Deutsch, Tiếng Việt |
| Währung | EUR, VND |
| Bewegungstyp | Wareneingang, Bestandskorrektur, Lieferfreigabe, Transfer Eingang, Transfer Ausgang, Storno |
| Lagerstatus | Aktiv, Inaktiv |
| Kundentyp | Privatkunde, Unternehmen, Sonstiges |
| Kursquelle | Manuell, API, Bank, Sonstige |

Die Felder `Aktiv` nutzen die Liste „JaNein“ und werden in Django zu `BooleanField`.
Sprachwerte werden zu Sprachcodes (`de`, `vi`).

## Blatt „Produkte“ (21 Spalten)

| Spalte | Typ | Hinweis |
|---|---|---|
| Produkt_ID | Text | Eindeutig, stabil. Beispiel: `P0001` |
| SKU_intern | Text | Eindeutig, stabil. Beispiel: `SKU-0001` |
| SKU_Lieferant | Text | Artikelnummer des Lieferanten, Beispiel: `SUP-001` |
| Name_DE | Text | Pflicht |
| Name_VI | Text | Pflicht |
| Kategorie | Liste Produktkategorie | |
| Lieferant_Name | Text | Im Prototyp Freitext; eigene Lieferantentabelle erst später |
| Beschreibung_DE | Text, lang | optional |
| Beschreibung_VI | Text, lang | optional |
| Einheit | Liste Einheit | |
| Einkaufspreis_EUR | Dezimal | 2 Nachkommastellen |
| Verkaufspreis_VND | Ganzzahl | VND kennt keine Nachkommastellen |
| Referenzpreis_EUR_optional | Dezimal | optional |
| Wechselkurs_Referenz_EUR_zu_VND | Dezimal | Kurs, mit dem der Referenzpreis gerechnet wurde |
| Mindestbestand | Ganzzahl | Schwelle für Bestandswarnung |
| Nettogewicht_kg | Dezimal | 3 Nachkommastellen |
| Bruttogewicht_kg | Dezimal | 3 Nachkommastellen |
| Verpackungsinfo | Text | Für die Packliste, Beispiel: „1 Stück pro Karton“ |
| Ursprungsland_optional | Text | optional |
| Aktiv | JaNein | → BooleanField |
| Notizen | Text | optional |

**Kein Bestandsfeld.** Der Bestand wird aus den Lagerbewegungen berechnet.

## Blatt „Kunden“ (23 Spalten)

| Spalte | Typ | Hinweis |
|---|---|---|
| Kunden_ID | Text | Eindeutig. Beispiel: `C0001` |
| Kundennummer | Text | Lesbar, eindeutig. Beispiel: `K-0001` |
| Kundentyp | Liste Kundentyp | |
| Name_Firma | Text | Pflicht |
| Kontaktperson | Text | optional |
| Steuer_ID_optional | Text | optional |
| Rechnung_Strasse, Rechnung_PLZ, Rechnung_Stadt, Rechnung_Provinz, Rechnung_Land | Text | Rechnungsadresse |
| Liefer_Strasse, Liefer_PLZ, Liefer_Stadt, Liefer_Provinz, Liefer_Land | Text | Lieferadresse, getrennt von der Rechnungsadresse |
| E-Mail | Text | optional |
| Telefon | Text | Als Text speichern, führende `+` und Nullen erhalten |
| Zahlungswaehrung | Liste Währung | Standard VND |
| Zahlungsziel_Tage | Ganzzahl | Standard 14 |
| Dokumentensprache | Liste Sprache | |
| Aktiv | JaNein | → BooleanField |
| Interne_Notizen | Text | optional |

Kennzahlen wie letztes Bestelldatum, Anzahl Bestellungen und Umsatz werden **nicht**
gespeichert, sondern aus den Vorgängen berechnet.

## Blatt „Firma“ – Schlüssel/Wert-Einstellungen

| Schlüssel | Vorbelegung | Hinweis |
|---|---|---|
| Firmenname | – | Pflicht; zentriert am Kopf der Dokumente |
| Adresszeile_1 | – | Firmenadresse |
| Adresszeile_2 | – | optional |
| PLZ | – | |
| Stadt | – | |
| Bundesland_Provinz | – | optional |
| Land | Deutschland | Sitz des Unternehmens |
| USt_ID | – | optional, nur wenn anwendbar |
| E-Mail | – | optional |
| Telefon | – | optional |
| Standard_UI_Sprache | Deutsch | unabhängig von der Dokumentensprache |
| Standard_Dokumentsprache | Tiếng Việt | je Kunde/Dokument überschreibbar |
| Standard_Währung_Rechnung | VND | |
| Standard_Zahlungsziel_Tage | 14 | kundenbezogen überschreibbar |

Umsetzung als Modell mit genau einem Datensatz (Singleton), nicht als Schlüssel/Wert-Tabelle.

## Blatt „Wechselkurse“ (9 Spalten)

| Spalte | Typ | Hinweis |
|---|---|---|
| Kurs_ID | Text | Beispiel: `FX-0001` |
| Kursdatum | Datum | Gültigkeitsdatum |
| Basiswaehrung | Liste Währung | EUR |
| Zielwaehrung | Liste Währung | VND |
| Kurs_Basis_zu_Ziel | Dezimal | Beispiel: 28000 bedeutet 1 EUR = 28.000 VND |
| Quelle | Liste Kursquelle | |
| Manuell_gesetzt | JaNein | → BooleanField |
| Abrufzeitpunkt | Datum/Zeit | |
| Notiz | Text | optional |

Die Richtung wird immer explizit über Basis- und Zielwährung gespeichert. Ein später
aktualisierter Kurs darf bereits erstellte Rechnungen nicht verändern.

## Blatt „Lagerorte“ (6 Spalten)

| Spalte | Typ |
|---|---|
| Lager_ID | Text |
| Lagername | Text |
| Land | Text |
| Adresse | Text, optional |
| Lagerstatus | Liste Lagerstatus |
| Notizen | Text, optional |

Vorhandene Datensätze (produktiv zu verwenden, keine Beispieldaten):

| Lager_ID | Lagername | Land | Notiz |
|---|---|---|---|
| W-DE-01 | Hauptlager Deutschland | Deutschland | Ausgangslager |
| W-VN-01 | Zwischenlager Vietnam | Vietnam | Firmeneigenes Zwischenlager |

## Blatt „Lagerbewegungen“ (10 Spalten)

| Spalte | Typ | Hinweis |
|---|---|---|
| Bewegung_ID | Text | Eindeutig. Beispiel: `LM-0001` |
| Datum | Datum | |
| Produkt_ID | Verweis auf Produkte | |
| Lager_ID | Verweis auf Lagerorte | |
| Bewegungstyp | Liste Bewegungstyp | |
| Menge_plus_Minus | Ganzzahl | Vorzeichenbehaftet: Zugang positiv, Abgang negativ |
| Referenztyp | Text | Beispiel: `Initialbestand`, später `Lieferung` |
| Referenz_ID | Text | Beispiel: `INIT-001` |
| Erfasst_von | Text | Beispiel: `Admin` |
| Notiz | Text | optional |

**Wichtiger Hinweis aus der Vorlage:** Jede Bewegung braucht eine eindeutige ID. In der
Anwendung müssen Bewegungen idempotent sein; eine Lieferfreigabe darf keine doppelten
Lagerabgänge erzeugen.

## Umsetzungshinweise für den Import

- Beispieldatensätze (Notiz enthält „Beispieldatensatz“ oder „Beispielbewegung“) beim Import
  überspringen oder kennzeichnen.
- Die Blätter „Produkte“, „Kunden“, „Wechselkurse“ und „Lagerbewegungen“ enthalten viele leere
  Vorratszeilen; Zeilen ohne ID überspringen.
- Der Import muss wiederholbar sein: bestehende Datensätze anhand ihrer ID aktualisieren statt
  doppelt anlegen.
- Geldbeträge als `DecimalField`, nie als Gleitkommazahl.
