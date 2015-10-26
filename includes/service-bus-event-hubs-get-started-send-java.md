## Event Hub へのメッセージ送信
このセクションでは、イベントをイベント ハブに送信する Java コンソール アプリを作成します。[Apache Qpid プロジェクト](http://qpid.apache.org/)の JMS AMQP プロバイダーを使用します。これは、[ここ](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md)に示すように、Java による AMQP を利用した Service Bus キューとトピックの使用に似ています。詳細については、[Qpid JMS のドキュメント](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html)と [Java メッセージング サービス](http://www.oracle.com/technetwork/java/jms/index.html)に関するページをご覧ください。

1. Eclipse で [Azure Toolkit for Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx) をインストールします。これには、Qpid JMS AMQP クライアント ライブラリが含まれます。

2. Eclipse で、**Sender** という名前の新しい Java プロジェクトを作成します。

3. Eclipse Package Explorer で、**Sender** プロジェクトを右クリックし、**[Properties]** を選択します。ダイアログの左側のウィンドウで、**[Java Build Path]**、**[Libraries]** タブ、**[Add Library]** の順にクリックします。**[Package for Apache Qpid Client Libraries for JMS (by MS Open Tech)]** を選択し、**[Next]**、**[Finish]** の順にクリックします。

	![][8]

4. 次の内容で、**servicebus.properties** という名前のファイルを **Sender** プロジェクトのルートに作成します。忘れずに次の値を代入してください。
	- イベント ハブ名。
	- 名前空間名 (末尾は通常 `{event hub name}-ns`)。
	- URL にエンコードされた **SendRule** キー (イベント ハブの作成時にこのキーを書き留めました)。[ここ](http://www.w3schools.com/tags/ref_urlencode.asp)で URL でエンコードすることができます。

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. **Sender** という名前の新しいクラスを作成します。次の `import` ステートメントを追加します。

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. これに次のコードを追加します。

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=Oct15_HO3-->