# 📝 Riepilogo commit `12af5fb`

- **Repository:** realessandroo/hermes-agent
- **Commit:** `12af5fb6c24ca53ff63ee435044b75c94a53d255`
- **Autore:** realessandroo
- **Data:** 2026-10-13 16:06:15 UTC
- **Messaggio:** feat(i18n): localize setup wizard core (setup.py, setup_quick.py)

`hermes setup` ignored display.language: every prompt in the wizard
was hard-coded English. Move the strings of the wizard orchestrator
and the quick / portal / blank-slate / missing-items flows onto the
existing agent.i18n t() catalogs under `setup.*`.

- Labels resolve at call time (SETUP_SECTIONS / _FIRST_TIME_MODES now
  hold catalog keys), so the active profile's language applies.
- Banner rows are padded by _box_line so translated titles keep the
  right border aligned (the old hand-padded rows were one column short).
- en.yaml carries the original English verbatim; it.yaml is fully
  translated; other locales get the English text as placeholder to
  keep catalog parity until translators fill them in.

Remaining setup_* modules and gateway_setup_wizard.py follow in
separate PRs.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>

---

## Sintesi
Il commit internazionalizza il cuore del wizard `hermes setup`, sostituendo le stringhe hard-coded in inglese con chiavi del catalogo `agent.i18n` sotto il namespace `setup.*`, così da rispettare la lingua attiva definita da `display.language`.

## Modifiche principali
- **`hermes_cli/setup.py`**
  - Aggiunto uso di `agent.i18n.t()` per prompt, titoli, messaggi di errore, banner, help e conferme del wizard.
  - Introdotto helper `_lines()` per gestire voci di catalogo multi-linea tramite `split("\n")`.
  - `SETUP_SECTIONS` e `_FIRST_TIME_MODES` ora contengono **chiavi di catalogo** invece di etichette testuali dirette; la risoluzione avviene al momento dell’uso.
  - Aggiunto `_box_line()` per calcolare dinamicamente il padding delle righe del banner, mantenendo il bordo destro allineato anche con testo tradotto.
  - Localizzati i flussi:
    - setup non interattivo;
    - navigazione/annullamento;
    - selezione provider;
    - impostazioni agent;
    - telemetry/shared metrics;
    - reconfigure, quick setup, banner iniziale, riepilogo e messaggi di backup/reset.
  - `run_setup_action_with_navigation()` ora usa il messaggio predefinito `setup.nav.cancelled` se non viene fornito `cancelled_message`.

- **`hermes_cli/setup_quick.py`**
  - Localizzati i flussi:
    - **Nous Portal one-shot**;
    - **Quick Setup** first-time;
    - **Blank Slate**;
    - **missing items**;
    - hint macOS Full Disk Access.
  - Aggiunta costante `_PORTAL_SIGNUP_URL` e riuso di `_lines()`/`_box_line()` dal modulo `setup`.
  - Alcuni blocchi ripetitivi per plugin/MCP sono stati rifattorizzati con chiavi dinamiche `setup.blank.{kind}_*`.

- **`locales/*.yaml`**
  - Aggiunto il namespace `setup` con numerose sottochiavi.
  - `en.yaml` mantiene il testo inglese originale.
  - `it.yaml` risulta completamente tradotto secondo il messaggio del commit.
  - Altre lingue ricevono l’inglese come placeholder per mantenere parità di catalogo in attesa di traduzione.

## Impatto e rischi
- **Nessun breaking change funzionale diretto** sul comportamento del setup: la logica resta invariata, cambia solo la provenienza delle stringhe.
- Possibili problemi se una chiave `setup.*` manca o è errata in un locale: il comportamento dipende dall’implementazione di `t()` e dal fallback.
- Le traduzioni lunghe possono rompere l’allineamento del banner: `_box_line()` usa larghezza fissa e non gestisce troncamento o caratteri wide/emoji/RTL.
- `prompt_yes_no()` localizza le opzioni nel menu curses, ma l’input testuale continua ad accettare solo `y`, `n`, `yes`, `no`: in italiano l’utente potrebbe provare a rispondere `sì` e ricevere errore.
- `SETUP_SECTIONS` e `_FIRST_TIME_MODES` ora espongono chiavi i18n invece di stringhe leggibili: eventuali caller/test che si aspettavano etichette testuali potrebbero richiedere adattamento.
- La copertura i18n è parziale: altri moduli `setup_*` e `gateway_setup_wizard.py` saranno internazionalizzati in PR separati.
- Da verificare la resa reale dei testi multi-linea tradotti, soprattutto per banner, help lunghi e messaggi con segnaposti `{}`.

---
*Generato automaticamente da git-summarizer con `qwen3.8-max`.*
