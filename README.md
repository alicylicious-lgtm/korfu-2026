# Corfu 2026 — Setup

## 1. Firebase-Projekt vorbereiten

Falls du dein bestehendes Firebase-Projekt (z. B. `tage-challenge-d793c` oder
`challenge-75-days`) mitverwenden willst, kannst du das tun — sonst leg ein
neues Projekt an unter https://console.firebase.google.com

**Google-Anmeldung aktivieren:**
1. Firebase Console → dein Projekt → **Authentication** → Tab **Sign-in method**
2. **Google** als Anbieter aktivieren, Support-E-Mail eintragen, speichern

**Firestore aktivieren:**
1. **Firestore Database** → **Datenbank erstellen**
2. Modus: *Produktionsmodus* (die mitgelieferten `firestore.rules` regeln den Zugriff)
3. Region wählen (z. B. `eur3 (europe-west)`)

**Web-App registrieren & Config holen:**
1. Projektübersicht (Zahnrad) → **Projekteinstellungen** → runter scrollen zu **Deine Apps**
2. **Web-App hinzufügen** (`</>`-Symbol), Namen vergeben (z. B. „Corfu App")
3. Firebase zeigt dir ein `firebaseConfig`-Objekt mit `apiKey`, `authDomain`, `projectId` usw.

## 2. Config eintragen

Öffne `index.html`, suche den Abschnitt `FIREBASE CONFIG` (ca. Zeile 190)
und ersetze die Platzhalter mit deinen echten Werten aus Schritt 1:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "...",
};
```

## 3. Firestore-Regeln hochladen

Mit der Firebase CLI (falls noch nicht installiert: `npm install -g firebase-tools`):

```bash
firebase login
firebase init firestore   # wähle dein bestehendes Projekt
firebase deploy --only firestore:rules
```

Alternativ: Inhalt von `firestore.rules` einfach in der Firebase Console unter
**Firestore Database → Regeln** einfügen und **Veröffentlichen** klicken.

## 4. Deployen (GitHub Pages oder Vercel — wie deine anderen Apps)

**GitHub Pages:**
```bash
git init
git add .
git commit -m "Corfu 2026 App"
git remote add origin https://github.com/alicylicious-lgtm/corfu-2026.git
git push -u origin main
```
Dann in den Repo-Settings → **Pages** → Branch `main` als Quelle aktivieren.

**Vercel:** Ordner einfach per `vercel` CLI oder Drag&Drop im Vercel-Dashboard deployen.

## 5. Wichtig: autorisierte Domain eintragen

Google-Login funktioniert nur auf Domains, die Firebase kennt:
1. Firebase Console → **Authentication** → Tab **Settings** → **Autorisierte Domains**
2. Deine Deploy-Domain hinzufügen (z. B. `alicylicious-lgtm.github.io` oder deine `.vercel.app`-Domain)

Ohne diesen Schritt zeigt der Google-Login-Button einen Fehler.

## Wie es funktioniert

- Beim ersten Login mit Google wählt jeder einmalig seinen Namen aus der
  Familienliste — das wird mit dem Google-Account verknüpft (`profiles`-Collection)
  und danach automatisch erkannt.
- Checklisten-Status und Bearbeitungen liegen in `tripData/state`, News in
  `tripData/news` — beide in Echtzeit synchronisiert (kein Neuladen nötig).
- Das News-Eingabefeld ist nur sichtbar, wenn der verknüpfte Name „Sarang" ist.
