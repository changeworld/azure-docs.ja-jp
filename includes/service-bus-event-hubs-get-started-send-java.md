## Event Hub へのメッセージ送信
このセクションでは、Java アプリを記述して、イベントを Event Hub に送信します。[Apache Qpid プロジェクト](http://qpid.apache.org/)の JMS AMQP プロバイダーを使用します。これは、[こちら](../articles/service-bus-java-how-to-use-jms-api-amqp.md)に示すように、Java 経由の AMQP を Service Bus キューと Topics と使用するのに似ています。をご覧ください。詳細については、「[Qpid JMS のドキュメント](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html)」と「[Java メッセージング サービス](http://www.oracle.com/technetwork/java/jms/index.html)」をご覧ください。

1. Eclipse で、**Sender** という名前の新しい Java プロジェクトを作成します。

2. **Qpid JMS AMQP 1.0** ライブラリの最新リリースを[こちら](http://qpid.apache.org/components/qpid-jms/index.html)からダウンロードします。

3. アーカイブからファイルを抽出し、アーカイブの  `qpid-amqp-1-0-client-jms<version>\lib` ディレクトリから Eclipse **Sender** プロジェクトに次の jar ファイルをコピーします。

4. Eclipse Package Explorer で、**Sender** プロジェクトを右クリックし、**[Properties]** を選択します。ダイアログの左側のウィンドウで、**[Java Build Path]**、**[Libraries]** タブ、**[Add JARs]** の順にクリックします。前にコピーした jar ファイルを選択してから **[OK]** をクリックします。

	![][8]

5. 次の内容で、**servicebus.properties** という名前のファイルを **Sender** プロジェクトのルートに作成します。Event Hub の名前と名前空間の名前の値を置き換えます (通常、後者は  `{event hub name}-ns` です)。前に作成した **SendRule** のキーの URL でエンコードされたバージョンも代入する必要があります。[こちら](http://www.w3schools.com/tags/ref_urlencode.asp)で URL でエンコードできます。

		# servicebus.properties - sample JNDI configuration

		# Register a ConnectionFactory in JNDI using the form:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Register some queues in JNDI using the form
		# queue.[jndi_name] = [physical_name]
		# topic.[jndi_name] = [physical_name]
		queue.EventHub = {event hub name}

5. **Sender** という名前の新しいクラスを作成します。次の  `import` ステートメントを追加します。

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

8. これに次のコードを追加します。

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// JNDI 環境を構成
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// 接続を作成
			Connection connection = cf.createConnection();
	
			// 送信側セッションと MessageProducer を作成
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
[Azure 管理ポータル]: https://manage.windowsazure.com/


<!-- Images -->
[8]:./media/service-bus-event-hubs-getstarted/create-sender-java1.png
<!--HONumber=52--> 
