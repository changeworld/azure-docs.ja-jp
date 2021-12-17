---
title: Azure Service Bus を使用する .NET 多層アプリケーション | Microsoft Docs
description: Service Bus キューを使用して層間で通信する多層アプリケーションを Azure で開発するのに役立つ .NET チュートリアルです。
ms.devlang: dotnet
ms.topic: article
ms.date: 04/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 56ee5afdb907f660079fd1ac853ccbd80258b30b
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416598"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Azure Service Bus キューを使用する .NET 多層アプリケーション

Microsoft Azure 向けアプリケーションは、Visual Studio および無料の Azure SDK for .NET を使用して簡単に開発できます。 このチュートリアルでは、ローカル環境で実行されている複数の Azure リソースを使用するアプリケーションを作成する手順について説明します。

学習内容は次のとおりです。

* 1 回のダウンロードとインストールで、Azure 開発用に コンピューターを準備する方法
* Azure 開発のための Visual Studio の使用方法
* Web ロールおよび Worker ロールを使用して Azure に 多層アプリケーションを作成する方法
* Service Bus キューを使用して各層間で通信する方法

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

このチュートリアルでは、Azure のクラウド サービスで多層アプリケーションを構築して実行します。 フロントエンドは ASP.NET MVC Web ロール、バックエンドは Service Bus キューを使用する worker ロールです。 同じ多層アプリケーションを作成するときに、クラウド サービスではなく Azure Web サイトにデプロイされる Web プロジェクトとしてフロントエンドを実装することもできます。 また、[.NET オンプレミス/クラウド ハイブリッド アプリケーション](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md)のチュートリアルを試すこともできます。

次に示すのは完成したアプリケーションのスクリーンショットです。

:::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png" alt-text="アプリケーションの送信ページ":::

## <a name="scenario-overview-inter-role-communication"></a>シナリオの概要: ロール間通信
処理を要求するため、Web ロールで実行されているフロントエンド UI コンポーネントは、worker ロールで実行されている中間層ロジックと対話する必要があります。 この例では、各層間での通信に Service Bus メッセージングが使用されています。

Web 層と中間層との間で Service Bus メッセージングを使用すると、2 つのコンポーネントの結合が解除されます。 直接メッセージング (TCP や HTTP) とは異なり、Web 層は中間層に直接接続しません。その代わりに、作業をメッセージとして Service Bus にプッシュします。Service Bus は、このメッセージを中間層が受け取り、処理する用意ができるまで確実に保持します。

Service Bus には、ブローカー メッセージングをサポートする、キューとトピックという 2 つのエンティティがあります。 キューでは、各メッセージは単一のレシーバーが使用するキューに送信されます。 トピックは発行/サブスクライブ パターンをサポートし、発行された各メッセージをトピックに登録されているサブスクリプションで使用します。 各サブスクリプションは、独自のメッセージ キューを論理的に管理しています。 また、サブスクリプションにフィルター ルールを構成し、サブスクリプション キューに渡すメッセージをフィルターに一致するメッセージのみに制限できます。 次の例では、Service Bus キューを使用します。

:::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png" alt-text="Web ロール、Service Bus、および Worker ロール間の通信を示す図。":::

この通信メカニズムには、直接メッセージングと比較した場合に、次のような利点があります。

