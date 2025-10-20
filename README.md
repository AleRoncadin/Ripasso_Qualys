# FONDAMENTI QUALYS

## Cos'è Qualys?
Qualys è una piattaforma cloud-based leader nel settore della sicurezza informatica e compliance. Fondata nel 1999, offre soluzioni per identificare, valutare e proteggere le risorse IT dalle vulnerabilità e dalle minacce informatiche.
Caratteristiche principali:

Cloud-native: tutto funziona dal cloud, niente da installare on-premise (tranne gli scanner)
Scalabile: può gestire da poche decine a milioni di asset
Multi-modulo: non solo vulnerability management, ma anche compliance, web app scanning, container security, ecc.

## Come Funziona Qualys?

[Asset da scansionare] ←→ [Qualys Scanner] ←→ [Qualys Cloud Platform] ←→ [Utente/API]


1. **Scanner Qualys**: applicazione leggera installata nella tua rete che esegue le scansioni
2. **Cloud Platform**: cervello centrale dove vengono analizzati i dati
3. **Interfaccia Web/API**: come accedi ai risultati e gestisci le scansioni

### Tipi di Scansioni

**Vulnerability Scan (scansione vulnerabilità)**
- Analizza gli asset per trovare vulnerabilità note
- Controlla patch mancanti, configurazioni errate, porte aperte
- Restituisce un report con severity score (1-5 o CVSS)

**Compliance Scan**
- Verifica la conformità a standard (PCI-DSS, HIPAA, CIS benchmarks)
- Controlla configurazioni rispetto a policy aziendali

**Discovery Scan**
- "Censimento" della rete: trova tutti i dispositivi presenti
- Identifica sistema operativo, servizi attivi, porte aperte

## Concetti Fondamentali

### 1. Asset
Un **asset** è qualsiasi risorsa IT che vuoi proteggere:
- Server (fisici o virtuali)
- Workstation
- Dispositivi di rete (router, switch, firewall)
- Applicazioni web
- Container e cloud instances

Ogni asset ha:
- **IP address** (identificativo principale)
- **Hostname**
- **Sistema operativo**
- **Tracking method** (come Qualys lo monitora)

### 2. Vulnerabilità (Vulnerability)

Una **vulnerabilità** è una debolezza nel sistema che può essere sfruttata da un attaccante.

**Elementi chiave:**
- **QID (Qualys ID)**: identificativo univoco della vulnerabilità in Qualys
- **CVE (Common Vulnerabilities and Exposures)**: identificativo standard internazionale (es: CVE-2024-1234)
- **Severity**: gravità (1-5, oppure CVSS score 0-10)
- **CVSS Score**: punteggio standardizzato di rischio
- **Exploit available**: esiste già un exploit pubblico?
- **Patch available**: esiste una patch per risolvere?

### 3. Scan (Scansione)

Una **scan** è il processo di analisi degli asset per trovare vulnerabilità.

**Tipi di scan:**
- **Authenticated scan**: con credenziali (più approfondita)
- **Unauthenticated scan**: senza credenziali (solo esterna)
- **Network scan**: dalla rete
- **Agent-based scan**: tramite agente installato sull'asset

**Fasi di una scansione:**
1. **Discovery**: identifica asset attivi
2. **Port scanning**: trova porte aperte
3. **Service detection**: identifica servizi in esecuzione
4. **Vulnerability detection**: cerca vulnerabilità note
5. **Reporting**: genera il report

### 4. Remediation (Rimedio)

**Remediation** è il processo di correzione delle vulnerabilità trovate.

**Azioni tipiche:**
- Applicare patch/aggiornamenti
- Modificare configurazioni
- Disabilitare servizi non necessari
- Implementare controlli compensativi (se la patch non è disponibile)

### 5. Asset Groups e Option Profiles

**Asset Groups**: raggruppamenti logici di asset
- Per location (es: "Server Roma", "Workstation Milano")
- Per funzione (es: "Web Servers", "Database Servers")
- Per criticità (es: "Production", "Development")

