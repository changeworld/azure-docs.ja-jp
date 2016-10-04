## Java の EventProcessorHost を使用したメッセージの受信

EventProcessorHost は、永続的なチェックポイントと、Event Hubsからの並列受信を管理することで、Event Hubs のイベントの受信を簡素化する Java クラスです。EventProcessorHost を使用すると、異なる複数のノードでホストされている場合でも、複数の受信側の間でイベントを分割することができます。この例では、受信側が単一の場合に EventProcessorHost を使用する方法を示します。

###ストレージ アカウントの作成

EventProcessorHost を使用するには [Azure ストレージ アカウント][]が必要です。

1. [Azure クラシック ポータル][]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[Data Services]**、**[Storage]**、**[簡易作成]** の順にクリックし、ストレージ アカウントの名前を入力します。目的のリージョンを選択し、**[ストレージ アカウントの作成]** をクリックします。

    ![][11]

3. 新しく作成したストレージ アカウントをクリックし、**[アクセス キーの管理]** をクリックします。

    ![][12]

    このチュートリアルの後で使用するため、プライマリ アクセス キーをメモしておきます。

###EventProcessor ホストを使用した Java プロジェクトの作成

Event Hubs の Java クライアント ライブラリは、[Maven Central Repository][Maven Package] の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>{VERSION}</version>
</dependency>
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs-eph</artifactId>
	<version>{VERSION}</version>
</dependency>
```
 
ビルド環境の種類に応じて、[Maven Central Repository][Maven Package] または [GitHub のリリース配布ポイント](https://github.com/Azure/azure-event-hubs/releases)から最新リリースの JAR ファイルを明示的に取得できます。

1. 次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。このクラスを ```ErrorNotificationHandler``` と呼びます。

	``` Java
	import java.util.function.Consumer;
	import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

	public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
	{
		@Override
		public void accept(ExceptionReceivedEventArgs t)
		{
			System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
		}
	}
	```

2. 次のコードで ```EventProcessor``` という新しいクラスを作成します。

	```Java
	import com.microsoft.azure.eventhubs.EventData;
	import com.microsoft.azure.eventprocessorhost.CloseReason;
	import com.microsoft.azure.eventprocessorhost.IEventProcessor;
	import com.microsoft.azure.eventprocessorhost.PartitionContext;

	public class EventProcessor implements IEventProcessor
	{
		private int checkpointBatchingCount = 0;

		@Override
		public void onOpen(PartitionContext context) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
		}

		@Override
		public void onClose(PartitionContext context, CloseReason reason) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
		}
		
		@Override
		public void onError(PartitionContext context, Throwable error)
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
		}

		@Override
		public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
			int messageCount = 0;
			for (EventData data : messages)
			{
				System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
						data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
				messageCount++;
				
				this.checkpointBatchingCount++;
				if ((checkpointBatchingCount % 5) == 0)
				{
					System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
						data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
					context.checkpoint(data);
				}
			}
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
		}
	}
	```

3. 次のコードで ```EventProcessorSample``` という最後のクラスを作成します。

	```Java
	import com.microsoft.azure.eventprocessorhost.*;
	import com.microsoft.azure.servicebus.ConnectionStringBuilder;
	import com.microsoft.azure.eventhubs.EventData;

	public class EventProcessorSample
	{
		public static void main(String args[])
		{
			final String consumerGroupName = "$Default";
			final String namespaceName = "----ServiceBusNamespaceName-----";
			final String eventHubName = "----EventHubName-----";
			final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
			final String sasKey = "---SharedAccessSignatureKey----";

			final String storageAccountName = "---StorageAccountName----";
			final String storageAccountKey = "---StorageAccountKey----";
			final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
			
			ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
			
			EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
			
			System.out.println("Registering host named " + host.getHostName());
			EventProcessorOptions options = new EventProcessorOptions();
			options.setExceptionNotification(new ErrorNotificationHandler());
			try
			{
				host.registerEventProcessor(EventProcessor.class, options).get();
			}
			catch (Exception e)
			{
				System.out.print("Failure while registering: ");
				if (e instanceof ExecutionException)
				{
					Throwable inner = e.getCause();
					System.out.println(inner.toString());
				}
				else
				{
					System.out.println(e.toString());
				}
			}

			System.out.println("Press enter to stop");
			try
			{
				System.in.read();
				host.unregisterEventProcessor();
				
				System.out.println("Calling forceExecutorShutdown");
				EventProcessorHost.forceExecutorShutdown(120);
			}
			catch(Exception e)
			{
				System.out.println(e.toString());
				e.printStackTrace();
			}
			
			System.out.println("End of sample");
		}
	}
	```

4. 次のフィールドを、イベント ハブとストレージ アカウントの作成時に使用した値に置き換えます。

	``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";

	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";

	final String storageAccountName = "---StorageAccountName----"
	final String storageAccountKey = "---StorageAccountKey----";
	```

> [AZURE.NOTE] このチュートリアルでは、EventProcessorHost の単一のインスタンスを使用します。スループットを向上させるには、EventProcessorHost の複数のインスタンスを実行することをお勧めします。このような場合、受信したイベントの負荷を分散するために、さまざまなインスタンスが自動的に連携します。複数の受信側でぞれぞれ*すべて*のイベントを処理する場合、**ConsumerGroup** 概念を使用する必要があります。さまざまなコンピューターからイベントを受信する場合、デプロイしたコンピューター (またはロール) に基づいて EventProcessorHost インスタンスの名前を指定するのに便利です。

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[Azure ストレージ アカウント]: ../storage/storage-create-storage-account.md
[Azure クラシック ポータル]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

<!---HONumber=AcomDC_0928_2016-->