* **一時的な結合の解除。** 非同期メッセージング パターンでは、プロデューサーと コンシューマーが同時にオンラインになっている必要はありません。 コンシューマーが メッセージを受け取る用意ができるまで、Service Bus が確実に メッセージを保持します。 このため、保守などのために、分散型アプリケーションの各コンポーネントの接続を自主的に解除できます。また、コンポーネントのクラッシュによって接続が解除されても問題なく、接続解除の際にシステム全体に影響が及ぶことがありません。 さらに、コンシューマー側アプリケーションがオンラインになっている時間は、1 日のうち一定の時間だけで済みます。
* **負荷平準化。** 多くのアプリケーションでは、システム負荷が時間の経過とともに変化しますが、各作業単位に必要な処理時間は通常一定に保たれます。 メッセージ プロデューサーとメッセージ コンシューマーの間をキューで仲介することは、コンシューマー側アプリケーション (worker) はピーク時ではなく平均時の負荷に対応できるようにプロビジョニングすればいいということを意味します。 キューの深さは、受信の負荷の変化に応じて増減します。 このため、アプリケーション負荷への対応に必要なインフラストラクチャの観点から直接費用を節約できます。
* **負荷分散。** 負荷の増大に合わせて、キューからの読み取りのために worker プロセスを追加できます。 各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。 また、このプルベースの負荷分散では、各 worker マシンがそれぞれ独自の最大レートでメッセージをプルするため、worker マシンの処理能力が異なる場合であっても最適に使用できます。 このパターンは、しばしば *競合コンシューマー* のパターンと呼ばれます。
  
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png" alt-text="Web ロール、Service Bus、および 2 つの Worker ロール間の通信を示す図。":::
  
以降のセクションでは、このアーキテクチャを実装するコードについて説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Azure Active Directory (Azure AD) 認証を使用して `ServiceBusClient` と `ServiceBusAdministrationClient` のオブジェクトを作成します。 また `DefaultAzureCredential` を使用してこれを使用し、次の手順を実行して、アプリケーションを開発環境でローカルでテストする必要があります。

