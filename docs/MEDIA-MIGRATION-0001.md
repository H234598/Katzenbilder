# Medienmigration Wirtelprimpf-0001

## Ergebnis

Die historische Medienmigration von `Wirtelprimpf-0001` in GitHub Releases ist
am 1. August 2026 vollständig abgeschlossen worden. Alle Originale, Webderivate,
Shardmanifeste und reproduzierbaren Originalpakete wurden über ihre öffentlichen,
unauthentifizierten Release-Adressen zurückgeladen und gegen die lokalen
SHA-256-Werte geprüft.

Der aktuelle Git-Baum ist danach absichtlich bildfrei. Storytexte, Einzelteile,
Prompts, Archivmetadaten und das vollständige `media-manifest.json` bleiben in
`main`. Die vorhandene Historie wurde weder umgeschrieben noch mit einem
Force-Push ersetzt.

## Unveränderlicher Umfang

| Merkmal | Wert |
|---|---:|
| Originalbilder | 779 |
| Originalbytes | 3.654.670.091 |
| Storymedien | 440 |
| Classic-Medien | 339 |
| Legacy-Medien | 0 |
| Release-Shards | 4 |
| Release-Assets insgesamt | 2.345 |
| Release-Assetbytes insgesamt | 7.447.521.077 |
| Gesamtmanifest SHA-256 | `7dd13e053ba5c151814cdd24f68e4d8228e007b61b6d0b218859ed9d66ff9ef8` |

19 der 779 Originale stammten aus dem erhaltenen lokalen Publikationsvorsprung
und waren noch nicht Teil des damaligen Remote-Baums. Ihre 19 Promptdateien und
18 Story-Einzelteile wurden byteidentisch ergänzt. Die 19 Bilddateien wurden
nicht nach Git kopiert, sondern ebenso wie der übrige Bestand direkt in die
verifizierten Releases aufgenommen.

## Release-Inventar

| Release-Tag | Originale | Assets | Originalbytes | Release-Bytes | Originalpaket SHA-256 | Shardmanifest SHA-256 |
|---|---:|---:|---:|---:|---|---|
| `archive-0001-media-0001` | 250 | 752 | 1.180.733.946 | 2.405.046.934 | `460345fe23580956770670ba321dc1136a3002c7be03bae64c635c954410d59f` | `d4c114f2c32d3173a7abffa7ec295e4831855f0f2f1622a32cd4a57e39faa5b5` |
| `archive-0001-media-0002` | 250 | 752 | 1.116.361.685 | 2.273.347.261 | `38730800c896ade2b4d05c8f7c15e8fc054d7f4940c0080d59413a4f94324948` | `02226f92992f05969c4de3fe757f0a17b6d5fb4ee92dee94e246047a403fc796` |
| `archive-0001-media-0003` | 250 | 752 | 1.210.509.571 | 2.469.093.940 | `f53589e93b5e85747a899b27665882bdb7e41609ea004b5ce2009ef7ca6c460c` | `34d2e27aea48be8d34344ede07a365bd1a19408f9d8e8e573a757d3a07c135e9` |
| `archive-0001-media-0004` | 29 | 89 | 147.064.889 | 300.032.942 | `7f13383e418e3059c76bcb2f985cd21172ac07108d39fef411e777b2338922c3` | `115522c06ee744a62f1b84d4aa91d3147b687638da677a6f9120c08189a62143` |
| **Gesamt** | **779** | **2.345** | **3.654.670.091** | **7.447.521.077** |  |  |

Jeder Datensatz enthält das Original und WebP-Derivate für 640 und 1.280 Pixel
Breite. Jeder Shard enthält zusätzlich sein JSON-Manifest und ein
deterministisch erzeugtes ZIP sämtlicher Originale.

## Öffentlicher Prüfnachweis

Der idempotente Abschlusslauf meldete:

```text
publication_complete expected=2345 uploaded=1244 reused=1101 verified=2345
```

`reused` bedeutet dabei nicht ungeprüft übernommen. Jedes bereits vorhandene
Asset wurde erneut über den öffentlichen Downloadpfad geladen und lokal gehasht.
Kein vorhandenes Asset wurde überschrieben.

Eine zweite, unabhängige Prüfung verglich anschließend die GitHub-Release-API
mit Gesamtmanifest und lokalem Staging:

```text
archive-0001-media-0001 expected=752 actual=752 bytes=2405046934 missing=0 extra=0 bad_state=0 bad_size=0 bad_digest=0
archive-0001-media-0002 expected=752 actual=752 bytes=2273347261 missing=0 extra=0 bad_state=0 bad_size=0 bad_digest=0
archive-0001-media-0003 expected=752 actual=752 bytes=2469093940 missing=0 extra=0 bad_state=0 bad_size=0 bad_digest=0
archive-0001-media-0004 expected=89 actual=89 bytes=300032942 missing=0 extra=0 bad_state=0 bad_size=0 bad_digest=0
api_verification total_assets=2345 total_bytes=7447521077 problems=0
```

