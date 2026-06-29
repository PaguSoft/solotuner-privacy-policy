# SoloTuner — Privacy Roadmap

Documento interno di riferimento per la conformità privacy di SoloTuner (PaguSoft).  
Aggiornato: 29 giugno 2026.

---

## Indice

1. [Todo immediati sulla policy attuale](#1-todo-immediati-sulla-policy-attuale)
2. [Prima di attivare AdMob — Checklist completa](#2-prima-di-attivare-admob--checklist-completa)
3. [Aggiornamenti alla privacy policy per AdMob](#3-aggiornamenti-alla-privacy-policy-per-admob)
4. [Implementazione tecnica del consenso (UMP/ATT)](#4-implementazione-tecnica-del-consenso-umpatt)
5. [Google Play Data Safety — aggiornamenti](#5-google-play-data-safety--aggiornamenti)
6. [Riferimenti utili](#6-riferimenti-utili)

---

## 1. Todo immediati sulla policy attuale

Questi sono aggiustamenti minori alla policy già pubblicata. Rischio basso se non risolti subito, ma vanno inseriti al prossimo aggiornamento.

### 1.1 Retention delle email di supporto (Sezione 7)

**Problema:** La sezione 7 descrive bene il perché raccogliamo le email di supporto, ma non dice per quanto tempo le conserviamo. Il GDPR richiede di dichiararlo (art. 5.1(e), principio di limitazione della conservazione).

**Fix:** Aggiungere alla fine della sezione 7, dopo i bullet point, il seguente paragrafo:

```
We retain support emails and any information included in them for as long
as necessary to resolve your inquiry, and for up to 12 months thereafter,
after which they are deleted.
```

Scegli il periodo che riflette la realtà (12 mesi è standard e difendibile).

---

### 1.2 Diritti GDPR troppo vaghi (Sezione 5)

**Problema:** La sezione 5 ("Your rights") dice genericamente *"privacy laws may give you rights to access, correct, or delete"*. Per utenti europei, il GDPR richiede che i diritti siano elencati esplicitamente (art. 13.2(b)).

**Fix:** Sostituire il testo attuale con un elenco esplicito:

```
Depending on where you live, applicable privacy laws (including the GDPR
for users in the European Economic Area) may give you the following rights:

- Access: request a copy of the personal data we hold about you.
- Correction: request that inaccurate data be corrected.
- Deletion: request that we delete your personal data.
- Restriction: request that we limit how we process your data.
- Objection: object to processing based on legitimate interests.
- Portability: receive your data in a structured, machine-readable format.

Because we do not collect tuning audio or on-device settings on our
servers, most relevant actions are under your direct control as described
in Section 1.B. For any other request, contact us at pagusoft.dev@gmail.com.
You also have the right to lodge a complaint with your local data protection
authority (e.g. the Italian Garante della Privacy at www.garanteprivacy.it).
```

---

## 2. Prima di attivare AdMob — Checklist completa

> ⚠️ **Regola d'oro:** non rilasciare mai una versione con AdMob attivo senza aver completato tutti i punti di questa checklist. L'advertising SDK inizia a raccogliere dati non appena si avvia l'app — anche prima che l'utente veda un annuncio.

### 2.1 Decisioni preliminari da prendere

- [ ] **Scegliere il network:** Google AdMob è lo standard de facto per Android. Confermare che sia l'unica scelta o valutare alternative (Unity Ads, ironSource, ecc.).
- [ ] **Personalizzazione degli annunci:** decidere se mostrare annunci personalizzati (più redditizi) o non personalizzati (più semplici da gestire in Europa). In Europa, senza consenso esplicito, puoi mostrare solo annunci non personalizzati. La scelta influenza tutta l'architettura del consenso.
- [ ] **Frequenza degli annunci:** decidere il formato (banner, interstitial, rewarded). Ogni formato ha implicazioni UX diverse. Gli annunci rewarded richiedono un'interazione esplicita dell'utente e sono più "GDPR-friendly" per design.

### 2.2 Ordine corretto delle operazioni

1. Aggiornare la privacy policy (vedi [Sezione 3](#3-aggiornamenti-alla-privacy-policy-per-admob))
2. Implementare il consenso UMP/ATT nell'app (vedi [Sezione 4](#4-implementazione-tecnica-del-consenso-umpatt))
3. Integrare l'SDK AdMob **condizionato al consenso**
4. Aggiornare il Data Safety su Google Play (vedi [Sezione 5](#5-google-play-data-safety--aggiornamenti))
5. Testare il flusso di consenso su dispositivi fisici
6. Rilasciare

---

## 3. Aggiornamenti alla privacy policy per AdMob

Quando AdMob sarà attivo, la sezione 3 ("Future updates") dovrà essere convertita in una sezione permanente. Di seguito il contenuto preciso da aggiungere/sostituire.

### 3.1 Nuova sezione "Advertising"

```markdown
## X. Advertising

Starting from version [X.X.X], the App may display advertisements served
by Google AdMob, a service provided by Google LLC (1600 Amphitheatre
Parkway, Mountain View, CA 94043, USA).

**What AdMob collects:** To serve ads and measure their performance,
Google AdMob may automatically collect the following data:
- Your device's advertising identifier (Android Advertising ID / GAID)
- IP address (used to infer approximate location)
- Device information (model, OS version, language, screen size)
- App usage data (sessions, interactions with ads)
- Diagnostic and performance data

**Personalized vs. non-personalized ads:** If you give your consent,
ads may be personalized based on your interests. If you decline or
withdraw consent, you will see non-personalized (contextual) ads only.
You can change your choice at any time in the App's [Settings / About]
screen.

**Legal basis:** Personalized advertising is based on your explicit
consent (Art. 6(1)(a) GDPR). Non-personalized advertising, limited to
what is strictly necessary to serve ads, is based on our legitimate
interest (Art. 6(1)(f) GDPR).

**International transfers:** Google LLC is based in the United States.
Data transfers are covered by Google's participation in the EU–U.S.
Data Privacy Framework. For more information, see the
[Google Privacy Policy](https://policies.google.com/privacy) and
[How Google uses data when you use our partners' apps](https://policies.google.com/technologies/partner-sites).

**DPA:** Google acts as a data processor on our behalf under the
[Google Advertising Products Data Processing Terms](https://privacy.google.com/businesses/processorterms/).
```

### 3.2 Aggiornare la sezione "Future updates" (attuale Sezione 3)

Una volta che AdMob è live, rimuovere il riferimento agli ads come evento futuro dalla sezione 3. Lasciare solo la parte relativa alle funzionalità opzionali future se applicabile.

### 3.3 Aggiornare la data "Last updated"

Ricordarsi sempre di aggiornare la data in cima alla policy ad ogni modifica sostanziale.

---

## 4. Implementazione tecnica del consenso (UMP/ATT)

### 4.1 Android — Google UMP (User Messaging Platform)

Google fornisce un SDK dedicato alla gestione del consenso GDPR/CCPA per AdMob: **User Messaging Platform (UMP)**. È obbligatorio usarlo (o un CMP certificato IAB TCF 2.2) per continuare a usare AdMob in Europa.

**Cosa fa UMP:**
- Mostra automaticamente il banner di consenso agli utenti in EEA e UK
- Gestisce le preferenze granulari (personalizzato / non personalizzato)
- Integra nativamente con AdMob (non mostra ads prima del consenso)
- Salva le preferenze localmente e le rispetta ad ogni avvio

**Integrazione (React Native / Expo):**
Il pacchetto da usare è `react-native-google-mobile-ads` che include il supporto UMP nativo.

```bash
# da valutare al momento dell'integrazione
pnpm add react-native-google-mobile-ads
```

Il flusso standard è:
1. All'avvio dell'app, prima di inizializzare AdMob, chiamare `AdsConsent.requestInfoUpdate()`
2. Se UMP stabilisce che l'utente è in EEA e non ha ancora espresso consenso, mostrare il form con `AdsConsent.showForm()`
3. Solo dopo il consenso (o la sua negazione con conseguente scelta di non-personalizzato), inizializzare AdMob

**Riferimento ufficiale:** https://developers.google.com/admob/ump/android/quick-start

### 4.2 Opzione consenso nell'app

Aggiungere nell'About/Settings un punto che permette all'utente di **revocare o modificare il consenso** in qualsiasi momento, che riapre il form UMP. Questo è un requisito GDPR (art. 7.3: il consenso deve poter essere revocato con la stessa facilità con cui è stato dato).

### 4.3 Comportamento senza consenso

Definire e implementare cosa succede se l'utente nega il consenso:
- Mostrare annunci non personalizzati (contestuali) → richiede impostare `RequestConfiguration` con `TAG_FOR_UNDER_AGE_OF_CONSENT` e non personalizzato
- Non mostrare annunci → opzione più semplice ma meno redditizia
- Decidere questa scelta prima dell'integrazione

---

## 5. Google Play Data Safety — aggiornamenti

La sezione "Data Safety" su Google Play dovrà essere aggiornata prima del rilascio della versione con AdMob. Attualmente dichiara raccolte minime. Con AdMob bisognerà aggiungere:

| Tipo di dato | Raccolto | Condiviso con terze parti | Finalità |
|---|---|---|---|
| Device or other IDs (Advertising ID) | Sì | Sì (Google AdMob) | Advertising |
| App interactions | Sì | Sì (Google AdMob) | Analytics, Advertising |
| Crash logs | No (se non si usa Firebase) | — | — |
| Approximate location (da IP) | Sì (da AdMob) | Sì | Advertising |

> **Nota:** Google Play verifica la coerenza tra quanto dichiarato nel Data Safety e il comportamento effettivo dell'app durante la review. Dichiarare meno di quello che fa l'SDK è causa di rejection o rimozione dell'app.

---

## 6. Riferimenti utili

| Risorsa | URL |
|---|---|
| Google AdMob — Guida all'integrazione Android | https://developers.google.com/admob/android/quick-start |
| Google UMP SDK — Quick start | https://developers.google.com/admob/ump/android/quick-start |
| Google Ad Policy for Apps | https://support.google.com/admob/answer/6128543 |
| IAB TCF 2.2 (standard consenso) | https://iabeurope.eu/tcf-2-0/ |
| GDPR Art. 6 — Basi giuridiche | https://gdpr-info.eu/art-6-gdpr/ |
| GDPR Art. 7 — Condizioni per il consenso | https://gdpr-info.eu/art-7-gdpr/ |
| Garante Privacy italiano | https://www.garanteprivacy.it |
| GitHub General Privacy Statement | https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement |
| Google Privacy Policy | https://policies.google.com/privacy |
| Google DPA (Advertising Products) | https://privacy.google.com/businesses/processorterms/ |
