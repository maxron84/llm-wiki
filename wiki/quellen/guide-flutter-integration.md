---
date: 2026-07-10
type: quelle
tags: [quelle, produktion, schema]
status: active
---

# Guide: Flutter-Integration in die CLAUDE.md-Vorlage

**Zusammenfassung**: Anleitung, wie die Vorlage [claude-md-software](../vorlagen/claude-md-software.md) für ein Flutter-Projekt (Linux, Android, macOS, iOS, Windows) angepasst wird. Kein fertig ausgefülltes Template — die Anpassung übernimmt die Produktions-KI selbst anhand dieser Anleitung.
**Quellen**: `raw/guide_flutter_integration.md`
**Zuletzt aktualisiert**: 2026-07-10

---

Der Guide beschreibt zehn Anpassungspunkte, wenn die generische Software-Vorlage auf ein Multi-Platform-Flutter-Projekt trifft (Quelle: guide_flutter_integration.md):

1. **Moduswahl**: Ab Projekten, die über eine Wochenend-App hinausgehen, ist Modus "Dokumentiert" empfehlenswert — Plattformspezifika (iOS-Signing, Linux-Packaging, Android-Gradle) sind genau das Wissen, das ohne Wiki nach Monaten mühsam aus Code und Git-Log rekonstruiert werden muss. Reine Prototypen fahren mit "Lean".
2. **Platzhalter**: Sprache = Dart, Framework = Flutter, Paketmanager = pub.
3. **Schnelleinstieg**: Ein einzeiliger Build-Befehl reicht bei fünf Zielplattformen nicht — pro Plattform eine eigene Zeile für `flutter run`/`flutter build`.
4. **Projektstruktur**: Flutters `lib/`/`test/`/`integration_test/`/`android/`/`ios/`/`linux/`/`macos/`/`windows/`-Konvention ist toolingseitig vorgegeben und wird 1:1 übernommen statt des generischen `src/`-Baums.
5. **Architektur-Ergänzungen**: State-Management-Entscheidung (Riverpod/Bloc/Provider/GetX) einmal festlegen, sonst mischt ein Coding-Agent mehrere Ansätze im selben Projekt. Umgang mit plattformspezifischem Code (Conditional Imports statt verstreuter `Platform.is*`-Checks) explizit dokumentieren. Neuer Unterabschnitt `### Zielplattformen` verhindert versehentliche Web-/Windows-Codegenerierung außerhalb des Scopes.
6. **Konventionen**: `lower_snake_case.dart` statt kebab-case, Effective Dart als Stilrichtlinie, `analysis_options.yaml`/`flutter_lints` in die Konfigurationsdatei-Schutzliste aufnehmen.
7. **Abhängigkeiten-Tabelle**: Flutter-typische Beispielpakete (flutter_riverpod, go_router, freezed/json_serializable, dio/http).
8. **Tests**: explizite Dreiteilung Unit-/Widget-/Integrationstest, sonst schreibt die KI tendenziell nur Unit-Tests.
9. **Wiki-Unterordner** (nur Modus Dokumentiert): zusätzlicher Ordner `wiki/plattformen/` mit einer Seite pro Plattform (Signing, Packaging, Quirks) — sonst landet plattformspezifisches Wissen unübersichtlich in `probleme/`.
10. **Umgebungshinweis** (lokal, nicht Teil der Vorlage): Squid-Proxy erfordert ggf. `HTTP_PROXY`/`HTTPS_PROXY` für `pub get`; die Flutter-Extension für VS Codium ist über Open VSX verfügbar, nicht nur im MS-Marketplace.

Alles Framework-Agnostische aus der Original-Vorlage (Schnelleinstieg-Formatvorlage, Regeln-Abschnitt, Kostenkontrolle, Initialisierungs-Workflow, Wiki-Seitenformat) bleibt unverändert.

## Verwandte Seiten

- [flutter-claude-md-anpassung](../anleitungen/flutter-claude-md-anpassung.md) — Die vollständige Anpassungsanleitung
- [claude-md-software](../vorlagen/claude-md-software.md) — Die zugrundeliegende Vorlage
- [claude-md-design](../konzepte/claude-md-design.md) — Allgemeine Designprinzipien für CLAUDE.md-Dateien

---

[Wiki-Index](../index.md)
