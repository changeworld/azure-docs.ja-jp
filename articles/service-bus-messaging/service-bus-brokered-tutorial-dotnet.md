<properties 
    pageTitle="Service Bus ブローカー メッセージング .NET チュートリアル | Microsoft Azure"
    description="ブローカー メッセージング .NET チュートリアル。"
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

# Service Bus ブローカー メッセージング .NET チュートリアル

Azure Service Bus では、2 種類の包括的メッセージング ソリューションが提供されています。1 つ目は、クラウドで実行する一元的な "リレー" サービスによるもので、SOAP、WS-*、REST など、広範なトランスポート プロトコルと Web サービスの標準をサポートします。クライアントは、オンプレミス サービスと直接接続する必要はありません。また、クライアントはオンプレミス サービスの場所を知っている必要はありません。オンプレミス サービス側では、ファイアウォールの着信ポートを開く必要がありません。

2 つ目のメッセージング ソリューションでは、"ブローカー" メッセージング機能を使用します。この機能は、非同期的または分離されたメッセージング機能と考えることができ、Service Bus メッセージング インフラストラクチャを使用する発行/サブスクライブ、一時的な切り離し、負荷分散のシナリオをサポートします。分離型通信には、クライアントとサーバーを必要に応じて接続し、非同期に操作を実行できるなど多数の利点があります。

このチュートリアルでは、Service Bus ブローカー メッセージングのコア コンポーネントの 1 つであるキューに関する概要と実践的使用例を説明します。このチュートリアルの一連のトピックでは、メッセージの一覧を設定し、キューを作成して、そのキューにメッセージを送信するアプリケーションを作成します。最後に、アプリケーションはキューからメッセージを受信して表示した後、リソースをクリーンアップして終了します。Service Bus Relay を使用するアプリケーションを構築する方法を説明する該当のチュートリアルについては、「[Service Bus リレー型メッセージングのチュートリアル](../service-bus-relay/service-bus-relay-tutorial.md)」を参照してください。

## 概要と前提条件

キューでは、コンシューマーが競合している場合のメッセージ配信に先入先出法 (FIFO) を使用します。FIFO では、通常、メッセージはキューに追加された順番に受信され、処理されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ 1 件につき 1 つだけです。キューを使用する主なメリットは、アプリケーション コンポーネントの*一時的な結合の解除*を実現することです。つまり、メッセージはキューに永続的に格納されるため、プロデューサーとコンシューマーは同時にメッセージを送受信する必要はありません。関連する利点として*負荷平準化*があります。これにより、プロデューサーとコンシューマーは異なるレートでメッセージを送受信できます。

このチュートリアルを始める前に済ましておく必要がある管理および準備の手順を次に示します。最初に、サービス名前空間を作成し、Shared Access Signature (SAS) キーを取得します。名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。サービス名前空間が作成された時点で、SAS キーが生成されます。サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

### サービス名前空間を作成し、SAS キーを取得する

最初の手順では、サービス名前空間を作成し、[Shared Access Signature](../service-bus/service-bus-sas-overview.md) (SAS) キーを取得します。名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。サービス名前空間が作成された時点で、SAS キーが生成されます。サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

次に、Visual Studio プロジェクトを作成し、メッセージのコンマ区切りリストを厳密に型指定された [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) オブジェクトに読み込む 2 つのヘルパー関数を作成します。

### Visual Studio プロジェクトを作成する

1. 管理者として Visual Studio を開きます。これには、[スタート] メニューの [Visual Studio] を右クリックし、**[管理者として実行]** をクリックします。

