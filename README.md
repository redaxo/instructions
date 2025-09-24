# Instructions

In diesem Respository finden man die Dokumentation für das REDAXO CMS in Markdown Dateien.
Dabei ist das Ziel, möglichst umfänglich und aktuell die Funktionen und Möglichkeiten von REDAXO zu dokumentieren um diese in LLMs weiterverarbeiten zu können.

Bei soll man einerseits diese Teile in seinenen Projekten nutzen können, andererseits soll es auch möglich sein, diese Dokumentation in LLMs zu integrieren um so Fragen zu REDAXO beantworten zu können.

## MCP (Model Context Protocol)

Diese Dateien werden in einem REDAXO spezifischen MCP Server bereitgestellt werden und sind deswegen am besten so aktuell wie möglich zu halten.

## Datei-Struktur

In der obersten Ebene befinden sich die Hauptbereich, die wir im REDAXO Umfeld haben. Dazu gehören auch die relevantesten AddOns wie YForm, MForm, YRewrite, etc. und darf gerne erweitert werden.

Im Ordner "examples" befinden sich Beispiele zu den oberen Bereichen. Verschiedene Beispiele werden in einer einzelnen Datei gespeichert.

## Struktur in den Markdown Dateien

In jeder .md Datei sollte am Anfang die allgemeine Beschreibung stehen, inkl Verwendungszweck, Anwendung, Voraussetzungen, etc.
Weitere Inhalte sind abhängig vom Thema der Datei. Klassen, Funktionen, Bepielcode, Beschreiben wie diese im Kontext von anderen AddOns funktionieren
