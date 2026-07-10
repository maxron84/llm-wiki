---
date: 2026-07-10
type: anleitung
tags: [anleitung, vorlage, setup]
status: active
---

# Flutter-Projekt: Die Software-Vorlage anpassen

**Zusammenfassung**: Schritt-für-Schritt, wie die generische Vorlage [claude-md-software](../vorlagen/claude-md-software.md) für ein Flutter-Projekt über fünf Plattformen (Linux, Android, macOS, iOS, Windows) angepasst wird. Kein fertiges Template zum Kopieren — die Anpassung übernimmt die Produktions-KI selbst anhand dieser Anleitung.
**Quellen**: [guide-flutter-integration](../quellen/guide-flutter-integration.md)
**Zuletzt aktualisiert**: 2026-07-10

---

## 1. Moduswahl

Für ein Flutter-Projekt über fünf Plattformen: **Modus Dokumentiert** empfehlenswert, sobald das Projekt über eine Wochenend-App hinausgeht. Plattformspezifika (iOS-Signing, Linux-Packaging, Android-Gradle-Eigenheiten) sind genau der Typ Wissen, der ohne Wiki nach ein paar Monaten wieder mühsam aus Code und Git-Log rekonstruiert werden muss.

Bei einem reinen Prototyp/Experiment: **Lean** reicht, kann jederzeit hochgestuft werden.

## 2. Direkte Platzhalter-Werte

| Platzhalter | Wert |
|---|---|
| `{{Sprache}}` | Dart |
| `{{Framework}}` | Flutter |
| `{{Paketmanager}}` | pub |

## 3. Schnelleinstieg — Flutter-Befehle statt generischer Platzhalter

Ein einzeiliger Build-Befehl reicht bei fünf Zielplattformen nicht — eine Zeile pro Plattform:

```bash
# Abhängigkeiten installieren
flutter pub get

# Entwicklung (Device-ID via `flutter devices` ermitteln)
flutter run -d linux
flutter run -d chrome        # falls Web mit im Scope
flutter run -d <android-device-id>
flutter run -d <ios-simulator-id>   # nur von macOS aus möglich

# Tests
flutter test                 # Unit- & Widget-Tests
flutter test integration_test # falls Integrationstests vorhanden

# Linter/Formatter
dart format --set-exit-if-changed .
flutter analyze

# Builds pro Plattform
flutter build linux
flutter build apk            # oder: flutter build appbundle
flutter build ios            # nur von macOS aus möglich, erfordert Xcode + Signing
flutter build macos          # nur von macOS aus möglich
flutter build windows        # nur von Windows aus möglich (oder Cross-Build-Setup)
```

**Wichtig für die Vorlage**: iOS/macOS-Builds sind von Linux aus nicht möglich — das gehört explizit in die Vorlage, damit die KI nicht versucht, `flutter build ios` lokal auszuführen und dann über einen kryptischen Fehler rätselt.

## 4. Projektstruktur — Flutter hat eine feste Konvention

Der generische `src/`-Baum der Vorlage passt nicht. Flutters Struktur ist toolingseitig vorgegeben und wird 1:1 übernommen:

```
lib/                -- Dart-Quellcode (App-Code)
  main.dart
  ...
test/               -- Unit- & Widget-Tests
integration_test/   -- End-to-End-Tests (optional)
android/            -- Android-spezifischer nativer Code/Konfiguration
ios/                -- iOS-spezifischer nativer Code/Konfiguration
linux/              -- Linux-Desktop-Runner
macos/              -- macOS-Desktop-Runner
windows/            -- Windows-Desktop-Runner
web/                -- Web-Runner (falls im Scope)
pubspec.yaml        -- Abhängigkeiten & Metadaten (Äquivalent zu package.json)
wiki/               -- Projekt-Wiki (nur Modus Dokumentiert)
```

## 5. Architektur-Abschnitt — zwei Ergänzungen

Die generische Vorlage fragt nur nach Schichten/Datenfluss. Bei Multi-Platform-Flutter kommen zwei Architekturfragen dazu, die die KI sonst bei jeder Session neu erraten muss:

1. **State-Management-Entscheidung**: Riverpod, Bloc, Provider oder GetX — einmal festlegen und in die Architektur-Sektion schreiben. Ohne diese Angabe generiert ein Coding-Agent gerne einen Mix aus mehreren Ansätzen im selben Projekt.
2. **Umgang mit plattformspezifischem Code**: `Platform.isLinux`/`Platform.isIOS`-Verzweigungen, Conditional Imports (`if (dart.library.io)`), oder Plugin-basierte Abstraktion. Kurzer Satz reicht, z. B. „Plattformspezifischer Code wird über Conditional Imports in `lib/platform/` gekapselt, nie mit `Platform.is*`-Checks verstreut im Business-Logic-Code."