1. 新しいコンソール アプリケーション プロジェクトを作成します。**[ファイル]** メニューをクリックし、**[新規作成]** を選択して、**[プロジェクト]** をクリックします。**[新しいプロジェクト]** ダイアログで **[Visual C#]** をクリックします (**[Visual C#]** が表示されない場合は、**[他の言語]** からクリックします)。**[コンソール アプリケーション]** テンプレートをクリックし、「**QueueSample**」と名前を付けます。既定の **[場所]** を使用します。**[OK]** をクリックしてプロジェクトを作成します。

1. NuGet パッケージ マネージャーを使用して、Service Bus ライブラリをプロジェクトに追加します。
	1. ソリューション エクスプローラーで **QueueSample** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
	2. **[NuGet パッケージの管理]** ダイアログ ボックスで、**[参照]** タブをクリックします。"**Azure Service Bus**" を検索し、**[インストール]** をクリックします。<br />
1. ソリューション エクスプローラーで、Program.cs ファイルをダブルクリックして、Visual Studio エディターでそのファイルを開きます。名前空間の名前を、既定の名前である `QueueSample` から `Microsoft.ServiceBus.Samples` に変更します。

	```
	Microsoft.ServiceBus.Samples
	{
	    ...
	```

1. `using` ステートメントを次のコードで示すように変更します。

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	```

1. Data.csv という名前のテキスト ファイルを作成し、次のコンマ区切りテキストをコピーします。

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Data.csv ファイルを保存して閉じ、ファイルを保存した場所を覚えておきます。

1. ソリューション エクスプ ローラーで、プロジェクトの名前 (この例では **QueueSample**) を右クリックし、**[追加]** をクリックして、**[既存の項目]** をクリックします。

1. 手順 6 で作成した Data.csv ファイルの場所に移動します。ファイルをクリックし、**[追加]** をクリックします。ファイルの種類の一覧で **[すべてのファイル (*.*)]** が選択されていることを確認します。

### メッセージのリストを解析するメソッドを作成する

1. `Main()` メソッドの前の `Program` クラスで、2 つの変数を宣言します。1 つ目は **DataTable** 型で、Data.csv のメッセージのリストを格納します。もう 1 つは List 型オブジェクトで、[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) として厳密に型指定します。後者は、チュートリアルの後の手順で使用する仲介型メッセージのリストです。

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. `Main()` の外側で、`ParseCSV()` メソッドを定義します。このメソッドは、次に示すように、Data.csv のメッセージのリストを解析して、メッセージを [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) テーブルに読み込みます。このメソッドは、**DataTable** オブジェクトを返します。

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. `Main()` メソッドに、`ParseCSVFile()` メソッドを呼び出すステートメントを追加します。

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### メッセージのリストを読み込むメソッドを作成する

1. `Main()` の外側で、`GenerateMessages()` メソッドを定義します。このメソッドは、`ParseCSVFile()` によって返された **DataTable** オブジェクトを取得し、仲介型メッセージの厳密に型指定されたリストにテーブルを読み込みます。次の例で示すように、このメソッドは **List** オブジェクトを返します。

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. `Main()` で、`ParseCSVFile()` の呼び出しの直後に、`GenerateMessages()` メソッドを呼び出すステートメントを追加し、`ParseCSVFile()` からの戻り値を引数として渡します。

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### ユーザーの資格情報を取得する

1. 最初に、これらの値を保持するために、3 つのグローバル文字列変数を作成します。これらの変数は前の変数宣言の直後で宣言します。次に例を示します。

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList; 

	        // Add these variables
			private static string ServiceNamespace;
	        private static string sasKeyName = "RootManageSharedAccessKey";
	        private static string sasKeyValue;
	        …
	```

1. 次に、サービス名前空間と SAS キーを受け取って格納する関数を作成します。このメソッドを `Main()` の外側に追加します。次に例を示します。

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. `Main()` で、`GenerateMessages()` の呼び出しの直後に、`CollectUserInput()` メソッドを呼び出すステートメントを追加します。

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### ソリューションをビルドします。

Visual Studio の **[ビルド]** メニューで **[ソリューションのビルド]** をクリックするか、**Ctrl + Shift + B** キーを押して、ここまでの作業に問題がないことを確認します。

## 管理資格情報を作成する

この手順では、アプリケーションの承認に使用する Shared Access Signature (SAS) 資格情報を作成するために使用する管理操作を定義します。

1. わかりやすくするため、このチュートリアルではすべてのキュー操作を独立したメソッドに配置します。`Program` クラスで、`Main()` メソッドの後に非同期の `Queue()` メソッドを作成します。次に例を示します。
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static async Task Queue()
	{
	}
	```

1. 次の手順では、[TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) オブジェクトを使用して SAS 資格情報を作成します。作成メソッドは、`CollectUserInput()` メソッドで取得した SAS キーの名前と値を受け取ります。次のコードを `Queue()` メソッドに追加します。

	```
	static async Task Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```

2. 前の手順で取得した名前空間名と管理資格情報を含む URI を引数として使用して、新しい名前空間管理オブジェクトを作成します。前の手順で追加したコードの直後に、このコードを追加します。`<yourNamespace>` は実際のサービス名前空間の名前に置き換えてください。
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

### 例

この時点で、コードは次のようになります。

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## キューへのメッセージの送信

この手順では、キューを作成した後、仲介型メッセージのリストに含まれるメッセージをそのキューに送信します。

### キューを作成し、キューにメッセージを送信する

1. 最初に、キューを作成します。たとえば、`myQueue` という名前にして、1 つ前の手順の `Queue()` メソッドで追加した管理操作の直後で宣言します。

	```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. `Queue()` メソッドでは、新しく作成した Service Bus URI を引数として使用して、メッセージング ファクトリ オブジェクトを作成します。1 つ前の手順で追加した管理操作の直後に次のコードを追加します。`<yourNamespace>` は実際のサービス名前空間の名前に置き換えてください。

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

1. 次に、[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) クラスを使用してキュー オブジェクトを作成します。1 つ前の手順で追加したコードの直後に次のコードを追加します。

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. 次に、前に作成した仲介型メッセージのリストをループして各メッセージをキューに送信するコードを追加します。1 つ前の手順で追加した `CreateQueueClient()` ステートメントの直後に次のコードを追加します。
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    await myQueueClient.SendAsync(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## キューからメッセージを受け取る

この手順では、前の手順で作成したキューからメッセージのリストを取得します。

### レシーバーを作成し、キューからメッセージを受け取る

`Queue()` メソッドでは、キューを反復処理し、[QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) メソッドを使用してメッセージを受け取った後、各メッセージをコンソールに出力します。1 つ前の手順で追加したコードの直後に、次のコードを追加します。

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
	
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

`Thread.Sleep` は、メッセージ処理をシミュレートするためだけに使用されていることに注意してください。実際のメッセージング アプリケーションで使用することはないものと思われます。

### Queue メソッドを終了し、リソースをクリーンアップする

前のコードの直後に、メッセージ ファクトリおよびキュー リソースをクリーンアップする次のコードを追加します。

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### Queue メソッドを呼び出す

最後に、`Main()` から `Queue()` メソッドを呼び出すステートメントを追加します。Main() の最後に、次の強調表示されているコード行を追加します。
	
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
	
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
	
    // Add this call
    Queue();
}
```

### 例

次のコードには、完全な **QueueSample** アプリケーションが含まれています。

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## QueueSample アプリケーションをビルドして実行する

前の手順が完了したら、**QueueSample** アプリケーションをビルドして実行できます。

### QueueSample アプリケーションをビルドする

Visual Studio の **[ビルド]** メニューで **[ソリューションのビルド]** をクリックするか、**Ctrl + Shift + B** キーを押します。エラーが発生する場合は、前の手順の最後に示されている完全な例を基にして、コードが正しいことを確認してください。

## 次のステップ

このチュートリアルでは、Service Bus の ブローカー メッセージング機能を使用して、Service Bus クライアント アプリケーションとサービスを構築する方法を紹介しました。Service Bus [リレー](service-bus-messaging-overview.md#Relayed-messaging)を使用する類似のチュートリアルについては、「[Service Bus リレー型メッセージングのチュートリアル](../service-bus-relay/service-bus-relay-tutorial.md)」を参照してください。

[Service Bus](https://azure.microsoft.com/services/service-bus/) の詳細については、次のトピックを参照してください。

- [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
- [Service Bus の基礎](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus のアーキテクチャ](../service-bus/service-bus-architecture.md)

<!---HONumber=AcomDC_0928_2016-->