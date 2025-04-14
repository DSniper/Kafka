# Running  Latest Kakfa Without Zookeeper

I understand now! You want a more **step-by-step guide** that includes **when to open PowerShell**, **how to generate the GUID**, and the **exact sequence** to follow with proper instructions. Let me break it down in a **clear, easy-to-follow manner**. Here we go!

---

### **Complete Step-by-Step Guide to Setting Up Kafka (KRaft Mode) Locally**
---

### **1. Extract Kafka Files**
- **Step 1:** Download **Apache Kafka** (latest version, such as 2.13-4.0.0) from the official Kafka website.
- **Step 2:** Extract the **Kafka zip file** to a location on your machine. For example: `D:\kafka_2.13-4.0.0` .
---

### **2. Generate the GUID (for KRaft mode)**
Kafka needs a **unique identifier (GUID)** for its **storage formatting**.

#### **Step 1: Generate the GUID**
You can generate a GUID from PowerShell or any online GUID generator.

**In PowerShell**, you can generate a GUID by running the following command:

```powershell
[guid]::NewGuid()
```
It will return a GUID like this:

```
2a63efff-6d23-42a0-8766-4e5a40af2224
```
**Note:** Copy this GUID — you will need it for the next step.

---

### **3. Format Kafka Storage (with the GUID)**
**Step 1: Open PowerShell**
 Navigate to the **Kafka directory** where you extracted Kafka.

```powershell
cd D:\kafka_2.13-4.0.0
```
**Step 2: Run the Storage Format Command**
 Format the Kafka storage by running the following command. Replace `YOUR_GUID` with the GUID you generated earlier.

```powershell
cmd /c "bin\windows\kafka-storage.bat format -t 2a63efff-6d23-42a0-8766-4e5a40af2224 -c config\kraft\server.properties"
```
- The command will use the GUID to format Kafka’s storage.
If everything works fine, you should see something like:

```
2025-04-14T22:52:22.400320900Z main INFO Storage has been formatted.
```
---

### **4. Configure Kafka (**`**server.properties**`**)**
Now, you need to configure Kafka to run in **KRaft mode** (Kafka without Zookeeper).

#### **Step 1: Open **`**server.properties**` 
Navigate to the following file in the **Kafka directory**:

```text
D:\kafka_2.13-4.0.0\config\kraft\server.properties
```
#### **Step 2: Modify **`**server.properties**` 
Add or modify the configuration as shown below. This ensures Kafka runs in KRaft mode (with one broker and controller).

```properties
process.roles=broker,controller
node.id=1
controller.quorum.voters=1@localhost:9093
listeners=PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
advertised.listeners=PLAINTEXT://localhost:9092
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
inter.broker.listener.name=PLAINTEXT
controller.listener.names=CONTROLLER
log.dirs=./data/kafka-log
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
num.partitions=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
group.initial.rebalance.delay.ms=0
```
This ensures Kafka is set up for local usage with the correct network and partition configurations.

---

### **5. Start the Kafka Server**
Now that the Kafka server is properly configured, you can start it.

#### **Step 1: Open PowerShell**
Open **another PowerShell window** to run the Kafka server.

#### **Step 2: Start the Kafka Server**
Run this command to start the Kafka server:

```powershell
cmd /c "bin\windows\kafka-server-start.bat config\kraft\server.properties"
```
You should see logs starting to display, and Kafka will begin running on `localhost:9092`.

For example, you might see something like this:

```
[INFO] Kafka started successfully on localhost:9092.
```
---

### **6. Create a Kafka Topic**
Kafka uses **topics** to store messages. Let’s create a topic to test.

#### **Step 1: Open Another PowerShell Window**
In this new window, run the following command to create a Kafka topic.

```powershell
cmd /c "bin\windows\kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1"
```
You should see:

```
Created topic test-topic.
```
This creates a topic named `test-topic` for message communication.

---

### **7. Send Messages (Produce)**
Now that the topic is created, let’s send messages to Kafka.

#### **Step 1: Open Another PowerShell Window**
In this window, run the following command to start a Kafka producer.

```powershell
cmd /c "bin\windows\kafka-console-producer.bat --topic test-topic --bootstrap-server localhost:9092"
```
#### **Step 2: Produce Messages**
After running the above command, you’ll get a prompt where you can type messages. Press **Enter** after typing each message to send them.

Example:

```
Hello Kafka
This is a test message.
```
---

### **8. Receive Messages (Consume)**
To consume the messages you produced earlier, open another PowerShell window.

#### **Step 1: Open PowerShell**
In the new PowerShell window, run the following command to start a Kafka consumer:

```powershell
cmd /c "bin\windows\kafka-console-consumer.bat --topic test-topic --bootstrap-server localhost:9092 --from-beginning"
```
#### **Step 2: View Messages**
The consumer will start displaying the messages from the `test-topic`. You should see the messages you produced earlier, for example:

```
Hello Kafka
This is a test message.
```
---

### **9. Stop Kafka Server**
Once you’re done, **stop the Kafka server**.

#### **Step 1: Stop Kafka Server**
In the **PowerShell window** where Kafka is running, press `**Ctrl + C**` to stop it.

#### **Step 2: Manually Kill Kafka (if necessary)**
If Kafka doesn’t stop with `Ctrl + C`, you can forcefully kill the process:

- **Task Manager** (Windows): Open **Task Manager** (`Ctrl + Shift + Esc` ), find the **Java processes**, and **end the task**.
- **PowerShell Command**: Run the following command to stop the Java process running Kafka:
```powershell
Stop-Process -Name "java" -Force
```
---

### **Summary of Commands in Order**
1. **Format Kafka Storage**:
```powershell
cmd /c "bin\windows\kafka-storage.bat format -t 2a63efff-6d23-42a0-8766-4e5a40af2224 -c config\kraft\server.properties"
```
1. **Start Kafka Server**:
```powershell
cmd /c "bin\windows\kafka-server-start.bat config\kraft\server.properties"
```
1. **Create a Topic**:
```powershell
cmd /c "bin\windows\kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1"
```
1. **Start Kafka Producer**:
```powershell
cmd /c "bin\windows\kafka-console-producer.bat --topic test-topic --bootstrap-server localhost:9092"
```
1. **Start Kafka Consumer**:
```powershell
cmd /c "bin\windows\kafka-console-consumer.bat --topic test-topic --bootstrap-server localhost:9092 --from-beginning"
```
1. **Stop Kafka**:
Press `**Ctrl + C**` in the terminal where Kafka is running, or use **Task Manager** to kill the process.

---

Now you have a **clear, step-by-step guide** to set up Kafka locally, produce and consume messages, and stop Kafka properly.

Let me know if you have any further questions! 😊
Yours Dev DSniper

