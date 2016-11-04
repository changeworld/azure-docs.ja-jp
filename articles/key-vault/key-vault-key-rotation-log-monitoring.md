---
title: エンド ツー エンドのキー ローテーションと監査で Key Vault を設定する方法 | Microsoft Docs
description: キー ローテーションとキー コンテナー ログの監視による設定について説明します
services: key-vault
documentationcenter: ''
author: swgriffith
manager: ''
tags: ''

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: jodehavi;stgriffi

---
# エンド ツー エンドのキー ローテーションと監査で Key Vault を設定する方法
## はじめに
Azure Key Vault を作成したら、そのコンテナーを使って、キーとシークレットを保存できます。アプリケーションでキーやシークレットを保持する必要がなくなりました。キーやシークレットは、必要に応じてキー コンテナーに要求します。これによりアプリケーションの動作に影響を与えずにキーとシークレットを更新できるため、キーおよびシークレット管理の動作を取り巻く可能性の幅が広がります。

この記事では、Azure Key Vault を使ってシークレット (ここでは、アプリケーションがアクセスする Azure Storage アカウント キー) を格納する例について説明します。また、そのストレージ アカウント キーのスケジュールされたローテーションの実装も紹介します。最後に、キー コンテナー監査ログを監視し、予期しない要求が行われたときにアラートを生成する方法のデモを見ていきます。

> [!NOTE]
> このチュートリアルの目的は、Azure Key Vault の初期設定について詳しく説明することではありません。詳細については、「[Azure Key Vault の概要](key-vault-get-started.md)」をご覧ください。また、クロスプラットフォーム コマンドライン インターフェイスの手順については、[対応するチュートリアル](key-vault-manage-with-cli.md)をご覧ください。
> 
> 

## KeyVault の設定
アプリケーションが Azure Key Vault からシークレットを取得できるようにするには、最初にシークレットを作成し、それをコンテナーにアップロードする必要があります。次に示すように、これは PowerShell を使用して簡単に行うことができます。

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

```powershell
Login-AzureRmAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の 1 つを指定することが必要なことがあります。アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzureRmSubscription
```

ログ記録する Key Vault に関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

この記事では、ストレージ アカウント キーをシークレットとして格納しているため、そのストレージ アカウント キーを取得する必要があります。

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

自分のシークレット (ここではストレージ アカウント キー) を取得したら、それをセキュリティで保護された文字列に変換し、その値を使ってシークレットをキー コンテナーに作成します。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
次に、作成したシークレットの URI を取得します。これは、後で Key Vault を呼び出して、自分のシークレットを取得するときに使用します。次の PowerShell コマンドを実行し、"Id" の値をメモします。これが、シークレットの URI です。

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## アプリケーションの設定
これでシークレットを格納できました。次は、コードを使って、そのシークレットを取得して使用します。これに必要な手順はいくつかありますが、最も重要なのは、アプリケーションを Azure Active Directory に登録し、Azure Key Vault にアプリケーション情報を指定して、Azure Key Vault がアプリケーションからの要求を許可できるようにすることです。

> [!NOTE]
> アプリケーションは、Key Vault と同じ Azure Active Directory テナントに作成する必要があります。
> 
> 

最初に、Azure Active Directory の [アプリケーション] タブを開きます

![Azure AD でアプリケーションを開く](./media/keyvault-keyrotation/AzureAD_Header.png)

[追加] をクリックして、新しいアプリケーションを Azure AD に追加します