**Option Profiles**: configurazioni di scansione predefinite
- Tipo di scan da eseguire
- Porte da controllare
- Livello di approfondimento
- Timeout e performance

---

# Infrastrutture Critiche & Sicurezza

## Cosa sono le Infrastrutture Critiche?

Le **infrastrutture critiche** sono sistemi e asset (fisici o virtuali) essenziali per il funzionamento di una società, la cui distruzione o compromissione avrebbe un impatto grave sulla sicurezza nazionale, l'economia, la salute pubblica o la sicurezza.

### Settori delle Infrastrutture Critiche

**In Europa (secondo la Direttiva NIS2):**
1. **Energia**: centrali elettriche, reti di distribuzione, raffinerie
2. **Trasporti**: aeroporti, porti, ferrovie, metropolitane
3. **Banche e finanza**: sistemi di pagamento, borse valori
4. **Sanità**: ospedali, sistemi sanitari nazionali
5. **Acqua**: acquedotti, impianti di trattamento
6. **Telecomunicazioni**: reti telefoniche, internet backbone
7. **Pubblica amministrazione**: sistemi governativi
8. **Spazio**: satelliti, sistemi di navigazione (GPS)
9. **Alimentare**: produzione e distribuzione cibo
10. **Digitale**: cloud providers, data centers, DNS

### Perché sono "Critiche"?

**Esempio pratico:**
- Un attacco alla rete elettrica → città senza corrente → ospedali in emergenza → caos sociale
- Un attacco ai sistemi bancari → impossibile fare pagamenti → blocco economico
- Un attacco agli acquedotti → contaminazione acqua potabile → crisi sanitaria

## Caratteristiche delle Infrastrutture Critiche

### 1. Alta Disponibilità (High Availability)
- Devono funzionare 24/7/365
- Downtime = impatto grave
- Ridondanza e failover obbligatori

### 2. Interdipendenze
- Un settore dipende da altri (ospedali dipendono da energia e acqua)
- Effetto domino: un problema si propaga

### 3. Legacy Systems
- Spesso usano tecnologie vecchie (SCADA, sistemi industriali anni '80-'90)
- Difficili da aggiornare (rischio di blocco produzione)
- Non progettati per la sicurezza informatica

### 4. Obiettivi Primari per Attacchi
- **Cyberterrorismo**: gruppi terroristici
- **Nation-state attacks**: attacchi sponsorizzati da stati
- **Cybercrime**: ransomware per estorcere denaro

---

## Framework di Sicurezza

### 1. NIST Cybersecurity Framework

Il **NIST Framework** (sviluppato dal National Institute of Standards and Technology USA) è lo standard de facto per la cybersecurity.

**Le 5 funzioni core:**

#### IDENTIFY (Identificare)
- Inventario degli asset critici
- Valutazione dei rischi
- Comprensione dell'ambiente IT

**Nel tuo progetto:**
- Identificare tutti gli asset da scansionare con Qualys
- Classificare asset per criticità

#### PROTECT (Proteggere)
- Implementare controlli di sicurezza
- Access control, formazione utenti
- Protezione dei dati

**Nel tuo progetto:**
- Automatizzare l'applicazione di patch
- Configurazioni sicure

#### DETECT (Rilevare)
- Monitoraggio continuo
- Detection di anomalie e attacchi
- Vulnerability scanning

**Nel tuo progetto:**
- Scansioni automatiche regolari con Qualys
- Alert su nuove vulnerabilità critiche

#### RESPOND (Rispondere)
- Piano di incident response
- Comunicazione durante incident
- Mitigazione dell'impatto

**Nel tuo progetto:**
- Workflow automatizzato per notifiche
- Escalation vulnerabilità critiche

#### RECOVER (Recuperare)
- Ripristino servizi
- Lessons learned
- Miglioramento continuo

### 2. ISO 27001

Standard internazionale per la gestione della sicurezza delle informazioni (ISMS - Information Security Management System).

**Principi chiave:**
- **Risk-based approach**: focus sui rischi reali
- **PDCA cycle**: Plan-Do-Check-Act (miglioramento continuo)
- **Documentazione**: tutto deve essere documentato