1. [アプリケーションを Azure AD に登録します。](../active-directory/develop/quickstart-register-app.md)
1. [アプリケーションを`Service Bus Data Owner`ロール](service-bus-managed-service-identity.md#to-assign-azure-roles-using-the-azure-portal)に追加します。
1. `AZURE-CLIENT-ID`、`AZURE-TENANT-ID` および `AZURE-CLIENT-SECRET` の環境変数を設定します。 手順については、[この記事](/dotnet/api/overview/azure/identity-readme#environment-variables)を参照してください。


## <a name="create-a-namespace"></a>名前空間の作成

最初の手順では、*名前空間* を作成して、その [Shared Access Signature (SAS)](service-bus-sas.md) キーを取得します。 名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。 名前空間が作成された時点で、システムによって SAS キーが自動的に生成されます。 名前空間名と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Web ロールを作成する

このセクションでは、アプリケーションのフロントエンドを作成します。 最初に、アプリケーションで表示するページを作成します。
その後、Service Bus キューに項目を送信し、キューに関するステータス情報を表示するコードを追加します。

### <a name="create-the-project"></a>プロジェクトを作成する

1. 管理者特権で Visual Studio を起動します。**Visual Studio** のプログラム アイコンを右クリックし、**[管理者として実行]** をクリックしてください。 Azure Compute Emulator (後ほどこの記事で解説) を使用するには、管理者特権で Visual Studio を開始する必要があります。
   
   Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。
2. **[テンプレート]** ページで、次の手順を行います。
    1. プログラミング言語に **C#** を選択します。
    1. プロジェクトの種類に **クラウド** を選択します。
    1. **Azure Cloud Service** を選択します。
    1. **[次へ]** を選択します。 
   
        :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png" alt-text="[新しいプロジェクト] ダイアログ ボックスのスクリーンショット。クラウドが選択されており、Azure Cloud Services Visual C# が強調表示され、赤の枠線で囲まれています。":::
3.  プロジェクトに **MultiTierApp** という名前を付け、プロジェクトの場所を選択して、 **[作成]** を選択します。

    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/project-name.png" alt-text="プロジェクト名を指定します。":::    
1. **[ロール]** ページで、**ASP.NET Web ロール** をダブルクリックし、 **[OK]** を選択します。 
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png" alt-text="Web ロールを選択する":::
4. **[Azure のクラウド サービス ソリューション]** の **[WebRole1]** をポイントし、鉛筆のアイコンをクリックして、Web ロールの名前を "**FrontendWebRole**" に変更します。 次に、 **[OK]** をクリックします (「FrontEnd」ではなく「Frontend」と入力してください。小文字の "e" です)。
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png" alt-text="[新しい Microsoft Azure クラウド サービス] ダイアログ ボックスのスクリーンショット。ソリューションの名前が [FrontendWebRole] に変更されています。":::
5. **[Create a new ASP.NET Web Application]** (\新しい ASP.NET Web アプリケーションの作成\) ダイアログ ボックスで、 **[MVC]** を選択し、 **[作成]** を選択します。
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png" alt-text="[New ASP.NET Project]\(新しい ASP.NET プロジェクト\) ダイアログ ボックスのスクリーンショット。[MVC] が強調表示され、[認証の変更] オプションとともに赤の枠線で囲まれています。":::
8. **ソリューション エクスプローラー** で **FrontendWebRole** プロジェクトの **[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
9. **[参照]** タブをクリックして、**Azure.Messaging.ServiceBus** を検索します。 **Azure.Messaging.ServiceBus** パッケージを選択し、 **[インストール]** を選択して使用条件に同意します。
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png" alt-text="[NuGet パッケージの管理] ダイアログ ボックスのスクリーンショット。Azure.Messaging.ServiceBus が強調表示され、[インストール] オプションが赤の枠線で囲まれています。":::

   これで、必要なクライアント アセンブリを参照できるようになり、新しいコード ファイルがいくつか追加されました。
10. 同じ手順に従って、`Azure.Identity` NuGet パッケージをプロジェクトに追加します。  
10. **ソリューション エクスプローラー** で **FronendWebRole** を展開し、 **[モデル]** を右クリックして **[追加]** 、 **[クラス]** の順にクリックします。 **[名前]** ボックスに「**OnlineOrder.cs**」と入力します。 **[追加]** をクリックします。

### <a name="write-the-code-for-your-web-role"></a>Web ロール用のコードの作成
このセクションでは、アプリケーションで表示するさまざまなページを作成します。

1. Visual Studio で、OnlineOrder.cs ファイルの既存の名前空間定義を、次のコードに置き換えます。
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. **ソリューション エクスプローラー** で、**[Controllers\HomeController.cs]** をダブルクリックします。 次の **using** ステートメントを名前空間を含めるファイルの先頭に追加し、Service Bus と同様に先ほど作成したモデルの名前空間を追加します。
   
   ```csharp
    using FrontendWebRole.Models;
    using Azure.Messaging.ServiceBus;    
   ```
3. また、Visual Studio の HomeController.cs ファイルで既存の名前空間の定義を次のコードに置き換えます。 このコードには、キューへの項目の送信を処理するメソッドが含まれています。
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックして、ここまでの作業に問題がないことを確認します。
5. 次に、前の手順で作成した `Submit()` メソッドのビューを作成します。 **HomeController.cs** ファイルの `Submit()` メソッド (パラメーターを受け取らない `Submit()` のオーバーロード) 内で右クリックし、 **[ビューの追加]** を選択します。
6. **[Add New Scaffolded Item]** \(新しいスキャフォールディング項目の追加\) ダイアログ ボックスで、 **[追加]** を選びます。 
1. **[ビューの追加]** ダイアログ ボックスで、次の手順を行います。
    1. **[テンプレート]** ボックスの一覧から **[作成]** を選択します。 
    1. **[モデル クラス]** ボックスの一覧で **OnlineOrder** クラスを選択します。
    1. **[追加]** を選択します。 
   
        :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png" alt-text="[ビューの追加] ダイアログ ボックスのスクリーンショット。[テンプレート:] と [モデル クラス:] の各ドロップダウンリストが赤の枠線で囲まれています。":::
8. 次に、アプリケーションの表示名を変更します。 **ソリューション エクスプローラー** で、**Views\Shared\\_Layout.cshtml** ファイルをダブルクリックして Visual Studio エディターで開きます。
9. **My ASP.NET Application** となっている箇所をすべて **Northwind Traders Products** に置き換えます。
10. **Home**、**About**、および **Contact** の各リンクを削除します。 以下の強調表示されたコードを削除してください。
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png" alt-text="コードのスクリーンショット。3 行の HTML Action Link コードが強調表示されています。":::
11. 最後に、キューに関する情報を表示できるように、送信ページを変更します。 **ソリューション エクスプローラー** で、**Views\Home\Submit.cshtml** ファイルをダブルクリックし、Visual Studio エディターで開きます。 `<h2>Submit</h2>` という行の下に、次の行を追加します。 この時点では、`ViewBag.MessageCount` は空の状態です。 この値は後で入力します。
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. これで、UI の実装が終わりました。 **F5** キーを押してアプリケーションを実行し、期待どおりに表示されることを確認します。
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png" alt-text="アプリケーションの [送信] ページのスクリーンショット。":::

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Service Bus キューに項目を送信するためのコードの作成
次に、項目をキューに送信するためのコードを追加します。 最初に、Service Bus キューの接続情報を含むクラスを作成します。 次に、Global.aspx.cs から接続を初期化します。 最後に、Service Bus キューに実際に項目を送信するために、HomeController.cs 内に作成してある送信用のコードを更新します。

1. **ソリューション エクスプローラー** で、**FrontendWebRole** を右クリックします (ロールではなくプロジェクトを右クリック)。 **[追加]**、**[クラス]** の順にクリックします。
2. クラスに **QueueConnector.cs** という名前を付けます。 **[追加]** をクリックしてクラスを作成します。
3. 接続情報をカプセル化して、Service Bus のキューへの接続を初期化するコードを追加します。 QueueConnector.cs の内容全体を次のコードに置き換え、`your Service Bus namespace` の値 (名前空間の名前) と `yourKey` の値 (Azure Portal から取得した **プライマリ キー**) を入力します。
   
   ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    using Azure.Messaging.ServiceBus.Administration;
       
   namespace FrontendWebRole
   {
        public static class QueueConnector
        {
            // object to send messages to a Service Bus queue
            internal static ServiceBusSender SBSender;
    
            // object to create a queue and get runtime properties (like message count) of queue
            internal static ServiceBusAdministrationClient SBAdminClient;
        
            // Fully qualified Service Bus namespace
            private const string FullyQualifiedNamespace = "<SERVICE BUS NAMESPACE NAME>.servicebus.windows.net";
            
            // The name of your queue.
            internal const string QueueName = "OrdersQueue";
        
            public static async Task Initialize()
            {
                // Create a Service Bus client that you can use to send or receive messages
                ServiceBusClient SBClient = new ServiceBusClient(FullyQualifiedNamespace, new DefaultAzureCredential());
        
                // Create a Service Bus admin client to create queue if it doesn't exist or to get message count
                SBAdminClient = new ServiceBusAdministrationClient(FullyQualifiedNamespace, new DefaultAzureCredential());
        
                // create the OrdersQueue if it doesn't exist already
                if (!(await SBAdminClient.QueueExistsAsync(QueueName)))
                {
                    await SBAdminClient.CreateQueueAsync(QueueName);
                }
        
                // create a sender for the queue 
                SBSender = SBClient.CreateSender(QueueName);    
            }
        }    
   }
   ```
4. 次に、**Initialize** メソッドが呼び出されるようにします。 **ソリューション エクスプローラー** で、**Global.asax\Global.asax.cs** をダブルクリックします。
5. **Application_Start** メソッドの最後に次のコード行を追加します。
   
   ```csharp
    FrontendWebRole.QueueConnector.Initialize().Wait();
   ```
6. 最後に、前の手順で作成した Web コードを更新し、項目をキューに送信します。 **ソリューション エクスプローラー** で、**[Controllers\HomeController.cs]** をダブルクリックします。
7. キューのメッセージ数を取得するために、`Submit()` メソッド (パラメーターを受け取らないオーバーロード) を次のように更新します。
   
   ```csharp
        public ActionResult Submit()
        {
            QueueRuntimeProperties properties = QueueConnector.adminClient.GetQueueRuntimePropertiesAsync(QueueConnector.queueName).Result;
            ViewBag.MessageCount = properties.ActiveMessageCount;

            return View();
        }
   ```
8. キューに注文情報を送信するために、`Submit(OnlineOrder order)` メソッド (パラメーターを 1 つ受け取るオーバーロード) を次のように更新します。
   
   ```csharp
        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // create a message 
                var message = new ServiceBusMessage(new BinaryData(order));

                // send the message to the queue
                QueueConnector.sbSender.SendMessageAsync(message);

                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }
   ```
9. ここで再び、アプリケーションを実行します。 注文を送信するたびに、メッセージ数が増えていきます。
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png" alt-text="アプリケーションの [送信] ページのスクリーンショット。メッセージ数が 1 に増えています。":::

## <a name="create-the-worker-role"></a>worker ロールを作成する
次に、送信された注文を処理する worker ロールを作成します。 この例では、Visual Studio プロジェクト テンプレートの **Worker Role with Service Bus Queue** を使用します。 必要な資格情報は、既にポータルから取得しています。

1. Visual Studio を Azure アカウントに接続していることを確認します。
2. Visual Studio の **ソリューション エクスプローラー** で、**MultiTierApp** プロジェクト内の **Roles** フォルダーを右クリックします。
3. [**追加**] をクリックし、[**新しいワーカー ロール プロジェクト**] をクリックします。 **[新しいロール プロジェクトの追加]** ダイアログ ボックスが表示されます。

   :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png" alt-text="ソリューション エクスプローラー ウィンドウのスクリーンショット。[新しいワーカー ロール プロジェクト...] オプションと [追加] オプションが強調表示されています。":::
1. **[新しいロール プロジェクトの追加]** ダイアログ ボックスで、 **[Worker ロール]** を選択します。 レガシ Service Bus SDK を使用するコードが生成されるため、**Service Bus キューを持つ Worker ロール** を選択しないでください。 
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png" alt-text="[新しいロール プロジェクトの追加] ダイアログ ボックスのスクリーンショット。[Worker Role with Service Bus Queue]\(Service Bus キューを使用する Worker ロール\) オプションが強調表示され、赤の枠線で囲まれています。":::
5. [**名前**] ボックスで、プロジェクトに **OrderProcessingRole** という名前を付けます。 **[追加]** をクリックします。
1. **ソリューション エクスプローラー** で **OrderProcessingRole** プロジェクトを右クリックし、 **[NuGet パッケージの管理...]** を選びます。
9. **[参照]** タブをクリックして、**Azure.Messaging.ServiceBus** を選択します。 **Azure.Messaging.ServiceBus** パッケージを選択し、 **[インストール]** を選択して使用条件に同意します。
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png" alt-text="[NuGet パッケージの管理] ダイアログ ボックスのスクリーンショット。Azure.Messaging.ServiceBus が強調表示され、[インストール] オプションが赤の枠線で囲まれています。":::
1. 同じ手順に従って、`Azure.Identity` NuGet パッケージをプロジェクトに追加します。  
1. キューからの注文を処理するときの注文を表す **OnlineOrder** クラスを作成します。 作成済みのクラスを再利用できます。 **ソリューション エクスプローラー** で、**OrderProcessingRole** クラスを右クリックします (ロールではなく、クラスを右クリック)。 [**追加**] をクリックして、[**既存の項目**] をクリックします。
1. **FrontendWebRole\Models** のサブフォルダーに移動し、**OnlineOrder.cs** をダブルクリックしてこのプロジェクトに追加します。
1. **OrderProcessingRole** プロジェクトの **WorkerRole.cs** ファイルに次の `using` ステートメントを追加します。 

    ```csharp
    using FrontendWebRole.Models;
    using Azure.Messaging.ServiceBus;
    using Azure.Messaging.ServiceBus.Administration; 
    ```    
1. **WorkerRole.cs** で、次のプロパティを追加します。 

    > [!IMPORTANT]
    > 前提条件の一部として説明した名前空間の接続文字列を使用します。 

    ```csharp
        // Fully qualified Service Bus namespace
        private const string FullyQualifiedNamespace = "<SERVICE BUS NAMESPACE NAME>.servicebus.windows.net";

        // The name of your queue.
        private const string QueueName = "OrdersQueue";

        // Service Bus Receiver object to receive messages message the specific queue
        private ServiceBusReceiver SBReceiver;

    ```
1. `OnStart` メソッドを更新して `ServiceBusClient` オブジェクト、そして `ServiceBusReceiver` オブジェクトを作成し、`OrdersQueue` からメッセージを受信します。 
    
    ```csharp
        public override bool OnStart()
        {
            // Create a Service Bus client that you can use to send or receive messages
            ServiceBusClient SBClient = new ServiceBusClient(FullyQualifiedNamespace, new DefaultAzureCredential());

            CreateQueue(QueueName).Wait();

            // create a receiver that we can use to receive the message
            SBReceiver = SBClient.CreateReceiver(QueueName);

            return base.OnStart();
        }
        private async Task CreateQueue(string queueName)
        {
            // Create a Service Bus admin client to create queue if it doesn't exist or to get message count
            ServiceBusAdministrationClient SBAdminClient = new ServiceBusAdministrationClient(FullyQualifiedNamespace, new DefaultAzureCredential());

            // create the OrdersQueue if it doesn't exist already
            if (!(await SBAdminClient.QueueExistsAsync(queueName)))
            {
                await SBAdminClient.CreateQueueAsync(queueName);
            }
        }
    ```
12. メッセージを受信するコードを含める `RunAsync` メソッドを更新します。 

    ```csharp
        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                // receive message from the queue
                ServiceBusReceivedMessage receivedMessage = await SBReceiver.ReceiveMessageAsync();

                if (receivedMessage != null)
                {
                    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());

                    // view the message as an OnlineOrder
                    OnlineOrder order = receivedMessage.Body.ToObjectFromJson<OnlineOrder>();
                    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");

                    // complete message so that it's removed from the queue
                    await SBReceiver.CompleteMessageAsync(receivedMessage);
                }
            }
        }
    ```
14. これでアプリケーションが完成しました。 ソリューション エクスプローラーで、MultiTierApp のプロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択して F5 キーを押すと、完全なアプリケーションをテストできます。 メッセージ数が増えないことに注意してください。これは、worker ロールがキューの項目を処理し、完了としてマークしているためです。 Azure Compute Emulator のUI を表示すると、worker ロールのトレース出力を確認できます。 これを実行するには、タスク バーの通知領域のエミュレーター アイコンを右クリックし、**[Show Compute Emulator UI (Compute Emulator UI の表示)]** をクリックします。
    
<<<<<<< HEAD
    ![エミュレーター アイコンをクリックしたときに表示される内容のスクリーンショット。 [Show Compute Emulator UI]\(Compute Emulator UI の表示\) がオプションの一覧に表示されます。][19]
    
    ![[Microsoft Azure Compute Emulator (Express)]\(Microsoft Azure Compute Emulator (Express)\) ダイアログ ボックスのスクリーンショット。][20]
=======
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png" alt-text="エミュレーター アイコンをクリックしたときに表示される内容のスクリーンショット。[Show Compute Emulator UI]\(コンピューティング エミュレーター UI の表示\) は、オプションの一覧に表示されます。":::
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png" alt-text="[Microsoft Azure Compute Emulator (Express)]\(Microsoft Azure コンピューティング エミュレーター (Express)\) ダイアログ ボックスのスクリーンショット。":::
>>>>>>> repo_sync_working_branch

## <a name="next-steps"></a>次の手順
Service Bus の詳細については、次のリソースを参照してください。  

* [Service Bus キューの使用][sbacomqhowto]
* [Service Bus サービス ページ][sbacom]  


[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
