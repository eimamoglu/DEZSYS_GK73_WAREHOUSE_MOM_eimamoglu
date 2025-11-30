# DEZSYS_GK73_WAREHOUSE_MOM
Verfasser: Elyesa Imamoglu, 4CHIT

## Einführung

Diese Übung soll die Funktionsweise und Implementierung von eine Message Oriented Middleware (MOM) mit Hilfe des **Frameworks Apache Kafka** demonstrieren. **Message Oriented Middleware (MOM)** ist neben InterProcessCommunication (IPC), Remote Objects (RMI) und Remote Procedure Call (RPC) eine weitere Möglichkeit um eine Kommunikation zwischen mehreren Rechnern umzusetzen.

Die Umsetzung bas
Die Umsetzung basiert auf einem praxisnahen Beispiel eines Warenlagers. Die Zentrale des Warenlagers moechte jede Stunde den aktuellen Lagerstand aller Lagerstandorte abfragen.

Mit diesem Ziel soll die REST-Applikation aus MidEng 7.1 Warehouse REST and Dataformats bei einem entsprechenden Request http://<IP Wahllokal>/warehouse/send die Daten (JSON oder XML) in eine Message Queue der Zentral uebertragen. 
In regelmaessigen Abstaenden werden alle Message Queues der Zentrale abgefragt und die Daten aller Standorte gesammelt.

Die gesammelten Lagerstände werden ueber eine REST-Schnittstelle (in XML oder JSON) dem Berichtswesen des Managements zur Verfuegung gestellt.


## 1.4 Demo Applikation

*   Installation und starten des Message Broker Apache Kafka (Container)  
[https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart)    

*   Erstellen einer Message Queue "quickstart-events" (Terminal/Container)   
     `cd /opt/kafka`   
     `bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092`    

*  Senden von Nachrichten (via Terminal)    
    `bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092`   
    `> Hallo Spencer, hier ist Nachricht 1.`   
    `> Hallo Spencer, hier ist Nachricht 2`   
    `> Hallo Spencer, hier ist Nachricht 3.`   

*  Lesen von Nachrichten (via Terminal)   
    `bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092`   

### 1.4.1 warehouse_demo

Demo 1 beinhaltet eine Implementierung, die alle Einzelschritte zur Implementierung von Java und JMS beinhaltet und uebersichtlich darstellt. 

 *   Starten der Demo Applikation 
     `gradle clean bootRun`

 *   Senden einer Nachricht 
      http://localhost:8080/send?message=Hallo Spencer

 *   Empfang der Nachricht auf der Konsole
      Hallo Spencer


## 1.6 Fragestellung für Protokoll

*   Nennen Sie mindestens 4 Eigenschaften der Message Oriented Middleware?

    Asynchronität – Sender und Empfänger müssen nicht gleichzeitig online sein
    Entkopplung – Sender kennt Empfänger nicht
    Zuverlässigkeit – Nachrichten gehen nicht verloren (Persistenz, Acknowledgements)
    Skalierbarkeit – mehrere Producer/Consumer möglich
    Persistente Speicherung – Nachrichten können auf Platte gespeichert werden
    Lastverteilung – mehrere Consumer teilen sich Nachrichten

*   Was versteht man unter einer transienten und synchronen Kommunikation?

    Transient bedeutet, Nachrichten existieren nur, solange beide Systeme aktiv sind.
    Synchron heißt, der Sender wartet auf eine direkte Antwort und blockiert währenddessen.

*   Beschreiben Sie die Funktionsweise einer JMS Queue?

    Punkt-zu-Punkt Kommunikation
    1 Producer -> Queue -> 1 Consumer (ein Empfänger pro Nachricht)
    Nachrichten werden gespeichert, bis ein Consumer sie abholt (FIFO)
    Jeder Message wird genau einmal zugestellt (exactly-once / at-least-once)

*   JMS Overview - Beschreiben Sie die wichtigsten JMS Klassen und deren Zusammenhang?

    ConnectionFactory erstellt Verbindungen, Connection verbindet zum Broker, Session erzeugt Producer/Consumer, MessageProducer sendet Nachrichten, MessageConsumer empfängt sie, und Destination beschreibt Queue oder Topic.

*   Beschreiben Sie die Funktionsweise eines JMS Topic?

    Ein Topic funktioniert nach Publish/Subscribe: Ein Publisher sendet eine Nachricht, und alle aktiven Subscriber erhalten eine Kopie.

*   Was versteht man unter einem lose gekoppelten verteilten System? Nennen Sie ein Beispiel dazu. Warum spricht man hier von lose?

    Ein lose gekoppeltes System arbeitet unabhängig voneinander, z. B. Kafka, da Sender und Empfänger nichts voneinander wissen und asynchron kommunizieren.

`
## 1.6 Links & Dokumente

*   Grundlagen Message Oriented Middleware: [Presentation](https://elearning.tgm.ac.at/pluginfile.php/119077/mod_resource/content/1/dezsys_mom_einfuehrung.pdf)
*   Middleware:  [Apache Kafka](https://kafka.apache.org/quickstart)  
*   [Apache Kafka | Getting Started](https://kafka.apache.org/documentation/#gettingStarted)   

    
  https://medium.com/@abhishekranjandev/a-comprehensive-guide-to-integrating-kafka-in-a-spring-boot-application-a4b912aee62e
  https://spring.io/guides/gs/messaging-jms/  
  https://medium.com/@mailshine/activemq-getting-started-with-springboot-a0c3c960356e   
  http://www.academictutorials.com/jms/jms-introduction.asp   
  http://docs.oracle.com/javaee/1.4/tutorial/doc/JMS.html#wp84181    
  https://www.oracle.com/java/technologies/java-message-service.html   
  http://www.oracle.com/technetwork/articles/java/introjms-1577110.html  
  https://spring.io/guides/gs/messaging-jms  
  https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-messaging.html  
  https://dzone.com/articles/using-jms-in-spring-boot-1  
