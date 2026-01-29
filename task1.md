# Esercizio spostare dei file da un rds ad un bucket s3 con il servizio aws glue.
Ciao Antonio,
ecco i dettagli:
Glue Task
Importare dati da source a target
 
SOURCE – RDS
"*****lI******y-dev":{
      "server":"ho****-in*****.c*******ok.eu-so****-*.rds.amazonaws.com",
      "port":5432,
      "username":"glue_user",
      "password":"**************",
      "database":"*************"
“tabella”: “***************”
   }
 
TARGET
Creati un bucket S3 chiamato “antonio-esercizio-glue” e salva il file in formato .csv dentro la cartella /data.

# Preparazione esercizio
### IMPORTANTE !!! ricordarsi di creare tutto con il prefisso antonio
- Creare un bucket S3 con la regione di Milano(eu-south-1)
- Lo chiamiamo antonio-esercizio-glue e lasciamo tutte le impostazioni predefinite
- Creiamo un ruolo IAM clicchiamo ruoli e dentro la pagina principale in Use case dal menù a tendina selezioniamo Glue subito dopo avanti
- Diamo accesso completo ad S3 per scansionare i dati ecc..
- Diamo accesso completo ai registri cloudwatch
- Diamo accesso completo ad aws glue console 
- Chiamiamolo antonio-role-glue, clicchiamo: crea ruolo 
- Dentro S3 in object creiamo una cartella e la chiamiamo antonio-landing-zone(zona di destinazione) creiamo la cartella e carichiamo il nostro file.
- Dentro la cartella antonio-landing-zone creiamo un'altra cartella chiamata antonio-hotel(clienti) o qualcosa che centri con i nostri file
- Dentro la cartella customers creiamo un'altra cartella con la data di caricamento e la chiamiamo antonio-load-date=20260112 in questa cartella caricheremo i nostri file.
- Proseguiamo con il caricamento dei nostri file quindi clicchiamo add files e facciamo upload
- Adesso abbiamo quello che ci serve quindi andiamo nella console di glue e clicchiamo attraverso data catalog: databases
- Clicchiamo add database e lo chiamiamo antonio-db-project la posizione e facoltativa 
- Creeremo la tabella in questo database quindi clicchiamo su antonio-db-project e facciamo add table e la chiamiamo antonio-hotel
- Il database sarà antonio-db-project
- Table format Standard AWS Glue table(default)
- Data store: S3
- Data location: my account
- Include path: Brows S3 e clicchiamo antonio-esercizio-glue
- Clicchiamo subito dopo su antonio-landing-zone --> antonio-hotel
- Data format (dipende da quello che abbiamo) e il delimitatore(Apri il file con un editor di testo e guarda cojme sono separati i valori) fare next
- Possiamo modificare i dati manualmente quindi possimo modificare i dati della tabella come nome tipo di dati ecc.. 
- Puoi definire manualmente lo schema e tutto ciò che riguarda la tua tabella o eseguire quello che viene chiamato Glue Crawler.
- Abbandoniamo momentaneamente il processo e ci spostiamo sui databases e clicchimo su crea tabelle utilizzando crawler(serve per aggiungere manualmente una tabella) la chiamiamola antonio-hotel
- I tuoi dati sono già mappati alle tabelle glue: clicchiamo not yet
- Add data source: S3 --> brows s3 --> antonio-esercizio-glue --> antonio-landing-zone --> antonio-hotel --> choose
- Facciamo la scansione di tutte le sottocartelle e facciamo la scansine dell'origine dati, avanti
- Adesso dobbiamo selezionare il ruolo che verra utilizzato dal crawler per esegire la scansione di questi dati e creare una tabella per noi(avanti)
- Target database clicchiamo: antonio-db-project possiamo aggiungere un prefisso alla tabella sennò verrà utilizzato il nome del file che abbiamo caricato.
- Su frequency scegliamo quando utilizzeremo questo crawler, selezioniamo su richiesta in modo che venga utilizzato solo quando ne abbiamo bisogno
- A questo punto rivediamo tutto e clicchiamo su crea crawler.
- Tornando su crawler possiamo vedere se sta andando running nello state in caso aspettiamo e vediamo se crea una tabella
- una volta che l'esecuzione è stata completata torniamo nelle tabelle e controlliamo ci sia quella nuova
- Andando su databases e cliccando su antonio-db-project --> tables clicchiamo aggiorna e controliamo il contenuto che il crawler ha creato
- Adesso abbiamo una tabella con i dati S3 come origine.
# Come controllare i nostri dati attraverso athena(sql)
- Nella console di athena facciamo clic su query editor
- Possiamo vedere il catalogo dati e il database del progetto
- possiamo vedere l'anteprima della nostra tabella facendo clic sui tre puntini dentro tables affianco al nome della nostra tabella.
- Se stiamo usando athena per la prima volta dobbiamo selezionare una posizione s3 dove verranno archiviati questi risultati
- facendo clic su modifica impostazioni
- andare sul nostro bucket S3(antonio-esercizio-glue) e creiamo una nuova cartella chiamata antonio-query-results e facciamo crea cartella
- Torniamo nella schermata che avevamo lasciato in sospeso e selezioniamo antonio-query-results --> salva
- da adesso possiamo iniziare ad interrogare i nostri dati.
- eseguiamo la query
- possiamo notare dentro athena sezione labs tutto in basso nella nostra tabella la scritta antonio-load-date con la scritta partizionato al suo fianco, significa che dentro i nostri dati stiamo eseguendo il partizionamento utilizzando questa data di caricamento.
- aggiungendo altre cartelle con date diverse possiamo inserirle all'interno della query e avere questa specie di filtro che ci aiuterà ad isolare solamente quei dati. Questo ci aiuterà ad utilizzare solamente quei dati e quindi ad avere prestazioni migliori

