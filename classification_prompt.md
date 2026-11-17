# LLM coding prompt (German)

This document contains the original two-part coding prompt accompanying the Job Amenity Wordbook. It was used to instruct the language model to classify explicitly advertised employer benefits and attractive working conditions in German vacancy texts, return supporting verbatim evidence, and identify potentially unassigned benefits.

**Structure:** `prompt1` defines the classification task, cross-category boundaries and individual benefit definitions. The vacancy text is placed after its concluding `STELLENANZEIGE:` label. `prompt2` specifies the JSON output format, including `benefits`, `evidence`, `confidence` and `unassigned_benefit_evidence`.

> **Identifier note.** The prompt preserves the original German category identifiers (e.g. `gehalt`, `remote`, `altersvorsorge`). The published CSV/JSON wordbook uses English `snake_case` identifiers (e.g. `salary`, `remote_work`, `occupational_pension`). These correspond to the same concepts, but the identifiers must be mapped when comparing or merging outputs. The prompt below is reproduced without renaming category identifiers or editing its substantive rules.

## Part 1: Category definitions and coding rules (`prompt1`)

```text
Du bist ein wissenschaftlicher Kodierer für Benefits in deutschen Stellenanzeigen.

AUFGABE
Führe eine Multi-Label-Klassifikation durch. Prüfe JEDE unten definierte Benefit-Kategorie darauf, ob sie im Text als konkrete Leistung, Vorteil oder attraktive Arbeitsbedingung des Arbeitgebers genannt wird.

Mehrere Kategorien können gleichzeitig zutreffen, aber nur wenn der Text die Definition jeder Kategorie eigenständig erfüllt.

ALLGEMEINE KODIERREGELN

1. Ein Benefit zählt nur, wenn er explizit genannt oder semantisch eindeutig zugesagt wird.
2. Keine Vermutungen aus Branche, Unternehmensgröße, Image oder allgemeiner Werbung.
3. Negierte oder ausdrücklich ausgeschlossene Benefits zählen nicht.
4. Bewerberanforderungen sind keine Benefits.
   Beispiele:
   - "Bereitschaft zu flexiblen Arbeitszeiten" -> NICHT flexible_arbeitszeiten
   - "Führerschein und eigener Pkw erforderlich" -> NICHT firmenwagen
   - "Reisebereitschaft erforderlich" -> NICHT mobilitätszuschuss
   - "eigenverantwortliches Arbeiten wird erwartet" -> nur dann autonomie_gestaltungsfreiraum,
     wenn zugleich erkennbar ist, dass der Arbeitgeber tatsächlich entsprechenden Handlungsspielraum bietet.
5. Eine reine Tätigkeitsbeschreibung ist kein Benefit. Eine Arbeitsbedingung darf aber als Benefit zählen,
   wenn sie ausdrücklich positiv angeboten wird.
6. Kategorien dürfen sich überschneiden, aber nicht allein wegen thematischer Nähe.
   Beispiele:
   - "kostenfreie Parkplätze" -> parken UND free_parking
   - "attraktive Vergütung nach TVöD" -> gehalt UND tarifliche_bezahlung
   - "betriebseigene Kita mit Kita-Zuschuss" -> famliy_benefits+care
- "inklusives Arbeitsumfeld mit barrierefreiem Gebäude" -> diversity_inklusion UND barrierefrei
7. Adjektive müssen sich auf den relevanten Gegenstand beziehen:
   - "abwechslungsreiche Aufgaben" -> interessante_aufgaben
   - "abwechslungsreiches Arbeitsumfeld" -> NICHT automatisch interessante_aufgaben
   - "modern ausgestatteter Arbeitsplatz" -> equipment_arbeitsplatz
   - "modernes Arbeitsumfeld" -> NICHT automatisch equipment_arbeitsplatz
8. Für jeden positiven Treffer muss evidence einen möglichst kurzen ORIGINAL-Textschnipsel enthalten.
9. confidence liegt zwischen 0 und 1 und gibt die Sicherheit der Klassifikation an.
10. Prüfe zusätzlich auf konkrete Arbeitgeber-Benefits, die keiner Kategorie zugeordnet werden können.
    Diese kommen in unassigned_benefit_evidence.
    Dort NICHT aufnehmen:
    - bereits klassifizierte Benefits
    - Bewerberanforderungen
    - Tätigkeitsbeschreibungen
    - allgemeine Unternehmenswerbung
    - gesetzliche Mindeststandards
    - Dopplungen
WICHTIGE ABGRENZUNGEN

A) familie vs. unternehmenskultur_atmosphäre
- familie nur bei echter Familienfreundlichkeit/Vereinbarkeit für Beschäftigte:
  z.B. "familienfreundlicher Arbeitgeber", "berufundfamilie-zertifiziert",
  "familienbewusst", "familiengerechtes Arbeiten",
  "elternunterstützende Arbeitskonditionen", "Rahmenbedingungen für Eltern".
- NICHT familie bei "familiäre Atmosphäre", "familiäres Betriebsklima",
  "familiäres Arbeitsumfeld", "familiäre Unternehmenskultur",
  "familiäres Team" oder "Familienunternehmen".
  Solche Aussagen gehören ggf. zu unternehmenskultur_atmosphäre.

B) team vs. unternehmenskultur_atmosphäre vs. events
- team: positive Aussage über konkrete Kollegen/Team, z.B. "motiviertes Team",
  "kompetente Kollegen", "engagiertes Team", "unterstützende Kollegen".
- unternehmenskultur_atmosphäre: "kollegiales Arbeitsumfeld",
  "kollegiale Atmosphäre", "kollegiales Miteinander",
  "familiäres Betriebsklima", "wertschätzende Kultur".
- events: Teamaktivitäten, Feiern, Betriebsausflüge usw.
- "Teamplayer gesucht", "Teil unseres Teams", "Teamwork" allein -> NICHT team.

C) interessante_aufgaben
- Positiv bei eindeutigem Bezug auf Arbeitsinhalt:
  "abwechslungsreiche Tätigkeit", "vielseitige Aufgaben",
  "spannende Projekte", "anspruchsvolles Aufgabengebiet",
  "verantwortungsvoller Aufgabenbereich".
- NICHT automatisch bei:
  "interessanter Arbeitsplatz", "abwechslungsreiches Arbeitsumfeld",
  "spannende Einblicke", "innovative Projekte" oder "spannende Herausforderung"
  ohne eindeutigen Bezug auf den eigenen Arbeitsinhalt.

D) autonomie_gestaltungsfreiraum
- Erfordert echten eigenen Handlungs-/Entscheidungsspielraum:
  "Eigenverantwortlichkeit", "selbstständige Tätigkeit",
  "hohes Maß an Selbständigkeit", "Entscheidungsspielraum",
  "viel Freiraum", "eigene Ideen umsetzen", "Tag selbst planen".
- "Gestaltungs- und Entwicklungsmöglichkeiten", "Meinung einbringen" oder
  "Ideen einbringen" allein reichen NICHT, wenn kein tatsächlicher eigener
  Entscheidungs-/Gestaltungsspielraum erkennbar ist.

E) fortbildung vs. weiterentwicklung vs. aufstieg vs. coaching
- fortbildung = konkretes Lernangebot: Kurs, Schulung, Seminar, Training,
  Weiterbildung, Zertifizierung, Lernplattform, Akademie.
- weiterentwicklung = allgemeines persönliches/fachliches Wachstum:
  Entwicklungsmöglichkeiten, Personalentwicklung, Potenzialentwicklung,
  persönliche/fachliche Entwicklung.
- aufstieg = konkrete Karrierebewegung:
  Karriereweg/-pfad, Aufstieg, Beförderung, Führungslaufbahn,
  interne Karriere, Übernahme in höherwertige Position.
- coaching = persönliche laufende Begleitung:
  Mentoring, Coaching, Supervision, Patenmodell, regelmäßige Feedback-/
  Entwicklungsgespräche.
- Ein konkretes Training ist NICHT automatisch weiterentwicklung.
- Eine Entwicklungsmöglichkeit ist NICHT automatisch aufstieg.
- "Karriere- und Weiterbildungsmöglichkeiten" kann aufstieg UND fortbildung sein,
  aber nicht automatisch weiterentwicklung.
- "Praxisanleitung" oder reine Unterstützung beim Einstieg -> eher einarbeitung,
  nicht coaching, sofern keine längerfristige persönliche Begleitung beschrieben wird.

F) Arbeitszeit-Kategorien
- flexible_arbeitszeiten = Beschäftigte können Lage/Gestaltung beeinflussen:
  Gleitzeit, Vertrauensarbeitszeit, flexible Dienstplanung, Zeitkonto,
  individuelle Arbeitszeitgestaltung.
- planbare_geregelte_arbeitszeiten = Arbeitszeiten sind vorhersehbar/fest:
  geregelte Arbeitszeitmodelle, planbare Schichtsysteme, stabile Dienstplanung,
  gut planbare Dienstzeiten, im Voraus geplante Einsätze, geregelte Kernarbeitszeit.
- weniger_als_40h = explizit regulär unter 40 Wochenstunden:
  z.B. 20h, 30h, 35h, 37h, 38h oder 39h.
- 38/39 Stunden -> weniger_als_40h, NICHT allein planbare_geregelte_arbeitszeiten.
- Zeiterfassung oder elektronischer Dienstplan allein -> NICHT automatisch planbar.
- Zeitkonto allein -> flexible_arbeitszeiten, NICHT automatisch planbar.
- work_life_balance = ausdrücklich Vereinbarkeit Beruf/Privatleben oder konkrete
  Entlastung wie arbeitsfreie Wochenenden, keine Sonn-/Feiertagsarbeit,
  geringe Reisetätigkeit oder freie Tage bleiben frei.
- Freiraum/Selbstorganisation allein -> autonomie_gestaltungsfreiraum,
  NICHT automatisch work_life_balance.

G) Vergütungs-Kategorien
- gehalt = positive Aussage über reguläre Vergütung.
- übertarifliche_bezahlung = ausdrücklich übertariflich/außertariflich/
  überdurchschnittlich bezahlt.
- tarifliche_bezahlung = Vergütung nach Tarif/Entgeltordnung.
- bonus = variabel, leistungs-, erfolgs-, umsatz-, ziel- oder empfehlungsabhängig.
- extra_salary = feste/ereignisbezogene Zusatzleistung, z.B. Weihnachtsgeld,
  Urlaubsgeld, 13. Gehalt, Jahressonderzahlung, Jubiläumszahlung,
  Mitarbeiteraktienprogramm.
- "variable Vergütung", Provision, Erfolgsbeteiligung -> bonus.
- Jubiläumsprämie/-gratifikation -> extra_salary, nicht automatisch bonus.
- Eine positive reguläre Vergütung kann zusätzlich gehalt sein, wenn dieselbe
  Formulierung die reguläre Bezahlung eindeutig positiv bewertet.

H) Arbeitsplatz/Ausstattung
- equipment_arbeitsplatz nur bei konkreter Ausstattung/Infrastruktur:
  ergonomische Arbeitsplatzgestaltung, modern ausgestattete Arbeitsplätze,
  hochwertige Arbeitsmittel, moderne technische Infrastruktur,
  höhenverstellbare Schreibtische, moderne Maschinen/Geräte.
- "modernes Arbeitsumfeld", "moderne Arbeitsbedingungen",
  "modernes Unternehmen" oder "cooles Büro" allein reichen NICHT.
- Laptop/Notebook/Smartphone/Tablet allein -> laptop+firmenhandy.
- "freie Wahl der Arbeitswerkzeuge" -> eher autonomie_gestaltungsfreiraum;
  equipment_arbeitsplatz nur, wenn der Arbeitgeber die konkrete hochwertige
  Ausstattung tatsächlich bereitstellt.

I) Mobilität
- konkretes Job-/Deutschland-/Firmen-/ÖPNV-Ticket, auch bezuschusst ->
  jobticket.
- allgemeiner Fahrtkosten-/Mobilitätszuschuss, Fahrgeld, Reisekostenerstattung,
  Mobilitätsbudget -> mobilitätszuschuss.
- Zuschuss zum Deutschlandticket -> jobticket, nicht zusätzlich
  mobilitätszuschuss, sofern keine allgemeine Mobilitätsleistung genannt wird.

J) Sinn
- sinnstiftende_arbeit nur bei explizitem Sinn/Impact:
  "sinnstiftend", "gesellschaftlich relevant", "gesellschaftlicher Beitrag",
  "positiver Impact", "Beitrag zum Umweltschutz", "etwas bewirken".
- Formulierungen wie "etwas bewegen", "Ihr Beitrag zählt" oder "High Impact"
  zählen nur, wenn eindeutig der gesellschaftliche/menschliche/ökologische
  Effekt der Tätigkeit gemeint ist; reine Werbeslogans reichen nicht.

BENEFIT-KATEGORIEN

MONETÄRE BENEFITS

gehalt:
Positive Aussage über reguläre Vergütung, z.B. attraktives, faires, gutes,
angemessenes, markt-/branchengerechtes, wettbewerbsfähiges oder
leistungsgerechtes Gehalt/Entgelt/Einkommen; auch attraktives Fest-/Fixgehalt
oder attraktive Verdienstmöglichkeiten.
NICHT bei neutraler Erwähnung von Gehalt/Vergütung.
Variable erfolgsabhängige Vergütung -> bonus.

übertarifliche_bezahlung:
Explizit übertarifliche, außertarifliche oder überdurchschnittliche Bezahlung,
z.B. "über Tarif", "überdurchschnittliches Entgelt",
"überdurchschnittlich hohe Ausbildungsvergütung", "übertarifliches Fixum".
NICHT übertariflicher Urlaub oder außertarifliche Zusatzleistungen ohne Bezug zur Bezahlung.

tarifliche_bezahlung:
Vergütung nach Tarifvertrag/tariflicher Entgeltordnung, z.B. TVöD, TV-L, AVR,
IG Metall, BAP/DGB, iGZ, VKA, Haustarif, Chemietarif, Tariflohn,
Entgelt-/Vergütungsgruppe oder Besoldungsgruppe.
Tarifbezug muss die Vergütung/Entgeltbedingungen betreffen.

extra_salary:
Zusätzliche feste oder ereignisbezogene materielle Vergütung:
13./14. Gehalt, Weihnachts-/Urlaubsgeld, Jahressonderzahlung,
garantierter Einmalbetrag, Jubiläumszahlung/-gratifikation,
Sachbezug/Guthabenkarte, Mitarbeiteraktien-/Belegschaftsaktienprogramm.
NICHT leistungs-/erfolgsabhängig -> bonus.

bonus:
Variable, leistungs-, erfolgs-, umsatz-, ziel- oder empfehlungsabhängige Vergütung:
Bonus, Provision, Erfolgs-/Gewinnbeteiligung, Tantieme, Leistungsprämie,
Teamprämie, Empfehlungsbonus, Einstiegs-/Wechselbonus, Profit Sharing.
NICHT feste Weihnachts-/Urlaubs-/Jahressonderzahlung.

zulagen:
Explizite Zuschläge/Zulagen, z.B. Nacht-, Schicht-, Sonntags-, Feiertags-,
Branchen-, Ballungsraum-, Funktions-, Rufbereitschafts- oder Pflegezulagen.

vwl:
Vermögenswirksame Leistungen (VWL/VL), vermögenswirksames Sparen.
NICHT Altersvorsorge oder allgemeine Entgeltumwandlung zur Altersvorsorge.

sozialleistungen:
Explizit allgemeine Sozial-/Zusatzleistungen, Beihilfen oder Sozialkataloge.
Spezifische Benefits zusätzlich spezifisch kodieren.
Nicht jede beliebige konkrete Zusatzleistung automatisch auch hier kodieren.

altersvorsorge:
Betriebliche/zusätzliche Altersvorsorge oder Altersversorgung:
bAV, Betriebs-/Zusatz-/Firmenrente, Pensionskasse, Metallrente,
Rentenplan, Rentenbaustein, Entgeltumwandlung zur Altersvorsorge,
Arbeitgeberzuschuss zur Altersvorsorge.
"Rente" allein nur bei eindeutigem Arbeitgeber-Benefit-Kontext.

verträge_versicherungen:
Vom Arbeitgeber angebotene/bezuschusste Versicherungen:
betriebliche Krankenversicherung, Unfall-, Zusatz-, Berufsunfähigkeits-,
Lebens-/Erwerbsunfähigkeitsversicherung oder Mitarbeiter-Versicherungspakete.
Altersvorsorge/Rentenversicherung zusätzlich altersvorsorge, wenn entsprechend gemeint.

MOBILITÄT

jobticket:
Vom Arbeitgeber angebotenes, bezuschusstes, vergünstigtes oder kostenloses
ÖPNV-/Bahn-Ticket: Jobticket, Deutschlandticket, Firmenticket, Profiticket,
Monatsfahrkarte, HVV-/BVG-/IsarCard-Jobticket usw.
Allgemeine Fahrtkostenerstattung -> mobilitätszuschuss.

mobilitätszuschuss:
Allgemeine finanzielle/materielle Unterstützung von Mobilität/Arbeitsweg:
Fahrtkostenzuschuss/-erstattung, Fahrgeld, Mobilitätsbudget/-zuschuss,
Reisekostenerstattung, Tankgutschein, BahnCard/Freifahrten,
sofern es kein spezifisches Jobticket ist.

fahrrad:
Jobrad, Dienstrad, Firmenrad, BusinessBike, Bike-/Radleasing,
Fahrradleasingprogramm, Jobräder oder vergleichbares Arbeitgeber-Fahrradmodell.

firmenwagen:
Firmen-/Dienstwagen oder Firmen-/Dienstfahrzeug, ggf. Privatnutzung.
NICHT privater Pkw als Voraussetzung oder bloße Fahrzeugnutzung zur Aufgabenerfüllung.

verkehrsanbindung:
Vorteilhafte Lage/Erreichbarkeit des Arbeitsplatzes:
gute ÖPNV-/Verkehrs-/Autobahnanbindung, Bahnhofsnähe, zentrale Lage.
Muss sich auf den Arbeitsplatzstandort beziehen.

parken:
Zur Verfügung stehende Parkplätze, Mitarbeiterparkplätze, Parkhaus/Tiefgarage,
Stellplätze oder Parkmöglichkeiten.
Kostenlose Parkplätze zusätzlich free_parking.

free_parking:
Explizit kostenlose/kostenfreie Parkplätze, Tiefgarage oder Parkmöglichkeiten.
Wenn free_parking zutrifft, normalerweise auch parken.

ARBEITSPLATZ UND AUSSTATTUNG

equipment_arbeitsplatz:
Konkrete qualitativ gute, moderne oder ergonomische Arbeitsplatz-/Betriebsausstattung:
ergonomische Arbeitsplätze/Stühle, höhenverstellbare Schreibtische,
modern ausgestattete Arbeitsplätze/Büros/Labore, hochwertige Arbeitsmittel,
moderne Maschinen/Geräte, technische Infrastruktur.
NICHT nur "modernes Arbeitsumfeld", "moderne Arbeitsbedingungen",
"moderner Arbeitsplatz" ohne konkreten Ausstattungsbezug oder
"innovatives Unternehmen".

laptop+firmenhandy:
Vom Arbeitgeber bereitgestellte digitale Endgeräte:
Laptop, Notebook, MacBook, Firmen-/Diensthandy, Smartphone, iPhone,
Mobiltelefon, Tablet oder iPad.
"Notebook und Smartphone" -> diese eine Kategorie.
NICHT eigene/private Geräte als Voraussetzung.

schutzausrüstung:
Vom Arbeitgeber gestellte Arbeits-, Berufs-, Dienst- oder Schutzkleidung
bzw. persönliche Schutzausrüstung; auch kostenlose/hochwertige Arbeitskleidung.
NICHT bloße Kleidungsvorschriften.

ESSEN UND VERPFLEGUNG

drinks:
Vom Arbeitgeber bereitgestellte Getränke:
Wasser, Kaffee, Tee, Softdrinks, Heißgetränke, Getränkeflatrate/-angebot,
Wasserspender, Kaffeeautomat usw.

snacks:
Vom Arbeitgeber bereitgestellte Snacks, Obst/Obstkorb, Gemüse, Müsli,
Nüsse, Kuchen oder vergleichbare Zwischenverpflegung.
Kiosk am Standort allein zählt nicht.

food_support_kantine:
Betriebliche Verpflegung oder finanzielle Essensunterstützung:
Kantine/Mitarbeiterrestaurant/Bistro, Essenszuschuss/-gutschein,
bezuschusste/kostenlose Mahlzeiten, Lunch-/Meal-Allowance.
Gemeinsamer Teamlunch nur, wenn vom Arbeitgeber angeboten/finanziert;
sonst ggf. events.

rabatte:
Mitarbeiter-/Personalrabatte, Corporate Benefits, Einkaufsvorteile,
Mitarbeiterangebote, Sonderkonditionen, Shoppingplattformen oder vergleichbare Vergünstigungen.
Spezifische Essens-/Mobilitäts-/Fitnessangebote zusätzlich spezifisch zuordnen.

GESUNDHEIT UND FREIZEIT

gesundheitsmanagement:
Konkrete betriebliche Gesundheitsangebote:
Gesundheitsmanagement/-förderung, Vorsorgeuntersuchungen, Impfungen,
Betriebsarzt, Gesundheitschecks, Massagen, Präventionsprogramme,
arbeitsmedizinische Vorsorge, psychologische Unterstützungsangebote.
Reine Sport-/Fitnessangebote -> sportfreizeit_angebot.

sportfreizeit_angebot:
Vom Arbeitgeber angebotene/bezuschusste Sport-, Fitness- oder Freizeitangebote:
Betriebssport, Fitnessstudio, Wellpass, Qualitrain, EGYM/Wellhub,
Sport-/Yoga-/Fitnesskurse, Firmenfitness, Sportzuschuss.
Firmenlauf/Sportevent nur hier, wenn primär Sportangebot; events nur zusätzlich,
wenn es ausdrücklich als soziales Firmenevent beschrieben wird.

ARBEITSZEIT UND ARBEITSORT

flexible_arbeitszeiten:
Beschäftigte haben Spielraum bei Lage/Gestaltung ihrer Arbeitszeit:
Gleitzeit, Vertrauensarbeitszeit, flexible Dienstplanung,
Arbeitszeit-/Jahresarbeitszeitkonto, individuelle Arbeitszeitgestaltung,
flexible Start-/Endzeiten.
NICHT bloß geregelte oder planbare Arbeitszeiten.

planbare_geregelte_arbeitszeiten:
Arbeitszeiten/Dienstpläne ausdrücklich regelmäßig, vorhersehbar oder
langfristig planbar: geregelte Arbeitszeitmodelle, feste/geregelte Kernarbeitszeit,
stabile Dienstplanung, planbare Schichtsysteme, gut planbare Dienstzeiten,
im Voraus geplante Arbeitseinsätze.
"nach Dienstplan" zählt nur, wenn es als angebotene geregelte Arbeitsbedingung
und nicht bloß als organisatorische Beschreibung erscheint.
Zeiterfassung/Zeitkonto allein zählt nicht.

weniger_als_40h:
Explizit angebotene reguläre Wochenarbeitszeit unter 40 Stunden,
z.B. 20, 30, 35, 37, 38 oder 39 Wochenstunden.
NICHT allein Teilzeit. 40 Stunden zählen nicht.

teilzeit_möglich:
Teilzeit wird ausdrücklich angeboten:
Voll-/Teilzeit, Teilzeitoption/-möglichkeit, Teilzeitvertrag,
Teilzeitmodelle, Jobsharing, individuelle Teilzeitlösungen.

remote:
Homeoffice, Heimarbeit, mobiles/hybrides Arbeiten, Remote Work,
Telearbeit oder ortsunabhängiges Arbeiten als angebotene Möglichkeit.

work_life_balance:
Explizite Work-Life-Balance oder konkrete Vereinbarkeit von Beruf und Privatleben.
Auch klare Entlastungen wie arbeitsfreie Wochenenden, keine Sonn-/Feiertagsarbeit,
überwiegend tägliche Heimkehr, geringe Reisetätigkeit oder ausdrücklich freie Tage.
NICHT allein flexible/planbare Arbeitszeit, Teilzeit, familiäres Arbeitsklima
oder allgemeiner Freiraum/Selbstorganisation.

30_urlaub:
Mindestens 30 Urlaubstage pro Jahr explizit angeboten.
Auch 31/36/40/45 Tage oder mindestens 6 Wochen Urlaub.
"5 Wochen Urlaub" zählt NICHT automatisch, da dies bei 5-Tage-Woche 25 Tage sein kann.
Unter 30 Tage zählen nicht.

sonderurlaub:
Zusätzliche freie Tage außerhalb des regulären Urlaubs:
Sonder-/Zusatzurlaub, zusätzliche freie Tage, Heiligabend/Silvester frei,
Regenerationstage, Haushaltstage, Brauchtumstage, Exerzitientage,
Geburtstag frei, Sabbatical/persönliche Auszeit, wenn zusätzlich zum normalen Urlaub.
Normale 6 Wochen Urlaub -> 30_urlaub, nicht automatisch sonderurlaub.

FAMILIE

familie:
Allgemeine Familienfreundlichkeit/Vereinbarkeit von Familie und Beruf:
familienfreundlicher/-bewusster Arbeitgeber, berufundfamilie-Zertifizierung,
familiengerechtes Arbeiten, familienorientiertes Unternehmen,
elternunterstützende Arbeitskonditionen, Rahmenbedingungen für Eltern.
NICHT "familiäres Arbeitsklima/Arbeitsumfeld/Betriebsklima/Team",
"familiäre Atmosphäre/Unternehmenskultur" oder "Familienunternehmen".

famliy_benefits+care:
Konkrete finanzielle, organisatorische oder infrastrukturelle Unterstützung
für Beschäftigte mit Kindern/pflegebedürftigen Angehörigen:
Kita-/Kindergarten-/Kinderbetreuungszuschuss, Familienservice/-budget,
Kinderzulage, zusätzliche Entgeltfortzahlung bei Krankheit des Kindes,
Betriebskita/-kindergarten, reservierte Betreuungsplätze,
Kinder-/Ferien-/Notfallbetreuung, Eltern-Kind-Büro,
Unterstützung bei Kita-/Betreuungsplatzsuche oder konkrete Pflegeunterstützung.
Bloße allgemeine Familienfreundlichkeit -> familie.

ENTWICKLUNG UND KARRIERE

fortbildung:
Konkrete Trainings-/Bildungsangebote:
Weiterbildung, Fortbildung, Schulung, Seminar, Training, Kurs,
E-Learning, Zertifizierung, Qualifizierung, Lernplattform,
Arbeitgeber-Akademie oder Weiterbildungsermächtigung.

weiterentwicklung:
Allgemeine fachliche/persönliche Entwicklung:
Weiterentwicklungsmöglichkeiten/-chancen, persönliche/fachliche Entwicklung,
Personal-/Potenzialentwicklung, Entwicklungsprogramme/-pläne.
NICHT automatisch konkrete Trainings -> fortbildung.
NICHT automatisch Karriere-/Beförderung -> aufstieg.
Sehr allgemeines "Entwicklung" ohne Bezug zur Entwicklung der Beschäftigten reicht nicht.

aufstieg:
Konkrete Karriere-/Aufstiegsperspektiven:
Aufstiegschancen, Karrierechancen/-wege/-pfade, interne Karriere,
Führungs-/Managementlaufbahn, Beförderung, Karriereplanung,
Übernahme nach Ausbildung oder konkrete Perspektive auf höherwertige Position.
Allgemeine "Entwicklungsperspektive"/"Zukunftsperspektive" zählt nur,
wenn ein Karriere-/Positionsbezug erkennbar ist.

coaching:
Persönliche laufende Begleitung:
Coaching, Mentoring-/Mentorenprogramm, Paten-/Buddy-System,
Supervision, regelmäßige Feedback-/Entwicklungsgespräche,
intensive persönliche/fachliche Begleitung.
"vertrauensvolle Betreuung" allein reicht nicht.
Reine Einarbeitung/Praxisanleitung -> einarbeitung.

einarbeitung:
Strukturierte/unterstützte Einführung:
Einarbeitung, Onboarding/on boarding, Einlernphase,
Einarbeitungsplan/-konzept, Einführungsprogramm/-tage,
systematischer Onboardingprozess, Unterstützung beim Berufseinstieg.
Mentor/Buddy kann zusätzlich coaching sein, wenn persönliche Begleitung angeboten wird.

ARBEITSINHALT UND AUTONOMIE

interessante_aufgaben:
Aufgaben/Tätigkeiten ausdrücklich interessant, spannend, abwechslungsreich,
vielseitig, anspruchsvoll, herausfordernd oder verantwortungsvoll.
Auch entsprechend bezeichnete Projekte/Aufgabenfelder.
Der positive Begriff muss sich auf den Arbeitsinhalt beziehen.
NICHT interessantes Unternehmen, spannende Branche, bloß innovatives Projekt,
interessanter Arbeitsplatz oder abwechslungsreiches Arbeitsumfeld.

autonomie_gestaltungsfreiraum:
Echte eigene Entscheidungs-, Handlungs- oder Gestaltungsspielräume:
Eigenverantwortlichkeit, selbstständiges/eigenständiges Arbeiten,
Autonomie, Entscheidungs-/Handlungsspielraum, selbstbestimmter Arbeitsalltag,
eigene Projekte, eigene Ideen umsetzen, Freiraum für Eigeninitiative,
Tag/Arbeit selbst organisieren.
NICHT allein flache Hierarchien.
NICHT bloß verantwortungsvolle Aufgabe.
Allgemeine "Mitgestaltung", "Ideen einbringen" oder "Meinung einbringen"
nur dann, wenn daraus tatsächlicher eigener Gestaltungsspielraum hervorgeht.

sinnstiftende_arbeit:
Tätigkeit ausdrücklich sinnvoll, sinnstiftend oder gesellschaftlich wirksam:
Job/Tätigkeit mit Sinn, gesellschaftlich relevante Arbeit,
gesellschaftlicher/menschlicher/ökologischer Beitrag,
positiver Impact, Beitrag zum Umweltschutz,
etwas konkret bewirken.
Reine Werbeslogans oder nur interessante/verantwortungsvolle Aufgaben zählen nicht.

ORGANISATION, TEAM UND KULTUR

flach_offen:
Flache Hierarchien, kurze Entscheidungs-/Kommunikations-/Abstimmungswege,
direkte Kommunikation mit Führungskräften oder offene Türen.
"offene Unternehmenskultur", "transparente Kommunikation" oder
"Zusammenarbeit auf Augenhöhe" allein eher unternehmenskultur_atmosphäre,
wenn kein Struktur-/Hierarchiebezug erkennbar ist.

unternehmenskultur_atmosphäre:
Explizit positive Arbeits-/Unternehmenskultur oder Atmosphäre:
gutes Arbeitsklima, wertschätzende/respektvolle/vertrauensvolle Kultur,
familiäre Atmosphäre, familiäres Betriebsklima/Arbeitsumfeld,
kollegiales Arbeitsumfeld, kollegiales Miteinander,
offene positive Unternehmenskultur.
NICHT bloße neutrale Erwähnung von "Unternehmenskultur".
NICHT automatisch team oder flach_offen.

team:
Team/Kollegen ausdrücklich positiv als konkrete Personen/Gruppe:
motiviertes, erfahrenes, kompetentes, sympathisches, engagiertes,
unterstützendes Team oder entsprechende Kollegen/Kolleginnen.
NICHT bloß "unser Team", "Teil des Teams", "Teamplayer gesucht",
"Teamwork", "Wir-Gefühl", "kollegiales Umfeld/Miteinander"
oder Teamaktivitäten.

diversity_inklusion:
Explizites Arbeitgeberversprechen zu Diversity, Vielfalt, Inklusion,
Chancengleichheit oder Gleichberechtigung:
Diversity & Inclusion, Charta der Vielfalt, inklusives Arbeitsumfeld,
diversitätsbewusstes Umfeld, Equal Opportunity Employer,
Vielfalt wird geschätzt/gefördert oder vergleichbare explizite Aussagen.
NICHT automatisch bei bloßer Internationalität, einem "bunt gemischten Team"
ohne Diversity-Versprechen oder einer gesetzlichen Standardformulierung wie
"Bewerbungen schwerbehinderter Menschen sind willkommen/bevorzugt".
Tatsächliche räumliche/technische Barrierefreiheit -> barrierefrei.

barrierefrei:
Tatsächliche Barrierefreiheit oder konkrete Accessibility-Unterstützung
am Arbeitsplatz: barrierefreier/behindertengerechter Arbeitsplatz,
barrierefreies Gebäude/Zugang, rollstuhlgerechte Räumlichkeiten,
konkrete technische oder organisatorische Accommodations.
NICHT automatisch bei Diversity/Inklusion, Gleichstellungsversprechen oder
der bloßen Einladung/Bevorzugung von Bewerbungen schwerbehinderter Menschen.


international:
Tatsächlich internationales Arbeitsumfeld oder konkrete internationale Arbeit:
internationales/interkulturelles Team, internationale Zusammenarbeit,
länderübergreifende Projekte, Arbeit im internationalen Kontext,
internationale Projektteams, internationale Forschungsnetzwerke,
Auslandseinsätze oder internationale Austauschprogramme.
NICHT allein, weil das Unternehmen international/global tätig ist,
internationale Standorte besitzt, Auslandskunden hat oder sich als
"internationales Unternehmen/Konzern" beschreibt.

mitbestimmung:
Formalisierte Arbeitnehmervertretung oder betriebliche Mitbestimmung:
Betriebsrat, Personalrat, Mitarbeitervertretung, Interessenvertretung,
Works Council oder ausdrücklich institutionalisierte Mitbestimmungsrechte.
NICHT allgemeine Mitgestaltung, Mitsprache, eigene Ideen,
Gestaltungsspielraum oder "Meinung einbringen" -> autonomie_gestaltungsfreiraum.

SICHERHEIT

unbefristeten_arbeitsvertrag:
Explizit unbefristete Beschäftigung, Festanstellung oder
unbefristeter Arbeits-/Anstellungs-/Dienstvertrag.
NICHT allein langfristige Perspektive oder sicherer Arbeitsplatz.

job_sicherheit:
Explizite Arbeitsplatz-/Beschäftigungssicherheit:
sicherer, krisensicherer, krisenfester, zukunftssicherer,
stabiler Arbeitsplatz/Arbeitgeber oder eindeutig langfristige Beschäftigungsperspektive.
NICHT bloß aufgrund von Wachstum, Größe, Finanzstärke oder Unternehmenserfolg inferieren.
Unbefristeter Vertrag zusätzlich nur dann job_sicherheit,
wenn Sicherheit/Langfristigkeit ausdrücklich kommuniziert wird.

VERANSTALTUNGEN

events:
Vom Arbeitgeber organisierte soziale Veranstaltungen:
Team-/Firmenevents, Sommerfest, Weihnachtsfeier, Betriebsausflug,
Mitarbeiterfeier, Teambuilding-Veranstaltung, Stammtisch,
Azubi-Event, gemeinsame Freizeitaktivität.
Reine Teilnahme an Sportveranstaltung/Firmenlauf -> eher sportfreizeit_angebot.


WOHNEN UND UMZUG

wohnungs_umzugsunterstützung:
Konkrete Unterstützung bei Wohnen oder Umzug:
Hilfe bei Wohnungssuche/Wohnraumbeschaffung, Wohnungsvermittlung,
Personal-/Mitarbeiter-/Dienstwohnung, Personalwohnheim,
vorübergehende Wohnmöglichkeit, vergünstigte Apartments,
Umzugskostenbeteiligung oder Relocation Service.
NICHT bloß attraktive Wohnlage, Ferienwohnungen als Freizeitangebot
oder allgemeine Standortwerbung.

STELLENANZEIGE:
```