**Controlli rilevanti per il tuo progetto:**
- A.12.6: Gestione delle vulnerabilità tecniche
- A.18.2: Compliance con requisiti legali

---

## CVSS - Common Vulnerability Scoring System

Il **CVSS** è il sistema standardizzato per valutare la gravità delle vulnerabilità.

### CVSS Score: 0.0 - 10.0

**Classificazione:**
- **0.1 - 3.9**: Low (Bassa)
- **4.0 - 6.9**: Medium (Media)
- **7.0 - 8.9**: High (Alta)
- **9.0 - 10.0**: Critical (Critica)

### Metriche CVSS v3

**Base Metrics (caratteristiche intrinseche):**

1. **Attack Vector (AV)** - Da dove può avvenire l'attacco?
   - Network (N): da remoto via internet
   - Adjacent (A): stessa rete locale
   - Local (L): accesso locale richiesto
   - Physical (P): accesso fisico necessario

2. **Attack Complexity (AC)** - Quanto è difficile?
   - Low (L): facile da sfruttare
   - High (H): condizioni speciali necessarie

3. **Privileges Required (PR)** - Servono privilegi?
   - None (N): nessuno
   - Low (L): utente base
   - High (H): amministratore

4. **User Interaction (UI)** - Serve azione utente?
   - None (N): automatico
   - Required (R): utente deve fare qualcosa (es: cliccare link)

5. **Impact** - Cosa viene compromesso?
   - **Confidentiality**: perdita riservatezza dati
   - **Integrity**: modifica non autorizzata dati
   - **Availability**: servizio non disponibile
   
   Ognuno: None / Low / High

---

## Ciclo di Vita della Vulnerabilità

### 1. Discovery (Scoperta)
- Ricercatore trova una vulnerabilità
- Vendor viene informato (responsible disclosure)

### 2. Publication (Pubblicazione)
- Assegnazione CVE ID
- Pubblicazione advisory
- **Window of Exposure inizia**: tutti sanno della vulnerabilità

### 3. Exploit Development
- Hacker sviluppano exploit
- Exploit pubblicati (Metasploit, ExploitDB)
- **Finestra di rischio massimo**

### 4. Patch Release
- Vendor rilascia patch
- **Window of Exposure si riduce** (per chi applica patch)

### 5. Remediation
- Organizzazioni applicano patch
- Vulnerability scanner rilevano asset non patchati

### 6. End of Life
- Vulnerabilità considerata "vecchia"
- Ma sistemi legacy potrebbero essere ancora vulnerabili

**Il tuo progetto automatizza le fasi 5 (detection) e supporta la remediation!**

---

## Patch Management

Il **patch management** è il processo di gestione degli aggiornamenti software.

### Fasi del Patch Management

1. **Inventory**: sapere cosa hai (asset inventory)
2. **Assessment**: quali patch servono? (vulnerability scanning)
3. **Testing**: testare patch in ambiente di test
4. **Deployment**: applicare in produzione
5. **Verification**: verificare che sia applicata correttamente
6. **Reporting**: documentare tutto

### Sfide nelle Infrastrutture Critiche

**Problema principale:** downtime non accettabile

- **Soluzione 1**: Finestre di manutenzione programmate (es: domenica notte)
- **Soluzione 2**: Patching in rolling (a scaglioni)
- **Soluzione 3**: Controlli compensativi temporanei (firewall rules, segmentazione)

**Problema 2:** Sistemi legacy incompatibili con patch

- **Soluzione**: Isolamento di rete, monitoring rafforzato, controlli aggiuntivi

---

## Best Practices per VM in Infrastrutture Critiche

### 1. Frequenza Scansioni
- **Asset critici**: settimanale o continuo (con agenti)
- **Asset normali**: mensile
- **Dopo cambiamenti importanti**: scan immediato

### 2. Prioritizzazione
Non tutte le vulnerabilità sono uguali:

**Formula di rischio:**

Risk = Likelihood × Impact

Dove:
Likelihood = CVSS Score × (Exploit available? 1.5 : 1.0)
Impact = Criticità asset (1-5)
