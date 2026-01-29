# TASK N°4
# Creati una DynamoDB Table e sviluppa una lambda function che scriva un record al suo interno 

Usa il linguaggio che vuoi per la lambda: Python, nodejs, typescript.. uguale 

Ciao!
Dario

# lambda
- è un runtime gestito dove tu fornisci solo la funzione 
- è un codice che gira quando succede qualcosa è un codice che reagisce a eventi 
- output quello che vogliamo spesso un json
- dentro facciamo logica chiamate aws(DynamoDB)
- l'evento parte da un API gateway, EventBridge, test manuale, altra lambda
- la lambda non deve fidarsi dell'input, deve chidersi diverse cose come: attributo x esiste?, è una striga?, è vuota? ecc..
- se salviamo dati sporchi li abbiamo persi per sempre
- prima di scrivere una lambda normalizziamo i dati quindi per es. nomeX in lowercase, rimuoviamo spazi extra ed evitare differenze banali come "Stephen King" vs "stephen king"
- questo serve a: evitare duplicati logici e rendere le chiavi consistenti in modo da avere un lavoro di backend fatto bene
- la lambda non salva direttamente il json ricevuto
- costruisce le chiavi perchè DynammoDB ragiona solo per chiavi
- chiave di partizione: identidica il dominio logico(autore)
- chiave di ordinamento: identifica l'elemento specifico(libro)
- la logica è centralizzata
- nessun client decide la struttura del database
- un record di creazione in ambiente lavorativo non ha solo i dati base ma anche data di creazione ed eventualmente metadata(chi ha scritto, da dove)
- accortezze semplici rendono la lambda debuggabile, auditabile ed estendibile
- tramite permessi IAM possiamo evitare danni ed errori mettendo quelli specifici per il nostro caso d'uso
- la lambda deve sempre rispondere con un output quindi successo, errore o comunque un messaggio chiaro best practice per debugging e integrazione futura
- ogni lambda deve loggare in input anche parziale loggare errori e operazioni chiave es. "scrittura completata"

# NoSQL 
- si presenta in formato tabulare
- quando si parla di NoSQL non parliamo solo di SQL
- quando si parla di dati non organizzati quindi dati che non sono tra righe e colonne stiamo parlando di database non relazionali
- è una classe di database che gestisce dati non strutturati e con schema flessibile
- si tratta di dati coppia chiave valore o si tratta di dati in formato documento 
# DynamoDB
## chiave di partizione(chiave primaria della tabella) 
- è la prima risorsa che usa DynamoDB per localizzare i dati
- quando facciamo una ricerca parte sempre da quella.
- es. dovessimo scegliere la chiave di partizione "tephen king" significherebbe che tutti i record di spephen king sono nello stesso gruppo logico 
## chiave di ordinamento(chiave secondaria)
- raggruppa i record con la stessa chiave di partizione e li ordina
- permette di avere più record che condividono la stessa chiave di partizione
- permette query del tipo: "dammi tutti record di questo autore", "dammi solo quelli che iniziano con ***", "dammi quelli tra due valori"
- es. se scegliessimo la chiave di ordinamento "IT" vuol dire che per l'autore stephen king questo record è quello relativo al libro IT
- ha molto senso utilizzare entrambe queste chiavi con il nostro esempio perchè questo ci facilità la creazione di record e la ricerca di essi
- otteniamo tutti i libri dello stesso autore nello stesso "contenitore logico" e ogni libro è distinguibile dal valore della chiave di ordinamento
---
- è un servizio completamete gestito da aws che ci aiuterà ad archiviare dati NoSQL all'interno del database
- è un database senza server(serverless)
- una tabella dynamoDB(viene chiamata così ma non è la classica tabella)
- posso creare una voce all'interno della tabella e quella voce conterra tutti i dati specifici
- possiamo aggiungere altre "tabelle" e dati utilizzando altri ID e possiamo andare avanti cosi
- fornisce anche il ridimensionamento automatico, se il numero delle richieste aumenta dynamo si ridimensionerà gradualmente
- scalabilità automatica
- performance alte bassissima latenza, fornisce risposte in millisecondi indipendentemente da quanti dati archiviati abbiamo
- fornisce anche sicurezza tramite messagistica instantanea o crittografia
- memorizzazione nella cache in memoria fornita da dynamo
- abbiamo il ttl
- GSI / LSI
- controllo d'accesso granulare con IAM
- Sono disponibili opzioni di beckup e ripristino su richiesta
- se vogliamo evitare sovrascritture possiamo usare la condizione(conditionExpression) per dire: scrivi solo se esiste già 
# creazione  tabella 
- inseriamo il nome: antonio-DynamoDb-table
- chiave di partizione(chiave primaria della tabella)
- click create table
- andiamo all'interno di questa nuova tabella
- explore items ti fa vedere cosa c'è all'interno della nostra tabella
- cliccando sulla tabella ci fa vedere gli elementi
# creare elemento 
- forniamo la chiave di partizione(chiave primaria, partizione)
- aggiungiamo tutti i campi che vogliamo e facciamo crea elemento
- ora dentro la chiave primaria abbiamo tutti gli elementi che abbbiamo messo prima
- possiamo aggiungere tutti i dati che vogliamo essendo dati non strutturati

