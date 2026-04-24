# Kassensystem – Setup-Anleitung

## Voraussetzungen

Nur ein Browser wird benötigt. Kein Server, kein Build-Schritt.

---

## 1. Firebase-Projekt erstellen

1. Öffne [https://console.firebase.google.com/](https://console.firebase.google.com/)
2. Klicke auf **„Projekt hinzufügen"**
3. Projektnamen vergeben (z. B. `kassensystem`), Schritt-für-Schritt folgen
4. Im Projekt: **„Web-App hinzufügen"** (Symbol `</>`)
5. App registrieren → die **Firebase-Konfiguration** (JSON-Objekt) erscheint – diese Werte brauchst du

---

## 2. Firestore-Datenbank einrichten

1. Im Firebase-Projekt: **Firestore Database → Erstellen**
2. Modus: zunächst **Testmodus** wählen (für den internen Einsatz ausreichend)
3. Standort wählen (z. B. `europe-west3` für Frankfurt)

---

## 3. Firestore-Index erstellen (wichtig!)

Die Küchen-Ansicht benötigt einen **zusammengesetzten Index**:

| Collection | Feld 1    | Feld 2      | Reihenfolge |
|------------|-----------|-------------|-------------|
| `orders`   | `status`  | `timestamp` | Aufsteigend |

**Option A – automatisch:**  
Beim ersten Öffnen der Küchen-Ansicht erscheint ggf. ein Fehler-Toast.  
In der **Browser-Konsole** (F12) findest du dann einen direkten Link zur Index-Erstellung – einfach öffnen und bestätigen.

**Option B – manuell:**  
Firebase Console → Firestore → **Indizes** → Index hinzufügen:
- Collection-ID: `orders`
- Felder: `status Aufsteigend`, `timestamp Aufsteigend`

---

## 4. App starten

1. `index.html` im Browser öffnen (Doppelklick genügt)
2. Das **Firebase-Konfigurationsformular** erscheint automatisch
3. Werte aus der Firebase Console eintragen:
   - `apiKey`
   - `authDomain`
   - `projectId`
   - `storageBucket`
   - `messagingSenderId`
   - `appId`
4. **„Verbinden & Starten"** klicken

Die Konfiguration wird im Browser (localStorage) gespeichert – beim nächsten Öffnen verbindet sich die App automatisch.

---

## 5. Auf mehreren Geräten nutzen

- Die Datei `index.html` auf alle Geräte kopieren (oder per lokalem Webserver / GitHub Pages bereitstellen)
- Alle Geräte tragen dieselbe Firebase-Konfiguration ein
- Echtzeit-Synchronisation läuft dann automatisch über Firestore

---

## Ansichten

| Ansicht     | Zweck                                        |
|-------------|----------------------------------------------|
| 🧾 **Kasse**  | Produkte auswählen, Mengen & Preise anpassen, Bestellung abschicken |
| 👨‍🍳 **Küche** | Offene Bestellungen sehen, Mengen reduzieren wenn produziert |

---

## Produkte & Preise anpassen

Preise lassen sich direkt in der Kassen-Ansicht per Tipp/Klick anpassen.  
Für dauerhafte Änderungen der Standardpreise oder Produktnamen: in `index.html` das Array `PRODUCTS` bearbeiten (ca. Zeile 340).

---

## Sicherheitshinweis

Im **Produktionsbetrieb** (öffentlich zugängliches Netzwerk) sollten die Firestore-Regeln angepasst werden:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /orders/{orderId} {
      allow read, write: if true; // für geschlossenes Netz ausreichend
    }
  }
}
```

Für höhere Sicherheit: Firebase Authentication einrichten und Regeln entsprechend einschränken.