Zusätzlich wurden alle 779 bewahrten Originaldateien nach Abschluss noch einmal
vollständig gegen das Manifest gehasht:

```text
source_reverification records=779 bytes=3654670091 mismatches=0
```

## Fail-closed-Stabilisierung während der realen Migration

Zwei reale GitHub-Randfälle wurden vor der Bildentfernung reproduziert und
testgetrieben geschlossen:

1. Ein unmittelbar nach Upload bereits von der API bestätigtes Asset war über
   den öffentlichen CDN-Pfad kurzzeitig noch mit HTTP 404 unsichtbar. Commit
   `c0950c5cae2ed9f5d836cf961fbc0816d6c9d351` ergänzt einen eng begrenzten
   Wiederanlauf ausschließlich für diese Propagationslücke.
2. Eine einzelne öffentliche Verbindung lieferte über neun Minuten keine
   weiteren Bytes, obwohl eine frische Verbindung dasselbe Asset in 0,17
   Sekunden mit identischem Hash lud. Commit
   `1c74e4efc645827cae356191b85063562759fb79` trennt deshalb den großen
   Upload-Prozesstimeout von einem öffentlichen 120-Sekunden-Lese-Timeout und
   verbindet bei Timeout beziehungsweise `URLError` begrenzt neu.

Die Plattform-Suite bestand danach 61/61 Tests. Die Generator-CI-Läufe
`30672528612` und `30673419439` waren in den Jobs Applet, Plattform und Web
erfolgreich.

## Git- und Pages-Gates

Das verifizierte Manifest und die 37 fehlenden Textseitendateien wurden zuerst
als eigener normaler Commit `db5500b743b68dd47cdc2bb3d7f8896bea7557e1`
veröffentlicht. Der zugehörige Archiv-Pages-Lauf `30675216538` bestand Build,
Artefaktvalidator und Deployment vollständig.

Erst danach wurden exakt folgende getrackte Bildpfade aus dem aktuellen Baum
entfernt:

- 760 reguläre PNG-Dateien, sämtlich Teil des Manifests;
- der getrackte Zeiger `Wirtelprimpf/working/latest.png`;
- keine Story-, Prompt-, Manifest- oder sonstige Datei.

Die Entfernung erfolgt im selben normalen Nachfolgecommit wie dieser Bericht,
die aktualisierten READMEs und der Release-Only-Ignorevertrag. Sämtliche früheren
Bildstände bleiben aus der Git-Historie wiederherstellbar; die unveränderlichen
Releasekopien sind der aktuelle Publikationspfad.

## Seitenfabrik und Folgearchive

Der reale Archivbuild aus dem vollständigen Manifest erzeugte vor dem Cutover
824 Dateien, darunter 819 HTML-Dateien, und validierte 10.851 interne Links. Der
Artefakt-Tree-SHA-256 lautete
`caf41e2985cca484e5bfb06aea3d97b46fca2fc86839eeb3d62438de4cf2ef90`.
Der bildfreie Endstand wird nach diesem Commit erneut vollständig gebaut,
validiert und über GitHub Pages deployt.

Für alle Folgearchive gilt derselbe Vertrag automatisch. Seit Generatorcommit
`2443ffe9d2f2c73ce8defd88fbc9d748bdbc35a5` erhält jedes neu provisionierte
`Wirtelprimpf-NNNN` eine versionierte `.gitignore`, die binäre Bildmedien im
Publikationsbaum blockiert, während Markdown, TXT und Manifeste versionierbar
bleiben. Neue Bilder gelten erst nach öffentlichem Download und identischer
SHA-256-Prüfung als publiziert.

## Bewahrte lokale Quelle und offenes Domain-Gate

Die aktive Benutzerarbeitskopie unter
`/home/teladi/.local/share/wirtelprimpf/github/Katzenbilder` wurde inhaltlich
nicht verändert. Sie bleibt als lokale Quelle und zusätzliche Sicherung
erhalten. Das private, vollständig geprüfte Migrationsstaging verbleibt unter
`/home/teladi/.local/state/wirtelprimpf/media-migration-0001`.

GitHub Pages ist für `wirtelprimpf-0001.telacore.org` konfiguriert. Der
Cloudflare-CNAME und damit HTTPS bleiben getrennt fail-closed, bis eine auf die
Zone begrenzte DNS-Schreibberechtigung verfügbar ist. Dieser externe
Berechtigungszustand ändert nichts am vollständig bestandenen Mediengate.