# Glue *
- È un processo ETL (Extract, Transform, Load) gestito da aws.
- aws gestisce tutto il backend del server e tutto il provisioning del software.
- Aiuta a capire i dati, suggerisce trasformazioni e genera codice ETL in modo da dedicare meno tempo per la codifica manuale.
- Esegue processi ETL in modo flessibile in una piattaforma spark scalabile assegnando automaticamente le risorse necessarie per completarli.

# Catalogo
# Database:
- contenitore logico all'interno del catalogo dati all'interno di glue
- Archivia le tabelle dei metadati
- Essendo un database logico non esiste un vero è proprio database
- Possiamo creare diverse tabelle in glue e possono essere tutte raggruppate in un database.
- Queste tabelle contengono informazioni sui dati archiviati in varie sorgenti come Amazon S3, RDS, Redshifts ecc..
- I dati resteranno nella sorgente originale, le tabelle conterranno solo le informazioni di quei dati.
- Il raggrupamento di queste tabelle viene chiamato database
- Raggruppando le tabelle in un database sarà più semplice gestire i metadati.

# Tables
- metadati dei dati archiviati in varie origini dati
- svolgono un ruolo fondamentale nell'organizzazione nell'interrogazione e nella trasformazione dei dati fornendo un modo strutturato per accedere ai dati
- non si tratta di una tabella di dati fisica quindi i dati non vengono spostati nella tabella ma rimangono nella fonte es(s3,rds ecc..)
- contiene solo i metadati sui dati quindi dove sono archiviati qual'è lo schema ecc..
- Possiamo definire lo schema e tutto quello che può definire la nostra tabella 
- Una volta creata questa tabella con crawler possiamo mantenere i metadati dei nostri dati in altre parole: Il crawler crea e mantiene i metadati nel Glue Data Catalog, rendendo i dati in S3 facilmente interrogabili da più servizi AWS senza duplicarli.

# Athena
- Amazon Athena è un servizio serverless di AWS che permette di fare query SQL direttamente sui dati in S3, senza creare database o server
- Con Athena i dati non vengono spostati in un database: restano in S3 e vengono interrogati direttamente tramite SQL.
- Paghiamo solo per i dati scansionati

## Come usare athena
- Nella console di athena facciamo clic su query editor
- Possiamo vedere il catalogo dati e il database del progetto
- possiamo vedere l'anteprima della nostra tabella facendo clic sui tre puntini dentro tables affianco al nome della nostra tabella.
- Se stiamo usando athena per la prima volta dobbiamo selezionare una posizione s3 dove verranno archiviati questi risultati
- facendo clic su modifica impostazioni
- andare sul nostro bucket S3(antonio-esercizio-glue) e creiamo una nuova cartella chiamata antonio-query-results e facciamo crea cartella
- Torniamo nella schermata che avevamo lasciato in sospeso e selezioniamo antonio-query-results --> salva
- da adesso possiamo iniziare ad interrogare i nostri dati.
- eseguiamo la query
- possiamo notare dentro athena sezione labs tutto in basso alla nostra tabella la scritta antonio-loaddate

# Crawlers
- è un programma che si collega alla fonte dati e analizza automaticamente i dati in varie fondi dati determina lo schema e crea tabella di metadati
- Si collega al nostro archivio dati analizza i dati che si trovano lì e deduce lo schema e tutto il resto.
- Utilizzando lo schema attraverso l'archivio crea le tabelle con i metadati nel catalogo dati in glue.

# Connections
- Include un catalogo dati, un repository di metadati centrale.
- possiamo avere i nostri dati in qualunque database dentro aws, i nostri metadati ovunque essi siano possono essere archiviati dentro un catalogo in aws glue.
- metadati: informazioni correlate allo schema e alla fonte dei dati dove vengono correlati.
- Il catalogo dati può essere utilizzato per: Ottenere i metadati informativi sui tuoi dati, avere il monitoraggio degli accessi ecc(da approfondire)

# AWS Glue ETL
- Puoi scrivere il tuo codice nel notebooks tramite la tendina Data integration ETL
- Puoi creare e modificare visivamente il codice

# ETL jobs
- i processi ETL sono il fulcro della funzionalità di ETL aws glue
- vengono utilizzati per trasformare i dati
- e possibile estrarre i dati da una origine trasformarli nel processo e caricarli in una destinazione
- può essere scritto in spark(es. Pspark, strumento molto potente nel mondo dei big data)
- non è necessario installare spark perche aws è senza server e completamente gestito
- esistono due modi per creare questi processi ETL
- è possibile creare visivamente i processi in modo wspeciale ovvero specificando l'oririgene
- oppure è possibile generare uno script per l'utente o creare il tuo script

# Workflows
- È possibile creare monitorare ed eseguire visivamente le pipeline ETL
- Un motore ETL in grado di generare automaticamente codice python.
- Un motore flessibile che gestisce la risoluzione delle dipendense il monitoraggio dei processi e i nuovi tentativi.
- Insieme questi elementi facilitano una parte di lavoro pesante come individuazione, categorizzazione, pulizia, arricchimento e spostamento dei dati.
- Rileva automaticamente i dati determina lo schema e crea il nostro catalogo dati.
- Offre un'ntegrazione immediata con amazon athena, amazon EMR e amazon redshift spectrum
- Il codice ETL creato da glue è semplicemente codice python modificabile, riutilizzabile e portatile.
- è serverless quindi non ci sono risorse da gestire e paghi solo per quello che i nostri lavori consumano per l'esecuzione.
- Lavora molto con spark

