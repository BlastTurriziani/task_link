TASK n°8 

Creati un apache web server accessibile da browser che ti dica “Hello World”.
Consigli: dovrai creare un EC2, mi raccomando usa l’istanza micro e usa la subnet sbn-mic-p-009
 
Ciao e divertiti!
Dario

# EC2/apache web server 
- dalla dashboard andare su EC2 
- instance --> launch instances 
- mettiamo il nome --> Amazon Linux --> Amazon Linux 2023
- instance type: t3.micro
- key pair --> ne generiamo una e la salviamo in una cartella
- network settings --> Network nel mio caso avevo una sola scelta --> subnet sbn-ecc-009
- auto-assign public ip --> enable
- Firewall (security groups) --> create security group
- click su: Allow HTTP traffic from the internet --> edit --> nome
- sezione: Inbound Security Group Rules
- type: ssh -> protocol: tcp -> port range: 22 -> source type: my ip
- add security group role -> type: http -> protocol: tcp -> port range: 80 -> source type: anywhere
- per rendere la task più pulita ho usato lo user data per far partire lo script e avere gia tutto pronto
- questo script viene avviato solo una volta al primo avvio in caso di riavvio apache parte perchè enable ma lo script non si riesegue
- launch instance
- entriamo dentro l'instance copiamo e l'ip privato
- andiamo su internet e scriviamo http://ip-privato e se tutto è andato a buon fine abbiamo la nostra schermata con hello world 

# codice completo
- #!/bin/bash # Esegui questo script usando Bash
- dnf -y update || yum -y update # yum = fallback (per versioni più vecchie) --> -y = risponde automaticamente “yes”
- dnf -y install httpd || yum -y install httpd # httpd = Apache Web Server
- systemctl enable httpd # Avvia automaticamente Apache ogni volta che la macchina si riavvia
- echo "Hello World" > /var/www/html/index.html # Scrive Hello World Nel file principale del web server
- systemctl start httpd # Accende il web server immediatamente




 