## Part 2: Required output (`prompt2`)

```text
AUFGABE FÜR DIE AUSGABE

Prüfe intern JEDE oben definierte Kategorie.

Gib im JSON unter "benefits" aber NUR Kategorien aus, für die du einen Benefit
sicher oder hinreichend eindeutig erkannt hast.

Für jede ausgegebene Kategorie:
- Verwende exakt den oben definierten Kategorienamen als Key.
- "evidence" = möglichst kurzer ORIGINAL-Textschnipsel aus der Stellenanzeige.
- "confidence" = Sicherheit zwischen 0 und 1.
- Keine Kategorie ohne positive Evidenz ausgeben.
- Keine zusätzlichen Kategorienamen erfinden.
- Wenn eine Formulierung zwischen zwei Kategorien unklar ist, verwende die
  spezifischere Definition und gib nur Kategorien aus, deren Kriterien wirklich erfüllt sind.

WICHTIG:
Das Fehlen einer Kategorie im JSON bedeutet value = 0.
Du musst Kategorien mit value = 0 NICHT ausgeben.

Prüfe außerdem auf konkrete Benefits, die keiner bestehenden Kategorie zugeordnet werden können.
Gib ausschließlich valides JSON in exakt dieser Grundstruktur zurück:

{
  "benefits": {
    "kategoriename": {
      "evidence": "Originaltext",
      "confidence": 0.95
    }
  },
  "unassigned_benefit_evidence": [
    {
      "evidence": "Originaltext",
      "reason": "kurze Begründung",
      "possible_new_category": "sinnvoller Kategoriename"
    }
  ]
}

Wenn kein Benefit erkannt wird:
"benefits": {}

Wenn keine unzugeordneten Benefits vorhanden sind:
"unassigned_benefit_evidence": []

Kein Markdown.
Keine Kommentare.
Keine Erklärung außerhalb des JSON.
```
