# PowerShell - Guida Completa

Una guida pratica per PowerShell, essenziale per l'amministrazione di sistemi Windows e automazione IT.

## Indice

- [Introduzione a PowerShell](#introduzione-a-powershell)
- [Setup e Primi Passi](#setup-e-primi-passi)
- [Cmdlet - Comandi Base](#cmdlet---comandi-base)
- [Variabili e Tipi di Dati](#variabili-e-tipi-di-dati)
- [Pipeline e Oggetti](#pipeline-e-oggetti)
- [Operatori](#operatori)
- [Strutture di Controllo](#strutture-di-controllo)
- [Funzioni](#funzioni)
- [File e Directory](#file-e-directory)
- [Testo e Stringhe](#testo-e-stringhe)
- [Array e HashTable](#array-e-hashtable)
- [Gestione Errori](#gestione-errori)
- [Script PowerShell](#script-powershell)
- [Lavorare con API (REST)](#lavorare-con-api-rest)
- [XML e JSON](#xml-e-json)
- [CSV e Export Dati](#csv-e-export-dati)
- [Scheduling e Automazione](#scheduling-e-automazione)
- [Remoting](#remoting)
- [Best Practices](#best-practices)
- [Integrazione con Python](#integrazione-con-python)
- [Esempi Completi Qualys](#esempi-completi-qualys)

---

## Introduzione a PowerShell

### Cos'è PowerShell?

**PowerShell** è una shell da riga di comando e linguaggio di scripting sviluppato da Microsoft, progettato per l'automazione e la configurazione di sistemi.

**Caratteristiche principali:**
- **Object-oriented**: Lavora con oggetti .NET, non solo testo
- **Cross-platform**: PowerShell Core funziona su Windows, Linux, macOS
- **Verb-Noun syntax**: Comandi intuitivi (Get-Process, Set-Item, etc.)
- **Pipeline potente**: Passa oggetti tra comandi
- **Integrato con Windows**: Accesso completo al sistema operativo

### PowerShell vs CMD vs Bash

| Caratteristica | CMD | Bash | PowerShell |
|----------------|-----|------|------------|
| Oggetti | ❌ Testo | ❌ Testo | ✅ Oggetti .NET |
| Cross-platform | ❌ Solo Windows | ✅ Unix/Linux | ✅ Core: tutti |
| Scripting | ⚠️ Limitato | ✅ Potente | ✅ Molto potente |
| API .NET | ❌ | ❌ | ✅ Accesso completo |
| Sintassi | Criptica | Unix-style | Verb-Noun |

### Versioni PowerShell

- **Windows PowerShell 5.1**: Pre-installato su Windows 10/11 (solo Windows)
- **PowerShell Core 6+**: Cross-platform, open source
- **PowerShell 7+**: Versione corrente, consigliata

---

## Setup e Primi Passi

### Aprire PowerShell

**Windows:**
```powershell
# Cerca "PowerShell" nel menu Start

# Oppure Win+R, poi:
powershell

# PowerShell come Amministratore
# Click destro > Esegui come amministratore
```

**PowerShell Core (cross-platform):**
```bash
# Installare PowerShell 7+ da:
# https://github.com/PowerShell/PowerShell/releases

# Avviare
pwsh
```

### Verifica Versione

```powershell
# Versione PowerShell
$PSVersionTable

# Output:
# Name                           Value
# ----                           -----
# PSVersion                      7.4.0
# PSEdition                      Core
# ...

# Solo versione numero
$PSVersionTable.PSVersion
```

### Editor e IDE

**Editor consigliati:**
- **Visual Studio Code** con estensione PowerShell (migliore)
- **PowerShell ISE** (solo Windows, legacy)
- **Windows Terminal** (terminale moderno)

### Help System

```powershell
# Aggiornare help (prima volta)
Update-Help

# Help su comando
Get-Help Get-Process
Get-Help Get-Process -Full
Get-Help Get-Process -Examples
Get-Help Get-Process -Online

# Cercare comando
Get-Help *process*
Get-Command *service*

# Help concetti
Get-Help about_Variables
Get-Help about_If
Get-Help about_Functions
```

### Execution Policy

PowerShell ha policy di sicurezza per esecuzione script:

```powershell
# Verificare policy corrente
Get-ExecutionPolicy

# Policy disponibili:
# - Restricted: nessuno script (default Windows)
# - AllSigned: solo script firmati
# - RemoteSigned: script locali ok, remoti firmati
# - Unrestricted: tutti gli script
# - Bypass: nessuna restrizione

# Cambiare policy (come Amministratore)
Set-ExecutionPolicy RemoteSigned

# Bypass temporaneo per uno script
powershell -ExecutionPolicy Bypass -File script.ps1
```

---

## Cmdlet - Comandi Base

### Struttura Cmdlet

PowerShell usa sintassi **Verb-Noun**:

```powershell
Get-Process    # Verb: Get, Noun: Process
Set-Location   # Verb: Set, Noun: Location
New-Item       # Verb: New, Noun: Item
```

**Verbi comuni:**
- `Get`: Recupera informazioni
- `Set`: Modifica configurazione
- `New`: Crea nuovo elemento
- `Remove`: Elimina elemento
- `Start/Stop`: Avvia/ferma servizio
- `Import/Export`: Importa/esporta dati

### Alias

PowerShell ha alias per compatibilità:

```powershell
# Equivalenze comuni
ls      → Get-ChildItem
dir     → Get-ChildItem
cd      → Set-Location
pwd     → Get-Location
cat     → Get-Content
cp      → Copy-Item
mv      → Move-Item
rm      → Remove-Item
cls     → Clear-Host

# Vedere tutti gli alias
Get-Alias

# Trovare alias di un comando
Get-Alias -Definition Get-ChildItem

# Creare alias
Set-Alias ll Get-ChildItem
```

### Cmdlet Essenziali

```powershell
# Navigazione
Get-Location              # Directory corrente (pwd)
Set-Location C:\Windows   # Cambia directory (cd)
Get-ChildItem             # Lista file e directory (ls/dir)

# File system
Get-Content file.txt      # Leggi file (cat)
Set-Content file.txt      # Scrivi file
Add-Content file.txt      # Appendi a file
Copy-Item src dst         # Copia file (cp)
Move-Item src dst         # Sposta file (mv)
Remove-Item file          # Elimina file (rm)
New-Item -ItemType File   # Crea file
New-Item -ItemType Directory  # Crea directory

# Processi
Get-Process               # Lista processi
Stop-Process -Name notepad
Start-Process notepad.exe

# Servizi
Get-Service               # Lista servizi
Start-Service servicename
Stop-Service servicename
Restart-Service servicename

# Output
Write-Host "Messaggio"    # Stampa (colorato)
Write-Output "Dato"       # Output su pipeline
Clear-Host                # Pulisci schermo (cls)

# Misurazione
Measure-Object            # Conta, somma, media
Get-ChildItem | Measure-Object  # Conta file

# Ordinamento e filtro
Sort-Object               # Ordina
Where-Object              # Filtra
Select-Object             # Seleziona proprietà
```

---

## Variabili e Tipi di Dati

### Dichiarazione Variabili

```powershell
# Variabili iniziano con $
$nome = "Mario"
$eta = 30
$attivo = $true

# Tipizzazione dinamica
$var = 123        # Int32
$var = "testo"    # String (riassegnabile)

# Tipizzazione forte (casting)
[int]$numero = 42
[string]$testo = "Hello"
[bool]$flag = $true

# Multiple assignment
$a, $b, $c = 1, 2, 3
```

### Tipi di Dati

```powershell
# Stringhe
$str = "Hello World"
$str = 'Single quote'
$multiline = @"
Testo su
più righe
"@

# Numeri
$int = 42
$long = 1234567890L
$float = 3.14
$decimal = 19.99D

# Boolean
$true
$false

# Null
$null

# Date
$oggi = Get-Date
$data = [datetime]"2024-01-20"

# Array
$array = @(1, 2, 3, 4, 5)
$mixed = @("testo", 123, $true)

# HashTable (dizionario)
$hash = @{
    Nome = "Mario"
    Eta = 30
    Attivo = $true
}

# Verificare tipo
$var.GetType()
$var -is [string]
$var -is [int]
```

### Variabili Speciali

```powershell
# Variabili automatiche
$PSVersionTable    # Versione PowerShell
$HOME             # Home directory
$PWD              # Directory corrente
$_                # Oggetto corrente in pipeline
$?                # Successo ultimo comando
$LastExitCode     # Exit code ultimo programma
$Args             # Argomenti script
$PSScriptRoot     # Directory dello script
$PSCommandPath    # Path completo script

# Variabili ambiente
$env:USERNAME     # Nome utente
$env:COMPUTERNAME # Nome computer
$env:PATH         # Path sistema
$env:TEMP         # Directory temporanea

# Impostare variabile ambiente
$env:MY_VAR = "valore"
```

### Scope Variabili

```powershell
# Scope locale (default)
$locale = "valore"

# Scope globale
$global:globale = "valore"

# Scope script
$script:scriptvar = "valore"

# Scope private
$private:privata = "valore"
```

---

## Pipeline e Oggetti

### Pipeline - Il Cuore di PowerShell

La pipeline passa **oggetti** (non testo!) tra comandi:

```powershell
# Pipeline base
Get-Process | Where-Object {$_.CPU -gt 100}

# Get-Process restituisce oggetti Process
# Where-Object filtra oggetti
# Ogni oggetto ha proprietà (CPU, Name, etc.)
```

### Esplorare Oggetti

```powershell
# Ottenere proprietà oggetto
Get-Process | Get-Member

# Selezionare proprietà specifiche
Get-Process | Select-Object Name, CPU, Id

# Prime 5 proprietà
Get-Process | Select-Object -First 5

# Ultime 3
Get-Process | Select-Object -Last 3

# Proprietà custom
Get-Process | Select-Object Name, @{Name="CPUTime"; Expression={$_.CPU}}
```

### Where-Object (Filtro)

```powershell
# Sintassi completa
Get-Process | Where-Object {$_.Name -eq "powershell"}

# Sintassi abbreviata
Get-Process | Where-Object Name -eq "powershell"
Get-Process | ? Name -eq "powershell"  # alias

# Filtri multipli
Get-Service | Where-Object {$_.Status -eq "Running" -and $_.StartType -eq "Automatic"}

# Esempi
Get-ChildItem | Where-Object {$_.Length -gt 1MB}
Get-Process | Where-Object {$_.CPU -gt 100}
Get-Service | Where-Object Status -eq "Stopped"
```

### ForEach-Object (Iterazione)

```powershell
# Sintassi completa
Get-Process | ForEach-Object {$_.Name.ToUpper()}

# Sintassi abbreviata
Get-Process | ForEach-Object Name
Get-Process | % Name  # alias

# Con azioni
Get-ChildItem *.txt | ForEach-Object {
    $content = Get-Content $_.FullName
    Write-Host "File: $($_.Name) - Lines: $($content.Count)"
}

# Esempio pratico
1..10 | ForEach-Object {
    Write-Host "Numero: $_"
}
```

### Sort-Object (Ordinamento)

```powershell
# Ordinamento crescente
Get-Process | Sort-Object CPU

# Decrescente
Get-Process | Sort-Object CPU -Descending

# Multiple proprietà
Get-ChildItem | Sort-Object LastWriteTime, Name

# Solo valori unici
Get-Process | Select-Object Company | Sort-Object -Unique
```

### Group-Object (Raggruppamento)

```powershell
# Raggruppa per proprietà
Get-Service | Group-Object Status

# Con conteggio
Get-Process | Group-Object Company | Sort-Object Count -Descending

# Formattato
Get-ChildItem | Group-Object Extension | Select-Object Name, Count
```

### Measure-Object (Statistiche)

```powershell
# Conta elementi
Get-ChildItem | Measure-Object

# Somma, media, min, max
Get-Process | Measure-Object CPU -Sum -Average -Maximum -Minimum

# Conta righe file
Get-Content file.txt | Measure-Object -Line -Word -Character
```

---

## Operatori

### Operatori Aritmetici

```powershell
# Base
$a = 10 + 5      # 15 addizione
$b = 10 - 5      # 5  sottrazione
$c = 10 * 5      # 50 moltiplicazione
$d = 10 / 5      # 2  divisione
$e = 10 % 3      # 1  modulo (resto)

# Incremento/Decremento
$i++             # incremento
$i--             # decremento
$i += 5          # $i = $i + 5
$i -= 3          # $i = $i - 3
$i *= 2          # $i = $i * 2
$i /= 2          # $i = $i / 2
```

### Operatori di Confronto

```powershell
# Uguaglianza
$a -eq $b        # uguale
$a -ne $b        # diverso
$a -gt $b        # maggiore
$a -lt $b        # minore
$a -ge $b        # maggiore o uguale
$a -le $b        # minore o uguale

# Case-sensitive (aggiungere 'c')
$a -ceq $b       # case-sensitive equal
$a -cne $b       # case-sensitive not equal

# Esempi
5 -eq 5          # True
"abc" -eq "ABC"  # True (case-insensitive default)
"abc" -ceq "ABC" # False (case-sensitive)

# Like e Match (pattern)
"Hello" -like "H*"        # True (wildcard)
"Hello" -match "^H"       # True (regex)
"test123" -match "\d+"    # True (contiene numeri)
```

### Operatori Logici

```powershell
# AND, OR, NOT
$a -and $b       # AND logico
$a -or $b        # OR logico
-not $a          # NOT logico
!$a              # NOT (alternativo)

# Esempi
($a -gt 5) -and ($b -lt 10)
($status -eq "OK") -or ($status -eq "WARNING")
-not $error
```

### Operatori su Stringhe

```powershell
# Concatenazione
$full = $first + " " + $last
$full = "$first $last"            # interpolazione

# Ripetizione
$stars = "*" * 50

# Confronto
"abc" -eq "ABC"     # True (case-insensitive)
"abc" -ceq "ABC"    # False (case-sensitive)

# Contains
"Hello World" -like "*World*"
"test@email.com" -match "@.*\."

# Replace
$text -replace "old", "new"
```

### Operatori su Array

```powershell
# Contains
$array -contains "valore"
"valore" -in $array

# Join
$array -join ", "
1,2,3,4,5 -join "-"   # "1-2-3-4-5"

# Split
"a,b,c" -split ","    # @("a", "b", "c")
```

---

## Strutture di Controllo

### If-ElseIf-Else

```powershell
# If base
if ($eta -ge 18) {
    Write-Host "Maggiorenne"
}

# If-Else
if ($eta -ge 18) {
    Write-Host "Maggiorenne"
} else {
    Write-Host "Minorenne"
}

# If-ElseIf-Else
if ($voto -ge 90) {
    Write-Host "Ottimo"
} elseif ($voto -ge 75) {
    Write-Host "Buono"
} elseif ($voto -ge 60) {
    Write-Host "Sufficiente"
} else {
    Write-Host "Insufficiente"
}

# Condizioni multiple
if (($eta -ge 18) -and ($patente -eq $true)) {
    Write-Host "Può guidare"
}

# Operatore ternario (PowerShell 7+)
$result = $condition ? "vero" : "falso"
```

### Switch

```powershell
# Switch base
switch ($giorno) {
    "Lunedì"   { Write-Host "Inizio settimana" }
    "Venerdì"  { Write-Host "Quasi weekend!" }
    "Sabato"   { Write-Host "Weekend!" }
    "Domenica" { Write-Host "Weekend!" }
    default    { Write-Host "Giorno feriale" }
}

# Switch con wildcard
switch -Wildcard ($filename) {
    "*.txt"  { Write-Host "File di testo" }
    "*.ps1"  { Write-Host "Script PowerShell" }
    "*.csv"  { Write-Host "File CSV" }
    default  { Write-Host "Tipo sconosciuto" }
}

# Switch con regex
switch -Regex ($input) {
    "^\d+$"           { Write-Host "Numero" }
    "^[a-zA-Z]+$"     { Write-Host "Lettere" }
    "^\w+@\w+\.\w+$"  { Write-Host "Email" }
}

# Switch su array
switch (1,2,3,4,5) {
    {$_ -gt 3} { Write-Host "$_ è maggiore di 3" }
}
```

### For Loop

```powershell
# For classico
for ($i = 0; $i -lt 10; $i++) {
    Write-Host "Iterazione $i"
}

# Decremento
for ($i = 10; $i -gt 0; $i--) {
    Write-Host $i
}

# Step personalizzato
for ($i = 0; $i -le 100; $i += 10) {
    Write-Host $i
}

# Multiple variabili
for ($i = 0, $j = 10; $i -lt 10; $i++, $j--) {
    Write-Host "i=$i, j=$j"
}
```

### ForEach Loop

```powershell
# ForEach su array
$nomi = @("Mario", "Luigi", "Anna")
foreach ($nome in $nomi) {
    Write-Host "Ciao $nome"
}

# ForEach su range
foreach ($i in 1..10) {
    Write-Host "Numero: $i"
}

# ForEach su file
foreach ($file in Get-ChildItem *.txt) {
    Write-Host $file.Name
}

# Con indice
$array = @("a", "b", "c")
for ($i = 0; $i -lt $array.Count; $i++) {
    Write-Host "[$i] = $($array[$i])"
}
```

### While Loop

```powershell
# While
$i = 0
while ($i -lt 10) {
    Write-Host $i
    $i++
}

# While con condizione complessa
$risposta = ""
while ($risposta -ne "si" -and $risposta -ne "no") {
    $risposta = Read-Host "Risposta (si/no)"
}

# Do-While (esegue almeno una volta)
$numero = 0
do {
    $numero = Get-Random -Minimum 1 -Maximum 100
    Write-Host "Numero: $numero"
} while ($numero -ne 50)

# Do-Until
$counter = 0
do {
    Write-Host $counter
    $counter++
} until ($counter -eq 5)
```

### Break e Continue

```powershell
# Break (esci dal loop)
foreach ($i in 1..10) {
    if ($i -eq 5) {
        break
    }
    Write-Host $i
}

# Continue (salta iterazione)
foreach ($i in 1..10) {
    if ($i % 2 -eq 0) {
        continue  # salta numeri pari
    }
    Write-Host $i  # stampa solo dispari
}

# Break con label
:outer foreach ($i in 1..3) {
    foreach ($j in 1..3) {
        if ($i -eq 2 -and $j -eq 2) {
            break outer  # esci da entrambi i loop
        }
        Write-Host "$i,$j"
    }
}
```

---

## Funzioni

### Dichiarazione Base

```powershell
# Funzione semplice
function Say-Hello {
    Write-Host "Hello World!"
}

# Chiamata
Say-Hello

# Funzione con parametri
function Say-Hello {
    param(
        $Nome
    )
    Write-Host "Ciao $Nome!"
}

Say-Hello -Nome "Mario"
Say-Hello "Mario"  # posizionale
```

### Parametri Avanzati

```powershell
# Parametri tipizzati e obbligatori
function Get-Square {
    param(
        [Parameter(Mandatory=$true)]
        [int]$Numero
    )
    
    return $Numero * $Numero
}

# Con valori default
function Say-Hello {
    param(
        [string]$Nome = "Utente",
        [string]$Saluto = "Ciao"
    )
    
    Write-Host "$Saluto $Nome!"
}

Say-Hello                          # Ciao Utente!
Say-Hello -Nome "Mario"            # Ciao Mario!
Say-Hello -Nome "Mario" -Saluto "Buongiorno"  # Buongiorno Mario!

# Parametri multipli tipizzati
function Add-Numbers {
    param(
        [Parameter(Mandatory=$true)]
        [int]$Numero1,
        
        [Parameter(Mandatory=$true)]
        [int]$Numero2
    )
    
    return $Numero1 + $Numero2
}

$result = Add-Numbers -Numero1 5 -Numero2 3
```

### Return e Output

```powershell
# Return esplicito
function Get-Double {
    param([int]$Num)
    return $Num * 2
}

# Output implicito (tutto viene restituito)
function Get-Numbers {
    1
    2
    3
    # Restituisce array @(1,2,3)
}

# Write-Output (esplicito su pipeline)
function Get-Data {
    Write-Output "Dato 1"
    Write-Output "Dato 2"
}

# Write-Host NON va in pipeline (solo console)
function Show-Info {
    Write-Host "Questo va solo a video"
    Write-Output "Questo va in pipeline"
}
```

### Funzioni Avanzate

```powershell
# Funzione avanzata (CmdletBinding)
function Get-UserInfo {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true, ValueFromPipeline=$true)]
        [string]$Username,
        
        [Parameter()]
        [switch]$Detailed
    )
    
    begin {
        Write-Verbose "Inizio elaborazione"
    }
    
    process {
        $user = Get-ADUser $Username
        
        if ($Detailed) {
            return $user | Select-Object *
        } else {
            return $user | Select-Object Name, Email
        }
    }
    
    end {
        Write-Verbose "Elaborazione completata"
    }
}

# Uso
Get-UserInfo -Username "mario.rossi"
Get-UserInfo -Username "mario.rossi" -Detailed
Get-UserInfo -Username "mario.rossi" -Verbose

# Da pipeline
"mario.rossi", "luigi.verdi" | Get-UserInfo
```

### Parameter Validation

```powershell
function Set-ServerConfig {
    param(
        # Obbligatorio
        [Parameter(Mandatory=$true)]
        [string]$ServerName,
        
        # Solo valori specifici
        [Parameter()]
        [ValidateSet("Development", "Testing", "Production")]
        [string]$Environment = "Development",
        
        # Range numerico
        [Parameter()]
        [ValidateRange(1, 100)]
        [int]$Timeout = 30,
        
        # Lunghezza stringa
        [Parameter()]
        [ValidateLength(3, 20)]
        [string]$Username,
        
        # Pattern regex
        [Parameter()]
        [ValidatePattern("^[A-Z]{2}\d{4}$")]
        [string]$Code,
        
        # Script validation custom
        [Parameter()]
        [ValidateScript({Test-Path $_})]
        [string]$ConfigPath
    )
    
    # Logica funzione
}
```

---

## File e Directory

### Navigazione

```powershell
# Directory corrente
Get-Location
pwd

# Cambiare directory
Set-Location C:\Windows
cd C:\Windows

# Tornare indietro
cd ..

# Directory home
cd ~
cd $HOME

# Lista file e directory
Get-ChildItem
ls
dir

# Ricorsivo
Get-ChildItem -Recurse

# Solo file
Get-ChildItem -File

# Solo directory
Get-ChildItem -Directory

# Pattern
Get-ChildItem *.txt
Get-ChildItem -Filter "*.ps1"
```

### Creare e Eliminare

```powershell
# Creare file
New-Item -ItemType File -Name "test.txt"
New-Item -ItemType File -Path "C:\temp\file.txt" -Force

# Creare directory
New-Item -ItemType Directory -Name "cartella"
mkdir nuova_cartella

# Eliminare
Remove-Item file.txt
Remove-Item cartella -Recurse  # con contenuto
Remove-Item * -Include *.tmp   # pattern

# Eliminazione sicura (conferma)
Remove-Item file.txt -Confirm

# Forza eliminazione
Remove-Item file.txt -Force
```

### Copiare e Spostare

```powershell
# Copiare file
Copy-Item source.txt destination.txt
cp file.txt backup_file.txt

# Copiare directory
Copy-Item C:\Source C:\Destination -Recurse

# Spostare/Rinominare
Move-Item oldname.txt newname.txt
mv file.txt C:\Destination\

# Rinominare
Rename-Item old.txt new.txt
```

### Leggere e Scrivere File

```powershell
# Leggere file
$content = Get-Content file.txt

# Leggere linea per linea
Get-Content file.txt | ForEach-Object {
    Write-Host $_
}

# Prime/ultime righe
Get-Content file.txt -First 10
Get-Content file.txt -Last 5
Get-Content file.txt -Tail 5  # ultimi + segui (like tail -f)

# Scrivere file (sovrascrive)
"Contenuto" | Set-Content file.txt
Set-Content -Path file.txt -Value "Testo"

# Appendere (aggiungere)
"Nuova riga" | Add-Content file.txt
Add-Content -Path log.txt -Value "$(Get-Date): Log entry"

# Out-File (alternativa)
"Dati" | Out-File output.txt
Get-Process | Out-File processes.txt
```

### Test Esistenza

```powershell
# Test se esiste
Test-Path C:\file.txt
Test-Path C:\cartella

# Con if
if (Test-Path C:\temp\config.txt) {
    Write-Host "File esiste"
} else {
    Write-Host "File non trovato"
}

# Tipo specifico
Test-Path C:\Windows -PathType Container   # directory
Test-Path C:\file.txt -PathType Leaf       # file
```

### Path Management

```powershell
# Join path
$path = Join-Path C:\Users $env:USERNAME
$configPath = Join-Path $PSScriptRoot "config.json"

# Split path
$dir = Split-Path C:\Users\Mario\file.txt
$filename = Split-Path C:\Users\Mario\file.txt -Leaf

# Path assoluto
Resolve-Path .\relative\path

# Estensione
$ext = [System.IO.Path]::GetExtension("file.txt")  # .txt
$name = [System.IO.Path]::GetFileNameWithoutExtension("file.txt")  # file
```

### Attributi File

```powershell
# Informazioni file
$file = Get-Item file.txt
$file.Length          # dimensione
$file.LastWriteTime   # ultima modifica
$file.CreationTime    # creazione
$file.Attributes      # attributi

# Modificare attributi
$file.Attributes = "ReadOnly"
$file.Attributes = "Hidden"
$file.Attributes = "Normal"

# Impostare timestamp
$file.LastWriteTime = Get-Date
```

---

## Testo e Stringhe

### Manipolazione Base

```powershell
# Creazione
$str = "Hello World"
$str = 'Single quote'

# Interpolazione (solo con doppi apici)
$nome = "Mario"
$msg = "Ciao $nome"
$msg = "2 + 2 = $(2+2)"

# Here-String (multiline)
$text = @"
Questa è una stringa
su più righe
con $variabili espanse
"@

$literal = @'
Stringa letterale
$vari

```

### Formattazione Stringhe
`````powershell
# -f operator (format)
$nome = "Mario"
$eta = 30
$msg = "Mi chiamo {0} e ho {1} anni" -f $nome, $eta

# Con indici
$msg = "{0} {1} {0}" -f "Hello", "World"  # Hello World Hello

# Formattazione numeri
"{0:N2}" -f 1234.5678      # 1,234.57 (2 decimali)
"{0:C}" -f 19.99           # €19.99 (valuta)
"{0:P}" -f 0.75            # 75.00% (percentuale)
"{0:D5}" -f 42             # 00042 (padding)

# Formattazione date
$date = Get-Date
"{0:yyyy-MM-dd}" -f $date
"{0:dd/MM/yyyy HH:mm:ss}" -f $date

# Allineamento
"{0,10}" -f "test"         # allinea destra (10 caratteri)
"{0,-10}" -f "test"        # allinea sinistra

# Lunghezza
$str.Length

# Maiuscole/Minuscole
$str.ToUpper()
$str.ToLower()

# Trim (rimuovi spazi)
"  testo  ".Trim()
"  testo  ".TrimStart()
"  testo  ".TrimEnd()

# Substring
$str.Substring(0, 5)        # primi 5 caratteri
$str.Substring(6)           # dal carattere 6 alla fine

# Replace
$str.Replace("World", "PowerShell")
$str -replace "World", "PowerShell"

# Split
"a,b,c".Split(",")
"uno due tre" -split " "

# Join
$array -join ", "
-join @("a", "b", "c")      # "abc"

# Contiene
$str.Contains("World")
$str -like "*World*"
$str -match "W\w+"          # regex

# Inizia/Finisce
$str.StartsWith("Hello")
$str.EndsWith("World")
`````

### Regex (Espressioni Regolari)
`````powershell
# Match test
"test123" -match "\d+"                    # True
"test123" -match "^test\d+$"              # True
"email@example.com" -match "@.*\."       # True

# Cattura gruppi
"mario.rossi@example.com" -match "(.+)@(.+)"
$Matches[0]  # match completo
$Matches[1]  # primo gruppo (mario.rossi)
$Matches[2]  # secondo gruppo (example.com)

# Replace con regex
"test 123 abc 456" -replace "\d+", "NUM"  # test NUM abc NUM

# Select-String (grep per PowerShell)
Select-String -Path *.log -Pattern "ERROR"
Get-Content log.txt | Select-String "WARNING|ERROR"

# Con contesto
Select-String -Path log.txt -Pattern "ERROR" -Context 2,3
# 2 righe prima, 3 righe dopo

# Case sensitive
Select-String -Path log.txt -Pattern "Error" -CaseSensitive
`````

---

## Array e HashTable

### Array
`````powershell
# Creazione
$array = @(1, 2, 3, 4, 5)
$array = 1, 2, 3, 4, 5
$mixed = @("testo", 123, $true, (Get-Date))
$empty = @()

# Range
$numeri = 1..10
$lettere = 'a'..'z'

# Accesso elementi
$array[0]           # primo
$array[-1]          # ultimo
$array[0..2]        # primi 3
$array[1,3,5]       # elementi specifici

# Modificare elemento
$array[0] = 99

# Aggiungere elementi
$array += 6
$array = $array + 7

# Lunghezza
$array.Count
$array.Length

# Iterazione
foreach ($item in $array) {
    Write-Host $item
}

# Metodi utili
$array.Contains(5)          # True/False
$array.IndexOf(3)           # indice di elemento
$array.Reverse()            # inverti ordine
$array.Sort()               # ordina

# Array multidimensionali
$matrix = @(
    @(1, 2, 3),
    @(4, 5, 6),
    @(7, 8, 9)
)
$matrix[0][1]  # 2 (riga 0, colonna 1)

# Array di oggetti
$users = @(
    @{Name="Mario"; Age=30},
    @{Name="Luigi"; Age=25},
    @{Name="Anna"; Age=28}
)
`````

### HashTable (Dizionari)
`````powershell
# Creazione
$hash = @{
    Nome = "Mario"
    Cognome = "Rossi"
    Eta = 30
    Attivo = $true
}

# Accesso
$hash["Nome"]
$hash.Nome             # notazione punto

# Aggiungere/Modificare
$hash["Citta"] = "Roma"
$hash.Email = "mario@example.com"

# Rimuovere
$hash.Remove("Citta")

# Verificare esistenza chiave
$hash.ContainsKey("Nome")

# Iterazione
foreach ($key in $hash.Keys) {
    Write-Host "$key : $($hash[$key])"
}

# Iterazione con GetEnumerator
$hash.GetEnumerator() | ForEach-Object {
    Write-Host "$($_.Key) : $($_.Value)"
}

# Ordinato (mantiene ordine inserimento)
$ordered = [ordered]@{
    Primo = 1
    Secondo = 2
    Terzo = 3
}

# HashTable annidate
$config = @{
    Database = @{
        Server = "localhost"
        Port = 5432
        Name = "mydb"
    }
    API = @{
        Url = "https://api.example.com"
        Key = "secret123"
    }
}

$config.Database.Server  # localhost
`````

### PSCustomObject
`````powershell
# Oggetto custom (preferito per dati strutturati)
$person = [PSCustomObject]@{
    Name = "Mario"
    Age = 30
    City = "Roma"
}

# Accesso proprietà
$person.Name
$person.Age

# Array di oggetti
$people = @(
    [PSCustomObject]@{Name="Mario"; Age=30},
    [PSCustomObject]@{Name="Luigi"; Age=25},
    [PSCustomObject]@{Name="Anna"; Age=28}
)

# Filtrare e ordinare
$people | Where-Object Age -gt 26
$people | Sort-Object Age -Descending

# Aggiungere proprietà
$person | Add-Member -NotePropertyName "Email" -NotePropertyValue "mario@example.com"

# Export-friendly (funziona bene con Export-Csv, ConvertTo-Json, etc.)
$people | Export-Csv people.csv -NoTypeInformation
`````

---

## Gestione Errori

### Try-Catch-Finally
`````powershell
# Try-Catch base
try {
    $result = 10 / 0  # genera errore
} catch {
    Write-Host "Errore: $_"
}

# Catch specifici
try {
    $content = Get-Content "filenonesistente.txt"
} catch [System.IO.FileNotFoundException] {
    Write-Host "File non trovato"
} catch [System.UnauthorizedAccessException] {
    Write-Host "Accesso negato"
} catch {
    Write-Host "Errore generico: $_"
}

# Finally (eseguito sempre)
try {
    $file = [System.IO.File]::Open("file.txt", "Open")
    # operazioni
} catch {
    Write-Host "Errore: $_"
} finally {
    if ($file) {
        $file.Close()
    }
}

# Variabili errore
try {
    1/0
} catch {
    Write-Host "Messaggio: $($_.Exception.Message)"
    Write-Host "Tipo: $($_.Exception.GetType().FullName)"
    Write-Host "Stack: $($_.ScriptStackTrace)"
}
`````

### ErrorAction e ErrorVariable
`````powershell
# ErrorAction: controlla comportamento su errore
# - Stop: ferma esecuzione (diventa terminating error)
# - Continue: continua, mostra errore (default)
# - SilentlyContinue: continua, nasconde errore
# - Ignore: ignora completamente

Get-Content "nonesiste.txt" -ErrorAction Stop
Get-Content "nonesiste.txt" -ErrorAction SilentlyContinue

# ErrorVariable: salva errore in variabile
Get-Content "nonesiste.txt" -ErrorAction SilentlyContinue -ErrorVariable myError
$myError  # contiene l'errore

# Preference variable
$ErrorActionPreference = "Stop"  # applica a tutto lo script
`````

### Throw (Lanciare Errori)
`````powershell
# Throw semplice
if ($age -lt 0) {
    throw "Età non può essere negativa"
}

# Throw con oggetto
function Divide-Numbers {
    param($a, $b)
    
    if ($b -eq 0) {
        throw [System.DivideByZeroException]::new("Divisione per zero")
    }
    
    return $a / $b
}

# Terminare script con exit code
if ($error) {
    exit 1  # exit code 1 = errore
}
exit 0  # exit code 0 = successo
`````

### Write-Error e Warning
`````powershell
# Write-Error (errore non-terminating)
Write-Error "Questo è un errore"

# Write-Warning (avviso)
Write-Warning "Attenzione: operazione rischiosa"

# Write-Verbose (dettagli extra)
Write-Verbose "Dettaglio operazione"  # visibile con -Verbose

# Write-Debug (debugging)
Write-Debug "Valore variabile: $var"  # visibile con -Debug

# Esempio in funzione
function Process-Data {
    [CmdletBinding()]
    param($Data)
    
    Write-Verbose "Inizio elaborazione dati"
    
    if (-not $Data) {
        Write-Warning "Nessun dato fornito"
        return
    }
    
    Write-Debug "Data = $Data"
    
    # elaborazione
}

# Uso
Process-Data -Data "test" -Verbose -Debug
`````

---

## Script PowerShell

### Creare Script
`````powershell
# File con estensione .ps1
# Esempio: MioScript.ps1

# Intestazione script (comment-based help)
<#
.SYNOPSIS
    Breve descrizione script

.DESCRIPTION
    Descrizione dettagliata di cosa fa lo script

.PARAMETER Nome
    Descrizione parametro Nome

.PARAMETER Verbose
    Output verboso

.EXAMPLE
    .\MioScript.ps1 -Nome "Mario"
    
.NOTES
    Autore: Mario Rossi
    Data: 2024-01-20
#>

param(
    [Parameter(Mandatory=$true)]
    [string]$Nome,
    
    [switch]$Verbose
)

# Codice script
Write-Host "Ciao $Nome!"

# Esecuzione
# .\MioScript.ps1 -Nome "Mario"
`````

### Parametri Script
`````powershell
# Parametri posizionali
param(
    [string]$Param1,
    [string]$Param2
)

# Uso: .\script.ps1 "valore1" "valore2"

# Parametri con validazione
param(
    [Parameter(Mandatory=$true)]
    [ValidateSet("Dev", "Test", "Prod")]
    [string]$Environment,
    
    [ValidateRange(1, 100)]
    [int]$Timeout = 30,
    
    [ValidateScript({Test-Path $_})]
    [string]$ConfigPath
)

# Parametri da pipeline
param(
    [Parameter(ValueFromPipeline=$true)]
    [string[]]$InputObject
)

# Switch parameters
param(
    [switch]$Force,
    [switch]$Verbose,
    [switch]$WhatIf
)

# Uso: .\script.ps1 -Force -Verbose
`````

### Dot Sourcing e Modules
`````powershell
# Dot sourcing (esegue script nello scope corrente)
. .\functions.ps1  # carica funzioni da altro file

# Importare modulo
Import-Module MyModule

# Creare modulo semplice
# File: MyModule.psm1
function Get-Greeting {
    param([string]$Name)
    return "Hello $Name!"
}

Export-ModuleMember -Function Get-Greeting

# Usare modulo
Import-Module .\MyModule.psm1
Get-Greeting -Name "Mario"
`````

### Script Template Completo
`````powershell
<#
.SYNOPSIS
    Template script PowerShell

.DESCRIPTION
    Template completo per script PowerShell professionali

.PARAMETER ConfigPath
    Path al file di configurazione

.PARAMETER Environment
    Ambiente: Dev, Test, Prod

.PARAMETER Verbose
    Output verboso

.EXAMPLE
    .\ScriptTemplate.ps1 -ConfigPath "config.json" -Environment "Dev"

.NOTES
    Autore: Mario Rossi
    Versione: 1.0
    Data: 2024-01-20
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [ValidateScript({Test-Path $_})]
    [string]$ConfigPath,
    
    [Parameter()]
    [ValidateSet("Dev", "Test", "Prod")]
    [string]$Environment = "Dev",
    
    [Parameter()]
    [switch]$WhatIf
)

# Inizializzazione
$ErrorActionPreference = "Stop"
$ScriptPath = $PSScriptRoot
$StartTime = Get-Date

Write-Verbose "Script avviato: $StartTime"
Write-Verbose "Path: $ScriptPath"
Write-Verbose "Environment: $Environment"

# Funzioni helper
function Write-Log {
    param([string]$Message)
    
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $Message"
    
    Write-Verbose $logMessage
    Add-Content -Path "$ScriptPath\script.log" -Value $logMessage
}

# Main script
try {
    Write-Log "Inizio elaborazione"
    
    # Carica configurazione
    $config = Get-Content $ConfigPath | ConvertFrom-Json
    Write-Log "Configurazione caricata"
    
    # Logica principale
    Write-Log "Esecuzione logica principale"
    
    if ($WhatIf) {
        Write-Host "WhatIf: Simulazione completata"
    } else {
        # Esecuzione reale
        Write-Log "Operazioni completate"
    }
    
    Write-Host "✓ Script completato con successo" -ForegroundColor Green
    exit 0
    
} catch {
    Write-Log "ERRORE: $_"
    Write-Host "✗ Errore: $_" -ForegroundColor Red
    exit 1
    
} finally {
    $EndTime = Get-Date
    $Duration = $EndTime - $StartTime
    Write-Log "Script terminato. Durata: $($Duration.TotalSeconds) secondi"
}
`````

---

## Lavorare con API (REST)

### Invoke-RestMethod
`````powershell
# GET request
$response = Invoke-RestMethod -Uri "https://api.example.com/data"

# Con parametri
$params = @{
    page = 1
    limit = 10
}
$response = Invoke-RestMethod -Uri "https://api.example.com/users" -Body $params

# POST con JSON
$body = @{
    username = "mario"
    email = "mario@example.com"
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "https://api.example.com/users" `
    -Method Post `
    -Body $body `
    -ContentType "application/json"

# Headers personalizzati
$headers = @{
    "Authorization" = "Bearer TOKEN123"
    "Accept" = "application/json"
}

$response = Invoke-RestMethod -Uri "https://api.example.com/data" `
    -Headers $headers

# PUT request
$body = @{
    name = "Mario Rossi"
    age = 31
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "https://api.example.com/users/123" `
    -Method Put `
    -Body $body `
    -ContentType "application/json"

# DELETE request
Invoke-RestMethod -Uri "https://api.example.com/users/123" -Method Delete
`````

### Invoke-WebRequest (più controllo)
`````powershell
# WebRequest (restituisce più dettagli)
$response = Invoke-WebRequest -Uri "https://api.example.com/data"

# Proprietà response
$response.StatusCode        # 200, 404, etc.
$response.StatusDescription # OK, Not Found, etc.
$response.Headers          # headers risposta
$response.Content          # corpo risposta (string)
$response.RawContent       # risposta completa

# Parse JSON manualmente
$data = $response.Content | ConvertFrom-Json

# Salvare file
Invoke-WebRequest -Uri "https://example.com/file.pdf" -OutFile "file.pdf"
`````

### Autenticazione
`````powershell
# Basic Authentication
$username = "user"
$password = "pass"
$base64 = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes("${username}:${password}"))

$headers = @{
    "Authorization" = "Basic $base64"
}

$response = Invoke-RestMethod -Uri "https://api.example.com/data" -Headers $headers

# Bearer Token
$token = "your_access_token"
$headers = @{
    "Authorization" = "Bearer $token"
}

$response = Invoke-RestMethod -Uri "https://api.example.com/data" -Headers $headers

# API Key
$headers = @{
    "X-API-Key" = "your-api-key"
}

# Credential object
$cred = Get-Credential
Invoke-RestMethod -Uri "https://api.example.com/data" -Credential $cred
`````

### Gestione Errori API
`````powershell
# Try-Catch per errori HTTP
try {
    $response = Invoke-RestMethod -Uri "https://api.example.com/data"
    Write-Host "Successo!"
} catch {
    $statusCode = $_.Exception.Response.StatusCode.value__
    $statusDescription = $_.Exception.Response.StatusDescription
    
    Write-Host "Errore HTTP $statusCode : $statusDescription"
    
    # Corpo errore (se presente)
    $errorBody = $_.ErrorDetails.Message
    if ($errorBody) {
        $errorData = $errorBody | ConvertFrom-Json
        Write-Host "Dettagli: $($errorData.message)"
    }
}

# Timeout
$response = Invoke-RestMethod -Uri "https://api.example.com/data" -TimeoutSec 30

# Retry logic
$maxRetries = 3
$retryCount = 0
$success = $false

while (-not $success -and $retryCount -lt $maxRetries) {
    try {
        $response = Invoke-RestMethod -Uri "https://api.example.com/data"
        $success = $true
    } catch {
        $retryCount++
        Write-Warning "Tentativo $retryCount fallito. Riprovo..."
        Start-Sleep -Seconds (5 * $retryCount)
    }
}
`````

### Client API Completo
`````powershell
class APIClient {
    [string]$BaseUrl
    [hashtable]$Headers
    
    APIClient([string]$baseUrl, [string]$apiKey) {
        $this.BaseUrl = $baseUrl.TrimEnd('/')
        $this.Headers = @{
            "Authorization" = "Bearer $apiKey"
            "Content-Type" = "application/json"
        }
    }
    
    [object] Get([string]$endpoint) {
        $url = "$($this.BaseUrl)/$($endpoint.TrimStart('/'))"
        
        try {
            return Invoke-RestMethod -Uri $url `
                -Method Get `
                -Headers $this.Headers `
                -TimeoutSec 30
        } catch {
            Write-Error "GET $url fallito: $_"
            throw
        }
    }
    
    [object] Post([string]$endpoint, [object]$data) {
        $url = "$($this.BaseUrl)/$($endpoint.TrimStart('/'))"
        $body = $data | ConvertTo-Json -Depth 10
        
        try {
            return Invoke-RestMethod -Uri $url `
                -Method Post `
                -Headers $this.Headers `
                -Body $body `
                -TimeoutSec 30
        } catch {
            Write-Error "POST $url fallito: $_"
            throw
        }
    }
    
    [object] Put([string]$endpoint, [object]$data) {
        $url = "$($this.BaseUrl)/$($endpoint.TrimStart('/'))"
        $body = $data | ConvertTo-Json -Depth 10
        
        return Invoke-RestMethod -Uri $url `
            -Method Put `
            -Headers $this.Headers `
            -Body $body `
            -TimeoutSec 30
    }
    
    [void] Delete([string]$endpoint) {
        $url = "$($this.BaseUrl)/$($endpoint.TrimStart('/'))"
        
        Invoke-RestMethod -Uri $url `
            -Method Delete `
            -Headers $this.Headers `
            -TimeoutSec 30
    }
}

# Uso
$client = [APIClient]::new("https://api.example.com", "YOUR_API_KEY")

$users = $client.Get("/users")
$newUser = $client.Post("/users", @{name="Mario"; email="mario@test.com"})
`````

---

## XML e JSON

### JSON
`````powershell
# PowerShell object a JSON
$object = @{
    Name = "Mario"
    Age = 30
    Skills = @("PowerShell", "Python", "Bash")
    Address = @{
        City = "Roma"
        ZIP = "00100"
    }
}

$json = $object | ConvertTo-Json
$json = $object | ConvertTo-Json -Depth 10  # per oggetti annidati

# Pretty print
$json = $object | ConvertTo-Json -Depth 10 | Out-String

# JSON a PowerShell object
$jsonString = '{"name":"Mario","age":30}'
$object = $jsonString | ConvertFrom-Json

# Leggere JSON da file
$config = Get-Content config.json | ConvertFrom-Json

# Scrivere JSON su file
$object | ConvertTo-Json -Depth 10 | Set-Content config.json

# Accesso proprietà
$config.Name
$config.Address.City

# Modificare
$config.Age = 31
$config | ConvertTo-Json | Set-Content config.json
`````

### XML
`````powershell
# Parse XML
$xmlString = @"
<catalog>
    <book id="1">
        <title>PowerShell Guide</title>
        <author>Mario Rossi</author>
        <price>29.99</price>
    </book>
    <book id="2">
        <title>Scripting Automation</title>
        <author>Luigi Verdi</author>
        <price>39.99</price>
    </book>
</catalog>
"@

[xml]$xml = $xmlString

# Accesso elementi
$xml.catalog.book                    # tutti i libri
$xml.catalog.book[0].title          # primo titolo
$xml.catalog.book | Where-Object {$_.price -gt 30}

# Attributi
$xml.catalog.book[0].id             # "1"

# Leggere XML da file
[xml]$xml = Get-Content data.xml

# XPath
$xml.SelectNodes("//book[@id='1']")
$xml.SelectNodes("//book[price>30]")
$xml.SelectSingleNode("//book[@id='1']/title").InnerText

# Creare XML
$xml = New-Object System.Xml.XmlDocument
$root = $xml.CreateElement("catalog")
$xml.AppendChild($root)

$book = $xml.CreateElement("book")
$book.SetAttribute("id", "1")

$title = $xml.CreateElement("title")
$title.InnerText = "New Book"
$book.AppendChild($title)

$root.AppendChild($book)

# Salvare
$xml.Save("C:\output.xml")

# Formattare XML
$stringWriter = New-Object System.IO.StringWriter
$xmlWriter = New-Object System.Xml.XmlTextWriter($stringWriter)
$xmlWriter.Formatting = "Indented"
$xml.WriteContentTo($xmlWriter)
$xmlWriter.Flush()
$stringWriter.ToString()
`````

---

## CSV e Export Dati

### Export-Csv
`````powershell
# Creare array di oggetti
$data = @(
    [PSCustomObject]@{Name="Mario"; Age=30; City="Roma"},
    [PSCustomObject]@{Name="Luigi"; Age=25; City="Milano"},
    [PSCustomObject]@{Name="Anna"; Age=28; City="Napoli"}
)

# Export base
$data | Export-Csv -Path users.csv -NoTypeInformation

# Con delimitatore personalizzato
$data | Export-Csv -Path users.csv -Delimiter ";" -NoTypeInformation

# Encoding
$data | Export-Csv -Path users.csv -NoTypeInformation -Encoding UTF8

# Append
$newData | Export-Csv -Path users.csv -Append -NoTypeInformation

# Processi a CSV
Get-Process | Select-Object Name, CPU, WorkingSet | 
    Export-Csv -Path processes.csv -NoTypeInformation
`````

### Import-Csv
`````powershell
# Import CSV
$data = Import-Csv users.csv

# Con delimitatore
$data = Import-Csv users.csv -Delimiter ";"

# Accesso dati
foreach ($row in $data) {
    Write-Host "$($row.Name) ha $($row.Age) anni"
}

# Filtrare
$data | Where-Object Age -gt 26

# Header personalizzato
$data = Import-Csv data.csv -Header "Col1", "Col2", "Col3"
`````

### ConvertTo-Csv e ConvertFrom-Csv
`````powershell
# Convert to CSV (stringa, non file)
$csvString = $data | ConvertTo-Csv -NoTypeInformation

# Convert from CSV string
$data = $csvString | ConvertFrom-Csv
`````

### Export Altri Formati
`````powershell
# Export HTML
$data | ConvertTo-Html | Out-File report.html

# Con stile
$data | ConvertTo-Html -Title "Report" -PreContent "<h1>User Report</h1>" | 
    Out-File report.html

# Export XML
$data | Export-Clixml data.xml
$data = Import-Clixml data.xml

# Export JSON
$data | ConvertTo-Json | Set-Content data.json
`````

### Esempio Report Completo
`````powershell
function Export-VulnerabilityReport {
    param(
        [Parameter(Mandatory=$true)]
        [array]$Vulnerabilities,
        
        [Parameter(Mandatory=$true)]
        [string]$OutputPath
    )
    
    # Crea oggetti strutturati
    $report = $Vulnerabilities | ForEach-Object {
        [PSCustomObject]@{
            IP = $_.IP
            QID = $_.QID
            Severity = $_.Severity
            Title = $_.Title
            CVE = $_.CVE -join ", "
            CVSS_Score = $_.CVSS_Score
            Date = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        }
    }
    
    # Export CSV
    $csvPath = Join-Path $OutputPath "vulnerabilities.csv"
    $report | Export-Csv -Path $csvPath -NoTypeInformation
    
    # Export JSON
    $jsonPath = Join-Path $OutputPath "vulnerabilities.json"
    $report | ConvertTo-Json -Depth 10 | Set-Content $jsonPath
    
    # Export HTML
    $htmlPath = Join-Path $OutputPath "vulnerabilities.html"
    $html = $report | ConvertTo-Html -Title "Vulnerability Report" `
        -PreContent "<h1>Vulnerability Scan Report</h1><p>Generated: $(Get-Date)</p>"
    $html | Out-File $htmlPath
    
    Write-Host "Report esportati in $OutputPath"
}
`````

---

## Scheduling e Automazione

### Task Scheduler
`````powershell
# Creare Scheduled Task
$action = New-ScheduledTaskAction -Execute "PowerShell.exe" `
    -Argument "-File C:\Scripts\backup.ps1"

$trigger = New-ScheduledTaskTrigger -Daily -At 2am

$principal = New-ScheduledTaskPrincipal -UserID "NT AUTHORITY\SYSTEM" `
    -LogonType ServiceAccount -RunLevel Highest

$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries `
    -DontStopIfGoingOnBatteries

Register-ScheduledTask -TaskName "Daily Backup" `
    -Action $action `
    -Trigger $trigger `
    -Principal $principal `
    -Settings $settings `
    -Description "Backup giornaliero alle 2:00"

# Trigger multipli
$trigger1 = New-ScheduledTaskTrigger -Daily -At 2am
$trigger2 = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Sunday -At 11pm

Register-ScheduledTask -TaskName "Multiple Triggers" `
    -Action $action `
    -Trigger $trigger1,$trigger2

# Gestire task esistenti
Get-ScheduledTask -TaskName "Daily Backup"
Start-ScheduledTask -TaskName "Daily Backup"
Stop-ScheduledTask -TaskName "Daily Backup"
Disable-ScheduledTask -TaskName "Daily Backup"
Enable-ScheduledTask -TaskName "Daily Backup"
Unregister-ScheduledTask -TaskName "Daily Backup" -Confirm:$false

# Trigger On Logon
$trigger = New-ScheduledTaskTrigger -AtLogOn

# Trigger On Startup
$trigger = New-ScheduledTaskTrigger -AtStartup

# Trigger Personalizzato (ogni 30 minuti)
$trigger = New-ScheduledTaskTrigger -Once -At (Get-Date) `
    -RepetitionInterval (New-TimeSpan -Minutes 30) `
    -RepetitionDuration ([TimeSpan]::MaxValue)
`````

### Loop Scheduling in Script
`````powershell
# Loop infinito con pausa
while ($true) {
    Write-Host "Esecuzione task: $(Get-Date)"
    
    # Logica task
    Get-Process | Export-Csv "processes_$(Get-Date -Format 'yyyyMMdd_HHmmss').csv"
    
    # Pausa 5 minuti
    Start-Sleep -Seconds 300
}

# Loop con orario specifico
while ($true) {
    $now = Get-Date
    
    # Esegui alle 2:00
    if ($now.Hour -eq 2 -and $now.Minute -eq 0) {
        Write-Host "Esecuzione task programmato"
        # Logica task
        
        # Pausa per evitare esecuzioni multiple
        Start-Sleep -Seconds 120
    }
    
    Start-Sleep -Seconds 30
}
`````

---

## Remoting

### Enable Remoting
`````powershell
# Abilitare remoting (come Admin)
Enable-PSRemoting -Force

# Configurare TrustedHosts (per workgroup)
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "*.domain.com" -Force
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "*" -Force  # tutti (non sicuro!)

# Verificare configurazione
Get-PSSessionConfiguration
Test-WSMan
`````

### Invoke-Command
`````powershell
# Eseguire comando su computer remoto
Invoke-Command -ComputerName Server01 -ScriptBlock {
    Get-Service
}

# Con credenziali
$cred = Get-Credential
Invoke-Command -ComputerName Server01 -Credential $cred -ScriptBlock {
    Get-Process
}

# Multipli computer
Invoke-Command -ComputerName Server01, Server02, Server03 -ScriptBlock {
    Get-EventLog -LogName System -Newest 10
}

# Passare argomenti
$serviceName = "Spooler"
Invoke-Command -ComputerName Server01 -ScriptBlock {
    param($name)
    Get-Service -Name $name
} -ArgumentList $serviceName

# Eseguire script remoto
Invoke-Command -ComputerName Server01 -FilePath C:\Scripts\test.ps1
`````

### New-PSSession (Sessioni Persistenti)
`````powershell
# Creare sessione
$session = New-PSSession -ComputerName Server01

# Usare sessione
Invoke-Command -Session $session -ScriptBlock {
    $var = "valore"  # variabile persiste nella sessione
}

Invoke-Command -Session $session -ScriptBlock {
    Write-Host $var  # valore accessibile
}

# Copiare file
Copy-Item -Path C:\local\file.txt -Destination C:\remote\ -ToSession $session

# Chiudere sessione
Remove-PSSession $session

# Sessioni multiple
$sessions = New-PSSession -ComputerName Server01, Server02, Server03
Invoke-Command -Session $sessions -ScriptBlock { Get-Process }
$sessions | Remove-PSSession
`````

### Enter-PSSession (Interattivo)
`````powershell
# Sessione interattiva
Enter-PSSession -ComputerName Server01

# Ora sei su Server01
PS Server01> Get-Process
PS Server01> exit  # torna al computer locale

# Con credenziali
$cred = Get-Credential
Enter-PSSession -ComputerName Server01 -Credential $cred
`````

---

## Best Practices

### Naming Conventions
`````powershell
# Verb-Noun per funzioni (Approved Verbs)
function Get-UserData { }       # ✓ Corretto
function Fetch-UserData { }     # ✗ Verb non approvato#
`````

### Lista verbi approvati
`````powershell
Get-Verb
`````

### PascalCase per funzioni e parametri
`````powershell
function Get-ServerStatus { }
param([string]$ServerName)
`````

### camelCase per variabili
`````powershell
$userName = "mario"
$configPath = "C:\config"
`````

### UPPERCASE per costanti
`````powershell
$MAX_RETRIES = 3
$API_TIMEOUT = 30
`````

### Commenti e Documentazione
`````powershell
# Commenti inline
$result = Get-Process  # ottieni lista processi

# Commenti blocco
<#
Questo è un commento
su più righe
#>

# Comment-based help (SEMPRE per funzioni pubbliche)
function Get-UserReport {
    <#
    .SYNOPSIS
        Genera report utenti
    
    .DESCRIPTION
        Genera un report dettagliato degli utenti nel sistema
        con statistiche di utilizzo e stato account.
    
    .PARAMETER UserName
        Nome utente o pattern (supporta wildcard)
    
    .PARAMETER IncludeDisabled
        Include anche utenti disabilitati
    
    .EXAMPLE
        Get-UserReport -UserName "mario*"
        Genera report per tutti gli utenti che iniziano con mario
    
    .EXAMPLE
        Get-UserReport -IncludeDisabled
        Include utenti disabilitati nel report
    
    .INPUTS
        String
    
    .OUTPUTS
        PSCustomObject
    
    .NOTES
        Autore: Mario Rossi
        Versione: 1.0
        Data: 2024-01-20
    
    .LINK
        https://docs.example.com/get-userreport
    #>
    
    param(
        [string]$UserName = "*",
        [switch]$IncludeDisabled
    )
    
    # Implementazione
}

# Visualizzare help
Get-Help Get-UserReport
Get-Help Get-UserReport -Examples
Get-Help Get-UserReport -Full
`````

### Error Handling Best Practices
`````powershell
# SEMPRE usare try-catch per operazioni critiche
try {
    $data = Invoke-RestMethod -Uri $apiUrl
} catch {
    Write-Error "Errore API: $_"
    throw
}

# Usare ErrorAction appropriato
Get-ChildItem -Path C:\NoExist -ErrorAction SilentlyContinue

# Validare input
function Process-Data {
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$InputPath
    )
    
    if (-not (Test-Path $InputPath)) {
        throw "Path non valido: $InputPath"
    }
    
    # Continua elaborazione
}

# Logging errori
try {
    # Operazione
} catch {
    $errorMsg = "Errore: $_"
    Write-Error $errorMsg
    Add-Content -Path error.log -Value "$((Get-Date)) - $errorMsg"
    throw
}
`````



### Performance
`````powershell
# ✗ LENTO: Modificare array in loop
$array = @()
foreach ($i in 1..10000) {
    $array += $i  # crea nuovo array ogni volta
}

# ✓ VELOCE: Usare ArrayList o List
$list = [System.Collections.ArrayList]@()
foreach ($i in 1..10000) {
    [void]$list.Add($i)
}

# ✓ VELOCE: Usare pipeline
$array = 1..10000 | ForEach-Object { $_ }

# Misurare performance
Measure-Command {
    # Codice da misurare
}

# ✗ LENTO: Multiple chiamate a Get-
foreach ($user in $users) {
    $groups = Get-ADPrincipalGroupMembership $user
}

# ✓ VELOCE: Query unica
$allGroups = Get-ADGroup -Filter *
foreach ($user in $users) {
    $userGroups = $allGroups | Where-Object {$_.Members -contains $user}
}

# Usare Where-Object vs .Where()
# .Where() è più veloce (PowerShell 4+)
$filtered = $array.Where({$_ -gt 5})
`````

### Sicurezza
`````powershell
# NON hardcodare credenziali
# ✗ MALE
$password = "Secret123!"

# ✓ BENE: Usare Get-Credential
$cred = Get-Credential

# ✓ BENE: Variabili ambiente
$apiKey = $env:API_KEY

# ✓ BENE: Secure String
$securePassword = Read-Host "Password" -AsSecureString
$cred = New-Object System.Management.Automation.PSCredential($username, $securePassword)

# Convertire da/a secure string
$secureString = ConvertTo-SecureString "Password123" -AsPlainText -Force
$plainText = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
    [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secureString)
)

# Salvare credenziali (encrypted per utente corrente)
$cred | Export-Clixml credentials.xml
$cred = Import-Clixml credentials.xml
`````

---
