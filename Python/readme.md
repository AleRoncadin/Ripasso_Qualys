# Python per Qualys API - Guida Completa

Una guida pratica per sviluppare script Python per l'automazione del Vulnerability Management con Qualys API.

## Indice

- [Setup Ambiente](#setup-ambiente)
- [Ripasso Base Python](#ripasso-base-python)
- [Gestione Stringhe](#gestione-stringhe)
- [Strutture Dati](#strutture-dati)
- [Libreria Requests - HTTP/API](#libreria-requests---httpapi)
- [Parsing XML](#parsing-xml)
- [Gestione JSON](#gestione-json)
- [Export CSV](#export-csv)
- [Logging Professionale](#logging-professionale)
- [Argparse - Parametri CLI](#argparse---parametri-cli)
- [Scheduling e Automazione](#scheduling-e-automazione)
- [Best Practices](#best-practices)
- [Esempi Completi Qualys](#esempi-completi-qualys)
- [Troubleshooting](#troubleshooting)

---

## Setup Ambiente

### Virtual Environment

```bash
# Creare virtual environment
python3 -m venv venv

# Attivare (Linux/Mac)
source venv/bin/activate

# Attivare (Windows)
venv\Scripts\activate

# Disattivare
deactivate

# Verificare ambiente attivo
which python
python --version
```

### Installazione Dipendenze

```bash
# Installare librerie necessarie
pip install requests
pip install lxml
pip install schedule
pip install python-dotenv

# Creare requirements.txt
pip freeze > requirements.txt

# Installare da requirements.txt
pip install -r requirements.txt
```

### File requirements.txt (esempio)

```txt
requests==2.31.0
lxml==4.9.3
schedule==1.2.0
python-dotenv==1.0.0
```

### Struttura Progetto

```
qualys-automation/
│
├── venv/                    # Virtual environment (non committare)
├── src/
│   ├── __init__.py
│   ├── qualys_client.py     # Client API Qualys
│   ├── scanner.py           # Logica scansioni
│   └── reporter.py          # Generazione report
├── config/
│   ├── .env                 # Credenziali (non committare!)
│   └── config.yaml          # Configurazione
├── logs/                    # Log file
├── output/                  # Output report
├── tests/
│   └── test_qualys.py
├── main.py                  # Script principale
├── requirements.txt
└── README.md
```

---

## Ripasso Base Python

### Sintassi Fondamentale

```python
# Commenti
# Questo è un commento singolo

"""
Questo è un commento
multi-linea (docstring)
"""

# Variabili (tipizzazione dinamica)
nome = "Mario"              # string
eta = 30                    # int
altezza = 1.75              # float
attivo = True               # bool

# Tipizzazione statica (type hints - Python 3.5+)
nome: str = "Mario"
eta: int = 30

# Print
print("Hello World")
print(f"Nome: {nome}, Età: {eta}")  # f-string (Python 3.6+)
print("Valore:", eta, "anni")

# Input utente
nome = input("Inserisci nome: ")
eta = int(input("Inserisci età: "))

# Operatori
somma = 10 + 5
differenza = 10 - 5
prodotto = 10 * 5
divisione = 10 / 3          # 3.333... (float)
divisione_intera = 10 // 3  # 3 (int)
resto = 10 % 3              # 1
potenza = 2 ** 3            # 8

# Confronto
uguale = (5 == 5)           # True
diverso = (5 != 3)          # True
maggiore = (5 > 3)          # True

# Logici
and_logic = True and False  # False
or_logic = True or False    # True
not_logic = not True        # False
```

### Strutture di Controllo

```python
# If-elif-else
eta = 18

if eta < 18:
    print("Minorenne")
elif eta == 18:
    print("Appena maggiorenne")
else:
    print("Maggiorenne")

# Operatore ternario
stato = "maggiorenne" if eta >= 18 else "minorenne"

# For loop
for i in range(5):          # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):       # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 10, 2):   # 0, 2, 4, 6, 8
    print(i)

# Iterare su lista
frutti = ["mela", "pera", "banana"]
for frutto in frutti:
    print(frutto)

# Enumerate (con indice)
for indice, frutto in enumerate(frutti):
    print(f"{indice}: {frutto}")

# While loop
contatore = 0
while contatore < 5:
    print(contatore)
    contatore += 1

# Break e continue
for i in range(10):
    if i == 3:
        continue  # salta questa iterazione
    if i == 7:
        break     # esci dal loop
    print(i)
```

### Funzioni

```python
# Funzione base
def saluta(nome):
    return f"Ciao {nome}!"

risultato = saluta("Mario")
print(risultato)

# Parametri con default
def saluta(nome, titolo="Sig."):
    return f"Ciao {titolo} {nome}!"

print(saluta("Rossi"))              # Ciao Sig. Rossi!
print(saluta("Rossi", "Dr."))       # Ciao Dr. Rossi!

# Type hints
def somma(a: int, b: int) -> int:
    return a + b

# Parametri multipli (*args)
def somma_tutti(*numeri):
    return sum(numeri)

print(somma_tutti(1, 2, 3, 4, 5))   # 15

# Parametri keyword (**kwargs)
def info_persona(**dati):
    for chiave, valore in dati.items():
        print(f"{chiave}: {valore}")

info_persona(nome="Mario", eta=30, citta="Roma")

# Lambda (funzioni anonime)
quadrato = lambda x: x ** 2
print(quadrato(5))  # 25

# Map, filter
numeri = [1, 2, 3, 4, 5]
quadrati = list(map(lambda x: x**2, numeri))
pari = list(filter(lambda x: x % 2 == 0, numeri))
```

### Comprehensions

```python
# List comprehension
quadrati = [x**2 for x in range(10)]
pari = [x for x in range(20) if x % 2 == 0]

# Dict comprehension
quadrati_dict = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Set comprehension
pari_set = {x for x in range(20) if x % 2 == 0}

# Con condizione
risultato = [x if x % 2 == 0 else -x for x in range(10)]
```

---

## Gestione Stringhe

### Operazioni Base

```python
# Creazione
testo = "Hello World"
testo2 = 'Python'
multiriga = """Questo è
un testo
su più righe"""

# Concatenazione
completo = testo + " " + testo2
completo = f"{testo} {testo2}"      # f-string (preferito)

# Ripetizione
ripetuto = "=" * 50

# Lunghezza
lunghezza = len(testo)

# Accesso caratteri
primo = testo[0]        # 'H'
ultimo = testo[-1]      # 'd'

# Slicing
primi_5 = testo[0:5]    # 'Hello'
dal_6 = testo[6:]       # 'World'
fino_5 = testo[:5]      # 'Hello'
inverso = testo[::-1]   # 'dlroW olleH'
```

### Metodi Stringhe

```python
testo = "  Hello World  "

# Case
print(testo.upper())        # '  HELLO WORLD  '
print(testo.lower())        # '  hello world  '
print(testo.capitalize())   # '  hello world  '
print(testo.title())        # '  Hello World  '

# Whitespace
print(testo.strip())        # 'Hello World'
print(testo.lstrip())       # 'Hello World  '
print(testo.rstrip())       # '  Hello World'

# Ricerca
print("World" in testo)         # True
print(testo.find("World"))      # 8 (indice, -1 se non trovato)
print(testo.index("World"))     # 8 (errore se non trovato)
print(testo.count("o"))         # 2

# Sostituzione
nuovo = testo.replace("World", "Python")
print(nuovo)  # '  Hello Python  '

# Split e Join
parole = testo.strip().split()  # ['Hello', 'World']
unito = "-".join(parole)        # 'Hello-World'

# Controlli
print("123".isdigit())      # True
print("abc".isalpha())      # True
print("abc123".isalnum())   # True
print(testo.startswith("  He"))  # True
print(testo.endswith("d  "))     # True
```

### Formattazione

```python
nome = "Mario"
eta = 30
altezza = 1.75

# f-string (Python 3.6+, preferito)
msg = f"Mi chiamo {nome} e ho {eta} anni"
msg = f"Altezza: {altezza:.2f} m"  # 2 decimali

# format()
msg = "Mi chiamo {} e ho {} anni".format(nome, eta)
msg = "Mi chiamo {n} e ho {e} anni".format(n=nome, e=eta)
msg = "Prezzo: {:.2f} €".format(19.99)

# % formatting (vecchio stile)
msg = "Mi chiamo %s e ho %d anni" % (nome, eta)

# Padding e allineamento
print(f"{nome:>10}")    # destra
print(f"{nome:<10}")    # sinistra
print(f"{nome:^10}")    # centro
print(f"{nome:*^10}")   # centro con riempimento
```

### Espressioni Regolari

```python
import re

testo = "La mia email è mario.rossi@example.com"

# Cerca pattern
match = re.search(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', testo)
if match:
    print(f"Email trovata: {match.group()}")

# Trova tutte le occorrenze
emails = re.findall(r'\S+@\S+', testo)

# Sostituzione
nuovo = re.sub(r'@\S+', '@hidden.com', testo)

# Split
parti = re.split(r'\s+', testo)

# Compilazione pattern (per riuso)
pattern = re.compile(r'\d+')
numeri = pattern.findall("Ho 2 mele e 5 pere")  # ['2', '5']
```

---

## Strutture Dati

### Liste

```python
# Creazione
lista_vuota = []
lista = [1, 2, 3, 4, 5]
mista = [1, "due", 3.0, True]

# Accesso
primo = lista[0]
ultimo = lista[-1]
slice = lista[1:3]  # [2, 3]

# Modifica
lista[0] = 10
lista.append(6)         # aggiungi alla fine
lista.insert(0, 0)      # inserisci in posizione
lista.extend([7, 8])    # aggiungi lista

# Rimozione
lista.remove(10)        # rimuovi per valore
elemento = lista.pop()  # rimuovi e ritorna ultimo
elemento = lista.pop(0) # rimuovi e ritorna in posizione
del lista[0]            # rimuovi per indice
lista.clear()           # svuota lista

# Operazioni
numeri = [3, 1, 4, 1, 5, 9, 2, 6]
print(len(numeri))      # lunghezza
print(sum(numeri))      # somma
print(min(numeri))      # minimo
print(max(numeri))      # massimo
print(numeri.count(1))  # conta occorrenze
print(numeri.index(4))  # trova indice

# Ordinamento
numeri.sort()           # ordina in-place
numeri.sort(reverse=True)  # decrescente
ordinati = sorted(numeri)  # ritorna nuova lista

numeri.reverse()        # inverti ordine

# Copia
copia_shallow = numeri.copy()
copia_deep = numeri[:]

# Unione e ripetizione
lista1 = [1, 2, 3]
lista2 = [4, 5, 6]
unita = lista1 + lista2
ripetuta = lista1 * 3

# Controllo presenza
if 5 in numeri:
    print("5 è nella lista")
```

### Tuple (immutabili)

```python
# Creazione
tupla = (1, 2, 3)
tupla_singola = (1,)    # virgola necessaria!
tupla_vuota = ()

# Accesso (come liste)
primo = tupla[0]
ultimo = tupla[-1]

# Unpacking
x, y, z = tupla
a, *resto, z = (1, 2, 3, 4, 5)  # a=1, resto=[2,3,4], z=5

# Tuple come chiavi dizionario
coordinate = {(0, 0): "origine", (1, 1): "diagonale"}

# Named tuples (più leggibili)
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
```

### Dizionari

```python
# Creazione
dizionario_vuoto = {}
persona = {
    "nome": "Mario",
    "cognome": "Rossi",
    "eta": 30
}

# Creazione alternativa
persona = dict(nome="Mario", cognome="Rossi", eta=30)

# Accesso
nome = persona["nome"]
nome = persona.get("nome")          # None se non esiste
nome = persona.get("nome", "N/A")   # valore default

# Modifica
persona["eta"] = 31
persona["citta"] = "Roma"  # aggiungi nuova chiave

# Rimozione
del persona["citta"]
eta = persona.pop("eta")        # rimuovi e ritorna
persona.clear()                 # svuota dizionario

# Operazioni
persona = {"nome": "Mario", "eta": 30, "citta": "Roma"}

print(len(persona))             # numero chiavi
print("nome" in persona)        # True
print("indirizzo" in persona)   # False

# Iterazione
for chiave in persona:
    print(chiave, persona[chiave])

for chiave, valore in persona.items():
    print(f"{chiave}: {valore}")

for chiave in persona.keys():
    print(chiave)

for valore in persona.values():
    print(valore)

# Metodi utili
persona.update({"professione": "Ingegnere", "eta": 31})
persona.setdefault("email", "default@email.com")

# Merge dizionari (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
merged = dict1 | dict2

# Dizionari annidati
utenti = {
    "user1": {"nome": "Mario", "eta": 30},
    "user2": {"nome": "Luigi", "eta": 25}
}
print(utenti["user1"]["nome"])  # Mario
```

### Set (insiemi)

```python
# Creazione
set_vuoto = set()
numeri = {1, 2, 3, 4, 5}
numeri = set([1, 2, 2, 3, 4, 4, 5])  # {1, 2, 3, 4, 5}

# Aggiunta e rimozione
numeri.add(6)
numeri.remove(1)        # errore se non esiste
numeri.discard(1)       # no errore se non esiste
numeri.pop()            # rimuovi elemento casuale
numeri.clear()

# Operazioni insiemistiche
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

unione = set1 | set2            # {1, 2, 3, 4, 5, 6}
intersezione = set1 & set2      # {3, 4}
differenza = set1 - set2        # {1, 2}
diff_simmetrica = set1 ^ set2   # {1, 2, 5, 6}

# Controlli
print(3 in set1)                # True
print(set1.issubset(set2))      # False
print(set1.issuperset(set2))    # False
print(set1.isdisjoint(set2))    # False

# Frozenset (immutabile)
immutabile = frozenset([1, 2, 3])
```

### Collections Avanzate

```python
from collections import defaultdict, Counter, deque

# defaultdict (valore default automatico)
contatore = defaultdict(int)
contatore['a'] += 1  # non serve inizializzare

gruppi = defaultdict(list)
gruppi['frutti'].append('mela')

# Counter (conteggio elementi)
parole = ['mela', 'pera', 'mela', 'banana', 'mela']
conteggio = Counter(parole)
print(conteggio)  # Counter({'mela': 3, 'pera': 1, 'banana': 1})
print(conteggio.most_common(2))  # [('mela', 3), ('pera', 1)]

# deque (coda doppia, efficiente per operazioni alle estremità)
coda = deque([1, 2, 3])
coda.append(4)       # aggiungi a destra
coda.appendleft(0)   # aggiungi a sinistra
coda.pop()           # rimuovi da destra
coda.popleft()       # rimuovi da sinistra
```

---

## Libreria Requests - HTTP/API

### Installazione e Import

```python
# Installare
# pip install requests

import requests
from requests.auth import HTTPBasicAuth
from requests.exceptions import RequestException, Timeout, HTTPError
```

### GET Request

```python
# GET base
response = requests.get('https://api.example.com/data')

# Status code
print(response.status_code)  # 200, 404, 500, ecc.

# Contenuto
print(response.text)         # stringa
print(response.content)      # bytes
data = response.json()       # parsing JSON automatico

# Headers response
print(response.headers)
print(response.headers['Content-Type'])

# GET con parametri
params = {
    'page': 1,
    'limit': 10,
    'filter': 'active'
}
response = requests.get('https://api.example.com/users', params=params)
# URL: https://api.example.com/users?page=1&limit=10&filter=active

# GET con headers personalizzati
headers = {
    'User-Agent': 'MyApp/1.0',
    'Accept': 'application/json'
}
response = requests.get('https://api.example.com/data', headers=headers)

# GET con timeout
try:
    response = requests.get('https://api.example.com/data', timeout=5)
except Timeout:
    print("Richiesta scaduta")
```

### POST Request

```python
# POST con dati JSON
data = {
    'username': 'mario',
    'email': 'mario@example.com'
}
response = requests.post('https://api.example.com/users', json=data)

# POST con dati form
data = {
    'username': 'mario',
    'password': 'secret123'
}
response = requests.post('https://api.example.com/login', data=data)

# POST con headers
headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer YOUR_TOKEN'
}
response = requests.post(
    'https://api.example.com/create',
    json=data,
    headers=headers
)

# Upload file
files = {'file': open('documento.pdf', 'rb')}
response = requests.post('https://api.example.com/upload', files=files)

# Upload con dati aggiuntivi
files = {'file': open('documento.pdf', 'rb')}
data = {'descrizione': 'Documento importante'}
response = requests.post(
    'https://api.example.com/upload',
    files=files,
    data=data
)
```

### PUT e DELETE

```python
# PUT (aggiornamento)
data = {'nome': 'Mario Rossi', 'eta': 31}
response = requests.put('https://api.example.com/users/123', json=data)

# PATCH (aggiornamento parziale)
data = {'eta': 31}
response = requests.patch('https://api.example.com/users/123', json=data)

# DELETE
response = requests.delete('https://api.example.com/users/123')

# Verifica successo
if response.status_code == 204:
    print("Eliminato con successo")
```

### Autenticazione

```python
# Basic Authentication
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://api.example.com/protected',
    auth=HTTPBasicAuth('username', 'password')
)

# Shortcut per Basic Auth
response = requests.get(
    'https://api.example.com/protected',
    auth=('username', 'password')
)

# Bearer Token (API Token)
headers = {
    'Authorization': 'Bearer YOUR_ACCESS_TOKEN'
}
response = requests.get('https://api.example.com/data', headers=headers)

# API Key in header
headers = {
    'X-API-Key': 'your-api-key-here'
}
response = requests.get('https://api.example.com/data', headers=headers)

# API Key in URL
params = {'api_key': 'your-api-key'}
response = requests.get('https://api.example.com/data', params=params)
```

### Gestione Errori

```python
from requests.exceptions import RequestException, Timeout, HTTPError, ConnectionError

# Try-except base
try:
    response = requests.get('https://api.example.com/data', timeout=5)
    response.raise_for_status()  # solleva eccezione per status 4xx/5xx
    data = response.json()
except Timeout:
    print("Timeout: il server non ha risposto in tempo")
except ConnectionError:
    print("Errore di connessione")
except HTTPError as e:
    print(f"Errore HTTP: {e}")
except RequestException as e:
    print(f"Errore generico: {e}")

# Controllo status code manuale
response = requests.get('https://api.example.com/data')

if response.status_code == 200:
    data = response.json()
elif response.status_code == 404:
    print("Risorsa non trovata")
elif response.status_code == 500:
    print("Errore server")
else:
    print(f"Errore: {response.status_code}")

# Retry automatico
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

session = requests.Session()
retry = Retry(
    total=3,
    backoff_factor=1,
    status_forcelist=[500, 502, 503, 504]
)
adapter = HTTPAdapter(max_retries=retry)
session.mount('http://', adapter)
session.mount('https://', adapter)

response = session.get('https://api.example.com/data')
```

### Session (Connessioni Persistenti)

```python
# Session mantiene cookies, headers, auth
session = requests.Session()

# Imposta headers per tutte le richieste
session.headers.update({
    'User-Agent': 'MyApp/1.0',
    'Accept': 'application/json'
})

# Login
response = session.post('https://api.example.com/login', json={
    'username': 'mario',
    'password': 'secret'
})

# Le richieste successive mantengono l'autenticazione
response = session.get('https://api.example.com/profile')
data = session.get('https://api.example.com/data').json()

# Chiudi session
session.close()

# Context manager (chiude automaticamente)
with requests.Session() as session:
    session.headers.update({'Authorization': 'Bearer TOKEN'})
    response = session.get('https://api.example.com/data')
    data = response.json()
```

### Esempio Completo API Client

```python
import requests
from typing import Dict, Any, Optional
import logging

class APIClient:
    """Client generico per API REST"""
    
    def __init__(self, base_url: str, api_key: Optional[str] = None):
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
        
        if api_key:
            self.session.headers.update({
                'Authorization': f'Bearer {api_key}',
                'Content-Type': 'application/json'
            })
        
        self.logger = logging.getLogger(__name__)
    
    def _make_request(self, method: str, endpoint: str, **kwargs) -> requests.Response:
        """Esegue richiesta HTTP con gestione errori"""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        
        try:
            response = self.session.request(method, url, **kwargs)
            response.raise_for_status()
            return response
        except requests.exceptions.HTTPError as e:
            self.logger.error(f"HTTP Error: {e}")
            raise
        except requests.exceptions.ConnectionError as e:
            self.logger.error(f"Connection Error: {e}")
            raise
        except requests.exceptions.Timeout as e:
            self.logger.error(f"Timeout Error: {e}")
            raise
        except requests.exceptions.RequestException as e:
            self.logger.error(f"Request Error: {e}")
            raise
    
    def get(self, endpoint: str, params: Optional[Dict] = None) -> Dict[str, Any]:
        """GET request"""
        response = self._make_request('GET', endpoint, params=params, timeout=30)
        return response.json()
    
    def post(self, endpoint: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        """POST request"""
        response = self._make_request('POST', endpoint, json=data, timeout=30)
        return response.json()
    
    def put(self, endpoint: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        """PUT request"""
        response = self._make_request('PUT', endpoint, json=data, timeout=30)
        return response.json()
    
    def delete(self, endpoint: str) -> bool:
        """DELETE request"""
        response = self._make_request('DELETE', endpoint, timeout=30)
        return response.status_code == 204
    
    def close(self):
        """Chiudi session"""
        self.session.close()

# Uso
if __name__ == "__main__":
    client = APIClient('https://api.example.com', api_key='YOUR_KEY')
    
    try:
        # GET
        users = client.get('/users', params={'page': 1})
        print(users)
        
        # POST
        new_user = client.post('/users', data={'name': 'Mario', 'email': 'mario@test.com'})
        print(new_user)
        
    finally:
        client.close()
```

---

## Parsing XML

### xml.etree.ElementTree

```python
import xml.etree.ElementTree as ET

# Parsing da stringa
xml_string = """
<catalog>
    <book id="1">
        <title>Python Guide</title>
        <author>Mario Rossi</author>
        <price>29.99</price>
    </book>
    <book id="2">
        <title>Web Development</title>
        <author>Luigi Bianchi</author>
        <price>39.99</price>
    </book>
</catalog>
"""

root = ET.fromstring(xml_string)

# Parsing da file
tree = ET.parse('data.xml')
root = tree.getroot()

# Accesso elementi
print(root.tag)        # 'catalog'
print(root.attrib)     # {} attributi root

# Iterare sui figli
for book in root.findall('book'):
    book_id = book.get('id')
    title = book.find('title').text
    author = book.find('author').text
    price = float(book.find('price').text)
    print(f"Book {book_id}: {title} by {author} - €{price}")

# XPath-like queries
all_titles = root.findall('.//title')  # tutti i title
for title in all_titles:
    print(title.text)

first_book = root.find('.//book[@id="1"]')  # book con id=1

# Modificare XML
book = root.find('.//book[@id="1"]')
book.find('price').text = '24.99'

# Aggiungere elemento
new_book = ET.SubElement(root, 'book', id='3')
ET.SubElement(new_book, 'title').text = 'New Book'
ET.SubElement(new_book, 'author').text = 'New Author'
ET.SubElement(new_book, 'price').text = '19.99'

# Scrivere su file
tree = ET.ElementTree(root)
tree.write('output.xml', encoding='utf-8', xml_declaration=True)

# Pretty print
ET.indent(tree, space="  ")  # Python 3.9+
tree.write('output_pretty.xml', encoding='utf-8', xml_declaration=True)
```

### lxml (più potente e veloce)

```python
# pip install lxml

from lxml import etree

# Parsing
xml_string = """
<catalog>
    <book id="1">
        <title>Python Guide</title>
        <author>Mario Rossi</author>
        <price currency="EUR">29.99</price>
    </book>
</catalog>
"""

root = etree.fromstring(xml_string.encode('utf-8'))

# XPath (più potente)
titles = root.xpath('//title/text()')  # tutti i testi dei title
print(titles)  # ['Python Guide']

books_over_20 = root.xpath('//book[price>20]')
eur_prices = root.xpath('//price[@currency="EUR"]/text()')

# Namespaces
xml_with_ns = """
<root xmlns="http://example.com/ns">
    <item>Value</item>
</root>
"""

root = etree.fromstring(xml_with_ns.encode('utf-8'))
namespaces = {'ns': 'http://example.com/ns'}
items = root.xpath('//ns:item/text()', namespaces=namespaces)

# Pretty print
pretty_xml = etree.tostring(root, pretty_print=True, encoding='unicode')
print(pretty_xml)

# Validazione con XSD
xsd_doc = etree.parse('schema.xsd')
xsd = etree.XMLSchema(xsd_doc)

xml_doc = etree.parse('data.xml')
is_valid = xsd.validate(xml_doc)
print(f"Valid: {is_valid}")
```

### Esempio Parsing Response Qualys

```python
import xml.etree.ElementTree as ET
from typing import List, Dict

def parse_qualys_scan_results(xml_response: str) -> List[Dict]:
    """
    Parsa i risultati di una scansione Qualys da XML
    """
    root = ET.fromstring(xml_response)
    vulnerabilities = []
    
    # Qualys usa namespace
    for host in root.findall('.//HOST'):
        ip = host.find('IP').text if host.find('IP') is not None else 'N/A'
        
        for vuln in host.findall('.//VULN'):
            qid = vuln.find('QID').text if vuln.find('QID') is not None else 'N/A'
            severity = vuln.find('SEVERITY').text if vuln.find('SEVERITY') is not None else 'N/A'
            title = vuln.find('TITLE').text if vuln.find('TITLE') is not None else 'N/A'
            
            vulnerabilities.append({
                'ip': ip,
                'qid': qid,
                'severity': severity,
                'title': title
            })
    
    return vulnerabilities

# Uso
xml_response = """<?xml version="1.0"?>
<SCAN_OUTPUT>
    <HOST>
        <IP>192.168.1.100</IP>
        <VULN>
            <QID>105123</QID>
            <SEVERITY>5</SEVERITY>
            <TITLE>Critical Vulnerability</TITLE>
        </VULN>
    </HOST>
</SCAN_OUTPUT>
"""

vulns = parse_qualys_scan_results(xml_response)
for v in vulns:
    print(f"{v['ip']} - QID {v['qid']}: {v['title']} (Severity: {v['severity']})")
```

---

## Gestione JSON

### Operazioni Base

```python
import json

# Python dict a JSON string
data = {
    'nome': 'Mario',
    'eta': 30,
    'attivo': True,
    'skills': ['Python', 'Linux', 'Docker'],
    'indirizzo': {
        'citta': 'Roma',
        'cap': '00100'
    }
}

# Serializzazione (dict -> JSON string)
json_string = json.dumps(data)
print(json_string)

# Pretty print
json_string = json.dumps(data, indent=4, ensure_ascii=False)
print(json_string)

# Deserializzazione (JSON string -> dict)
parsed_data = json.loads(json_string)
print(parsed_data['nome'])

# Scrivere su file
with open('data.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=4, ensure_ascii=False)

# Leggere da file
with open('data.json', 'r', encoding='utf-8') as f:
    loaded_data = json.load(f)

print(loaded_data)
```

### Gestione Tipi Complessi

```python
import json
from datetime import datetime
from decimal import Decimal

# Custom encoder per tipi non standard
class CustomEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        if isinstance(obj, Decimal):
            return float(obj)
        if isinstance(obj, set):
            return list(obj)
        return super().default(obj)

# Uso
data = {
    'timestamp': datetime.now(),
    'price': Decimal('19.99'),
    'tags': {'python', 'json', 'tutorial'}
}

json_string = json.dumps(data, cls=CustomEncoder, indent=2)
print(json_string)

# Custom decoder
def custom_decoder(dct):
    if 'timestamp' in dct:
        dct['timestamp'] = datetime.fromisoformat(dct['timestamp'])
    return dct

loaded = json.loads(json_string, object_hook=custom_decoder)
```

### Validazione JSON

```python
import json

def validate_json(json_string: str) -> bool:
    """Valida se una stringa è JSON valido"""
    try:
        json.loads(json_string)
        return True
    except json.JSONDecodeError as e:
        print(f"JSON invalido: {e}")
        return False

# Test
valid = '{"name": "Mario", "age": 30}'
invalid = '{name: "Mario", age: 30}'  # chiavi senza virgolette

print(validate_json(valid))    # True
print(validate_json(invalid))  # False

# Validazione schema con jsonschema
# pip install jsonschema

from jsonschema import validate, ValidationError

schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "integer", "minimum": 0}
    },
    "required": ["name", "age"]
}

data = {"name": "Mario", "age": 30}

try:
    validate(instance=data, schema=schema)
    print("JSON valido secondo schema")
except ValidationError as e:
    print(f"Errore validazione: {e}")
```

---

## Export CSV

### Scrivere CSV

```python
import csv

# Scrivere liste
data = [
    ['Nome', 'Età', 'Città'],
    ['Mario', 30, 'Roma'],
    ['Luigi', 25, 'Milano'],
    ['Anna', 28, 'Napoli']
]

with open('persone.csv', 'w', newline='', encoding='utf-8') as f:
    writer = csv.writer(f)
    writer.writerows(data)

# Scrivere dizionari
data = [
    {'nome': 'Mario', 'eta': 30, 'citta': 'Roma'},
    {'nome': 'Luigi', 'eta': 25, 'citta': 'Milano'},
    {'nome': 'Anna', 'eta': 28, 'citta': 'Napoli'}
]

with open('persone_dict.csv', 'w', newline='', encoding='utf-8') as f:
    fieldnames = ['nome', 'eta', 'citta']
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    
    writer.writeheader()
    writer.writerows(data)

# Delimitatore personalizzato
with open('data.csv', 'w', newline='') as f:
    writer = csv.writer(f, delimiter=';')
    writer.writerow(['campo1', 'campo2', 'campo3'])

# Quote automatico
with open('data.csv', 'w', newline='') as f:
    writer = csv.writer(f, quoting=csv.QUOTE_ALL)  # tutto tra virgolette
    writer.writerow(['test', 'with spaces', 'comma,here'])
```

### Leggere CSV

```python
import csv

# Leggere come liste
with open('persone.csv', 'r', encoding='utf-8') as f:
    reader = csv.reader(f)
    header = next(reader)  # salta header
    
    for row in reader:
        nome, eta, citta = row
        print(f"{nome} ha {eta} anni e vive a {citta}")

# Leggere come dizionari
with open('persone_dict.csv', 'r', encoding='utf-8') as f:
    reader = csv.DictReader(f)
    
    for row in reader:
        print(f"{row['nome']} ha {row['eta']} anni")

# Leggere tutto in memoria
with open('data.csv', 'r') as f:
    reader = csv.reader(f)
    data = list(reader)

# Delimitatore personalizzato
with open('data.csv', 'r') as f:
    reader = csv.reader(f, delimiter=';')
    for row in reader:
        print(row)
```

### Esempio Export Vulnerabilità Qualys

```python
import csv
from typing import List, Dict

def export_vulnerabilities_to_csv(vulnerabilities: List[Dict], filename: str):
    """
    Esporta lista di vulnerabilità in CSV
    """
    if not vulnerabilities:
        print("Nessuna vulnerabilità da esportare")
        return
    
    fieldnames = ['IP', 'QID', 'Severity', 'Title', 'CVE', 'CVSS_Score']
    
    with open(filename, 'w', newline='', encoding='utf-8') as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        
        for vuln in vulnerabilities:
            writer.writerow({
                'IP': vuln.get('ip', 'N/A'),
                'QID': vuln.get('qid', 'N/A'),
                'Severity': vuln.get('severity', 'N/A'),
                'Title': vuln.get('title', 'N/A'),
                'CVE': vuln.get('cve', 'N/A'),
                'CVSS_Score': vuln.get('cvss_score', 'N/A')
            })
    
    print(f"Esportate {len(vulnerabilities)} vulnerabilità in {filename}")

# Uso
vulnerabilities = [
    {'ip': '192.168.1.100', 'qid': '105123', 'severity': '5', 
     'title': 'Critical Bug', 'cve': 'CVE-2024-1234', 'cvss_score': '9.8'},
    {'ip': '192.168.1.101', 'qid': '105124', 'severity': '3', 
     'title': 'Medium Bug', 'cve': 'CVE-2024-5678', 'cvss_score': '5.3'}
]

export_vulnerabilities_to_csv(vulnerabilities, 'vulnerabilities_report.csv')
```

### Pandas per CSV (opzionale)

```python
# pip install pandas

import pandas as pd

# Leggere CSV
df = pd.read_csv('data.csv')
print(df.head())  # prime 5 righe

# Scrivere CSV
data = {
    'nome': ['Mario', 'Luigi', 'Anna'],
    'eta': [30, 25, 28],
    'citta': ['Roma', 'Milano', 'Napoli']
}
df = pd.DataFrame(data)
df.to_csv('output.csv', index=False, encoding='utf-8')

# Operazioni su DataFrame
df_filtrato = df[df['eta'] > 26]
df_ordinato = df.sort_values('eta', ascending=False)

# Statistiche
print(df['eta'].mean())
print(df['eta'].max())
print(df.describe())
```

---

## Logging Professionale

### Configurazione Base

```python
import logging

# Configurazione base
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S'
)

# Livelli di log
logging.debug('Messaggio di debug')      # DEBUG
logging.info('Informazione')             # INFO
logging.warning('Attenzione')            # WARNING
logging.error('Errore!')                 # ERROR
logging.critical('Errore critico!')      # CRITICAL

# Solo livelli >= WARNING vengono mostrati di default
```

### Logger Personalizzato

```python
import logging

# Creare logger
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# Handler per console
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)

# Handler per file
file_handler = logging.FileHandler('app.log', encoding='utf-8')
file_handler.setLevel(logging.DEBUG)

# Formatter
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S'
)
console_handler.setFormatter(formatter)
file_handler.setFormatter(formatter)

# Aggiungere handlers
logger.addHandler(console_handler)
logger.addHandler(file_handler)

# Uso
logger.debug('Debug message')
logger.info('Info message')
logger.warning('Warning message')
logger.error('Error message')
logger.critical('Critical message')
```

### Logging con Rotazione File

```python
import logging
from logging.handlers import RotatingFileHandler, TimedRotatingFileHandler

# Rotazione per dimensione
handler = RotatingFileHandler(
    'app.log',
    maxBytes=10*1024*1024,  # 10 MB
    backupCount=5,           # mantieni 5 file di backup
    encoding='utf-8'
)

# Rotazione per tempo
handler = TimedRotatingFileHandler(
    'app.log',
    when='midnight',        # ruota a mezzanotte
    interval=1,             # ogni giorno
    backupCount=7,          # mantieni 7 giorni
    encoding='utf-8'
)

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(handler)
```

### Logging Avanzato

```python
import logging
import sys

def setup_logging(log_file: str = 'app.log', debug: bool = False):
    """
    Configura logging con console e file
    """
    # Livello base
    level = logging.DEBUG if debug else logging.INFO
    
    # Root logger
    logger = logging.getLogger()
    logger.setLevel(level)
    
    # Formatter dettagliato
    detailed_formatter = logging.Formatter(
        '%(asctime)s - %(name)s - [%(levelname)s] - %(filename)s:%(lineno)d - %(message)s',
        datefmt='%Y-%m-%d %H:%M:%S'
    )
    
    # Formatter semplice per console
    simple_formatter = logging.Formatter(
        '%(levelname)s - %(message)s'
    )
    
    # Console handler (solo INFO e superiori)
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(logging.INFO)
    console_handler.setFormatter(simple_formatter)
    
    # File handler (tutto)
    file_handler = logging.FileHandler(log_file, encoding='utf-8')
    file_handler.setLevel(level)
    file_handler.setFormatter(detailed_formatter)
    
    # Aggiungi handlers
    logger.addHandler(console_handler)
    logger.addHandler(file_handler)
    
    return logger

# Uso
logger = setup_logging('qualys_automation.log', debug=True)

logger.info("Script avviato")
logger.debug("Dettaglio per debugging")
logger.error("Errore nell'esecuzione")
```

### Logging Eccezioni

```python
import logging

logger = logging.getLogger(__name__)

try:
    result = 10 / 0
except ZeroDivisionError:
    logger.exception("Errore nella divisione")  # include traceback
    
# Alternativa
try:
    result = 10 / 0
except ZeroDivisionError as e:
    logger.error(f"Errore: {e}", exc_info=True)  # include traceback
```

### Context Manager per Logging

```python
import logging
from contextlib import contextmanager

@contextmanager
def log_execution(operation: str, logger: logging.Logger):
    """Context manager per loggare inizio e fine operazione"""
    logger.info(f"Inizio {operation}")
    try:
        yield
        logger.info(f"Completato {operation}")
    except Exception as e:
        logger.error(f"Errore in {operation}: {e}", exc_info=True)
        raise

# Uso
logger = logging.getLogger(__name__)

with log_execution("scansione Qualys", logger):
    # codice della scansione
    pass
```

---

## Argparse - Parametri CLI

### Base

```python
import argparse

# Creare parser
parser = argparse.ArgumentParser(
    description='Script di automazione Qualys',
    epilog='Esempio: python script.py --scan --target 192.168.1.0/24'
)

# Argomenti posizionali
parser.add_argument('filename', help='Nome del file di input')

# Argomenti opzionali
parser.add_argument('-v', '--verbose', action='store_true', 
                    help='Output verboso')
parser.add_argument('-o', '--output', default='output.csv',
                    help='File di output (default: output.csv)')
parser.add_argument('-t', '--timeout', type=int, default=30,
                    help='Timeout in secondi (default: 30)')

# Parse
args = parser.parse_args()

# Uso
print(f"Filename: {args.filename}")
print(f"Verbose: {args.verbose}")
print(f"Output: {args.output}")
print(f"Timeout: {args.timeout}")
```

### Argparse Avanzato

```python
import argparse

parser = argparse.ArgumentParser(description='Qualys Automation Tool')

# Subparser (sotto-comandi)
subparsers = parser.add_subparsers(dest='command', help='Comandi disponibili')

# Comando 'scan'
scan_parser = subparsers.add_parser('scan', help='Esegui scansione')
scan_parser.add_argument('--target', required=True, help='Target da scansionare')
scan_parser.add_argument('--profile', default='default', help='Profilo di scansione')

# Comando 'report'
report_parser = subparsers.add_parser('report', help='Genera report')
report_parser.add_argument('--scan-id', required=True, help='ID della scansione')
report_parser.add_argument('--format', choices=['csv', 'json', 'pdf'], 
                          default='csv', help='Formato output')

# Opzioni globali
parser.add_argument('-v', '--verbose', action='store_true')
parser.add_argument('--config', default='config.yaml', help='File di configurazione')

# Parse
args = parser.parse_args()

# Uso
if args.command == 'scan':
    print(f"Scansione target: {args.target}")
    print(f"Profilo: {args.profile}")
elif args.command == 'report':
    print(f"Report per scan: {args.scan_id}")
    print(f"Formato: {args.format}")

# Esempio chiamata:
# python script.py scan --target 192.168.1.0/24 --profile web-app
# python script.py report --scan-id 12345 --format json
```

### Tipi e Validazione

```python
import argparse
import ipaddress

def validate_ip(value):
    """Validatore custom per IP"""
    try:
        return ipaddress.ip_address(value)
    except ValueError:
        raise argparse.ArgumentTypeError(f"{value} non è un IP valido")

def validate_positive_int(value):
    """Valida intero positivo"""
    ivalue = int(value)
    if ivalue <= 0:
        raise argparse.ArgumentTypeError(f"{value} deve essere > 0")
    return ivalue

parser = argparse.ArgumentParser()

# Type personalizzato
parser.add_argument('--ip', type=validate_ip, help='Indirizzo IP')
parser.add_argument('--count', type=validate_positive_int, help='Numero (>0)')

# Choices
parser.add_argument('--severity', choices=['1', '2', '3', '4', '5'],
                    help='Livello severity')

# Multiple values
parser.add_argument('--targets', nargs='+', help='Lista di target')
parser.add_argument('--coords', nargs=2, type=float, help='Coordinate (x y)')

# File type
parser.add_argument('--input', type=argparse.FileType('r'), 
                    help='File di input')

args = parser.parse_args()
```

### Esempio Completo Script Qualys

```python
#!/usr/bin/env python3
"""
Qualys Automation Script
"""

import argparse
import logging
import sys

def setup_argparse():
    """Configura argparse"""
    parser = argparse.ArgumentParser(
        description='Automazione Qualys VM',
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog='''
Esempi:
  %(prog)s scan --target 192.168.1.0/24 --output scan_results.csv
  %(prog)s report --scan-id 12345 --format json --output report.json
  %(prog)s list-scans --from 2024-01-01
        '''
    )
    
    # Subcommands
    subparsers = parser.add_subparsers(dest='command', required=True)
    
    # Scan command
    scan_parser = subparsers.add_parser('scan', help='Esegui nuova scansione')
    scan_parser.add_argument('--target', required=True, help='Target (IP o network)')
    scan_parser.add_argument('--profile', default='default', help='Profilo scansione')
    scan_parser.add_argument('--output', default='scan_results.csv', help='File output')
    
    # Report command
    report_parser = subparsers.add_parser('report', help='Genera report')
    report_parser.add_argument('--scan-id', required=True, help='ID scansione')
    report_parser.add_argument('--format', choices=['csv', 'json', 'pdf'], 
                              default='csv', help='Formato output')
    report_parser.add_argument('--output', required=True, help='File output')
    
    # List scans
    list_parser = subparsers.add_parser('list-scans', help='Lista scansioni')
    list_parser.add_argument('--from', dest='from_date', help='Data inizio (YYYY-MM-DD)')
    list_parser.add_argument('--to', dest='to_date', help='Data fine (YYYY-MM-DD)')
    
    # Global options
    parser.add_argument('-v', '--verbose', action='store_true', help='Modalità verbose')
    parser.add_argument('--config', default='config.yaml', help='File configurazione')
    parser.add_argument('--log', default='qualys.log', help='File log')
    
    return parser

def main():
    parser = setup_argparse()
    args = parser.parse_args()
    
    # Setup logging
    log_level = logging.DEBUG if args.verbose else logging.INFO
    logging.basicConfig(
        level=log_level,
        format='%(asctime)s - %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler(args.log),
            logging.StreamHandler()
        ]
    )
    
    logger = logging.getLogger(__name__)
    logger.info(f"Comando: {args.command}")
    
    # Esegui comando
    if args.command == 'scan':
        logger.info(f"Scansione target: {args.target}")
        # Logica scansione qui
        
    elif args.command == 'report':
        logger.info(f"Generazione report per scan {args.scan_id}")
        # Logica report qui
        
    elif args.command == 'list-scans':
        logger.info("Lista scansioni")
        # Logica lista qui
    
    logger.info("Completato")
    return 0

if __name__ == '__main__':
    sys.exit(main())
```

---

## Scheduling e Automazione

### Schedule (libreria Python)

```python
# pip install schedule

import schedule
import time
import logging

logger = logging.getLogger(__name__)

def job():
    """Funzione da eseguire"""
    logger.info("Esecuzione job periodico")
    # Logica qui

# Schedule diversi
schedule.every(10).minutes.do(job)
schedule.every().hour.do(job)
schedule.every().day.at("10:30").do(job)
schedule.every().monday.do(job)
schedule.every().wednesday.at("13:15").do(job)

# Loop principale
while True:
    schedule.run_pending()
    time.sleep(1)
```

### Schedule Avanzato

```python
import schedule
import time
import logging
from datetime import datetime

logger = logging.getLogger(__name__)

def daily_scan():
    """Scansione giornaliera"""
    logger.info("Inizio scansione giornaliera")
    # Logica scansione
    logger.info("Fine scansione giornaliera")

def weekly_report():
    """Report settimanale"""
    logger.info("Generazione report settimanale")
    # Logica report
    logger.info("Report completato")

def job_with_params(target):
    """Job con parametri"""
    logger.info(f"Scansione target: {target}")

# Schedule con parametri
schedule.every().day.at("02:00").do(daily_scan)
schedule.every().sunday.at("23:00").do(weekly_report)
schedule.every(30).minutes.do(job_with_params, target="192.168.1.0/24")

# Job che si cancella dopo esecuzione
schedule.every().day.at("15:00").do(daily_scan).tag('one-time')

# Cancella job con tag
schedule.clear('one-time')

# Loop con graceful shutdown
try:
    while True:
        schedule.run_pending()
        time.sleep(60)  # check ogni minuto
except KeyboardInterrupt:
    logger.info("Scheduler terminato")
```

### Cron (Sistema Linux)

```python
# Per usare cron, crea script Python eseguibile

#!/usr/bin/env python3
"""
Script da eseguire con cron
"""

import logging
from datetime import datetime

# Setup logging (importante per cron!)
logging.basicConfig(
    filename='/var/log/qualys_automation.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

def main():
    logger.info("Script avviato da cron")
    
    try:
        # Logica script
        logger.info("Esecuzione completata")
        return 0
    except Exception as e:
        logger.error(f"Errore: {e}", exc_info=True)
        return 1

if __name__ == '__main__':
    exit(main())
```

```bash
# Aggiungere a crontab
# crontab -e

# Esegui ogni giorno alle 2:00
0 2 * * * /usr/bin/python3 /path/to/script.py

# Esegui ogni lunedì alle 9:00
0 9 * * 1 /path/to/venv/bin/python /path/to/script.py

# Esegui ogni 30 minuti
*/30 * * * * /usr/bin/python3 /path/to/script.py

# Con output su file
0 2 * * * /usr/bin/python3 /path/to/script.py >> /var/log/cron_output.log 2>&1
```

### APScheduler (Più Potente)

```python
# pip install apscheduler

from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.triggers.cron import CronTrigger
import logging

logger = logging.getLogger(__name__)

def scan_job():
    logger.info("Esecuzione scansione")

def report_job():
    logger.info("Generazione report")

# Crea scheduler
scheduler = BlockingScheduler()

# Aggiungi job
scheduler.add_job(scan_job, 'interval', minutes=30)  # ogni 30 min
scheduler.add_job(report_job, CronTrigger(hour=2, minute=0))  # 02:00
scheduler.add_job(report_job, 'cron', day_of_week='mon', hour=9)  # Lun 09:00

# Avvia
try:
    logger.info("Scheduler avviato")
    scheduler.start()
except (KeyboardInterrupt, SystemExit):
    logger.info("Scheduler terminato")
```

---

## Best Practices

### Gestione Eccezioni

```python
# Base
try:
    result = risky_operation()
except ValueError as e:
    print(f"Errore valore: {e}")
except KeyError as e:
    print(f"Chiave non trovata: {e}")
except Exception as e:
    print(f"Errore generico: {e}")
finally:
    # Eseguito sempre
    cleanup()

# Cattura multipla
try:
    result = operation()
except (ValueError, TypeError) as e:
    print(f"Errore tipo/valore: {e}")

# Re-raise
try:
    dangerous_operation()
except Exception as e:
    logger.error(f"Errore: {e}")
    raise  # rilancia la stessa eccezione

# Custom exception
class QualysAPIError(Exception):
    """Eccezione per errori API Qualys"""
    pass

def call_qualys_api():
    if error_condition:
        raise QualysAPIError("Errore nella chiamata API")

# Context manager per gestione risorse
class DatabaseConnection:
    def __enter__(self):
        self.connection = connect_to_db()
        return self.connection
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.connection.close()
        return False  # propaga eccezioni

# Uso
with DatabaseConnection() as conn:
    conn.query("SELECT * FROM vulnerabilities")
```

### Funzioni e Modularità

```python
# Funzione ben documentata
def scan_target(target: str, profile: str = 'default', timeout: int = 300) -> dict:
    """
    Esegue scansione Qualys su target specificato.
    
    Args:
        target: Indirizzo IP o network (CIDR)
        profile: Nome profilo di scansione (default: 'default')
        timeout: Timeout in secondi (default: 300)
    
    Returns:
        dict: Risultato scansione con chiavi:
            - scan_id: ID della scansione
            - status: Stato della scansione
            - vulnerabilities: Lista vulnerabilità trovate
    
    Raises:
        QualysAPIError: Errore nella comunicazione API
        ValueError: Parametri invalidi
    
    Example:
        >>> result = scan_target('192.168.1.100', profile='web-app')
        >>> print(result['scan_id'])
        12345
    """
    # Validazione input
    if not is_valid_target(target):
        raise ValueError(f"Target invalido: {target}")
    
    # Logica funzione
    scan_id = start_scan(target, profile, timeout)
    vulnerabilities = get_scan_results(scan_id)
    
    return {
        'scan_id': scan_id,
        'status': 'completed',
        'vulnerabilities': vulnerabilities
    }

# Separazione responsabilità
def read_config(filename: str) -> dict:
    """Legge configurazione da file"""
    pass

def validate_config(config: dict) -> bool:
    """Valida configurazione"""
    pass

def execute_scan(config: dict) -> dict:
    """Esegue scansione"""
    pass

def save_results(results: dict, filename: str):
    """Salva risultati"""
    pass

# Composizione
def main():
    config = read_config('config.yaml')
    
    if not validate_config(config):
        raise ValueError("Configurazione invalida")
    
    results = execute_scan(config)
    save_results(results, 'results.json')
```

### Leggere/Scrivere File

```python
# Context manager (preferito)
with open('file.txt', 'r', encoding='utf-8') as f:
    content = f.read()

# Leggi linee
with open('file.txt', 'r', encoding='utf-8') as f:
    for line in f:
        print(line.strip())

# Leggi tutto in lista
with open('file.txt', 'r', encoding='utf-8') as f:
    lines = f.readlines()

# Scrittura
with open('output.txt', 'w', encoding='utf-8') as f:
    f.write("Contenuto\n")
    f.writelines(['linea1\n', 'linea2\n'])

# Append
with open('log.txt', 'a', encoding='utf-8') as f:
    f.write("Nuova entry\n")

# Binario
with open('image.jpg', 'rb') as f:
    data = f.read()

# Path management
from pathlib import Path

file_path = Path('data/config.yaml')

if file_path.exists():
    content = file_path.read_text(encoding='utf-8')

# Crea directory se non esiste
output_dir = Path('output')
output_dir.mkdir(parents=True, exist_ok=True)

# Iterare file in directory
for file in Path('data').glob('*.csv'):
    print(file.name)

# File temporanei
import tempfile

with tempfile.NamedTemporaryFile(mode='w', delete=False) as f:
    f.write("Temporary data")
    temp_path = f.name

# Usa temp_path
# ...

# Pulisci
Path(temp_path).unlink()
```

### Variabili d'Ambiente

```python
import os
from pathlib import Path

# Leggere variabile d'ambiente
api_key = os.getenv('QUALYS_API_KEY')
api_url = os.getenv('QUALYS_API_URL', 'https://qualysapi.com')  # default

# Verificare esistenza
if not os.getenv('QUALYS_API_KEY'):
    raise ValueError("QUALYS_API_KEY non impostata")

# File .env (python-dotenv)
# pip install python-dotenv

from dotenv import load_dotenv

# Carica da file .env
load_dotenv()

# Ora disponibili come variabili ambiente
api_key = os.getenv('QUALYS_API_KEY')
username = os.getenv('QUALYS_USERNAME')
password = os.getenv('QUALYS_PASSWORD')

# File .env.example (template da committare)
"""
QUALYS_API_URL=https://qualysapi.qualys.com
QUALYS_USERNAME=your_username
QUALYS_PASSWORD=your_password
QUALYS_API_KEY=your_api_key
"""

# .gitignore (NON committare credenziali!)
"""
.env
config/credentials.yaml
*.log
venv/
__pycache__/
"""

# Configurazione sicura
class Config:
    """Configurazione applicazione da env vars"""
    
    def __init__(self):
        load_dotenv()
        
        # Required
        self.api_url = self._get_required('QUALYS_API_URL')
        self.username = self._get_required('QUALYS_USERNAME')
        self.password = self._get_required('QUALYS_PASSWORD')
        
        # Optional
        self.timeout = int(os.getenv('TIMEOUT', '300'))
        self.debug = os.getenv('DEBUG', 'false').lower() == 'true'
        self.log_file = os.getenv('LOG_FILE', 'qualys.log')
    
    def _get_required(self, key: str) -> str:
        """Ottieni variabile richiesta o fallisci"""
        value = os.getenv(key)
        if not value:
            raise ValueError(f"Variabile {key} richiesta ma non impostata")
        return value

# Uso
config = Config()
print(config.api_url)
```

### Struttura Progetto Professionale

```python
# qualys_automation/
# ├── src/
# │   ├── __init__.py
# │   ├── client.py
# │   ├── scanner.py
# │   ├── parser.py
# │   └── reporter.py
# ├── tests/
# │   ├── __init__.py
# │   ├── test_client.py
# │   └── test_scanner.py
# ├── config/
# │   ├── .env.example
# │   └── config.yaml
# ├── logs/
# ├── output/
# ├── requirements.txt
# ├── setup.py
# ├── README.md
# └── main.py

# src/client.py
"""
Qualys API Client
"""
import logging
import requests
from typing import Optional, Dict
from requests.auth import HTTPBasicAuth

logger = logging.getLogger(__name__)

class QualysClient:
    """Client per Qualys API"""
    
    def __init__(self, base_url: str, username: str, password: str):
        self.base_url = base_url.rstrip('/')
        self.auth = HTTPBasicAuth(username, password)
        self.session = requests.Session()
        self.session.auth = self.auth
        
    def _request(self, method: str, endpoint: str, **kwargs) -> requests.Response:
        """Esegue richiesta HTTP"""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        
        try:
            response = self.session.request(method, url, **kwargs)
            response.raise_for_status()
            return response
        except requests.exceptions.RequestException as e:
            logger.error(f"Errore richiesta {method} {endpoint}: {e}")
            raise
    
    def launch_scan(self, scan_title: str, target: str, option_id: str) -> str:
        """Lancia nuova scansione"""
        params = {
            'action': 'launch',
            'scan_title': scan_title,
            'ip': target,
            'option_id': option_id
        }
        
        response = self._request('POST', '/api/2.0/fo/scan/', data=params)
        # Parsa risposta XML per ottenere scan_id
        # ...
        return scan_id
    
    def get_scan_results(self, scan_ref: str) -> str:
        """Ottieni risultati scansione"""
        params = {
            'action': 'fetch',
            'scan_ref': scan_ref,
            'mode': 'extended'
        }
        
        response = self._request('GET', '/api/2.0/fo/scan/', params=params)
        return response.text
    
    def close(self):
        """Chiudi session"""
        self.session.close()

# src/scanner.py
"""
Logica scansione
"""
import logging
from typing import List, Dict
from .client import QualysClient

logger = logging.getLogger(__name__)

class VulnerabilityScanner:
    """Gestisce scansioni vulnerabilità"""
    
    def __init__(self, client: QualysClient):
        self.client = client
    
    def scan_target(self, target: str, scan_title: str, option_id: str = '12345') -> str:
        """Esegue scansione su target"""
        logger.info(f"Inizio scansione: {scan_title} su {target}")
        
        try:
            scan_id = self.client.launch_scan(scan_title, target, option_id)
            logger.info(f"Scansione lanciata con ID: {scan_id}")
            return scan_id
        except Exception as e:
            logger.error(f"Errore lancio scansione: {e}")
            raise
    
    def get_vulnerabilities(self, scan_ref: str) -> List[Dict]:
        """Ottieni vulnerabilità da scansione"""
        logger.info(f"Recupero risultati per scan {scan_ref}")
        
        xml_results = self.client.get_scan_results(scan_ref)
        vulnerabilities = self._parse_vulnerabilities(xml_results)
        
        logger.info(f"Trovate {len(vulnerabilities)} vulnerabilità")
        return vulnerabilities
    
    def _parse_vulnerabilities(self, xml_data: str) -> List[Dict]:
        """Parsa XML e estrae vulnerabilità"""
        # Implementazione parsing
        pass

# main.py
"""
Script principale
"""
import sys
import logging
from pathlib import Path
from src.client import QualysClient
from src.scanner import VulnerabilityScanner
from dotenv import load_dotenv
import os

def setup_logging(debug: bool = False):
    """Setup logging"""
    level = logging.DEBUG if debug else logging.INFO
    
    logging.basicConfig(
        level=level,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler('logs/qualys_automation.log'),
            logging.StreamHandler()
        ]
    )

def main():
    # Carica env vars
    load_dotenv()
    
    # Setup
    setup_logging(debug=os.getenv('DEBUG', 'false') == 'true')
    logger = logging.getLogger(__name__)
    
    try:
        # Crea client
        client = QualysClient(
            base_url=os.getenv('QUALYS_API_URL'),
            username=os.getenv('QUALYS_USERNAME'),
            password=os.getenv('QUALYS_PASSWORD')
        )
        
        # Crea scanner
        scanner = VulnerabilityScanner(client)
        
        # Esegui scansione
        scan_id = scanner.scan_target(
            target='192.168.1.0/24',
            scan_title='Daily Scan',
            option_id='12345'
        )
        
        logger.info(f"Scansione completata: {scan_id}")
        
        return 0
        
    except Exception as e:
        logger.error(f"Errore: {e}", exc_info=True)
        return 1
    
    finally:
        if 'client' in locals():
            client.close()

if __name__ == '__main__':
    sys.exit(main())
```

---

## Esempi Completi Qualys

### Client Qualys Completo

```python
"""
Qualys API Client Completo
"""
import logging
import time
import xml.etree.ElementTree as ET
from typing import List, Dict, Optional
import requests
from requests.auth import HTTPBasicAuth
from requests.exceptions import RequestException

logger = logging.getLogger(__name__)

class QualysAPIError(Exception):
    """Eccezione per errori API Qualys"""
    pass

class QualysClient:
    """
    Client completo per Qualys API v2
    """
    
    def __init__(self, base_url: str, username: str, password: str):
        """
        Inizializza client Qualys
        
        Args:
            base_url: URL base API Qualys (es: https://qualysapi.qualys.com)
            username: Username Qualys
            password: Password Qualys
        """
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
        self.session.auth = HTTPBasicAuth(username, password)
        self.session.headers.update({
            'X-Requested-With': 'Python Qualys Client'
        })
        
        logger.info(f"Client Qualys inizializzato: {base_url}")
    
    def _make_request(self, method: str, endpoint: str, 
                     params: Optional[Dict] = None,
                     data: Optional[Dict] = None,
                     timeout: int = 300) -> requests.Response:
        """
        Esegue richiesta HTTP con retry
        """
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        max_retries = 3
        retry_delay = 5
        
        for attempt in range(max_retries):
            try:
                logger.debug(f"{method} {url}")
                
                response = self.session.request(
                    method=method,
                    url=url,
                    params=params,
                    data=data,
                    timeout=timeout
                )
                
                # Qualys rate limiting: 429
                if response.status_code == 429:
                    wait_time = int(response.headers.get('Retry-After', retry_delay))
                    logger.warning(f"Rate limited, attendo {wait_time}s")
                    time.sleep(wait_time)
                    continue
                
                response.raise_for_status()
                return response
                
            except RequestException as e:
                logger.error(f"Tentativo {attempt + 1}/{max_retries} fallito: {e}")
                
                if attempt < max_retries - 1:
                    time.sleep(retry_delay * (attempt + 1))
                else:
                    raise QualysAPIError(f"Richiesta fallita dopo {max_retries} tentativi: {e}")
    
    def launch_scan(self, scan_title: str, ip_list: str, 
                   option_id: str, scan_type: str = 'on_demand') -> str:
        """
        Lancia nuova scansione
        
        Args:
            scan_title: Titolo della scansione
            ip_list: IP o network da scansionare (es: 192.168.1.0/24)
            option_id: ID del profilo di scansione
            scan_type: Tipo scansione (on_demand, scheduled)
        
        Returns:
            str: ID della scansione lanciata
        
        Raises:
            QualysAPIError: Errore nell'API
        """
        logger.info(f"Lancio scansione: {scan_title} su {ip_list}")
        
        params = {
            'action': 'launch',
            'scan_title': scan_title,
            'ip': ip_list,
            'option_id': option_id,
            'iscanner_name': 'External'
        }
        
        response = self._make_request('POST', '/api/2.0/fo/scan/', data=params)
        
        # Parsa risposta XML
        root = ET.fromstring(response.content)
        
        # Controlla risposta
        response_elem = root.find('.//RESPONSE')
        if response_elem is None:
            raise QualysAPIError("Risposta API malformata")
        
        # Estrai scan ID
        scan_ref = root.find('.//VALUE')
        if scan_ref is None:
            error_text = root.find('.//TEXT')
            error_msg = error_text.text if error_text is not None else "Errore sconosciuto"
            raise QualysAPIError(f"Errore lancio scansione: {error_msg}")
        
        scan_id = scan_ref.text
        logger.info(f"Scansione lanciata: ID {scan_id}")
        
        return scan_id
    
    def get_scan_status(self, scan_ref: str) -> Dict:
        """
        Ottieni stato scansione
        
        Args:
            scan_ref: ID della scansione
        
        Returns:
            dict: Informazioni stato con chiavi status, state, target
        """
        params = {
            'action': 'list',
            'scan_ref': scan_ref,
            'show_status': '1'
        }
        
        response = self._make_request('GET', '/api/2.0/fo/scan/', params=params)
        root = ET.fromstring(response.content)
        
        scan = root.find('.//SCAN')
        if scan is None:
            raise QualysAPIError(f"Scansione {scan_ref} non trovata")
        
        status_elem = scan.find('.//STATUS')
        state_elem = status_elem.find('.//STATE') if status_elem is not None else None
        target_elem = scan.find('.//TARGET')
        
        return {
            'status': status_elem.text if status_elem is not None else 'Unknown',
            'state': state_elem.text if state_elem is not None else 'Unknown',
            'target': target_elem.text if target_elem is not None else 'Unknown'
        }
    
    def wait_for_scan_completion(self, scan_ref: str, 
                                 poll_interval: int = 60,
                                 max_wait: int = 3600) -> bool:
        """
        Attendi completamento scansione
        
        Args:
            scan_ref: ID scansione
            poll_interval: Intervallo controllo in secondi
            max_wait: Tempo massimo attesa in secondi
        
        Returns:
            bool: True se completata, False se timeout
        """
        logger.info(f"Attendo completamento scansione {scan_ref}")
        
        elapsed = 0
        
        while elapsed < max_wait:
            status = self.get_scan_status(scan_ref)
            state = status['state']
            
            logger.debug(f"Stato scansione: {state}")
            
            if state in ['Finished', 'Canceled', 'Error']:
                logger.info(f"Scansione terminata: {state}")
                return state == 'Finished'
            
            time.sleep(poll_interval)
            elapsed += poll_interval
        
        logger.warning(f"Timeout attesa scansione dopo {max_wait}s")
        return False
    
    def get_scan_results(self, scan_ref: str) -> str:
        """
        Scarica risultati scansione in XML
        
        Args:
            scan_ref: ID scansione
        
        Returns:
            str: XML con risultati scansione
        """
        logger.info(f"Scaricamento risultati scansione {scan_ref}")
        
        params = {
            'action': 'fetch',
            'scan_ref': scan_ref,
            'mode': 'extended',
            'output_format': 'xml'
        }
        
        response = self._make_request('GET', '/api/2.0/fo/scan/', params=params)
        return response.text
    
    def parse_scan_results(self, xml_data: str) -> List[Dict]:
        """
        Parsa risultati XML e estrae vulnerabilità
        
        Args:
            xml_data: XML risultati scansione
        
        Returns:
            list: Lista vulnerabilità con dettagli
        """
        logger.info("Parsing risultati scansione")
        
        root = ET.fromstring(xml_data.encode('utf-8'))
        vulnerabilities = []
        
        for host in root.findall('.//IP'):
            ip = host.get('value', 'N/A')
            
            for vuln in host.findall('.//VULN'):
                qid = vuln.find('QID')
                severity = vuln.find('SEVERITY')
                title = vuln.find('TITLE')
                cvss_base = vuln.find('CVSS_BASE')
                cve_list = vuln.findall('.//ID[@source="CVE"]')
                
                vuln_dict = {
                    'ip': ip,
                    'qid': qid.text if qid is not None else 'N/A',
                    'severity': severity.text if severity is not None else 'N/A',
                    'title': title.text if title is not None else 'N/A',
                    'cvss_score': cvss_base.text if cvss_base is not None else 'N/A',
                    'cve_ids': [cve.text for cve in cve_list] if cve_list else []
                }
                
                vulnerabilities.append(vuln_dict)
        
        logger.info(f"Trovate {len(vulnerabilities)} vulnerabilità")
        return vulnerabilities
    
    def export_to_csv(self, vulnerabilities: List[Dict], filename: str):
        """
        Esporta vulnerabilità in CSV
        """
        import csv
        
        logger.info(f"Export vulnerabilità in {filename}")
        
        if not vulnerabilities:
            logger.warning("Nessuna vulnerabilità da esportare")
            return
        
        fieldnames = ['ip', 'qid', 'severity', 'cvss_score', 'title', 'cve_ids']
        
        with open(filename, 'w', newline='', encoding='utf-8') as f:
            writer = csv.DictWriter(f, fieldnames=fieldnames)
            writer.writeheader()
            
            for vuln in vulnerabilities:
                # Converti lista CVE in stringa
                vuln_copy = vuln.copy()
                vuln_copy['cve_ids'] = ', '.join(vuln['cve_ids'])
                writer.writerow(vuln_copy)
        
        logger.info(f"Esportate {len(vulnerabilities)} vulnerabilità")
    
    def close(self):
        """Chiudi session"""
        self.session.close()
        logger.info("Session Qualys chiusa")

# Esempio uso completo
if __name__ == "__main__":
    import os
    from dotenv import load_dotenv
    
    # Setup logging
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    
    # Carica credenziali
    load_dotenv()
    
    # Crea client
    client = QualysClient(
        base_url=os.getenv('QUALYS_API_URL'),
        username=os.getenv('QUALYS_USERNAME'),
        password=os.getenv('QUALYS_PASSWORD')
    )
    
    try:
        # Lancia scansione
        scan_id = client.launch_scan(
            scan_title='Automated Scan',
            ip_list='192.168.1.0/24',
            option_id='12345'
        )
        
        # Attendi completamento
        if client.wait_for_scan_completion(scan_id, poll_interval=60):
            # Scarica risultati
            xml_results = client.get_scan_results(scan_id)
            
            # Parsa vulnerabilità
            vulnerabilities = client.parse_scan_results(xml_results)
            
            # Esporta in CSV
            client.export_to_csv(vulnerabilities, 'scan_results.csv')
            
            # Stampa riepilogo
            print(f"\n{'='*60}")
            print(f"Scansione completata: {scan_id}")
            print(f"Vulnerabilità totali: {len(vulnerabilities)}")
            
            # Conta per severity
            from collections import Counter
            severity_count = Counter(v['severity'] for v in vulnerabilities)
            
            print(f"\nDistribuzione per severity:")
            for severity in ['5', '4', '3', '2', '1']:
                count = severity_count.get(severity, 0)
                print(f"  Severity {severity}: {count}")
            
            print(f"{'='*60}\n")
        
        else:
            print("Timeout attesa completamento scansione")
    
    except QualysAPIError as e:
        print(f"Errore Qualys API: {e}")
    
    except Exception as e:
        print(f"Errore: {e}")
    
    finally:
        client.close()
```

### Script Automazione Completo

```python
#!/usr/bin/env python3
"""
Qualys VM Automation Script
Automazione completa del processo di Vulnerability Management
"""

import sys
import logging
import argparse
from pathlib import Path
from typing import List, Dict, Optional
from datetime import datetime
import json

# Importa moduli custom
from src.client import QualysClient, QualysAPIError
from dotenv import load_dotenv
import os

def setup_logging(log_file: str, debug: bool = False):
    """Setup logging con file e console"""
    level = logging.DEBUG if debug else logging.INFO
    
    # Crea directory logs se non esiste
    Path('logs').mkdir(exist_ok=True)
    
    # Formatter
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        datefmt='%Y-%m-%d %H:%M:%S'
    )
    
    # Handler file
    file_handler = logging.FileHandler(log_file, encoding='utf-8')
    file_handler.setLevel(level)
    file_handler.setFormatter(formatter)
    
    # Handler console
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.INFO)
    console_handler.setFormatter(formatter)
    
    # Root logger
    logger = logging.getLogger()
    logger.setLevel(level)
    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    
    return logger

def setup_argparse():
    """Configura argparse"""
    parser = argparse.ArgumentParser(
        description='Qualys VM Automation Tool',
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog='''
Esempi:
  %(prog)s scan --target 192.168.1.0/24 --title "Daily Scan"
  %(prog)s scan --target 10.0.0.0/24 --wait --export results.csv
  %(prog)s status --scan-id scan/12345
        '''
    )
    
    subparsers = parser.add_subparsers(dest='command', required=True)
    
    # Comando scan
    scan_parser = subparsers.add_parser('scan', help='Lancia nuova scansione')
    scan_parser.add_argument('--target', required=True, 
                            help='Target IP o network (CIDR)')
    scan_parser.add_argument('--title', required=True,
                            help='Titolo scansione')
    scan_parser.add_argument('--profile', default='12345',
                            help='ID profilo scansione (default: 12345)')
    scan_parser.add_argument('--wait', action='store_true',
                            help='Attendi completamento scansione')
    scan_parser.add_argument('--export', metavar='FILE',
                            help='Esporta risultati in CSV')
    
    # Comando status
    status_parser = subparsers.add_parser('status', help='Controlla stato scansione')
    status_parser.add_argument('--scan-id', required=True,
                              help='ID scansione')
    
    # Opzioni globali
    parser.add_argument('--debug', action='store_true',
                       help='Abilita modalità debug')
    parser.add_argument('--log', default='logs/qualys_automation.log',
                       help='File log (default: logs/qualys_automation.log)')
    
    return parser

def command_scan(args, client: QualysClient, logger: logging.Logger):
    """Esegue comando scan"""
    try:
        # Lancia scansione
        scan_id = client.launch_scan(
            scan_title=args.title,
            ip_list=args.target,
            option_id=args.profile
        )
        
        print(f"\n✓ Scansione lanciata con successo")
        print(f"  ID: {scan_id}")
        print(f"  Target: {args.target}")
        
        # Attendi completamento se richiesto
        if args.wait:
            print(f"\n⏳ Attendo completamento scansione...")
            
            if client.wait_for_scan_completion(scan_id, poll_interval=60):
                print(f"✓ Scansione completata")
                
                # Esporta se richiesto
                if args.export:
                    print(f"\n📊 Esportazione risultati...")
                    
                    xml_results = client.get_scan_results(scan_id)
                    vulnerabilities = client.parse_scan_results(xml_results)
                    client.export_to_csv(vulnerabilities, args.export)
                    
                    print(f"✓ Risultati esportati in {args.export}")
                    print(f"  Vulnerabilità trovate: {len(vulnerabilities)}")
            else:
                print(f"⚠ Timeout attesa completamento")
        
        return 0
        
    except QualysAPIError as e:
        logger.error(f"Errore API Qualys: {e}")
        print(f"\n✗ Errore: {e}")
        return 1

def command_status(args, client: QualysClient, logger: logging.Logger):
    """Esegue comando status"""
    try:
        status = client.get_scan_status(args.scan_id)
        
        print(f"\n📊 Stato Scansione {args.scan_id}")
        print(f"  Stato: {status['status']}")
        print(f"  State: {status['state']}")
        print(f"  Target: {status['target']}")
        
        return 0
        
    except QualysAPIError as e:
        logger.error(f"Errore: {e}")
        print(f"\n✗ Errore: {e}")
        return 1

def main():
    """Main function"""
    # Parse arguments
    parser = setup_argparse()
    args = parser.parse_args()
    
    # Setup logging
    logger = setup_logging(args.log, debug=args.debug)
    logger.info("=" * 60)
    logger.info(f"Qualys Automation avviato - Comando: {args.command}")
    
    # Carica env vars
    load_dotenv()
    
    # Verifica credenziali
    required_vars = ['QUALYS_API_URL', 'QUALYS_USERNAME', 'QUALYS_PASSWORD']
    missing_vars = [var for var in required_vars if not os.getenv(var)]
    
    if missing_vars:
        print(f"\n✗ Variabili ambiente mancanti: {', '.join(missing_vars)}")
        print("  Configura le credenziali nel file .env")
        return 1
    
    # Crea client
    try:
        client = QualysClient(
            base_url=os.getenv('QUALYS_API_URL'),
            username=os.getenv('QUALYS_USERNAME'),
            password=os.getenv('QUALYS_PASSWORD')
        )
    except Exception as e:
        logger.error(f"Errore inizializzazione client: {e}")
        print(f"\n✗ Errore inizializzazione: {e}")
        return 1
    
    try:
        # Esegui comando
        if args.command == 'scan':
            return command_scan(args, client, logger)
        elif args.command == 'status':
            return command_status(args, client, logger)
        
    except KeyboardInterrupt:
        logger.info("Interrotto dall'utente")
        print("\n\n⚠ Operazione interrotta")
        return 130
    
    except Exception as e:
        logger.error(f"Errore imprevisto: {e}", exc_info=True)
        print(f"\n✗ Errore imprevisto: {e}")
        return 1
    
    finally:
        client.close()
        logger.info("Qualys Automation terminato")

if __name__ == '__main__':
    sys.exit(main())
```

---

## Troubleshooting

### Problemi Comuni

#### ImportError / ModuleNotFoundError

```python
# Problema: ModuleNotFoundError: No module named 'requests'

# Soluzione 1: Installa modulo
pip install requests

# Soluzione 2: Verifica virtual environment attivo
which python  # deve puntare a venv/bin/python

# Soluzione 3: Installa in venv
source venv/bin/activate
pip install -r requirements.txt

# Soluzione 4: Path di import errato
# Se struttura è src/module.py
from src.module import function  # corretto
from module import function      # errato
```

#### SSL Certificate Errors

```python
# Problema: SSL: CERTIFICATE_VERIFY_FAILED

# Soluzione 1: Disabilita verifica (solo per test!)
import requests
response = requests.get('https://api.example.com', verify=False)

# Soluzione 2: Specifica certificato
response = requests.get('https://api.example.com', 
                       verify='/path/to/cert.pem')

# Soluzione 3: Aggiorna certifi
pip install --upgrade certifi

# Soluzione 4: Usa certificati sistema
import certifi
response = requests.get('https://api.example.com',
                       verify=certifi.where())
```

#### Timeout Errors

```python
# Problema: requests.exceptions.Timeout

# Soluzione 1: Aumenta timeout
response = requests.get(url, timeout=60)  # 60 secondi

# Soluzione 2: Timeout separati (connect, read)
response = requests.get(url, timeout=(5, 30))  # 5s connect, 30s read

# Soluzione 3: Retry automatico
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()
retry = Retry(total=3, backoff_factor=1)
adapter = HTTPAdapter(max_retries=retry)
session.mount('http://', adapter)
session.mount('https://', adapter)
```

#### Encoding Issues

```python
# Problema: UnicodeDecodeError

# Soluzione 1: Specifica encoding
with open('file.txt', 'r', encoding='utf-8') as f:
    content = f.read()

# Soluzione 2: Gestisci errori
with open('file.txt', 'r', encoding='utf-8', errors='ignore') as f:
    content = f.read()

# Soluzione 3: Rileva encoding
import chardet

with open('file.txt', 'rb') as f:
    raw = f.read()
    detected = chardet.detect(raw)
    content = raw.decode(detected['encoding'])
```

#### XML Parsing Errors

```python
# Problema: xml.etree.ElementTree.ParseError

# Soluzione 1: Valida XML prima del parse
import xml.etree.ElementTree as ET

def is_valid_xml(xml_string):
    try:
        ET.fromstring(xml_string)
        return True
    except ET.ParseError as e:
        print(f"XML invalido: {e}")
        return False

# Soluzione 2: Gestisci namespace
root = ET.fromstring(xml_with_namespace)
namespaces = {'ns': 'http://example.com/ns'}
elements = root.findall('.//ns:element', namespaces)

# Soluzione 3: Pulisci XML
import re
xml_clean = re.sub(r'[^\x09\x0A\x0D\x20-\x7E]', '', xml_string)
root = ET.fromstring(xml_clean)
```

### Debug Tips

```python
# 1. Stampa variabili
print(f"DEBUG: variabile = {variabile}")
print(f"DEBUG: type = {type(variabile)}")

# 2. Usa pdb (Python Debugger)
import pdb

def problematic_function():
    x = 10
    pdb.set_trace()  # breakpoint
    y = x * 2
    return y

# 3. Logging debug
logger.debug(f"Valore: {value}")
logger.debug(f"Request: {method} {url}")
logger.debug(f"Response: {response.text[:200]}")

# 4. Ispeziona oggetti
import pprint
pprint.pprint(complex_dict)

# 5. Dir e help
print(dir(oggetto))  # tutti gli attributi
help(funzione)       # documentazione

# 6. Traceback dettagliato
import traceback

try:
    risky_operation()
except Exception:
    traceback.print_exc()

# 7. Assert per verifiche
assert isinstance(value, int), "value deve essere int"
assert len(lista) > 0, "lista non può essere vuota"

# 8. Timeit per performance
import timeit

execution_time = timeit.timeit(
    'function()',
    setup='from __main__ import function',
    number=1000
)
print(f"Tempo: {execution_time}s")
```

---

## Testing (Bonus)

### Unittest Base

```python
import unittest
from src.client import QualysClient

class TestQualysClient(unittest.TestCase):
    """Test per QualysClient"""
    
    def setUp(self):
        """Setup eseguito prima di ogni test"""
        self.client = QualysClient(
            base_url='https://test.qualys.com',
            username='test_user',
            password='test_pass'
        )
    
    def tearDown(self):
        """Cleanup dopo ogni test"""
        self.client.close()
    
    def test_client_initialization(self):
        """Test inizializzazione client"""
        self.assertEqual(self.client.base_url, 'https://test.qualys.com')
        self.assertIsNotNone(self.client.session)
    
    def test_parse_scan_results(self):
        """Test parsing risultati"""
        xml_data = """<?xml version="1.0"?>
        <SCAN_OUTPUT>
            <IP value="192.168.1.100">
                <VULN>
                    <QID>12345</QID>
                    <SEVERITY>5</SEVERITY>
                    <TITLE>Test Vulnerability</TITLE>
                </VULN>
            </IP>
        </SCAN_OUTPUT>
        """
        
        vulnerabilities = self.client.parse_scan_results(xml_data)
        
        self.assertEqual(len(vulnerabilities), 1)
        self.assertEqual(vulnerabilities[0]['qid'], '12345')
        self.assertEqual(vulnerabilities[0]['severity'], '5')

if __name__ == '__main__':
    unittest.main()
```

### Mock per Testing API

```python
import unittest
from unittest.mock import Mock, patch
import requests

class TestAPIClient(unittest.TestCase):
    
    @patch('requests.Session.get')
    def test_get_request(self, mock_get):
        """Test GET con mock"""
        # Setup mock
        mock_response = Mock()
        mock_response.status_code = 200
        mock_response.json.return_value = {'result': 'success'}
        mock_get.return_value = mock_response
        
        # Test
        from src.client import QualysClient
        client = QualysClient('https://test.com', 'user', 'pass')
        
        # Verifica mock chiamato
        mock_get.assert_called()
```

---

## Risorse Utili

### Documentazione

- **Python Official**: https://docs.python.org/3/
- **Requests**: https://requests.readthedocs.io/
- **lxml**: https://lxml.de/
- **Qualys API**: https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf

### Strumenti

- **pip**: Gestore pacchetti Python
- **venv**: Virtual environment
- **pylint**: Linter per Python
- **black**: Code formatter
- **mypy**: Type checker

```bash
# Installare strumenti dev
pip install pylint black mypy

# Lint codice
pylint src/

# Format codice
black src/

# Type checking
mypy src/
```

### Best Practice Checklist

- ✅ Usa virtual environment per ogni progetto
- ✅ Mantieni requirements.txt aggiornato
- ✅ Non committare credenziali (usa .env)
- ✅ Usa type hints per chiarezza
- ✅ Documenta funzioni con docstring
- ✅ Gestisci eccezioni appropriatamente
- ✅ Usa logging invece di print
- ✅ Separa logica in moduli
- ✅ Scrivi test per codice critico
- ✅ Usa context manager per risorse

---

## Quick Reference

### Comandi Essenziali

```bash
# Virtual environment
python3 -m venv venv
source venv/bin/activate
deactivate

# Pacchetti
pip install package
pip install -r requirements.txt
pip freeze > requirements.txt
pip list

# Eseguire script
python script.py
python -m module.submodule
```

### Import Comuni

```python
# Standard library
import os
import sys
import logging
from pathlib import Path
from typing import List, Dict, Optional
from datetime import datetime

# Terze parti
import requests
from requests.auth import HTTPBasicAuth
import xml.etree.ElementTree as ET
from lxml import etree
from dotenv import load_dotenv
import schedule
```

### Pattern Comuni

```python
# Leggere file con context manager
with open('file.txt', 'r', encoding='utf-8') as f:
    content = f.read()

# HTTP request con gestione errori
try:
    response = requests.get(url, timeout=30)
    response.raise_for_status()
    data = response.json()
except requests.exceptions.RequestException as e:
    logger.error(f"Errore: {e}")

# Parsing XML
root = ET.fromstring(xml_string)
for element in root.findall('.//tag'):
    value = element.text

# Export CSV
import csv
with open('output.csv', 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['col1', 'col2'])
    writer.writeheader()
    writer.writerows(data)

# Logging
logger = logging.getLogger(__name__)
logger.info("Messaggio informativo")
logger.error("Errore", exc_info=True)
```

---

## Conclusione

Questa guida copre tutti gli strumenti essenziali per sviluppare script Python professionali per l'automazione Qualys:

✅ **Setup ambiente** con virtual environment  
✅ **Ripasso Python** completo  
✅ **Libreria requests** per API HTTP  
✅ **Parsing XML** con ElementTree e lxml  
✅ **Export dati** in CSV e JSON  
✅ **Logging professionale** per debugging  
✅ **Argparse** per CLI user-friendly  
✅ **Scheduling** con schedule e cron  
✅ **Best practices** per codice robusto  
✅ **Esempi completi** pronti all'uso

**Prossimi passi per il tuo progetto:**

1. Setup ambiente virtuale e installa dipendenze
2. Crea file `.env` con credenziali Qualys
3. Implementa client base seguendo gli esempi
4. Testa chiamate API con piccoli script
5. Espandi funzionalità gradualmente
6. Aggiungi logging e gestione errori
7. Crea script scheduling per automazione
8. Documenta codice e crea README

**Ricorda:** Testa sempre in ambiente di sviluppo prima di usare in produzione, specialmente quando lavori con infrastrutture critiche!

Buon coding! 🐍🚀

---

*Ultima modifica: Gennaio 2025*