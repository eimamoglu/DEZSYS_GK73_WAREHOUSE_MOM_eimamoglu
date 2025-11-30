# DEZSYS_GK73_WAREHOUSE_MOM
Verfasser: Elyesa Imamoglu, 4CHIT

## Einführung

Diese Übung soll die Funktionsweise und Implementierung von eine Message Oriented Middleware (MOM) mit Hilfe des **Frameworks Apache Kafka** demonstrieren. **Message Oriented Middleware (MOM)** ist neben InterProcessCommunication (IPC), Remote Objects (RMI) und Remote Procedure Call (RPC) eine weitere Möglichkeit um eine Kommunikation zwischen mehreren Rechnern umzusetzen.

Die Umsetzung bas
Die Umsetzung basiert auf einem praxisnahen Beispiel eines Warenlagers. Die Zentrale des Warenlagers moechte jede Stunde den aktuellen Lagerstand aller Lagerstandorte abfragen.

Mit diesem Ziel soll die REST-Applikation aus MidEng 7.1 Warehouse REST and Dataformats bei einem entsprechenden Request http://<IP Wahllokal>/warehouse/send die Daten (JSON oder XML) in eine Message Queue der Zentral uebertragen. 
In regelmaessigen Abstaenden werden alle Message Queues der Zentrale abgefragt und die Daten aller Standorte gesammelt.

Die gesammelten Lagerstände werden ueber eine REST-Schnittstelle (in XML oder JSON) dem Berichtswesen des Managements zur Verfuegung gestellt.


## Code Snippets

`` Java
    public class WarehouseController {

        @PostMapping("/send")
        public ResponseEntity<String> sendWarehouseData(@RequestBody WarehouseData data) {
            // Sende Daten an Kafka Topic
            kafkaTemplate.send("warehouse-input", data);
            return ResponseEntity.ok("Daten gesendet");
        }
    }
``

## Fragestellung für Protokoll

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

## Wichtig fürs Testen (Terminal)

* Starten der Spring Boot Applikation (Optional)
    ./gradlew --refresh-dependencies bootRun

* Starten der Docker Container
    docker compose up -d

* Überprüfen ob Kafka läuft
  docker exec -it kafka kafka-topics --list --bootstrap-server localhost:9092

* Versenden von Lagerbestandsdaten an die Zentrale (Beispiel JSON)
    curl -X POST http://localhost:8080/warehouse/send \
    -H "Content-Type: application/json" \
    -d "{\"warehouseId\":\"W1\",\"quantity\":50}"

* Abrufen des Lagerbestands in XML
    http://localhost:8080/central/stock.xml

* Abrufen des Lagerbestands in JSON
    http://localhost:8080/central/stock

* Versenden einer Nachricht
    http://localhost:8080/send?message=HalloSpencer

* Anlegen der Topics in Kafka (Optional hier zur demonstration)
    docker exec -it kafka bash
    [appuser@c113f19e1521 ~]$ kafka-topics --create --topic warehouse-input --bootstrap-server localhost:9092
    Created topic warehouse-input.
    ^[[C[appuser@c113f19e1521 ~]$ kafka-topics --create --topic warehouse-response --bootstrap-server localhost:9092
    Created topic warehouse-response.
    [appuser@c113f19e1521 ~]$ kafka-topics --list --bootstrap-server localhost:9092
    warehouse-input
    warehouse-response

## Links & Dokumente

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