![[アプリケーションの追加] を選択する](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

アプリケーションの種類は [Web アプリケーションや Web API] のままにして、アプリケーション名を入力します。

![アプリケーションに名前を付ける](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

アプリケーションの [サインオン URL] と [アプリケーション ID/URI] を指定します。このデモでは、任意の値を入力できます。この値は、必要に応じて後で変更できます。

![必要な URI を指定する](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

アプリケーションが Azure AD に追加されると、アプリケーション ページが表示されます。そこで [構成] タブをクリックし、"クライアント ID" の値を見つけてコピーします。後で使用できるように、クライアント ID をメモします。

次に、Azure AD と対話できるように、アプリケーションのキーを生成する必要があります。これは、[構成] タブの [キー] セクションで作成できます。後で使用できるように、Azure AD アプリケーションから新しく生成されたキーをメモします。

![Azure AD アプリのキー](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Key Vault へのアプリケーションからの呼び出しを確立する前に、アプリケーションとそのアクセス許可について Key Vault に指定する必要があります。次のコマンドは、Azure AD アプリからコンテナーの名前とクライアント ID を取得し、アプリケーションのキー コンテナーに対する "Get" アクセスを許可します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

この時点で、アプリケーション呼び出しの構築をいつでも開始できます。アプリケーションでは、まず、Azure Key Vault および Azure Active Directory との対話に必要な NuGet パッケージをインストールする必要があります。Visual Studio パッケージ マネージャー コンソールで、次のコマンドを入力します。この記事が書かれた時点で、ActiveDirectory パッケージの最新バージョンは 3.10.305231913 です。したがって、現在の最新のバージョンを確認し、必要に応じて更新してください。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

アプリケーション コードで、Active Directory 認証用のメソッドを保持するクラスを作成します。この例では、そのクラスは "Utils" と呼ばれます。その後、次の using を追加する必要があります。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

次に、以下のメソッドを追加して、Azure AD から JWT トークンを取得します。保守容易性のために、ハードコーディングされた文字列の値を Web またはアプリケーション構成に移動できます。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

最後に、必要なコードを追加して Key Vault を呼び出し、シークレット値を取得できます。まず、次の using ステートメントを追加する必要があります。

```csharp
using Microsoft.Azure.KeyVault;
```

次に、メソッド呼び出しを追加して Key Vault を呼び出し、シークレットを取得します。このメソッドでは、前の手順で保存したシークレットの URI を提供します。上記で作成した Utils クラスから GetToken メソッドを使用している点に注意してください。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

アプリケーションを実行するときに、Azure Active Directory への認証を行い、Azure Key Vault からシークレット値を取得しているはずです。

## Azure Automation を使用したキー ローテーション
Azure Key Vault シークレットとして格納する値のローテーション戦略は、さまざまな方法で実装できます。シークレットのローテーションは、手動プロセスの一環として行ったり、API 呼び出しを利用してプログラムによって実行したりできます。また、自動化スクリプトを使うこともできます。この記事では、Azure PowerShell と Azure Automation を組み合わせて使用して、Azure Storage アカウントのアクセス キーを変更し、その新しいキーでキー コンテナーを更新します。

Azure Automation でキー コンテナーのシークレット値を設定できるようにするには、Azure Automation インスタンスを確立したときに作成された、"AzureRunAsConnection" という接続のクライアント ID を取得する必要があります。この ID を取得するには、Azure Automation インスタンスから [アセット] を選択します。そこで [接続]、[AzureRunAsConnection] サービス プリンシパルの順に選択し、"アプリケーション ID" をメモします。

![Azure Automation クライアント ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

[アセット] ウィンドウで [モジュール] も選択します。モジュールでは、[ギャラリー] を選択し、次の各モジュールの更新バージョンを検索して、インポートします。

    Azure
    Azure.Storage    
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> この記事が書かれた時点では、以下のスクリプトで更新が必要なのは上記のモジュールだけです。自動化ジョブが失敗した場合は、必要なモジュールとその依存関係がすべてインポートされていることを確認してください。
> 
> 

Azure Automation 接続のアプリケーション ID を取得したら、このアプリケーションにコンテナー内のシークレットを更新するアクセス権があることを、Azure Key Vault に通知する必要があります。これを行うには、次の PowerShell コマンドを使用します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

次に、Azure Automation インスタンスで [Runbook] リソースを選択し、[Runbook の追加] を選択します。[簡易作成] を選択します。Runbook に名前を付けて、Runbook の種類として [PowerShell] を選択します。必要に応じて、説明を追加します。最後に、[作成] をクリックします。

![Runbook を作成する](./media/keyvault-keyrotation/Create_Runbook.png)

新しい Runbook のエディター ペインに、次の PowerShell スクリプトを貼り付けます。

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

エディター ペインで [テスト ウィンドウ] を選択して、スクリプトをテストします。エラーなしでスクリプトが実行されると、[Publish (発行)] オプションを選択できます。その後、Runbook 構成ウィンドウに戻って、Runbook のスケジュールを適用できます。

## Key Vault 監査のパイプライン
Azure Key Vault を設定するときに、監査をオンにして、Key Vault へのアクセス要求のログを収集できます。これらのログは、指定された Azure Storage アカウントに保存され、抽出、監視、および分析することができます。ここでは、Azure Functions、Azure Logic Apps、および Key Vault の監査ログを利用してパイプラインを作成し、Web アプリのアプリ ID と一致するアプリがコンテナーからシークレットを取得したときに、電子メールを送信するシナリオを見ていきます。

最初に、Key Vault へのログオンを有効にする必要があります。これを行うには、次の PowerShell コマンドを使用します (詳細については、[こちら](key-vault-logging.md)をご覧ください)。

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

ログオンを有効にすると、監査ログによって、指定されたストレージ アカウントへの収集が開始されます。こうしたログには、いつ、だれが、どのような方法で Key Vault にアクセスしたかに関するイベントが含まれます。

> [!NOTE]
> Key Vault の操作を行ってからログ情報にアクセスできるようになるまでの時間は最大で 10 分です。ほとんどの場合は、これよりも早く確認できます。
> 
> 

次は、[Azure Service Bus キューを作成](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)します。ここにキー コンテナーの監査ログがプッシュされます。キューに入れられたログは、Logic App が取得して、処理を実行します。Service Bus は比較的簡単に作成できます。簡単な作成手順を次に示します。

1. Service Bus 名前空間を作成します (使用できるものが既にある場合は、手順 2 に進みます)。
2. ポータルで Service Bus に移動して、キューを作成する名前空間を選択します。
3. [新規] を選択し、[Service Bus]、[キュー] の順に選択して、必要な情報を入力します。
4. 名前空間を選択し [接続情報] をクリックして、Service Bus 接続情報を取得します。この情報は、次の部分に必要です。

次に、[Azure 関数を作成](../azure-functions/functions-create-first-azure-function.md)して、ストレージ アカウント内で Key Vault ログをポーリングし、新しいイベントを取得します。これが、スケジュールでトリガーされる関数です。

Azure 関数を作成します (ポータルで [新規]、[Function App] の順に選択)。作成中、既存のホスティング プランを使用するか、新しいプランを作成できます。動的ホスティングを選択することもできます。関数のホスティング オプションの詳細については、[こちら](../azure-functions/functions-scale.md)をご覧ください。

Azure 関数を作成するときに、この画面にアクセスしてタイマー関数と C# を選択し、開始画面で **[作成]** をクリックします。

![Azure Functions スタート ブレード](./media/keyvault-keyrotation/Azure_Functions_Start.png)

[開発] タブで、run.csx コードを次のコードに置き換えます。

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> [!NOTE]
> 上記のコードの変数を、Key Vault ログが書き込まれたストレージ アカウント、以前に作成した Service Bus、およびキー コンテナーのストレージ ログへの特定のパスを指すように置き換えます。
> 
> 

関数は Key Vault ログが書き込まれたストレージ アカウントから最新のログ ファイルを取得します。その後、そのファイルから最新のイベントを取得して、Service Bus キューにプッシュします。1 つのファイルに複数のイベントが (たとえば 1 時間以上にわたって) 保持されている可能性があるため、*sync.txt* ファイルを作成します。関数はこのファイルも参照して、取得された最後のイベントのタイム スタンプを確認します。これにより、同じイベントが複数回プッシュされなくなります。この *sync.txt* ファイルには、最後に発生したイベントのタイムスタンプのみが含まれます。ログは、読み込まれたときに、タイムスタンプに基づいて正しく並べ替える必要があります。

この関数では、Azure Functions ですぐには使用できない追加ライブラリをいくつか参照します。ライブラリを追加するには、Azure Functions で、nuget を使用してそのライブラリをプルするがあります。[ファイルを表示] オプションを選択します

![[ファイルの表示] オプション](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

次に、以下のコンテンツが含まれる *project.json* という新しいファイルを追加します。

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
"保存" すると、Azure Functions によって必要なバイナリがダウンロードされます。

**[統合]** タブに切り替えて、関数内で使用するタイマー パラメーターにわかりやすい名前を付けます。上記のコードでは、タイマーは *myTimer* と呼ばれます。[CRON 式](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) を次のように指定します: 0 ***** (1 分ごとに関数を実行するタイマーの場合)。

同じ **[統合]** タブで、種類が *Azure Blob Storage* になる入力を追加します。これは *sync.txt* ファイルを指します。このファイルには、関数によって参照される最後のイベントのタイムスタンプが含まれます。これは関数内でパラメーター名によって使用できます。上記のコードの Azure Blob Storage 入力では、パラメーター名は *inputBlob* です。*sync.txt* ファイルが存在するストレージ アカウント (同じまたは異なるストレージ アカウントの場合があります) を選択し、パス フィールドに、ファイルが存在するパスを {container-name}/path/to/sync.txt の形式で指定します。

種類が *Azure Blob Storage* になる出力を追加します。これも、入力で定義した *sync.txt* ファイルを指します。これは、参照される最後のイベントのタイムスタンプを書き込むときに、関数によって使用されます。上記のコードでは、このパラメーターは *outputBlob* と呼ばれます。

これで関数の準備ができました。**[開発]** タブに切り替えて、コードを "保存" します。出力ウィンドウでコンパイル エラーがないかどうかを確認し、エラーがある場合は修正します。コンパイルできる場合、コードは現在実行されており、1 分ごとに Key Vault ログが確認され、新しいイベントすべてが定義済み Service Bus キューにプッシュされます。関数がトリガーされるたびに、ログ情報がログ ウィンドウに書き出されることがわかります。

### Azure Logic App
次に、Azure Logic App を作成する必要があります。このアプリは、関数が Service Bus キューにプッシュするイベントを取得し、コンテンツを解析して、一致する条件に基づいて電子メールを送信します。

[新規]、[Logic App] の順に移動し、[Logic App を作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

Logic App が作成されたら、そのアプリに移動し、[編集] を選択します。Logic App エディターで、[Service Bus キュー] マネージ API を選択し、Service Bus 資格情報を入力して、キューに接続します。

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

次に、"条件を追加" します。条件で "高度なエディター" に切り替えて次を入力し、APP\_ID を、Web アプリの実際の APP\_ID に置き換えます。

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

この式は、基本的に、受信イベント (Service Bus メッセージの本文) の **appid** プロパティが、アプリの **appid** でない場合に、**false** を返します。

ここで、[If no, do nothing... (いいえの場合は、何もしない)] オプションでアクションを作成します。

![Azure Logic App 選択アクション](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

アクションで、[Office 365 - send email (Office 365 - メール送信)] を選択します。フィールドに入力して、定義された条件が false を返したときに送信する電子メールを作成します。Office 365 がない場合は、他の方法で同じことを実現できます。

この時点では、1 分ごとに新しい Key Vault 監査ログを確認するエンド ツー エンドのパイプラインがあります。このパイプラインは、新しいログを見つけたら Service Bus キューにプッシュします。Logic App は、新しいメッセージがキューに入るとすぐにトリガーされ、イベント内のアプリ ID が呼び出し元アプリケーションのアプリ ID と一致しない場合に、電子メールを送信します。

<!---HONumber=AcomDC_0928_2016-->