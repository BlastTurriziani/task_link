# load balancer 
- è un server che si interfaccia alla nostra applicazione e inoltra tutto il traffico alle istanze della nostra applicazione 
- quando uno user si connette non si connette direttamente alla instance ma al load balancer
- quindi una volta che es. user si collega il load balancer reindirizza il traffico verso l'instance che a sua volta restituisce la risposta al ALB che la restituisce a user
- in caso ci fosse anche user2 e user3 ALB manderebbe ognuno in una instance differente lo scopo è bilanciare il carico delle richieste questa è l'idea di base del load          balancer
- ottimi per i microservizi e applicazioni basate su container docker e amazon ECS
- tutti gli LB hanno nomi statici
- possono essere scalabili ma non istantaneamente, se prevedi un carico elevato e un'aumento di prestazioni dovresti contattare aws per far "riscaldare" il LB affinchè si        adatti alle tue esigenze
- se dovessimo ricevere un errore 503 vuol dire che il nostro LB non ha più capacità  (4xx client, 5xx application)
## tipologie d'uso
- per bilanciare il carico in applicazioni downstream
- esporre un solo punto d'accesso (DNS) per la nostra applicazione
- per evitare che instance che devono stare up vanno down, avendo la funzione health check le instance vengono regolarmente controllate e se non dovessero funzionare l'ALB       reindirizzerà il traffico verso instance che funzionano
- può fornire l'SSL o HTTPS per i nostri siti web, quindi vuol dire che la crittografia e tra il client e L'ALB
- può anche forzare la persistenza con i cookies quindi fondamentalmente comunicherà con la stessa instance nel tempo
- lavora ad alte prestazioni anche su più zone, quindi può essere eseguito su più zone in questo modo la nostra applicazione sarà altamente disponibile anche se una zona di      disponibilità non funziona
- possiamo anche separare il traffico pubblico da quello privato
## health check
- sono fondamentali per i LB perchè consentono di reindirizzare il traffico verso instance integre
- capisce quali instance sono disponibili per rispondere alla richiesta
- il controllo d'integrità è semplicissimo basta fornire porta e percorso e funziona con risposte come 200 (quindi ok) al contrario non sarà integra
## application load balancer V2
- l'application load balancer V2 (layer 7) viene chiamato così perchè consente di lavorare a livello http 
- consentono di gestire più applicazioni http su più macchine
- possiamo bilanciare il carico su più applicazioni sulla stessa macchina e si tratta di container
- possiamo bilanciare il carico utilizzando il root nell'URL
- possiamo bilanciare il carico anche con l'host name nell'URL
- consente una maggiorre flessibilità
- c'è una funzionalità di mappature delle porte che ci permette di indirizzare a qualsiasi porta dinamica ne backend
- potremmo utilizzarlo di fronte a 10 applicazioni e andrebbe benissimo
- possiamo abilitare la persistenza a livello di gruppo di destinazione, lo stesso utente accede sempre alla stessa instance è la persistenza verra generata dal nostro ALB
- ALB supporta tre cose protocolli HTTP/HTTPS e Websockets 
- i server delle applicazioni non vedono direttamente l'ip del client il vero IP del client viene inserito in una intestazione denominata x-forwarded-for-header
## Network load balancer (v2)
- sono di livello 4 e funzionano a traffico TCP
- questi LB sono considerati ad altissime prestazioni
- hanno una latency minore sispetto a ALB, con 1oo ms per NLB e 400 per ALB
- il processo è uguale a quello di ALB la cosa che cambia è che lavora a livello TCP
- è l'unico che vede direttamente l'ip del client quindi non c'è alcun inoltro x 

# construzione application load balancer
- come pre-requisiti abbiamo bisogno della ec2 creata in precedenza per poi collegarla al nostro load balancer
## analisi d'ambiente
- mi sono reso conto che nel mio caso non avevo lo'opzione di crare una internet gateway per via di permessi aziendali non concessi
- dentro il mio ambiente (dev) non ho nulla che mi faccia creare una infrastruttura pubblica quindi non ho potuto fare l'ALB internet-facing
- mi sono adeguato alla situazione e ho usato l'internal
## creazione target group 
- target type: instances
- protocol: HTTP
- port: 80
- health check path: /
- registriamo la ec2
- se il taget è healty fine(vuol dire che la ALB comunica con la ec2)
## creazione application load balancer
- type: ALB
- scheme: internal
- 2 Subnet in AZ diverse
- liestener: HTTP 80
- Default action: Forward al target group
- il load balancer espone un DNS interno:internal-alb-antonio-web-xxxxx.eu-south-1.elb.amazonaws.com
## security group 
# inbound:
- http 80
- source: rete consentita(inizialmente 0.0.0./0 per test)
## security group EC2 
# inbound:
- http 80
- source: security group dell'ALB
- questo impedisce accesso diretto all'EC2
- il traffico deve passare dal load balancer
## fine 
- aprendo il DNS del load balancer da browser viene mostrato "hello world"(lo troviamo nella pagina principale come: DNS name) 






