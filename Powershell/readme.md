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