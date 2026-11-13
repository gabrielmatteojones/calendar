# Jones Calendar · calendar.joneshouse.it

Calendario privato di Gabriel Jones. Stesso impianto della webmail (Jekyll + Netlify, stessa grafica, stessa password), ma con un solo strumento dentro: il calendario.

## Cosa fa
- **Quattro viste**: Giorno, Settimana, Mese, Agenda. Si parte dal lunedì. Su telefono si apre l'Agenda; toccando un giorno del mese si entra nel giorno.
- **Eventi**: titolo, data (anche su più giorni), orario o «tutto il giorno», luogo, note, 6 colori.
- **Ripetizioni**: ogni giorno, settimana, mese, anno, con data di fine facoltativa. «Salta solo questa data» esclude una singola occorrenza.
- **Festività italiane** (Pasqua e Lunedì dell'Angelo calcolati per ogni anno), attivabili dal riquadro sotto il calendario.
- **Ricerca** in titolo, luogo e note; linea rossa dell'ora attuale in Giorno e Settimana.
- **Esporta / importa .ics**: compatibile con Google Calendar, Apple Calendar, Outlook. L'importazione non duplica gli eventi già presenti.
- **Tasti**: N nuovo · T oggi · ← → precedente/successivo · G S M A cambia vista · / cerca.

## Come è protetta
- `netlify/edge-functions/auth.js` chiede utente e password per **ogni** richiesta (pagine e API). Senza `PORTAL_USER` e `PORTAL_PASSWORD` il sito resta chiuso (503).
- Gli eventi stanno su **Netlify Blobs** (store `portale`, collezione `calendario`) tramite `netlify/functions/data.mjs`, raggiungibile solo dopo il login.
- Il pulsante **Esci** fa dimenticare al browser utente e password.
- Nessun motore di ricerca può indicizzarlo (`robots.txt`, intestazioni `X-Robots-Tag`).

## Pubblicazione su Netlify
1. Carica questa cartella in un repository Git e importalo su Netlify (le impostazioni di build sono già in `netlify.toml`).
2. **Site configuration → Environment variables**: `PORTAL_USER` e `PORTAL_PASSWORD` (puoi usare gli stessi della webmail). Rilancia il deploy.
3. **Domain management → Add a domain** → `calendar.joneshouse.it`.
   - Se il DNS di `joneshouse.it` è su Netlify, il record si crea da solo.
   - Altrimenti, dove gestisci il DNS aggiungi un record **CNAME**: nome `calendar`, valore `IL-TUO-SITO.netlify.app`.
4. Il certificato HTTPS lo crea Netlify da solo (può servire qualche minuto).

## In locale
`cp .env.example .env` (metti utente e password), poi `npm install && npx netlify dev` → http://localhost:8888.
Le funzioni di archiviazione e la protezione funzionano solo con `netlify dev` (o sul sito pubblicato): aprendo i file in altro modo gli eventi restano solo nel browser.

## Dove mettere le mani
- Interfaccia: `index.html`, `assets/js/calendar.js`, stili in `assets/css/calendar.css`
- Logica (date, ripetizioni, festività, .ics): `assets/js/calendar-lib.js` (senza DOM: si prova anche con node)
- Festività: elenco in `festivitaAnno()` dentro `calendar-lib.js` (per aggiungere, ad esempio, il santo patrono)
- Intestazione: `_includes/header.html` · login e archivio: `netlify/`
- `core.js` e `portal.css` sono quelli del portale originale
