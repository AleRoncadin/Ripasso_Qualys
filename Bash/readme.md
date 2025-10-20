# Bash Scripting - Guida Completa

Una guida pratica per la programmazione shell in Bash, basata sul corso di Laboratorio di Sistemi Operativi.

## Indice

- [Fondamenti della Shell](#fondamenti-della-shell)
- [Metacaratteri](#metacaratteri)
- [File e Directory](#file-e-directory)
- [Permessi](#permessi)
- [Redirezione I/O e Pipe](#redirezione-io-e-pipe)
- [Processi e Job Control](#processi-e-job-control)
- [History e Command Editing](#history-e-command-editing)
- [Variabili](#variabili)
- [Scripting Bash](#scripting-bash)
- [Comandi Filtro](#comandi-filtro)
- [Editor](#editor)

---

## Fondamenti della Shell

### Tipi di Shell
- `sh` - Bourne shell
- `bash` - Bourne Again Shell (la più comune)
- `csh` - C shell
- `tcsh` - Teach C shell
- `ksh` - Korn shell

### Comandi Base
```bash
# Scoprire quale shell stai usando
echo $SHELL

# Uscire dalla shell
exit
logout
# oppure Ctrl-D (con linea vuota)

# Pulire lo schermo
clear

# Consultare il manuale
man comando
man -s2 mkdir    # sezione specifica
man -a passwd    # tutte le sezioni
```

---

## Metacaratteri

### Wildcards (Espansione pathname)

| Metacarattere | Significato | Esempio |
|---------------|-------------|---------|
| `*` | 0 o più caratteri | `ls *.txt` |
| `?` | Singolo carattere | `ls file?.txt` |
| `[...]` | Carattere tra quelli elencati | `ls [abc].txt` |
| `[a-z]` | Range di caratteri | `ls [a-z]*.sh` |
| `{...}` | Sequenza di stringhe | `mkdir {bin,doc,lib}` |

### Quoting (Inibizione Metacaratteri)

```bash
# Backslash - escape singolo carattere
cp file file\?

# Apici singoli - tutto letterale
cat 'file*?'

# Apici doppi - alcuni metacaratteri attivi
echo "Il valore è: $variabile"
```

### Controllo Esecuzione

| Simbolo | Significato | Esempio |
|---------|-------------|---------|
| `;` | Sequenza comandi | `pwd; ls; cd /tmp` |
| `\|\|` | OR logico (esegui se precedente fallisce) | `cc prog.c \|\| echo errore` |
| `&&` | AND logico (esegui se precedente ha successo) | `cc prog.c && ./a.out` |
| `(...)` | Raggruppamento comandi | `(date; ls) > output.txt` |
| `&` | Esecuzione in background | `xemacs &` |
| `#` | Commento | `ls # questo è un commento` |

---

## File e Directory

### Navigazione

```bash
# Directory corrente
pwd

# Cambiare directory
cd /path/to/dir
cd          # vai alla home
cd ..       # directory madre
cd ~user    # home dell'utente user
cd -        # directory precedente

# Alias speciali
.           # directory corrente
..          # directory madre
~           # home directory
~user       # home di user
```

### Manipolazione File

```bash
# Listing
ls
ls -l       # long format
ls -a       # mostra file nascosti
ls -al      # combinato
ls -R       # ricorsivo

# Creare/Rimuovere directory
mkdir dir1
rmdir dir1  # solo se vuota

# Copiare
cp file1 file2
cp file1 file2 file3 dir/    # multipli in directory

# Spostare/Rinominare
mv file1 file2
mv file1 file2 dir/

# Cancellare
rm file
rm -r dir/      # ricorsivo
rm -f file      # forza (no conferma)
rm -rf dir/     # ricorsivo + forza (ATTENZIONE!)

# Link
ln file1 file2          # hard link
ln -s target link_name  # symbolic link

# Trovare file
find /path -name "*.txt" -print
find . -type d          # solo directory
find . -name "*.bak" -exec rm {} \;

# Confronto file
cmp file1 file2    # byte per byte
diff file1 file2   # differenze dettagliate
```

### Visualizzazione Contenuto

```bash
cat file           # tutto il file
more file          # pagina per pagina
less file          # come more ma meglio
head file          # prime 10 righe
head -n 5 file     # prime 5 righe
tail file          # ultime 10 righe
tail -n 5 file     # ultime 5 righe
tail -f file       # segui il file in tempo reale
```

---

## Permessi

### Struttura Permessi

```
-rwxr-xr--
│└┬┘└┬┘└┬┘
│ │  │  └─ altri (world)
│ │  └──── gruppo (group)
│ └─────── proprietario (owner)
└───────── tipo file (- file, d dir, l link)
```

### Significato

| Simbolo | File | Directory |
|---------|------|-----------|
| `r` | Lettura | Listare contenuto |
| `w` | Scrittura | Creare/eliminare file |
| `x` | Esecuzione | Attraversare (cd) |

### Chmod

```bash
# Notazione ottale (più comune)
chmod 755 file    # rwxr-xr-x
chmod 644 file    # rw-r--r--
chmod 700 file    # rwx------

# Conversione: rwxr-xr-x
#              111 101 101
#                7   5   5

# Notazione simbolica
chmod u=rwx,g=rx,o=rx file   # uguale a 755
chmod u+x file                # aggiungi esecuzione a owner
chmod go-w file               # rimuovi scrittura da group e others
chmod a+r file                # aggiungi lettura a tutti

# Dove:
# u = user (owner)
# g = group
# o = others (world)
# a = all
```

---

## Redirezione I/O e Pipe

### Redirezione

```bash
# Output
comando > file          # sovrascrivi
comando >> file         # append

# Input
comando < file

# Errori (bash)
comando 2> errori.txt
comando 2>&1            # errori su stdout
comando > out 2> err    # separa output ed errori

# Here document
wc <<FINE
questa è una linea
questa è un'altra
FINE
```

### Pipe

```bash
# Collega output di un comando all'input del successivo
ls | wc -l              # conta file
ps | grep firefox       # trova processo
cat file | sort | uniq  # ordina e rimuovi duplicati

# Esempi complessi
ls /usr/bin | wc -w
who | wc -l
find . -name "*.c" | wc -l
```

---

## Processi e Job Control

### Informazioni Processi

```bash
# Processi correnti
ps              # processi del terminale corrente
ps -af          # tutti con terminale, full format
ps -el          # tutti, long listing
ps -aux         # formato BSD

# Monitoraggio real-time
top

# PID corrente
echo $$

# Terminale corrente
tty
```

### Controllo Processi

```bash
# Terminare processo
kill PID
kill -9 PID         # SIGKILL (forza)
kill -s KILL PID    # equivalente

# Background
comando &           # esegui in background
[1] 12345          # [job_number] PID

# Job control
jobs               # lista job
fg                 # porta job in foreground
fg %1              # porta job 1 in foreground
bg                 # riprendi job in background
bg %1              # riprendi job 1 in background

# Sospendere processo in foreground
Ctrl-Z

# Terminare job
kill %1
```

---

## History e Command Editing

### History List

```bash
# Visualizzare history
history
history | tail -5

# Ripetere comandi
!!                 # ultimo comando
!515               # comando numero 515
!ls                # ultimo comando che inizia con ls
!?ls?              # ultimo comando che contiene ls

# Modificare e ripetere
!ls:s/al/i/        # sostituisci 'al' con 'i'
```

### Command Line Editing

| Tasto | Azione |
|-------|--------|
| Freccia Su/Giù | Scorri history |
| Freccia Sinistra/Destra | Muovi cursore |
| `Ctrl-A` | Inizio linea |
| `Ctrl-E` | Fine linea |
| `Backspace` | Cancella carattere precedente |
| `Tab` | Command/filename completion |

### Alias

```bash
# Creare alias
alias ll='ls -la'
alias rm='rm -i'

# Rimuovere alias
unalias ll

# Alias permanenti
# Aggiungi in ~/.bashrc o ~/.bash_profile
alias dir='ls -a'
```

---

## Variabili

### Dichiarazione e Uso

```bash
# Assegnamento (NO SPAZI intorno a =)
variabile=valore
nome="Mario Rossi"

# Accesso
echo $variabile
echo ${variabile}    # sintassi alternativa

# Esportare (rendere globale)
export variabile
export PATH=$PATH:/nuovo/path
```

### Variabili di Ambiente

| Variabile | Contenuto |
|-----------|-----------|
| `$HOME` | Home directory |
| `$PATH` | Percorsi di ricerca comandi |
| `$PWD` | Directory corrente |
| `$USER` | Nome utente |
| `$UID` | User ID |
| `$SHELL` | Shell corrente |
| `$PS1` | Prompt primario |
| `$PS2` | Prompt secondario |

### Variabili Speciali

| Variabile | Significato |
|-----------|-------------|
| `$?` | Exit status ultimo comando |
| `$$` | PID shell corrente |
| `$!` | PID ultimo comando in background |
| `$0` | Nome dello script |
| `$1, $2, ..., $9` | Parametri posizionali |
| `$#` | Numero parametri |
| `$*` | Tutti i parametri (stringa unica) |
| `$@` | Tutti i parametri (array) |

### Aritmetica

```bash
# Metodo 1
num=$[$num + 1]
risultato=$[5 * 3 + 2]

# Metodo 2
num=$((num + 1))
risultato=$((5 * 3 + 2))

# Operatori: +, -, *, /, %, ++, --
```

### Command Substitution

```bash
# Backtick (vecchio stile)
data=`date`
file=`basename /usr/bin/man`

# $() (preferito)
data=$(date)
file=$(basename /usr/bin/man)
dimensione=$(wc -l < file.txt)
```

---

## Scripting Bash

### Struttura Base Script

```bash
#!/bin/bash
# Commento: descrizione dello script

# Comandi
echo "Hello World"
exit 0    # exit status (0 = successo)
```

### Rendere Eseguibile

```bash
chmod +x script.sh
./script.sh

# Oppure
bash script.sh
```

### Debug

```bash
# All'interno dello script
set -x    # stampa comandi mentre vengono eseguiti
set -v    # stampa comandi mentre vengono letti
set -     # disattiva debug

# Da linea di comando
bash -x script.sh
```

### Parametri

```bash
#!/bin/bash
echo "Nome script: $0"
echo "Primo parametro: $1"
echo "Secondo parametro: $2"
echo "Numero parametri: $#"
echo "Tutti i parametri: $*"

# Shift
shift         # $2 diventa $1, $3 diventa $2, ecc.
shift 2       # shift di 2 posizioni
```

### Comando test

```bash
# Sintassi
test espressione
[ espressione ]    # equivalente (spazi obbligatori!)

# File
[ -e file ]    # esiste
[ -f file ]    # è file regolare
[ -d file ]    # è directory
[ -r file ]    # leggibile
[ -w file ]    # scrivibile
[ -x file ]    # eseguibile

# Stringhe
[ -z str ]         # lunghezza zero
[ -n str ]         # lunghezza non zero
[ str1 = str2 ]    # uguali
[ str1 != str2 ]   # diverse

# Numeri
[ num1 -eq num2 ]  # uguale
[ num1 -ne num2 ]  # diverso
[ num1 -lt num2 ]  # minore
[ num1 -gt num2 ]  # maggiore
[ num1 -le num2 ]  # minore o uguale
[ num1 -ge num2 ]  # maggiore o uguale

# Logica
[ exp1 -a exp2 ]   # AND
[ exp1 -o exp2 ]   # OR
[ ! exp ]          # NOT
```

### If-Then-Else

```bash
# Sintassi base
if [ condizione ]; then
    comandi
fi

# If-else
if [ condizione ]; then
    comandi_vero
else
    comandi_falso
fi

# If-elif-else
if [ condizione1 ]; then
    comandi1
elif [ condizione2 ]; then
    comandi2
else
    comandi_default
fi

# Esempio
if [ $# -ne 2 ]; then
    echo "Uso: $0 file1 file2"
    exit 1
fi

if [ -f "$1" ]; then
    echo "$1 è un file regolare"
fi
```

### While Loop

```bash
# Sintassi
while [ condizione ]; do
    comandi
done

# Esempio
counter=1
while [ $counter -le 5 ]; do
    echo "Iterazione $counter"
    counter=$((counter + 1))
done

# Loop infinito
while true; do
    comandi
done
```

### Until Loop

```bash
# Sintassi (esegue finché condizione è falsa)
until [ condizione ]; do
    comandi
done

# Esempio
counter=1
until [ $counter -gt 5 ]; do
    echo "Counter: $counter"
    counter=$((counter + 1))
done
```

### For Loop

```bash
# Sintassi 1: lista
for var in lista; do
    comandi
done

# Esempi
for i in 1 2 3 4 5; do
    echo $i
done

for file in *.txt; do
    echo "Processing $file"
done

for user in $(cat users.txt); do
    echo "User: $user"
done

# Sintassi 2: stile C
for ((i=1; i<=10; i++)); do
    echo $i
done
```

### Case

```bash
# Sintassi
case $variabile in
    pattern1)
        comandi1
        ;;
    pattern2)
        comandi2
        ;;
    *)
        comandi_default
        ;;
esac

# Esempio
case $1 in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Uso: $0 {start|stop|restart}"
        exit 1
        ;;
esac

# Pattern con wildcard
case $file in
    *.txt)
        echo "File di testo"
        ;;
    *.jpg|*.png)
        echo "File immagine"
        ;;
esac
```

### Read (Input Utente)

```bash
# Sintassi base
read variabile
read var1 var2 var3    # split automatico

# La variabile REPLY contiene l'input se non specifichi nome
read
echo "Hai scritto: $REPLY"

# Esempio
echo -n "Inserisci il tuo nome: "
read nome
echo "Ciao $nome!"

# Prompt integrato
read -p "Confermi? (s/n): " risposta
```

---

## Comandi Filtro

I filtri sono comandi che leggono da stdin, processano, e scrivono su stdout.

### grep, egrep, fgrep

```bash
# Cerca pattern in file
grep pattern file
grep "stringa" file1 file2

# Opzioni comuni
grep -i pattern file       # ignora maiuscole/minuscole
grep -v pattern file       # linee che NON contengono pattern
grep -n pattern file       # numera le linee
grep -l pattern *.txt      # solo nomi file
grep -w pattern file       # parola intera
grep -r pattern dir/       # ricorsivo

# egrep (extended regex)
egrep 'pattern1|pattern2' file
egrep '[0-9]+' file
egrep '^[A-Z]' file

# fgrep (fixed string, più veloce)
fgrep 'stringa.letterale' file

# Esempi
grep '^root' /etc/passwd
ps | grep firefox
ls -l | grep '^d'          # solo directory
```

### Espressioni Regolari

| Metacarattere | Significato |
|---------------|-------------|
| `^` | Inizio linea |
| `$` | Fine linea |
| `.` | Qualsiasi carattere |
| `*` | 0 o più ripetizioni |
| `+` | 1 o più ripetizioni (egrep) |
| `?` | 0 o 1 ripetizione (egrep) |
| `[abc]` | Uno tra a, b, c |
| `[^abc]` | Nessuno tra a, b, c |
| `[a-z]` | Range |
| `\<` | Inizio parola |
| `\>` | Fine parola |
| `\|` | OR (egrep) |
| `(...)` | Raggruppamento (egrep) |

### sort

```bash
# Ordinamento base
sort file
sort file1 file2

# Opzioni
sort -r file           # reverse (decrescente)
sort -n file           # numerico
sort -f file           # ignora maiuscole
sort -u file           # rimuovi duplicati
sort -o output file    # salva risultato

# Per colonne
sort -t: -k3 -n /etc/passwd    # ordina per campo 3, numericamente
sort -t',' -k2,2 file.csv      # solo campo 2

# Opzioni campo
sort -k1,1 file        # solo primo campo
sort -k2 -k1 file      # prima per campo 2, poi per campo 1
```

### uniq

```bash
# Rimuove linee duplicate adiacenti (usa con sort!)
sort file | uniq

# Opzioni
uniq -c file           # conta occorrenze
uniq -d file           # solo duplicati
uniq -u file           # solo unici

# Esempio
sort names.txt | uniq -c | sort -rn    # frequenza parole
```

### wc (Word Count)

```bash
wc file               # linee parole caratteri
wc -l file            # solo linee
wc -w file            # solo parole
wc -c file            # solo byte

# Esempi
ls | wc -l            # conta file
who | wc -l           # utenti connessi
```

### tr (Translate)

```bash
# Sintassi
tr set1 set2

# Esempi
tr 'a-z' 'A-Z'             # minuscole → maiuscole
tr -d 'aeiou'              # cancella vocali
tr -s ' '                  # comprimi spazi multipli
tr -c 'a-zA-Z0-9' ' '      # sostituisci non-alfanumerici

# Da file
cat file | tr 'a-z' 'A-Z'
tr 'a-z' 'A-Z' < input.txt > output.txt
```

### cut

```bash
# Estrae colonne
cut -d: -f1 /etc/passwd              # campo 1, delimitatore :
cut -d',' -f1,3 file.csv             # campi 1 e 3
cut -c1-10 file                      # caratteri 1-10
cut -c1,5,10 file                    # caratteri 1, 5, 10

# Esempi
cut -d: -f1,6 /etc/passwd            # user e home
ps | cut -c1-20                      # primi 20 caratteri
```

### paste

```bash
# Unisce file riga per riga
paste file1 file2                    # default tab
paste -d',' file1 file2              # delimitatore virgola
paste -s file                        # tutte le righe su una linea

# Esempio
cut -d: -f1 /etc/passwd > users.txt
cut -d: -f6 /etc/passwd > homes.txt
paste users.txt homes.txt
```

### sed (Stream Editor)

```bash
# Sostituzione base
sed 's/old/new/' file                # prima occorrenza per riga
sed 's/old/new/g' file               # tutte le occorrenze
sed 's/old/new/2' file               # seconda occorrenza

# Range di righe
sed '1,10s/old/new/g' file           # righe 1-10
sed '5s/old/new/' file               # solo riga 5
sed '$s/old/new/' file               # ultima riga

# Cancellazione
sed '3d' file                        # cancella riga 3
sed '1,10d' file                     # cancella righe 1-10
sed '/pattern/d' file                # cancella righe con pattern

# Stampa
sed -n 'p' file                      # stampa tutto (come cat)
sed -n '5p' file                     # stampa solo riga 5
sed -n '/pattern/p' file             # stampa righe con pattern

# Multiple operazioni
sed -e 's/old/new/' -e 's/foo/bar/' file
sed 's/old/new/; s/foo/bar/' file

# In-place editing (modifica file)
sed -i 's/old/new/g' file            # Linux
sed -i '' 's/old/new/g' file         # macOS

# Esempi avanzati
sed 's/:x:/::/g' /etc/passwd
sed '/^#/d' config.ini               # rimuovi commenti
sed 's/^[ \t]*//' file               # rimuovi spazi iniziali
```

---

## Editor

### vi/vim - Modalità e Comandi Essenziali

#### Modalità

1. **Normal Mode** (default) - navigazione
2. **Insert Mode** - inserimento testo
3. **Command Mode** - comandi avanzati

#### Normal Mode

```
# Movimento
h, j, k, l        sinistra, giù, su, destra
w, b              parola avanti/indietro
0, $              inizio/fine riga
gg, G             inizio/fine file
Ctrl-f, Ctrl-b    pagina avanti/indietro

# Cancellazione
x                 cancella carattere
dw                cancella parola
dd                cancella riga
D                 cancella fino a fine riga

# Copia e incolla
yy                copia riga
p                 incolla dopo
P                 incolla prima

# Altro
u                 undo
Ctrl-r            redo
.                 ripeti ultimo comando
```

#### Insert Mode

```
i                 inserisci prima del cursore
a                 inserisci dopo il cursore
I                 inserisci a inizio riga
A                 inserisci a fine riga
o                 nuova riga sotto
O                 nuova riga sopra
Esc               torna a Normal Mode
```

#### Command Mode

```
:w                salva
:q                esci
:wq o ZZ          salva e esci
:q!               esci senza salvare
:w file           salva come
:/pattern         cerca
:s/old/new/       sostituisci
:s/old/new/g      sostituisci (tutte occorrenze)
:%s/old/new/g     sostituisci in tutto il file
:set number       mostra numeri riga
:help             aiuto
```

### Emacs - Comandi Base

```
C-x C-f           apri file
C-x C-s           salva
C-x C-c           esci

C-a               inizio riga
C-e               fine riga
C-k               taglia fino a fine riga

C-Space           set mark
C-w               taglia regione
M-w               copia regione
C-y               incolla

C-x u             undo
C-g               annulla comando
```

### nano - Editor Semplice

```
nano file         apri file

# Comandi (mostrati in basso)
Ctrl-O            salva
Ctrl-X            esci
Ctrl-K            taglia riga
Ctrl-U            incolla
Ctrl-W            cerca
Ctrl-G            aiuto
```

---

## Filesystem e Spazio Disco

### Mount

```bash
# Visualizzare filesystem montati
mount
df

# Montare filesystem (come root)
mount /dev/sda1 /mnt/disk

# Smontare
umount /mnt/disk

# File /etc/fstab contiene configurazione mount automatici
```

### Spazio Disco

```bash
# Spazio disponibile
df              # tutti i filesystem
df -h           # formato human-readable
df .            # filesystem della directory corrente

# Spazio usato
du              # directory corrente
du -h           # human-readable
du -s           # solo totale
du -sh *        # totale per ogni elemento
```

---

## Script Utili - Esempi Completi

### Script con Parametri

```bash
#!/bin/bash
# Copia file in directory

if [ $# -ne 2 ]; then
    echo "Uso: $0 <file> <directory>"
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "Errore: $1 non è un file"
    exit 1
fi

if [ ! -d "$2" ]; then
    echo "Errore: $2 non è una directory"
    exit 1
fi

cp "$1" "$2/"
echo "File $1 copiato in $2/"
```

### Backup Script

```bash
#!/bin/bash
# Backup directory

SOURCE="/home/user/documenti"
DEST="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
ARCHIVE="backup_$DATE.tar.gz"

tar -czf "$DEST/$ARCHIVE" "$SOURCE"

if [ $? -eq 0 ]; then
    echo "Backup completato: $ARCHIVE"
else
    echo "Errore durante il backup"
    exit 1
fi
```

### Menu Interattivo

```bash
#!/bin/bash

while true; do
    echo "=== MENU ==="
    echo "1. Lista file"
    echo "2. Mostra data"
    echo "3. Processi"
    echo "4. Esci"
    echo -n "Scelta: "
    read scelta
    
    case $scelta in
        1) ls -la ;;
        2) date ;;
        3) ps aux ;;
        4) echo "Uscita..."; exit 0 ;;
        *) echo "Scelta non valida" ;;
    esac
    
    echo
    read -p "Premi Invio per continuare..."
done
```

### Trova File Grandi

```bash
#!/bin/bash
# Trova file più grandi di N MB

if [ $# -ne 2 ]; then
    echo "Uso: $0 <directory> <dimensione_MB>"
    exit 1
fi

find "$1" -type f -size +${2}M -exec ls -lh {} \; | awk '{print $9, $5}'
```

### Log Rotator

```bash
#!/bin/bash
# Ruota file di log

LOGFILE="app.log"
MAX_SIZE=10485760  # 10MB in byte

if [ ! -f "$LOGFILE" ]; then
    echo "Log file non trovato"
    exit 1
fi

SIZE=$(stat -f%z "$LOGFILE" 2>/dev/null || stat -c%s "$LOGFILE" 2>/dev/null)

if [ $SIZE -gt $MAX_SIZE ]; then
    TIMESTAMP=$(date +%Y%m%d_%H%M%S)
    mv "$LOGFILE" "${LOGFILE}.${TIMESTAMP}"
    touch "$LOGFILE"
    echo "Log ruotato: ${LOGFILE}.${TIMESTAMP}"
fi
```

---

## Tips & Tricks

### Shortcut Utili

```bash
Ctrl-C            termina comando
Ctrl-D            EOF / logout
Ctrl-Z            sospendi processo
Ctrl-L            pulisci schermo (come clear)
Ctrl-R            ricerca in history
Ctrl-A            inizio linea
Ctrl-E            fine linea
Ctrl-U            cancella inizio linea
Ctrl-K            cancella fine linea
```

### One-Liners Utili

```bash
# Trova file duplicati
find . -type f -exec md5sum {} \; | sort | uniq -w32 -d

# Processi che usano più memoria
ps aux | sort -k4 -rn | head -10

# Processi che usano più CPU
ps aux | sort -k3 -rn | head -10

# Spazio usato per directory (top 10)
du -sh * | sort -rh | head -10

# Linee più lunghe in un file
awk '{print length, $0}' file | sort -rn | head

# IP connessi al server
netstat -an | grep :80 | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -rn

# Trova file modificati nelle ultime 24 ore
find . -type f -mtime -1

# Rimuovi file vecchi (più di 30 giorni)
find /tmp -type f -mtime +30 -delete

# Conta righe di codice in progetto
find . -name "*.java" -exec wc -l {} + | tail -1

# Trova file vuoti
find . -type f -empty

# Estrai colonna da CSV
awk -F',' '{print $2}' file.csv

# Somma valori in colonna
awk '{sum += $1} END {print sum}' file.txt

# Trova righe comuni in due file
comm -12 <(sort file1) <(sort file2)

# Sostituisci spazi con underscore nei nomi file
for f in *\ *; do mv "$f" "${f// /_}"; done

# Converti tutti i file in minuscolo
for f in *; do mv "$f" "$(echo $f | tr '[:upper:]' '[:lower:]')"; done

# Backup rapido
cp file.txt{,.bak}    # crea file.txt.bak

# Crea directory e spostati dentro
mkdir nuovo && cd nuovo

# Scarica e esegui script
curl -fsSL url | bash
```

### File Temporanei Sicuri

```bash
# Nome unico con PID
TMPFILE="/tmp/script_$.tmp"
echo "data" > $TMPFILE
# ... usa il file
rm -f $TMPFILE

# mktemp (metodo migliore)
TMPFILE=$(mktemp)
echo "data" > $TMPFILE
# ... usa il file
rm -f $TMPFILE

# Directory temporanea
TMPDIR=$(mktemp -d)
# ... usa la directory
rm -rf $TMPDIR
```

### Trap - Cleanup su Exit

```bash
#!/bin/bash

# Definisci cleanup
cleanup() {
    echo "Pulizia in corso..."
    rm -f /tmp/tempfile_$
    # altri comandi di cleanup
}

# Esegui cleanup su EXIT
trap cleanup EXIT

# Script continua normalmente
TMPFILE="/tmp/tempfile_$"
touch $TMPFILE
# ... lavora con il file

# cleanup() sarà chiamata automaticamente all'uscita
```

### Funzioni in Bash

```bash
#!/bin/bash

# Definizione funzione
saluta() {
    echo "Ciao $1!"
}

# Funzione con return
somma() {
    local result=$(($1 + $2))
    echo $result
}

# Funzione con validazione
controlla_file() {
    if [ ! -f "$1" ]; then
        echo "Errore: $1 non esiste"
        return 1
    fi
    return 0
}

# Uso
saluta "Mario"
risultato=$(somma 5 3)
echo "5 + 3 = $risultato"

if controlla_file "config.txt"; then
    echo "File trovato"
fi
```

### Array in Bash

```bash
# Dichiarazione
array=(elemento1 elemento2 elemento3)
array[3]="elemento4"

# Accesso
echo ${array[0]}        # primo elemento
echo ${array[@]}        # tutti gli elementi
echo ${#array[@]}       # numero elementi

# Iterazione
for elem in "${array[@]}"; do
    echo $elem
done

# Array da comando
files=($(ls *.txt))

# Array associativi (Bash 4+)
declare -A colori
colori[rosso]="#FF0000"
colori[verde]="#00FF00"
echo ${colori[rosso]}
```

### Gestione Stringhe

```bash
# Lunghezza
stringa="Hello World"
echo ${#stringa}        # 11

# Substring
echo ${stringa:0:5}     # Hello
echo ${stringa:6}       # World

# Sostituzione
echo ${stringa/World/Bash}    # Hello Bash (prima occorrenza)
echo ${stringa//o/0}          # Hell0 W0rld (tutte le occorrenze)

# Rimozione pattern
file="documento.txt"
echo ${file%.txt}       # documento (rimuove .txt alla fine)
echo ${file#*/}         # rimuove fino a primo /

path="/home/user/file.txt"
echo ${path##*/}        # file.txt (basename)
echo ${path%/*}         # /home/user (dirname)

# Maiuscole/Minuscole (Bash 4+)
echo ${stringa^^}       # HELLO WORLD
echo ${stringa,,}       # hello world
echo ${stringa^}        # Hello world (solo prima lettera)
```

### Verifica Condizioni Multiple

```bash
# File esiste E è leggibile
if [ -e "$file" ] && [ -r "$file" ]; then
    cat "$file"
fi

# Equivalente con [[
if [[ -e "$file" && -r "$file" ]]; then
    cat "$file"
fi

# Più condizioni
if [[ -f "$file" && -r "$file" && -w "$file" ]]; then
    echo "File accessibile in lettura e scrittura"
fi

# Pattern matching
if [[ "$file" == *.txt ]]; then
    echo "File di testo"
fi

# Regex (Bash 3+)
if [[ "$email" =~ ^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$ ]]; then
    echo "Email valida"
fi
```

### Opzioni Script Avanzate

```bash
#!/bin/bash

# Opzioni script
set -e          # esci su errore
set -u          # errore su variabile non definita
set -o pipefail # errore se comando in pipe fallisce
set -x          # debug (stampa comandi)

# Combinato
set -euo pipefail

# Gestione opzioni con getopts
while getopts "hvf:o:" opt; do
    case $opt in
        h)
            echo "Help"
            exit 0
            ;;
        v)
            verbose=true
            ;;
        f)
            input_file="$OPTARG"
            ;;
        o)
            output_file="$OPTARG"
            ;;
        \?)
            echo "Opzione invalida: -$OPTARG"
            exit 1
            ;;
    esac
done

# Uso: script.sh -v -f input.txt -o output.txt
```

### Colori nel Terminale

```bash
#!/bin/bash

# Codici colore ANSI
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[0;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Uso
echo -e "${RED}Errore!${NC}"
echo -e "${GREEN}Successo!${NC}"
echo -e "${YELLOW}Attenzione${NC}"

# Funzioni helper
print_error() {
    echo -e "${RED}[ERRORE]${NC} $1"
}

print_success() {
    echo -e "${GREEN}[OK]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

# Esempio uso
print_error "File non trovato"
print_success "Operazione completata"
print_warning "Spazio disco basso"
```

### Progress Bar

```bash
#!/bin/bash

# Progress bar semplice
progress_bar() {
    local duration=$1
    local steps=50
    local increment=$((duration / steps))
    
    for ((i=0; i<=steps; i++)); do
        local percent=$((i * 100 / steps))
        local filled=$((i * 50 / steps))
        local empty=$((50 - filled))
        
        printf "\r["
        printf "%${filled}s" | tr ' ' '='
        printf "%${empty}s" | tr ' ' ' '
        printf "] %d%%" $percent
        
        sleep 0.1
    done
    echo
}

# Uso
echo "Processando..."
progress_bar 10
echo "Completato!"
```

### Logging Avanzato

```bash
#!/bin/bash

LOGFILE="script.log"

# Funzione log
log() {
    local level=$1
    shift
    local message="$@"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] [$level] $message" | tee -a "$LOGFILE"
}

# Helper functions
log_info() {
    log "INFO" "$@"
}

log_error() {
    log "ERROR" "$@" >&2
}

log_warning() {
    log "WARNING" "$@"
}

# Uso
log_info "Script avviato"
log_warning "Spazio disco basso"
log_error "Operazione fallita"
```

### Validazione Input

```bash
#!/bin/bash

# Valida numero
is_number() {
    [[ "$1" =~ ^[0-9]+$ ]]
}

# Valida email
is_email() {
    [[ "$1" =~ ^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$ ]]
}

# Valida IP
is_ip() {
    [[ "$1" =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]
}

# Valida path
is_absolute_path() {
    [[ "$1" =~ ^/ ]]
}

# Prompt con validazione
read_number() {
    local prompt="$1"
    local var_name="$2"
    local input
    
    while true; do
        read -p "$prompt" input
        if is_number "$input"; then
            eval "$var_name=$input"
            break
        else
            echo "Errore: inserire un numero"
        fi
    done
}

# Uso
read_number "Inserisci età: " eta
echo "Età inserita: $eta"
```

---

## Debugging e Troubleshooting

### Tecniche di Debug

```bash
# Stampa variabili
echo "DEBUG: variabile=$variabile"
echo "DEBUG: numero parametri=$#"
echo "DEBUG: tutti i parametri=$@"

# Set -x per vedere esecuzione
set -x
comando1
comando2
set +x

# Debug condizionale
DEBUG=1
debug() {
    if [ "$DEBUG" = "1" ]; then
        echo "DEBUG: $@" >&2
    fi
}

debug "Entrato nella funzione"
debug "variabile=$var"

# Trace function calls
set -T
trap 'echo ">> Executing: $BASH_COMMAND"' DEBUG
```

### Gestione Errori

```bash
#!/bin/bash

# Exit su primo errore
set -e

# Funzione error handler
error_exit() {
    echo "Errore alla linea $1" >&2
    exit 1
}

# Trap errori
trap 'error_exit $LINENO' ERR

# Controllo exit status
if ! comando; then
    echo "Comando fallito"
    exit 1
fi

# Operatore ||
comando || {
    echo "Errore nell'esecuzione"
    exit 1
}

# Try-catch style
{
    comando_che_potrebbe_fallire
} || {
    echo "Gestione errore"
}
```

### Timeout per Comandi

```bash
# Timeout con timeout command (Linux)
timeout 10s comando

# Timeout manuale
comando &
PID=$!
sleep 10
if ps -p $PID > /dev/null; then
    kill $PID
    echo "Comando terminato per timeout"
fi

# Funzione timeout
run_with_timeout() {
    local timeout=$1
    shift
    local command="$@"
    
    $command &
    local pid=$!
    
    ( sleep $timeout; kill $pid 2>/dev/null ) &
    local killer=$!
    
    wait $pid 2>/dev/null
    local result=$?
    
    kill $killer 2>/dev/null
    return $result
}

# Uso
if run_with_timeout 5 "sleep 10"; then
    echo "Completato"
else
    echo "Timeout!"
fi
```

---

## Best Practices

### Convenzioni Codice

```bash
#!/bin/bash
#
# Nome Script: backup.sh
# Descrizione: Effettua backup dei file
# Autore: Nome Cognome
# Data: 2025-01-20
# Versione: 1.0
#

# Usa nomi variabili MAIUSCOLE per costanti
readonly CONFIG_FILE="/etc/myapp/config"
readonly VERSION="1.0"

# Usa nomi minuscoli per variabili
local_var="valore"
counter=0

# Usa underscore per separare parole
user_name="mario"
file_path="/tmp/data"

# Funzioni: lowercase con underscore
calculate_sum() {
    local a=$1
    local b=$2
    echo $((a + b))
}

# Usa 'local' nelle funzioni
my_function() {
    local local_var="locale"    # non esce dalla funzione
    global_var="globale"         # accessibile ovunque
}

# Commenti significativi
# Controlla se il file esiste prima di procedere
if [ -f "$file" ]; then
    # Processa il file
    process_file "$file"
fi
```

### Quote Variabili

```bash
# SEMPRE quote variabili che contengono path o input utente
file="my file.txt"

# SBAGLIATO
cat $file          # errore: cerca 'my' e 'file.txt'

# CORRETTO
cat "$file"        # ok: legge 'my file.txt'

# Quote in test
if [ -f "$file" ]; then    # corretto
    echo "Trovato"
fi

# Array: quote per preservare elementi
for item in "${array[@]}"; do    # corretto
    echo "$item"
done
```

### Controllo Precondizioni

```bash
#!/bin/bash

# Controlla di essere root
if [ "$EUID" -ne 0 ]; then
    echo "Questo script deve essere eseguito come root"
    exit 1
fi

# Controlla comando disponibile
if ! command -v rsync &> /dev/null; then
    echo "rsync non trovato. Installare prima di continuare."
    exit 1
fi

# Controlla variabili richieste
if [ -z "$DATABASE_URL" ]; then
    echo "Errore: DATABASE_URL non definita"
    exit 1
fi

# Controlla numero parametri
if [ $# -lt 2 ]; then
    echo "Uso: $0 <input> <output>"
    exit 1
fi
```

### Script Template Completo

```bash
#!/bin/bash
#
# Script Template
# Descrizione: Template base per script bash
#

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Configurazione
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"
readonly VERSION="1.0.0"

# Colori
readonly RED='\033[0;31m'
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly NC='\033[0m'

# Logging
log() {
    echo -e "[$SCRIPT_NAME] $*"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $*" >&2
}

log_success() {
    echo -e "${GREEN}[OK]${NC} $*"
}

log_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $*"
}

# Cleanup
cleanup() {
    log "Cleanup..."
    # Aggiungi qui operazioni di cleanup
}

trap cleanup EXIT

# Help
show_help() {
    cat << EOF
Uso: $SCRIPT_NAME [OPZIONI]

Descrizione dello script.

OPZIONI:
    -h, --help      Mostra questo help
    -v, --version   Mostra versione
    -d, --debug     Abilita debug mode

ESEMPI:
    $SCRIPT_NAME -d
    $SCRIPT_NAME --help

EOF
}

# Main function
main() {
    # Parse arguments
    while [[ $# -gt 0 ]]; do
        case $1 in
            -h|--help)
                show_help
                exit 0
                ;;
            -v|--version)
                echo "$SCRIPT_NAME version $VERSION"
                exit 0
                ;;
            -d|--debug)
                set -x
                shift
                ;;
            *)
                log_error "Opzione sconosciuta: $1"
                show_help
                exit 1
                ;;
        esac
    done
    
    # Script logic here
    log "Script avviato"
    
    # Esempio di lavoro
    log "Esecuzione operazioni..."
    
    log_success "Script completato"
}

# Esegui main se script chiamato direttamente
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main "$@"
fi
```

---

## Risorse e Riferimenti

### Documentazione

- `man bash` - Manuale completo Bash
- `man 1 comando` - Manuale comandi
- `info bash` - Documentazione info
- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Advanced Bash Scripting Guide](https://tldp.org/LDP/abs/html/)

### Strumenti Utili

- **ShellCheck** - Linter per script bash
  ```bash
  shellcheck script.sh
  ```

- **Bash Debugger** (bashdb)
  ```bash
  bashdb script.sh
  ```

- **explainshell.com** - Spiega comandi bash online

### Comandi di Sistema Utili

```bash
# Informazioni sistema
uname -a        # info kernel
hostname        # nome host
uptime          # tempo di attività
whoami          # utente corrente
id              # info utente
groups          # gruppi utente

# Rete
ifconfig        # configurazione rete (deprecato)
ip addr         # configurazione rete (moderno)
netstat -tulpn  # porte in ascolto
ss -tulpn       # come netstat (moderno)
ping host       # test connettività
traceroute host # traccia percorso
wget url        # scarica file
curl url        # trasferimento dati

# Disco e filesystem
fdisk -l        # lista dischi (root)
lsblk           # lista block devices
mount           # filesystem montati
umount          # smonta filesystem

# Processi e risorse
free -h         # memoria
top             # monitor processi
htop            # top migliorato
ps aux          # tutti i processi
pgrep nome      # trova PID per nome
pkill nome      # termina per nome

# Archivi
tar -czf arch.tar.gz dir/   # comprimi
tar -xzf arch.tar.gz        # estrai
zip -r arch.zip dir/        # zip
unzip arch.zip              # unzip
gzip file                   # comprimi file
gunzip file.gz              # decomprimi

# Trasferimento file
scp file user@host:/path    # copia remota
rsync -av src/ dest/        # sincronizza
```

---

## Conclusione

Questa guida copre gli aspetti fondamentali della programmazione Bash necessari per:

- Automatizzare task ripetitivi
- Amministrare sistemi Unix/Linux
- Processare dati testuali
- Creare pipeline complesse
- Scrivere script robusti e manutenibili
