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
# DMS 
## Replication instances 
- ho creato attraverso la dashboard sezione migrate or replicate --> provisioned istances
- una replication, come instance class una dms.t3.small
- allocated storage 50 ma anche meno andava(es.30)
- engine default
- single AZ
- advanced security --> default --> create
## Endpoints