# API Gateway
- è un servizio AWS che funge da "porta d'ingresso" per le applicazioni, permettendo agli sviluppatori di creare, pubblicare e gestire API sicure a qualsiasi scala
- gestione del traffico: gestisce centinaia di migliaia di chiamate simultanee
- sicurezza
- monitoraggio
- supporto multi-protocollo
- POST: Per creare (mandare il record a DynamoDB)
- GET: Per leggere
- PUT/PATCH: Per aggiornare
- DELETE: Per eliminare.

# postman
- piattaforma fondamentale per tester e sviluppatori
- piattaforma di collaborazione numero uno per API(application programming interfaces)

# nel nostro caso 
- lambda sarà un writer(riceve dati (autore/libro) --> li valida --> li trasforma --> li salva su DyanamoDB)
- nel campo case abbiamo messo questo codice:

import os # legge le variabili d'ambiente
import json # input / output json
import boto3 # SDK per comunicare con i servizi aws(Software deployment kit )
from datetime import datetime, timezone # creare timestamp UTC (debug/audit)

TABLE_NAME = os.environ["TABLE_NAME"] # setup fuori dall'handler/cold start # fa risparmiare tempo e soldi in quanto gira all'inizio dello script
table = boto3.resource("dynamodb").Table(TABLE_NAME) # nome tabella preso da env var (mai hardcodare quindi nessun dato sensibile o specifico)

def lambda_handler(event, context):
    try:
        autore = event.get("autore")  # prende autore
        libro = event.get("libro")    # prende libro

        if not autore or not libro:   # controllo minimo
            return {
                "statusCode": 400,
                "body": json.dumps({"error": "Mancano 'autore' o 'libro'"})
            }

        now = datetime.now(timezone.utc).isoformat()  # timestamp

        item = {
            "autore": f"AUTORE#{autore.lower()}",
            "libro": f"LIBRO#{libro.lower()}",
            "autore": autore,
            "libro": libro,
            "createdAt": now
        }

        table.put_item(Item=item)  # scrive su DynamoDB

        return {
            "statusCode": 200,
            "body": json.dumps({"message": "tabella aggiornata con successo"})
        }

    except Exception as e:
        print("Errore:", str(e))
        return {
            "body": json.dumps({"error": str(e)})
        }
# nel campo test ho semplicemente messo

{
  "autore": "pippo",
  "libro": "pluto"
}

# struttura concettuale del nostro esercizio
{
  "autore": "stephen king",
  "libro": "it"
}
# campo configuration --> Environment variables 
- ho inserito nella key TABLE_NAME e nel VALUE antonio-table-task4

# task4 --> task5 seconda parte: Attraverso postman, fai partire una chiamata api che farà partire lambda che scriverà un record su un dynamodb

# API Gateway REST passaggi
- nel nostro caso useremo API REST
- mettiamo il nome e click su create
- creiamo una risorsa che chiamiamo record nel mio caso 
- /record sarà adesso il mio percorso
- creiamo due metodi: post e get
- creiamo post, selezionando il nostro arn lambda come elaborazione backend e lasciamo tutto così comè 
- creiamo get, quindi inseriamo di nuovo arn lambda e andiamo avanti
- adesso deploy api e selezioniamo "new stage" e sotto dev
- facendo click sulla + affianco a dev apriamo i percorsi

# postman passaggi
- facciamo new e selezioniamo http
- click sulla + in alto vicino search collections e facciamo blanck collection
- diamo un nome e mettiamo dentro variables l'url che prendiamo dentro post in aws e mettiamo il nome
- andiamo sui tre puntini e facciamo add request

