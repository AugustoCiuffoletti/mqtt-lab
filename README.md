# Broker Raspberry per un'attività didattica sul protocollo MQTT

Il contenuto di questo repository guida nella configurazione di un laboratorio per la realizzazione di attività riguardanti il protocollo MQTT in una classe BYOD.

L'attrezzatura necessaria a ciascuno studente comprende il PC di proprietà e un dispositivo IoT con interfaccia WIFi integrata, del tipo ESP8266.

L'attrezzatura del docente comprende un RaspberryPi di tipo B o superiore, e un router WiFi. Non è necessaria la disponibililtà di accesso Internet. Il docente avrà pre a disposizione un PC collegato tramite WiFi allo stesso hotspot per svolgere attività di assistenza e verifica.

Questo repo contiene le istruzioni e materiale utile, e tratta la sola configurazione del Raspberry Pi in modo non esaustivo.

### Finalità e architettura

L'obiettivo è dare allo studente la possibilità di osservare il funzionamento del broker, realizzando client in grado di implementare operazioni di publish/subscribe sul dispositivo IoT. Lo studente deve quindi poter osservare l'esito di tali operazioni attraverso un client fornito dal docente

L'infrastruttura realizzata prevede che tale client sia accessibile tramite il browser, per evitare operazioni di installazioni critiche sui PC degli studenti. Quindi attraverso il browser lo studente accede ad una interfaccia che gli consente le operazioni proprie del protocollo MQTT sul broker realizzato tramite il Raspberry. Quando avrà sviluppato il proprio dispositivo potrà quindi inviare controlli con la publish, e ricevere dati tramite la subscribe.

Il client viene fornito allo studente visitando con il browser il server web realizzato sul Raspberry.  Sul tale componente quindi è installato un server Web che fornisce la singola pagina contenente, insieme all'HTML, anche il Javascript che realizza l'accesso al broker MQTT.

Per realizzare l'accesso al server MQTT tramite il browser è necessario utilizzare uno strumento compatibile con HTTP, e la scelta ricade necessariamente su websocket. Quindi il broker MQTT deve offrire sia l'interfaccia TCP classica su porta 1883, che l'accesso HTTP Websocket. Il broker Mosquitto, opportunamente configurato, offre entrambe le interfacce.

In conclusione l'architettura software del Raspberry Pi consiste nel broker Mosquitto, configurato con interfaccia TCP (porta 1883) e Websocket (porta 9001) e un webserver nginx che fornisce una singola pagina contenente la "single page application" che comunica con il broker tramite i websocket.

Le comunicazioni non sono cifrate per essere facilmente tracciabili con strumenti come Wireshark o forgiabili con PackETH.

### Preparazione della lezione

Per configurare il collegamento WiFi serve collegare al Raspberry tastiera, mouse e video per accedere senza credenziali all'interfaccia grafica Debian.

Per operare è necessario conoscere l'indirizzo IP del Raspberry. Questo si ottiene:

* tramite l'interfaccia grafica del Raspberry (ifconfig wlan0), oppure
* con un PC all'hotspot, che di solito offre una interfaccia Web per la configurazione e l'ispezione

Le precedenti operazioni sono da fare una volta sola. In genere l'hotspot si ricorda l'ultima configurazione, in particolare l'indirizzo IP assegnato al Raspberry.

Nel seguito considero che il Raspberry abbia, sulla interfaccia WiFi, indirizzo IP 192.168.1.10.

### Uso in classe

Iniziare mettendo in funzione l'hotspot. Quando il PC del docente è collegato all'hotspot alimentare il Raspberry senza periferiche. Il Raspberry impiega qualche decina di secondi per avviarsi, collegarsi all'hotspot ed avviare il broker ed il webserver. A questo punto l'infrastruttura è pronta.

Allo studente viene indicato l'IP del broker (nell'esempio 192.168.1.10).

Lo studente si collega con il suo browser a http://192.168.1.10 per sperimentare inizialmente il funzionamento del broker. Poi creerà il suo dispositivo collegandolo al broker per pubblicare o ricevere messaggi che potranno essere osservati o spediti tramite l'interfaccia web sul browser.

### Dettagli di configurazione (for future reference)

In fase di configurazione e sviluppo il router WiFi funziona da Internet gateway (impossibile con gli SSID di ateneo) per scaricare pacchetti ed altro. In classe il router WiFi è sganciato da Internet ed è raggiungibile dagli studenti per le funzioni di broker e web server. 

* Accesso in ssh: user admin, password admin (da cambiare in chiave pubblica). (configurazione in /etc/ssh/sshd.conf) a admin@192.168.1.1
* sul RPi c'è il broker che funziona sulla porta 1883 (TCP/IP) e 9001 (Websocket). Configurazione commentata nel file /etc/mosquitto/mosquitto.conf
* sul RPI ho installato un webserver nginx. Collegandosi al RPi tramite il browser (http://192.168.1.1) si scarica una pagina con il Javascript per connettersi al broker tramite websocket. Installata in /var/www/index.html.

### Da fare

* aggiustare il javascript per avere una migliore presentazione, non indispensabile
* non sono riuscito ad attivare la porta eth0, ma ho installato dnsmasq per far funzionare un dhcp. Potrebbe essere comodo, non indispensabile. COnfigurazione non funziqonante in dnsmaq.conf e dhcpcd.conf)
* non ho completato il collegamento tramite USB, anche questo comodo ma non essenziale
* impedire accesso per PW e inserire le pubkeys necessarie in .ssh/authorized_keys
* github con note e codice (arriva presto)
* inserire l'indirizzo del broker in modo automatico nella pagina HTML (self.location.host). Evitare dunque la parte di connessione.

### Contenuto del repository
 

  * websocket* : pagine contenente client MQTT di vario genere tratte da [1]. Quella utilizzata nella configurazione è websockets-3-htm
  * test_python: due funzioni python per testare il broker. Richiedono l'installazione della libreria   "paho". Tratte da [2] che ne illustra bene le funzionalità e l'uso 

### Credits

* [1] [http://www.steves-internet-guide.com/using-javascript-mqtt-client-websockets/](https://www.emqx.com/en/blog/how-to-use-mqtt-in-python)
* [2] [https://www.emqx.com/en/blog/how-to-use-mqtt-in-python](https://www.emqx.com/en/blog/how-to-use-mqtt-in-python)