**Zusätzlicher Unterabschnitt** `### Zielplattformen`, direkt unter Architektur — verhindert, dass die KI versehentlich Web- oder Windows-Code generiert, wenn nur ein Teil der Plattformen im Scope ist:

```
### Zielplattformen

Aktiv: Linux, Android, macOS, iOS, Windows
Nicht im Scope: Web
Build-Einschränkung: iOS- und macOS-Builds erfordern eine macOS-Maschine mit Xcode.
```

## 6. Konventionen — Dart-Abweichungen von den Vorlagen-Beispielen

Die Vorlage nennt kebab-case als Beispiel für Dateibenennungen — das ist bei Dart falsch:

- **Dateibenennungen**: `lower_snake_case.dart` (Dart-Konvention, nicht kebab-case)
- **Code-Stil**: Effective Dart befolgen — Klassen in PascalCase, Variablen/Funktionen in camelCase, Konstanten in `lowerCamelCase` mit optionalem `k`-Präfix
- **Linting**: `analysis_options.yaml` referenzieren (meist mit `flutter_lints` als Basis) — unter „Ändere niemals Konfigurationsdateien ohne zu fragen" mit aufführen

## 7. Abhängigkeiten-Tabelle — Flutter-typische Beispielzeilen

| Paket | Zweck |
|---|---|
| flutter_riverpod (o. ä.) | State Management |
| go_router | Navigation |
| freezed / json_serializable | Code-Generierung für Models |
| dio oder http | HTTP-Client |

## 8. Tests — Flutter-spezifische Ergänzung

Die Vorlage fragt generisch nach Framework/Befehl — bei Flutter braucht es zusätzlich die explizite Unterscheidung Unit- vs. Widget- vs. Integrationstest, sonst schreibt die KI evtl. nur Unit-Tests:

```
- Unit-Tests: `flutter test test/unit/`
- Widget-Tests: `flutter test test/widget/`
- Integrationstests: `flutter test integration_test/`
```

## 9. Bei Modus Dokumentiert: zusätzlicher Wiki-Unterordner

Die Vorlage sieht `architektur/`, `module/`, `entscheidungen/`, `schnittstellen/`, `probleme/` vor. Für ein 5-Plattform-Projekt lohnt sich ein zusätzlicher Ordner, da plattformspezifisches Wissen (Signing-Prozesse, Paketformate, bekannte OS-Eigenheiten) sonst thematisch nirgends sauber hinpasst und in `probleme/` unübersichtlich würde:

```
wiki/
  plattformen/        -- Neu: pro Plattform eine Seite (Signing, Packaging, Quirks)
    linux.md
    android.md
    ios.md
    macos.md
    windows.md
```

## 10. Umgebungs-/Setup-Hinweis fürs konkrete Environment

Nicht Teil der Vorlage selbst, aber relevant für die Produktions-KI im Firmenumfeld — gehört eher in eine lokale Notiz oder unter „Bekannte Probleme":

- **Squid-Proxy**: `pub get` braucht ggf. `HTTP_PROXY`/`HTTPS_PROXY`-Umgebungsvariablen, sonst schlägt die Paketauflösung im Corporate-Netz fehl.
- **VS Codium**: Die Flutter-Extension ist im Open VSX Registry verfügbar (nicht nur im proprietären VS-Marketplace) — falls sie fehlt, zuerst dort suchen statt im MS-Marketplace.

## Was aus der Original-Vorlage 1:1 bleibt

Schnelleinstieg-Formatvorlage, Regeln-für-Claude-Abschnitt (Verbotsliste etc.), Kostenkontrolle-Abschnitt, Initialisierungs-Workflow, Wiki-Seitenformat und Wiki-Regeln — all das ist Framework-agnostisch und braucht keine Anpassung.

## Verwandte Seiten

- [claude-md-software](../vorlagen/claude-md-software.md) — Die zugrundeliegende Vorlage
- [guide-flutter-integration](../quellen/guide-flutter-integration.md) — Quellzusammenfassung
- [vorlage-einrichten](vorlage-einrichten.md) — Allgemeine Schritt-für-Schritt-Anleitung zum Vorlagen-Setup
- [claude-md-design](../konzepte/claude-md-design.md) — Allgemeine Designprinzipien für CLAUDE.md-Dateien

---

[Wiki-Index](../index.md)
