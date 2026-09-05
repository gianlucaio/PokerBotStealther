# PokerBotStealther v1.3

Libreria anti-ban per mouse e tastiera. Interviene prima che il bot agisca, rendendo i movimenti indistinguibili da quelli umani.

Fa parte dell'ecosistema poker insieme a [PokerBotAgent](../PokerBotAgent/), [PokerTableScope](../PokerTableScope/), [PokerProfileCreator](../PokerProfileCreator/), [PokerMttVirtual](../PokerMttVirtual/) e [PokerBotNexus-Suite](../PokerBotNexus-Suite/) (web app unificata).

## Indice

- [Panoramica](#panoramica)
- [Architettura](#architettura)
- [Installazione](#installazione)
- [Avvio GUI](#avvio-gui)
- [Moduli](#moduli)
- [Uso con PokerBotAgent](#uso-con-pokerbotagent)
- [Struttura progetto](#struttura-progetto)

---

## Panoramica

PokerBotStealther è un middleware che interviene **prima** di ogni azione mouse/tastiera, applicando pattern comportamentali umani:

- **Curva Bezier** per movimento naturale (non dritto)
- **Click con offset gaussiano** (mai esattamente al centro del pulsante)
- **Delay variabili** (post-azione, pre-imback, riflessione)
- **Drift fuori finestra** dopo ogni click
- **Tastiera** per bet sizing con ritmo variabile
- **Idle mouse continuo** (thread parallelo)
- **Trajectory recording/replay** da movimenti reali
- **Budget system** per adattarsi al timer del tavolo

### Profili comportamentali

Ogni sessione viene scelto casualmente un profilo:

| Profilo | Velocità | Latenza | Digitazione |
|---|---|---|---|
| **Cauteloso** | 3-6 | 3-6 | 3-6 |
| **Normale** | 5-8 | 5-8 | 5-8 |
| **Rapido** | 7-10 | 7-10 | 7-10 |

### Velocità regolabili dalla GUI

| Parametro | Min (1) | Max (10) |
|---|---|---|
| Velocità mouse | 5.0s | 0.2s |
| Ritardo digitazione | 0.8s | 0.1s |
| Latenza click | 1.0s | 0.1s |

---

## Architettura

```
eval_engine.py → act.py → PokerBotStealther → PyAutoGUI
                         │
                         ├─ PathBuilder (curva Bezier)
                         ├─ Clicker (offset gaussiano)
                         ├─ TimingManager (delay variabili)
                         ├─ DriftManager (drift fuori finestra)
                         └─ KeyboardManager (tastiera bet sizing)
```

---

## Installazione

```bash
cd ~/Documenti/PokerBotStealther
chmod +x avvio.sh
./avvio.sh
```

Lo script crea automaticamente il virtual environment, installa le dipendenze e apre la GUI.

**Dipendenze:** `pyautogui`, `pyscreeze` (solo Python stdlib + queste)

---

## Avvio GUI

```bash
./avvio.sh
# oppure
source .venv/bin/activate
python3 gui.py
```

### Controlli GUI

- **3 slider** per regolare velocità mouse, digitazione, latenza click
- **3 radio button** per selezionare il profilo (cauteloso/normale/rapido)
- **5 pulsanti test**: Mouse, Click, Drift, Tastiera, Tutto
- **Log** con timestamp
- **Status bar**

---

## Moduli

| Modulo | Funzione |
|---|---|
| `bezier.py` | Curve Bezier cubiche con easing, step 7-20px |
| `path.py` | Costruttore percorso completo A→B con variabile delay |
| `clicker.py` | Click con offset gaussiano (sigma=bounds/4) |
| `timing.py` | Delay post-azione, pre-imback, riflessione, pre-typing, typing |
| `drift.py` | Drift fuori finestra (parte inferiore destra) |
| `profiler.py` | 3 profili, budget system, downgrade automatico |
| `keyboard.py` | Click input field + Ctrl+A + digitazione ritmata + Enter |
| `gui.py` | GUI Tkinter con slider, test, log |
| `idle.py` | Thread parallelo per drift continuo |
| `record.py` | Trajectory recording a 60Hz |
| `replay.py` | Trajectory replay con deformazioni |

---

## Uso con PokerBotAgent

PokerBotStealther è già integrato in `act.py` di PokerBotAgent. L'integrazione è opzionale:

- Se PokerBotStealther è nel percorso `../PokerBotStealther/`, viene caricato automaticamente
- Se non è disponibile, act.py usa pyautogui diretto (fallback)

**Flusso integrato:**

```
1. eval_engine dice "RAISE 200"
2. act.click_by_action("RAISE", sizing="200")
3. PokerBotStealther applica:
   - Riflessione (0.5-2.5s)
   - Movimento Bezier (curva naturale)
   - Click gaussiano (offset casuale)
   - Post-delay (0.1-0.5s)
   - Drift fuori finestra
4. pyautogui esegue fisicamente
```

---

## Struttura progetto

```
PokerBotStealther/
├── avvio.sh              # Launcher
├── requirements.txt      # pyautogui, pyscreeze
├── __init__.py           # Package init
├── bezier.py             # Curve Bezier + easing
├── path.py               # Costruttore percorso
├── clicker.py            # Click gaussiano
├── timing.py             # Delay variabili
├── drift.py              # Drift fuori finestra
├── profiler.py           # Profili + budget
├── keyboard.py           # Gestione tastiera
├── gui.py                # GUI Tkinter
├── idle.py               # Idle mouse thread
├── record.py             # Trajectory recording
├── replay.py             # Trajectory replay
├── .venv/                # Virtual environment
├── GUIDA_MASTER_*.md     # Guida completa
└── PIANO_OPERATIVO_*.md  # Piano di sviluppo
```

---

## Note

- **Target:** iPoker/Peoples (anti-ban basso). PokerStars/888 usano spyware kernel — non supportati.
- **L'utente è presente** al PC mentre il bot gioca. Il tool serve a rendere i click meno rilevabili, non a nascondere il bot completamente.
- **La tastiera** non ha firma comportamentale — un carattere è un carattere, non distinguibile tra umano e bot.# PokerBotStealther
Tool for Stealth fot PokerBotAgent on windows poker rooms(not necessary on linux)
