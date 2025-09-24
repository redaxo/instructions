# AddOns

## Allgemeine Beschreibung

AddOns sind Erweiterungen für das REDAXO Content Management System, die die Kernfunktionalität erweitern und zusätzliche Features bereitstellen. Sie ermöglichen es Entwicklern, modulare und wiederverwendbare Komponenten zu erstellen, die einfach installiert, aktiviert und verwaltet werden können.

AddOns in REDAXO folgen einer standardisierten Struktur und nutzen das eingebaute Addon-System, welches eine zentrale Verwaltung, automatische Installation und Konfiguration ermöglicht.

## Typische Dateistruktur eines AddOns

```
addon_name/
├── package.yml                 # AddOn-Konfiguration und Metadaten
├── install.php                 # Installation des AddOns
├── uninstall.php              # Deinstallation des AddOns
├── boot.php                   # Wird bei jedem Seitenaufruf geladen
├── pages/                     # Backend-Seiten
│   ├── index.php
│   ├── config.php
│   └── help.php
├── lib/                       # PHP-Klassen und -Bibliotheken
│   └── addon_class.php
├── assets/                    # CSS, JS, Bilder
│   ├── css/
│   ├── js/
│   └── images/
├── lang/                      # Sprachdateien
│   ├── de_de.lang
│   └── en_gb.lang
├── fragments/                 # Template-Fragmente
└── vendor/                    # Externe Bibliotheken (Composer)
```

## package.yml - Die AddOn-Konfigurationsdatei

Die `package.yml` ist das Herzstück jedes AddOns und enthält alle wichtigen Metadaten:

```yaml
package: addon_name
version: '1.0.0'
author: 'Entwickler Name'
supportpage: 'https://github.com/user/addon'

page:
    title: 'translate:addon_name_title'
    perm: addon_name[]
    icon: rex-icon fa-puzzle-piece
    subpages:
        config:
            title: 'translate:addon_name_config'
        help:
            title: 'translate:addon_name_help'
            subPath: README.md

requires:
    redaxo: '^5.10.0'
    php:
        version: '^7.4 || ^8.0'
    packages:
        media_manager: '^2.8.0'

conflicts:
    packages:
        old_addon: '*'
```

### Wichtige package.yml Parameter:

- **package**: Der eindeutige AddOn-Name (nur Kleinbuchstaben, Zahlen und Unterstriche)
- **version**: Versionsnummer des AddOns
- **author**: Name des Entwicklers oder der Organisation
- **supportpage**: URL zur Support-Seite oder Repository
- **page**: Backend-Seiten-Konfiguration
- **requires**: Abhängigkeiten (REDAXO-Version, PHP-Version, andere AddOns)
- **conflicts**: AddOns die nicht gleichzeitig installiert sein können

## install.php - Installation des AddOns

Die `install.php` wird einmalig bei der Installation ausgeführt:

```php
<?php

// Datenbankstruktur erstellen
$sql = rex_sql::factory();

// Tabelle erstellen
$sql->setQuery('
    CREATE TABLE IF NOT EXISTS `' . rex::getTable('addon_data') . '` (
        `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
        `name` varchar(255) NOT NULL,
        `value` text,
        `createdate` datetime NOT NULL,
        `updatedate` datetime NOT NULL,
        PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
');

// Standardkonfiguration setzen
$this->setConfig('default_setting', 'default_value');

// Assets kopieren
rex_dir::copy(
    $this->getPath('assets'),
    $this->getAssetsPath()
);

// Erfolgreiche Installation
return true;
```

## uninstall.php - Deinstallation des AddOns

Die `uninstall.php` wird bei der Deinstallation ausgeführt:

```php
<?php

// Datenbankstrukturen entfernen
$sql = rex_sql::factory();
$sql->setQuery('DROP TABLE IF EXISTS `' . rex::getTable('addon_data') . '`');

// Assets löschen
rex_dir::delete($this->getAssetsPath());

// Konfiguration löschen
$this->removeConfig();

return true;
```

## rex_addon - Hauptklasse und Funktionen

### Grundlegende AddOn-Verwaltung

```php
// AddOn-Instanz abrufen
$addon = rex_addon::get('addon_name');

// AddOn-Status prüfen
if ($addon->isInstalled()) {
    // AddOn ist installiert
}

if ($addon->isActivated()) {
    // AddOn ist aktiviert
}

// AddOn-Informationen
$version = $addon->getVersion();
$author = $addon->getAuthor();
$name = $addon->getName();
```

### Pfade und URLs

```php
$addon = rex_addon::get('addon_name');

