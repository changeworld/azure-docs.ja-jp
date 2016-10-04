<properties 
    pageTitle="Service Bus ブローカー メッセージングの REST チュートリアル | Microsoft Azure"
    description="ブローカー メッセージングの REST チュートリアル。"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# Service Bus ブローカー メッセージングの REST チュートリアル

このチュートリアルでは、基本的な REST ベースの Azure Service Bus のキューおよびトピック/サブスクリプションを作成する方法を示します。

## 名前空間の作成

最初の手順では、サービス名前空間を作成し、[Shared Access Signature](../service-bus/service-bus-sas-overview.md) (SAS) キーを取得します。名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。サービス名前空間が作成された時点で、SAS キーが生成されます。サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## コンソール クライアントを作成する

Service Bus キューを使用すると、先入れ先出しキューにメッセージを保存することができます。トピックとサブスクリプションは、発行/サブスクライブ パターンを実装します。トピックを作成した後、そのトピックと関連付けて 1 つまたは複数のサブスクリプションを作成します。トピックに送信されたメッセージは、すぐにそのトピックのサブスクライバーに送信されます。

このチュートリアルのコードでは、次のことを行います。

- 名前空間と [Shared Access Signature](../service-bus/service-bus-sas-overview.md) (SAS) キーを使用して、Service Bus 名前空間のリソースにアクセスします。

- キューを作成し、キューにメッセージを送信して、キューからメッセージを読み取ります。

- トピックを作成し、そのトピックをサブスクライブした後、サブスクリプションにメッセージを送信して、サブスクリプションからメッセージを読み取ります。

- キュー、トピック、サブスクリプションのすべての情報 (サブスクリプション ルールなど) を Service Bus から取得します。

- キュー、トピック、サブスクリプションのリソースを削除します。

サービスは REST スタイルの Web サービスであるため、特別な型はなく、すべての交換は文字列で行われます。つまり、Visual Studio プロジェクトではどの Service Bus ライブラリも参照しないようにする必要があります。

最初の手順で名前空間と資格情報を取得した後、基本的な Visual Studio コンソール アプリケーションを作成します。

### コンソール アプリケーションの作成

1. 管理者として Visual Studio を開始します。そのためには、**[スタート]** メニューの [Visual Studio] を右クリックし、**[管理者として実行]** をクリックします。

