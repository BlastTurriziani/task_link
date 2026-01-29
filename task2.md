# Esercizio dario task 2 

Voglio che il glue job giri automaticamente una volta all’ora con questo pattern:
…
12:15
13:15
14:15
15:15
ecc..

C’è un servizio di AWS che ti aiuterà a farlo! 

Dario

# Procedimento
# glue job 
- avere un glue job funzionante e già testato
- per avere l'output desiderato ho modificato lo script del glue

# Bucket S3 
- avere un bucket S3 configurato e testato con glue job

# Eventbridge 
- Attraverso il tool EventBridge dalla dashboard ho fatto click su rules
- create scheduled rule
- ho inserito il nome e la descrizione
- ho fatto click su schedule group default
- recurring schedule
- time zone Europe/Rome
- crone based, ho inserito nei campi (minutes:(15), hours:(*), day of mounth:(?), month:(*), day of the week:(*), year:(*)).
- flexible time: off
- Timeframe ho lasciato i campi vuoti così da non avere vincoli sullo start
- target detail: all Apis: aws glue
- servizio StartjobRun
- dentro lo script nel campo "jobName": "Nome_del_tuo_bucket" e nel mio caso dato che volevo un output con nome+orario.csv ho aggiunto:"Arguments": {"--scheduled_time": "<aws.scheduler.scheduled-time>"
- schedule state Enable se vogliamo che parta una volta finito di configurare
- after schedule none
- Retry policy and dead-letter queue (DLQ), ho creato un SQS per avere alert di tracciabilità (aprire la schermata di creazione SQS in una nuova finestra per non perdere i progressi)
- una volta creato lo aggiungiamo dalla tendina
- permission ho messo la IAM che avevo creato per il glue(ho dovuto aggiornare i permessi per avere la compatibilità di questa operazione)
- Fine !