// Pfade
$addonPath = $addon->getPath();              // /redaxo/src/addons/addon_name/
$assetsPath = $addon->getAssetsPath();       // /assets/addons/addon_name/
$dataPath = $addon->getDataPath();           // /redaxo/data/addons/addon_name/

// URLs
$assetsUrl = $addon->getAssetsUrl();         // /assets/addons/addon_name/
$assetsUrl = $addon->getAssetsUrl('css/style.css'); // Spezifische Datei
```

### Konfiguration verwalten

```php
$addon = rex_addon::get('addon_name');

// Konfiguration setzen
$addon->setConfig('setting_name', 'value');
$addon->setConfig([
    'setting1' => 'value1',
    'setting2' => 'value2'
]);

// Konfiguration abrufen
$value = $addon->getConfig('setting_name');
$value = $addon->getConfig('setting_name', 'default_value');
$allConfig = $addon->getConfig();

// Konfiguration prüfen
if ($addon->hasConfig('setting_name')) {
    // Einstellung existiert
}

// Konfiguration entfernen
$addon->removeConfig('setting_name');
```

### Backend-Seiten erstellen

```php
// In pages/index.php
$addon = rex_addon::get('addon_name');

echo rex_view::title($addon->i18n('addon_title'));

$content = '<p>' . $addon->i18n('addon_description') . '</p>';

$fragment = new rex_fragment();
$fragment->setVar('class', 'info');
$fragment->setVar('title', $addon->i18n('info_title'));
$fragment->setVar('body', $content, false);
echo $fragment->parse('core/page/section.php');
```

### Sprachdateien verwenden

```php
$addon = rex_addon::get('addon_name');

// Übersetzung abrufen
$translation = $addon->i18n('translation_key');
$translationWithParams = $addon->i18n('translation_key', $param1, $param2);

// In Sprachdatei (lang/de_de.lang):
// addon_name_title = AddOn Titel
// addon_name_description = Dies ist die Beschreibung des AddOns
```

### Fehlerbehandlung und Logging

```php
$addon = rex_addon::get('addon_name');

// Fehler loggen
rex_logger::logException(new Exception('Fehler aufgetreten'));

// Debug-Informationen
if (rex::isDebugMode()) {
    dump($debugData);
}

// AddOn-spezifische Fehlerbehandlung
try {
    // Code ausführen
} catch (Exception $e) {
    $addon->setProperty('error', $e->getMessage());
}
```

## Wichtige rex_addon Methoden im Überblick

| Methode | Beschreibung |
|---------|-------------|
| `get($name)` | AddOn-Instanz abrufen |
| `isInstalled()` | Prüft ob AddOn installiert ist |
| `isActivated()` | Prüft ob AddOn aktiviert ist |
| `isAvailable()` | Prüft ob AddOn verfügbar ist |
| `getVersion()` | Version des AddOns |
| `getAuthor()` | Autor des AddOns |
| `getName()` | Name des AddOns |
| `getPath($file = '')` | Pfad zum AddOn |
| `getAssetsPath($file = '')` | Assets-Pfad |
| `getAssetsUrl($file = '')` | Assets-URL |
| `getDataPath($file = '')` | Daten-Pfad |
| `setConfig($key, $value)` | Konfiguration setzen |
| `getConfig($key, $default = null)` | Konfiguration abrufen |
| `hasConfig($key)` | Konfiguration prüfen |
| `removeConfig($key = null)` | Konfiguration entfernen |
| `i18n($key, ...$params)` | Übersetzung abrufen |
| `includeFile($file)` | Datei einbinden |
| `getProperty($key, $default = null)` | Property abrufen |
| `setProperty($key, $value)` | Property setzen |

## Best Practices

1. **Namespace verwenden**: Nutzen Sie PHP-Namespaces für Ihre Klassen
2. **Autoloading**: Implementieren Sie PSR-4 Autoloading
3. **Konfiguration**: Speichern Sie Einstellungen über das AddOn-Config-System
4. **Sprachdateien**: Verwenden Sie i18n für alle Texte
5. **Extension Points**: Nutzen Sie das Event-System für Erweiterbarkeit
6. **Fehlerbehandlung**: Implementieren Sie robuste Fehlerbehandlung
7. **Assets**: Kopieren Sie Assets bei Installation und entfernen Sie sie bei Deinstallation
8. **Dependencies**: Definieren Sie alle Abhängigkeiten in der package.yml
