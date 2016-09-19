## Event Hub へのメッセージ送信

Event Hubs の Java クライアント ライブラリは、[Maven セントラル リポジトリ](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>{VERSION}</version>
</dependency>
```
 
ビルド環境の種類に応じて、[Maven セントラル リポジトリ](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)または [GitHub のリリース配布ポイント](https://github.com/Azure/azure-event-hubs/releases)から最新リリースの JAR ファイルを明示的に取得できます。

単純なイベント パブリッシャーの場合は、Event Hubs クライアント クラス用の *com.microsoft.azure.eventhubs* パッケージと Azure Service Bus メッセージング クライアントと共有される一般的な例外などのユーティリティ クラス用の *com.microsoft.azure.servicebus* パッケージをインポートします。

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。このクラスを ```Send``` と呼びます。

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

名前空間と Event Hub の名前を、Event Hub の作成時に使用した値に置き換えます。

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

次に、文字列を UTF-8 バイト エンコードに変換して単数のイベントを作成します。その後、接続文字列から新しい Event Hubs クライアント インスタンスを作成し、メッセージを送信します。

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0907_2016-->