---
date: 2026-05-15
type: konzept
tags: [konzept, lokale-modelle, setup, automatisierung]
status: active
---

# Roo Code System-Prompt-Optimierung

**Zusammenfassung**: Roo Codes Standard-System-Prompt ist ~51 KB groß und verbraucht einen erheblichen Teil des verfügbaren Kontextfensters. Für lokale Setups gibt es drei Stufen der Optimierung — von sicher (MCP deaktivieren) bis riskant (Foot Gun System Prompt).
**Quellen**: `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Das Problem: System-Prompt frisst Kontext

Roo Codes System-Prompt enthält:
- Verhaltensregeln und Rollenbeschreibung
- Vollständige Werkzeugdefinitionen (XML-Format für alle Tools)
- MCP-Server-Anweisungen (wenn aktiviert)
- Moduswechsel-Logik

Standard-Größen:

| Konfiguration | System-Prompt-Größe |
|---|---|
| Standard (mit MCP) | ~51 KB |
| Ohne MCP | ~33 KB |
| Foot Gun (minimiert) | ~28 KB |

Bei einem Kontextfenster von 32K Token (~128 KB) nimmt der System-Prompt allein bereits 40–80% des verfügbaren Platzes ein, bevor die erste Datei oder Aufgabe hinzukommt.

---

## Stufe 1: MCP deaktivieren (sicher, empfohlen)

Wenn keine MCP-Server genutzt werden, können die entsprechenden Anweisungen aus dem System-Prompt entfernt werden:

**Roo Code Einstellungen → MCP → „Enable MCP Servers" deaktivieren**

Effekt: System-Prompt von ~51 KB → ~33 KB (ca. 35% Reduktion)

Kein Risiko — nur relevant wenn MCP-Server tatsächlich genutzt werden.

---

## Stufe 2: Moduswechsel-Anweisungen entfernen (mittleres Risiko)

Ab Roo Code 3.7.8: Anweisungen für automatischen Moduswechsel können deaktiviert werden.

**Roo Code Einstellungen → Prompt → Advanced → Moduswechsel-Anweisungen entfernen**

Effekt: ~33 KB → ~28 KB

Risiko: Automatischer Moduswechsel (z.B. von Ask zu Code) funktioniert nicht mehr.

---

## Stufe 3: Foot Gun System Prompt (hohes Risiko)

Ab Roo Code 3.7.8 gibt es die Möglichkeit, den gesamten System-Prompt durch einen eigenen zu ersetzen — der sogenannte „Foot Gun System Prompt" (der Name ist Warnung genug).

**Vorteile:**
- Maximale Kontextreduzierung
- Vollständige Kontrolle über Modell-Verhalten
- Angepasst an spezifisches Modell und Hardware-Setup

**Nachteile:**
- Zukünftige Verbesserungen des Roo Code System-Prompts gehen verloren
- Selbst zu warten und zu testen
- Zu starke Vereinfachung kann Werkzeugaufrufe destabilisieren

> „Oversimplification of the system prompt may cause the model to malfunction with Roo Code." (Quelle: `olilanzRooCode-Local-Evaluation.md`)

---

## Ergänzungen für Low-Memory-Setups

Statt den System-Prompt zu ersetzen, kann er durch spezifische Anweisungen ergänzt werden. Bewährtes Beispiel für speichersparenden Betrieb:

```
Als du in einer Low-Memory-Konfiguration läufst, ist es EXTREM wichtig,
dass du keine großen Refactoring-Schritte auf einmal machst. Deine Ausgabe
ist auf 12.000 Token begrenzt. Bei der Bearbeitung großer Dateien nutze
bevorzugt das apply_diff-Werkzeug, um nicht die gesamte Datei neu schreiben
zu müssen. Nutze write_file nur wenn du wirklich die komplette Datei änderst.
```

(Quelle: `olilanzRooCode-Local-Evaluation.md`)

---

## Zusammenspiel: num_predict und System-Prompt

Roo Codes maximale Ausgabelänge hängt auch von Ollama's `num_predict`-Parameter ab. Standard kann Code-Dateien mitten im Inhalt abschneiden. Empfehlung:

```dockerfile
PARAMETER num_predict 12000
```

Das stellt sicher, dass auch größere Funktionen oder Klassen vollständig ausgegeben werden.

---

## Priorisierte Empfehlung

1. **Zuerst**: Kontextfenster korrekt konfigurieren ([ollama-kontextfenster](ollama-kontextfenster.md))
2. **Dann**: MCP deaktivieren (immer sicher, sofern nicht genutzt)
3. **Bei weiteren Problemen**: Moduswechsel-Anweisungen deaktivieren
4. **Als letztes Mittel**: Foot Gun System Prompt — nur wenn man weiß was man tut

---

## Verwandte Seiten

- [ollama-kontextfenster](ollama-kontextfenster.md) — Kontextfenster korrekt konfigurieren
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Warum Tool-Nutzung mit kleinem Kontext scheitert
- [roo-code](../werkzeuge/roo-code.md) — Roo Code: Modi, Funktionsweise, Optimierungen
- [quantisierung](quantisierung.md) — VRAM-Kalkulation und Quantisierungsstufen

---

[Wiki-Index](../index.md)
