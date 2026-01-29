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
- sezione authorizers creiamo la nostra 

# postman passaggi
- facciamo new e selezioniamo http
- click sulla + in alto vicino search collections e facciamo blanck collection e diamo un nome inerente al lavoro 
- andiamo sui tre puntini e facciamo add request
- diamo un nome e mettiamo dentro variables l'url che prendiamo dentro Stages dalla dashboard API gateway
- click post --> dentro url mettere quello dentro post in aws 
- authorization --> auth type --> API key --> key: TABLE_NAME, value: //, add to Query Params
- Params --> key: TABLE_NAME 
- body {"autore":"Lorenzo Ostuni", "libro":"The cage"}
- click su send deve dare un 200 verde e il record sul dynamoDb

# script Lambda modificato per anche le richieste tramite postman
- è stato aggiunto il parsing del body (json.loads(event["body"])) per gestire correttamente le richieste provenienti da API Gateway, che incapsula il payload HTTP come stringa JSON.
- corrette le chiavi DynamoDB per evitare sovrascritture e migliorata la validazione degli input.

payload = event
if isinstance(event, dict) and "body" in event and isinstance(event["body"], str):
    payload = json.loads(event["body"])

autore = event.get("autore")
libro = event.get("libro")


if not isinstance(autore, str) or not autore.strip() or not isinstance(libro, str) or not libro.strip():

item = {
    "partition_key": f"AUTORE#{autore.lower()}",
    "sort_key": f"LIBRO#{libro.lower()}",
    "autore": autore,
    "libro": libro
}

return {
    "statusCode": 500,
    "body": json.dumps({"error": str(e)})
}




