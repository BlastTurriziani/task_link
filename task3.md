TASK N°3
prerequisito: creati un secondo glue job identico al primo che importa su S3 una tabella a tuo piacimento dello stesso database

WORKFLOW
Step 1) start glue job 1
Step 2) aspetta 5 minuti
Step 3) start glue job 2
Step 4) una volta che i glue job hanno finito la loro esecuzione dovrai ricevere un e-mail con scritto “Ciao, l’ingestion dei glue job è terminato”

Benvenuto nel mondo degli orchestratori! C’è un servizio specifico in AWS che ti aiuterà
# glue job 
- creazione secondo glue
- cambiare le informazione della cartella
- fare un run di prova

# orchestraction
- esegue diversi processi(workflow)
- l'orchestraction in glue funziona solo con i servizi glue, per tutti gli altri dobbiamo usare altri tools aws
- è necessario avere un trigger che attivera l'intera pipeline, sono disponibili quattro tipi di trigger
- il primo è su richiesta che verrà utilizzato per eseguire l'intero flusso di lavoro in un determinato momento in base alle nostre esigenze
- è possibile specificare che la pipeline venga eseguita ogni giorno ad un determinato momento in base alle nostre esigenze

# Aws Step functions
- dentro design workflow una volta selezionato il servizio glue startJobRun, sotto il json fare click su wait for task to complete(in questo modo aspetterà che l'altro finisca prima di partire)
- cerchiamo il servizio SNS publish(servzio per inviare notifiche) a noi serve per mandarci la mail
- selezioniamo il servizio di notifiche che ci serve
- nei parametri API scegliamo il nome dell'argomento(glue j)
- possiamo aggiungere un riprovatore con una logica specifica o quanti tentativi di ripetizione, con anche frequenza di backoff opzionale.
- click next, assegnamo un nome che deve iniziare con antonio

# SNS(Simple Notifications Service)
- permette di inviare un messaggio in diversi servizi contemporaneamente(es. email, messaggi ecc..)
- topic: canale sul quale un editore pubblicherà, ci saranno dei "consumatori" una volta iscritti, ogni volta che ci sarà una comunicazione arriverà a tutti gli endpoint
- abbiamo la crittografia, criteri d'accesso, criteri protezioni dei dati, criteri consegna http, loging e tag
- subscriptions: possiamo usare diversi servizi nella p
- possiamo filtrare il nostro messaggio
### creazione Topic
- inseriamo il nome antonio-ecc..
- opzione standard per non avere un ordinamento dei messaggi e fifo per averla
- andare avanti e tenere tutto standard
### creazione subscriptions
- protocol: selezioniamo email
- inseriamo la mail
- facciamo click su crea abbonamento 
- arriverà una mail su quella che abbiamo inserito e e seguiremo le istruzioni della pagina
- ricaricando pagina avremo successful
- andando sui topic e cliccando quello creato 
- mettiamo un messaggio di prova
- ttl facoltativo