1. 新しいコンソール アプリケーション プロジェクトを作成します。**[ファイル]** メニューから、**[新規作成]**、**[プロジェクト]** の順にクリックします。**[新しいプロジェクト]** ダイアログ ボックスで **[Visual C#]** をクリックします (**[Visual C#]** が表示されない場合は、**[他の言語]** からクリックします)。**[コンソール アプリケーション]** テンプレートを選択し、「**Microsoft.ServiceBus.Samples**」と名前を付けます。既定の [場所] を使用します。**[OK]** をクリックしてプロジェクトを作成します。

1. Program.cs で、`using` ステートメントが次のようになっていることを確認します。

	```
	using System;
	using System.Globalization;
	using System.IO;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Xml;
	```

1. 必要であれば、プログラムの名前空間を Visual Studio の既定の名前から `Microsoft.ServiceBus.Samples` に変更します。

1. `Program` クラスに、次のグローバル変数を追加します。
	
	```
	static string serviceNamespace;
	static string baseAddress;
	static string token;
	const string sbHostName = "servicebus.windows.net";
	```

1. `Main()` 内に、次のコードを貼り付けます。

	```
	Console.Write("Enter your service namespace: ");
	serviceNamespace = Console.ReadLine();
	
	Console.Write("Enter your SAS key: ");
	string SASKey = Console.ReadLine();
	
	baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
	try
	{
	    token = GetSASToken("RootManageSharedAccessKey", SASKey);
	
	    string queueName = "Queue" + Guid.NewGuid().ToString();
	
	    // Create and put a message in the queue
	    CreateQueue(queueName, token);
	    SendMessage(queueName, "msg1");
	    string msg = ReceiveAndDeleteMessage(queueName);
	
	    string topicName = "Topic" + Guid.NewGuid().ToString();
	    string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
	    CreateTopic(topicName);
	    CreateSubscription(topicName, subscriptionName);
	    SendMessage(topicName, "msg2");
	
	    Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
	
	    // Get an Atom feed with all the queues in the namespace
	    Console.WriteLine(GetResources("$Resources/Queues"));
	
	    // Get an Atom feed with all the topics in the namespace
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the subscriptions for the topic we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions"));
	
	    // Get an Atom feed with all the rules for the topic and subscription we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
	
	    // Delete the queue we created
	    DeleteResource(queueName);
	
	    // Delete the topic we created
	    DeleteResource(topicName);
	
	    // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Queues"));
	}
	catch (WebException we)
	{
	    using (HttpWebResponse response = we.Response as HttpWebResponse)
	    {
	        if (response != null)
	        {
	            Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
	        }
	        else
	        {
	            Console.WriteLine(we.ToString());
	        }
	    }
	}
	
	Console.WriteLine("\nPress ENTER to exit.");
	Console.ReadLine();
	```

## 管理資格情報を作成する

次の手順では、前の手順で入力した名前空間と SAS キーを処理して SAS トークンを返すメソッドを作成します。この例では、1 時間有効な SAS トークンを作成します。

### GetSASToken() メソッドを作成する

次のコードを `Program` クラスの `Main()` メソッドの後に貼り付けます。

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## キューを作成する

次の手順では、REST スタイルの HTTP PUT コマンドを使用してキューを作成するメソッドを作成します。

1 つ前の手順で追加した `GetSASToken()` のコードの直後に、次のコードを貼り付けます。

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## メッセージをキューに送信する

この手順では、REST スタイルの HTTP POST コマンドを使用して前の手順で作成したキューにメッセージを送信するメソッドを追加します。

1. 1 つ前の手順で追加した `CreateQueue()` のコードの直後に、次のコードを貼り付けます。

	```
	// Sends a message to the "queueName" queue, given the name and the value to enqueue
	// Uses an HTTP POST request.
	private static void SendMessage(string queueName, string body)
	{
	    string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
	    Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
	    WebClient webClient = new WebClient();
	    webClient.Headers[HttpRequestHeader.Authorization] = token;
	
	    webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
	}
	```

1. ブローカー メッセージの標準プロパティが `BrokerProperties` HTTP ヘッダーに配置されます。ブローカーのプロパティは、JSON 形式でシリアル化する必要があります。**TimeToLive** の値として 30 秒を指定し、メッセージ ラベル "M1" をメッセージに追加するには、前の例で示した `webClient.UploadData()` の呼び出しの直前に次のコードを追加します。

	```
	// Add brokered message properties "TimeToLive" and "Label"
	webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
	```

	ブローカー メッセージのプロパティが追加されていること、および今後追加されることに注意してください。したがって、送信要求では、要求の一部であるすべてのブローカー メッセージのプロパティをサポートする API バージョンを指定する必要があります。指定されている API のバージョンがブローカー メッセージのプロパティをサポートしていない場合は、そのプロパティは無視されます。

1. カスタム メッセージのプロパティは、キーと値のペアのセットとして定義されます。各カスタム プロパティは、独自の TPPT ヘッダーに格納されます。カスタム プロパティ "Priority" と "Customer" を追加するには、前の例で示した `webClient.UploadData()` の呼び出しの直前に次のコードを追加します。

	```
	// Add custom properties "Priority" and "Customer".
	webClient.Headers.Add("Priority", "High");
	webClient.Headers.Add("Customer", "12345");
	```

## キューからメッセージを受信して削除する

次の手順では、REST スタイルの HTTP DELETE コマンドを使用してキューからメッセージを受信し削除するメソッドを追加します。

1 つ前の手順で追加した `SendMessage()` のコードの直後に、次のコードを貼り付けます。

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## トピックとサブスクリプションを作成する

次の手順では、REST スタイルの HTTP PUT コマンドを使用してトピックを作成するメソッドを作成します。その後、そのトピックに対するサブスクリプションを作成するメソッドを作成します。

### トピックを作成する

1 つ前の手順で追加した `ReceiveAndDeleteMessage()` のコードの直後に、次のコードを貼り付けます。

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### サブスクリプションの作成

次のコードでは、前の手順で作成したトピックへのサブスクリプションを作成します。`CreateTopic()` の定義の直後に次のコードを追加します。

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## メッセージ リソースを取得する

この手順では、メッセージのプロパティを取得した後、前の手順で作成したメッセージング リソースを削除するコードを追加します。

### 指定されたリソースで Atom フィードを取得する

1 つ前の手順で追加した `CreateSubscription()` メソッドの直後に、次のコードを追加します。

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### メッセージング エンティティを削除する

1 つ前の手順で追加したコードの直後に、次のコードを追加します。

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### Atom フィードの書式を設定する

`GetResources()` メソッドの中で呼び出されている `FormatXml()` メソッドは、取得された Atom フィードの書式をさらに読みやすいように変更します。次に示すのは `FormatXml()` の定義です。前のセクションで追加した `DeleteResource()` のコードの直後に、このコードを追加します。

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## アプリケーションの構築と実行

アプリケーションをビルドして実行できる状態になりました。Visual Studio の **[ビルド]** メニューで **[ソリューションのビルド]** をクリックするか、**Ctrl + Shift + B** キーを押します。

### アプリケーションの実行

エラーがない場合は、F5 キーを押してアプリケーションを実行します。メッセージが表示されたら、最初の手順で取得した名前空間、SAS キーの名前、SAS キーの値を入力します。

### 例

次の例は、このチュートリアルのすべての手順を実行した後の、完全なコードです。

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## 次のステップ

詳細については、次の記事を参照してください。

- [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
- [Service Bus の基礎](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus リレー型メッセージングの REST チュートリアル](../service-bus-relay/service-bus-relay-rest-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->