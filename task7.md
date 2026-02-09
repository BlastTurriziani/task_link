TASK N°7 DMS 

RDS SOURCE
URL = ///////////////////////////////////////////
PORT = 5432
User = dba_user
Password = ***********************

RDS TARGET
URL = ///////////////////////////////////////////
PORT = 5432
USER = coreto_user
PASSWORD = ***********************

Creati la tabella antonio-film con attributi come (nomeFilm, regista, annoUscita) e prova il servizio DM

Dario

# RDS/DBeaver 
- per iniziare ho aperto DBeaver per controllare i database source e target
- ho usato le credenziali che mi ha fornito il mio toutor per accedere attraverso url nome db porta e password
- per accedere ho fatto nuova connessione e ho messo le informazioni dalla sezione URL
- ho creato una tabella dentro il source nella public e tre record al suo interno che ci serviranno dopo per fare la prova di migrazione 
# DMS 
## Replication instances - è il "motore" che ci serve per far sincronizzare e migrare i dati
- ho creato attraverso la dashboard sezione migrate or replicate --> provisioned istances
- una replication, come instance class una dms.t3.small
- allocated storage 50 ma anche meno andava(es.30)
- engine default
- single AZ
- advanced security --> default --> create
## Endpoints - source il sistema da cui DMS legge i dati e target Il sistema dove DMS scrive i dati
- ho creato una source endpoint
- Source engine PostgreSQL
- access to endpoint database --> Provide access information manually
- configurazione con le informazioni del nostro database
- Secure Socket layer(SSL) mode --> require
- database name --> create
- stesso identico procedimento con con il target
- quindi andiamo su Endpoint e questa volta scegliamo target e rifacciamo tutto
## Tasks - è il cervello dell'operazione di migrazione sceglie dove e come fare la migrazione 
- task identifier
- source database --> source creato nell'endpoint 
- target --> target creato nell'endpoint
- task type migrate and replicate nel nostro caso specifico
- settings --> wizard --> default 
- selection rules
- mettiamo il nome della tabella dove all'interno c'è la nostra cartella che abbiamo creato all'inizio
- schema table name --> nome che abbiamo dato alla tabella
- action include
- VPC ho messo l'unica che c'era
- subnet group ho messo quella consigliata consigliata da matteo guerra per essere sicuro
- VPC security group --> default
- aws kms key aws/dms
- single AZ(essendo un test cerchiamo di minimizzare i costi il più possibile)
- Availability zone --> no preference
- capacity
- 1 DCU
- max 2 DCU 

