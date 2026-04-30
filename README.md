# 🐻 Ace Atrassi Baby Shower — Site centralisé

Site single-page pour gérer la baby shower de Ace : RSVP temps réel, liste invités auto, wishlist Amazon + Babylist + items locaux QC/CA avec réservations grisées en direct.

**Stack :** HTML statique + Firebase Realtime Database. Déployé GitHub Pages.

---

## Setup (5 min)

### 1. Firebase config

1. Console Firebase → Project `ace-baby-shower` → ⚙️ Project Settings → Your apps → `</>` Web → Register app `Ace Shower Web`
2. Copie l'objet `firebaseConfig`
3. Ouvre `index.html`, remplace le bloc `// REMPLACE ICI` par tes vrais valeurs (apiKey, messagingSenderId, appId)

### 2. Realtime Database rules

Console Firebase → Build → Realtime Database → Onglet **Rules** → colle :

```json
{
  "rules": {
    "rsvps":  { ".read": true, ".write": true },
    "claims": { ".read": true, ".write": true },
    "guests": { ".read": true, ".write": true },
    "event":  { ".read": true, ".write": true }
  }
}
```

Clique **Publish**.

### 3. Configurer la date de l'événement

Console Firebase → Realtime Database → Data → ajoute manuellement nœud `event` :

```json
{
  "date": "2026-09-15T18:30:00-04:00",
  "time": "18 h 30 — 21 h 30",
  "dress_code": "Décontracté chic — tons crème & terre"
}
```

(Optionnel — par défaut le site utilise `2026-06-15` comme placeholder)

### 4. Deploy GitHub Pages

```bash
cd /Users/youssefa/Desktop/ace-baby-shower
git init
git add .
git commit -m "Initial baby shower site"
gh repo create shadow2256/ace-baby-shower --public --source=. --push
gh api -X POST /repos/shadow2256/ace-baby-shower/pages -f source[branch]=main -f source[path]=/
```

URL publique sera : `https://shadow2256.github.io/ace-baby-shower/`

(2-3 min pour propagation après push)

---

## Architecture

### Sections du site

1. **Hero** — Ours teddy + countdown live (jours/heures/minutes)
2. **Détails** — Date, heure, adresse 980 boul Laval, dress code
3. **RSVP** — Formulaire (nom + status yes/no/maybe + nb personnes + commentaire)
4. **Tally live** — Compteurs Confirmés / Peut-être / Ne peuvent pas mis à jour temps réel
5. **Guest list table** — 17 invités pré-saisis (Charles-Olivier, Cousine Marwa+2, Sonia+3, etc.) avec status auto en fonction des RSVP reçus
6. **Wishlist** — 2 boutons (Amazon + Babylist) + 12 items locaux QC/CA avec « Je le réserve »

### Schéma Firebase Realtime DB

```
/rsvps/<name-key> = { name, status, plus_ones, comment, ts }
/claims/<item-id> = { name, ts }
/guests/<name-key> = { name, details }   (pré-saisis)
/event = { date, time, dress_code }
```

### Sync temps réel

Tous les visiteurs voient mêmes données en direct via `onValue()`. Quand un invité soumet RSVP → tous les autres browsers ouverts voient le compteur s'incrémenter et la table se remplir sans refresh.

---

## Liens partagés

- 🛒 Wishlist Amazon : https://www.amazon.ca/hz/wishlist/ls/3BUL8BBGK9H54
- 🌟 Babylist Registry : https://www.babylist.com/list/youssef-atrassi
- 🐻 Site Baby Shower : https://shadow2256.github.io/ace-baby-shower/ (après deploy)

---

## Texte invitation à envoyer

```
🐻 Baby shower de Ace Atrassi 🐻

Alice & Youssef vous invitent à célébrer l'arrivée prochaine de leur petit Ace.

📅 [Date à confirmer]
🕐 18 h 30 — 21 h 30
📍 980 boul Laval, Laval, QC, H7S 2K2

Tout est sur la page :
👉 https://shadow2256.github.io/ace-baby-shower/

→ Confirme ta présence (yes/no/peut-être) en 30 sec
→ Voir et réserver un cadeau (Amazon + Babylist + petits artisans QC)
→ Tout se met à jour en temps réel — tu vois qui vient

Merci infiniment 🙏
Avec amour, Alice & Youssef
```

---

## Maintenance après l'événement

- Une fois la date confirmée → mettre à jour `event/date` dans Firebase Realtime DB
- Pour archiver : exporter les RSVP via Firebase Console → Data → ⋮ → Export JSON
- Le repo GitHub Pages reste public, tu peux le rendre privé après l'événement
