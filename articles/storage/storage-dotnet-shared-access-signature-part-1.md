<properties 
	pageTitle="Shared Access Signature: SAS モデルについて | Microsoft Azure" 
	description="Shared Access Signature (SAS) を使用して、BLOB、キュー、テーブル、ファイルを含む Azure Storage のリソースへのアクセスを委任する方法について説明します。Shared Access Signature を使用すると、ストレージ アカウント キーを保護しながら、アカウント内のリソースへのアクセスを他のユーザーに与えることができます。付与するアクセス許可と、SAS の有効期間を制御できます。また、ストアド アクセス ポリシーを確立すると、万が一アカウントのセキュリティが侵害される恐れがある場合に SAS を無効にできます。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="tamram"/>



# Shared Access Signature、第 1 部 : SAS モデルについて

## 概要

Shared Access Signature (SAS) の使用は、アカウント キーを知らせずに、自分のストレージ アカウントのオブジェクトへの制限付きアクセスを他のクライアントに許可するための優れた方法です。Shared Access Signature についてのこのチュートリアルの第 1 部では、SAS モデルの概要と SAS のベスト プラクティスについて説明します。チュートリアルの[パート 2](storage-dotnet-shared-access-signature-part-2.md) では、BLOB サービスで Shared Access Signature を作成するプロセスを詳しく説明します。

## Shared Access Signature とは

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。つまり、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。

## Shared Access Signature を使用するタイミング

SAS は、自分のアカウント キーを知らせたくないクライアントに、自分のストレージ アカウント内のリソースへのアクセスを許可する場合に使用できます。ストレージ アカウント キーには、プライマリ キーとセカンダリ キーの両方が含まれており、これらによって、アカウントとそのすべてのリソースへの管理アクセスが付与されます。これらのアカウント キーのいずれかを知らせると、悪意で、または誤ってアカウントが使用される可能性が生じます。Shared Access Signature は、アカウント キーが不要で安全な代替方法です。この方法で、他のクライアントは、付与されたアクセス許可に従ってストレージ アカウント内のデータの読み取り、書き込み、削除を実行できます。

SAS が役立つ一般的なシナリオは、ストレージ アカウント内でユーザーが自分のデータの読み取りや書き込みを行うサービスです。ストレージ アカウントにユーザー データが格納されるシナリオには、2 種類の典型的な設計パターンがあります。


1\.認証を実行するフロントエンド プロキシ サービス経由で、クライアントがデータのアップロードとダウンロードを行います。このフロントエンド プロキシ サービスには、ビジネス ルールの検証が可能であるという利点がありますが、データやトランザクションが大量である場合は、需要に応じて拡張可能なサービスの作成にコストがかかったり、困難が生じたりする可能性があります。

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.軽量サービスが、必要に応じてクライアントを認証してから、SAS を生成します。クライアントは、SAS を受信すると、SAS で定義されたアクセス許可と SAS で許可された期間で、ストレージ アカウントのリソースに直接アクセスできるようになります。SAS によって、すべてのデータをフロントエンド プロキシ サービス経由でルーティングする必要性が減少します。

![sas-storage-provider-service][sas-storage-provider-service]

実際のサービスの多くでは、関連するシナリオに応じて、この 2 種類のアプローチのハイブリッドを使用できます。つまり、一部のデータをフロントエンド プロキシで処理および検証し、それ以外のデータを SAS で直接保存したり、読み取ったりします。

また、特定のシナリオにおけるコピー操作でソース オブジェクトを認証するには、SAS を使用する必要があります。

- BLOB を別のストレージ アカウント内にある他の BLOB にコピーする場合、コピー元 BLOB の認証には SAS を使用する必要があります。バージョン 2015-04-05 では、コピー先 BLOB の認証に任意で SAS も使用できます。
- ファイルを別のストレージ アカウント内にある他のファイルにコピーする場合、コピー元ファイルの認証には SAS を使用する必要があります。バージョン 2015-04-05 では、コピー先ファイルの認証に任意で SAS も使用できます。
- BLOB をファイルにコピーしたり、ファイルを BLOB にコピーしたりする場合、同じストレージ アカウント内にコピー先とコピー元のオブジェクトがある場合でも、SAS を使用してソース オブジェクトを認証する必要があります。

>[AZURE.NOTE]現在、アカウント SAS は BLOB サービスと File サービスでのみサポートされています。近い将来、Table サービスと Queue サービスでもサポートされる予定です。

## Shared Access Signature の種類

バージョン 2015-04-05 の Azure Storage には、アカウント SAS という新しい種類の Shared Access Signature が導入されています。次のいずれかの種類の Shared Access Signature を作成できるようになりました。

- **アカウント SAS。** アカウント SAS では、1 つ以上のストレージ サービスのリソースへのアクセスを委任できます。サービス SAS を使用して実行できるすべての操作は、アカウント SAS でも実行できます。アカウント SAS では、**Get/Set Service Properties** や **Get Service Stats** など、特定のサービスに適用される操作へのアクセスも委任できます。サービス SAS で許可されていない BLOB コンテナー、テーブル、キューおよびファイル共有の読み取り、書き込みおよび削除操作へのアクセスも委任できます。アカウント SAS トークンの作成の詳細については、[アカウント SAS の作成](https://msdn.microsoft.com/library/mt584140.aspx)に関するページを参照してください。

- **サービス SAS。** サービス SAS は、1 つのストレージ サービス (BLOB、Queue、Table、または File サービスのいずれか) のリソースへのアクセスを委任します。サービス SAS トークンの作成の詳細については、[サービス SAS の作成](https://msdn.microsoft.com/library/dn140255.aspx)に関するページおよび[サービス SAS の例](https://msdn.microsoft.com/library/dn140256.aspx)に関するページを参照してください。

## Shared Access Signature の機能

Shared Access Signature とは、特殊なクエリ パラメーターのセットを含むトークンを含む、1 つ以上のストレージ リソースをポイントする URI です。このトークンは、リソースへのクライアントのアクセス方法を示します。このクエリ パラメーターの 1 つである署名は、SAS パラメーターで作成されており、アカウント キーで署名されています。この署名を使用して、Azure Storage が SAS を認証します。

アカウント SAS とサービス SAS のトークンには共通のパラメーターがいくつかあります。また別のパラメーターをいくつか取ります。

### アカウント SAS とサービス SAS のトークンに共通するパラメーター

- **API のバージョン。**要求の実行に使用するストレージ サービスのバージョンを指定する省略可能なパラメーターです。 
- **サービスのバージョン。**要求の認証に使用するストレージ サービスのバージョンを指定する必須パラメーターです。
- **開始時刻。** この時刻に SAS が有効になります。Shared Access Signature の開始時刻は省略可能です。省略した場合は、SAS がすぐに有効になります。 
- **有効期限。** この時刻の後、SAS が有効ではなくなります。ベスト プラクティスでは、SAS の有効期限を指定するか、保存されているアクセス ポリシーに SAS を関連付けることを推奨しています (以下を参照)。
- **アクセス許可。** SAS に指定されたアクセス許可は、クライアントが SAS を使用して、ストレージ リソースに対して実行できる操作を示します。使用可能なアクセス許可は、アカウント SAS とサービス SAS で異なります。
- **IP。** 要求の送信元である Azure 外部の IP アドレスまたは IP アドレスの範囲を指定する省略可能なパラメーター (Express Route については、「[ルーティング セッション構成の状態](../expressroute/expressroute-workflows.md#routing-session-configuration-state)」を参照してください)。 
- **プロトコル。** 要求に許可されているプロトコルを指定する省略可能なパラメーターです。指定できる値は、既定値の HTTPS と HTTP (https,http) か、HTTPS のみ (https) です。HTTP のみの値は許可されていないことに注意してください。
- **署名。** 署名は、トークンの一部として指定されたその他のパラメーターから構成され、その後に暗号化されます。これは、SAS の認証に使用されます。

### アカウント SAS トークンのパラメーター

- **単一または複数のサービス。** アカウント SAS では、1 つ以上のストレージ サービスにアクセスを委任できます。たとえば、BLOB およびファイル サービスにアクセスを委任するアカウント SAS を作成できます。または、(BLOB、キュー、テーブル、およびファイルの) 4 つのすべてのサービスにアクセスを委任する SAS を作成できます。
- **ストレージ リソースの種類。** アカウント SAS は、特定のリソースではなく、ストレージ リソースの 1 つ以上のクラスに適用されます。アカウント SAS を作成して、次へのアクセスを委任できます。
	- ストレージ アカウントのリソースを呼び出すサービスレベル API。たとえば、**Get/Set Service Properties**、**Get Service Stats**、**List Containers/Queues/Tables/Shares** です。
	- BLOB コンテナー、キュー、テーブル、およびファイル共有の各サービスのコンテナー オブジェクトを呼び出すコンテナーレベルの API。たとえば、**Create/Delete Container**、**Create/Delete Queue**、**Create/Delete Table**、**Create/Delete Share**、**List Blobs/Files and Directories** です。
	- BLOB、キュー メッセージ、テーブル エンティティ、およびファイルを呼び出すオブジェクトレベルの API。たとえば、**Put Blob**、**Query Entity**、**Get Messages**、**Create File** です。

### サービス SAS トークンのパラメーター

- **ストレージ リソース。** サービス SAS を使用してアクセスを委任できるストレージ リソースには次があります。
	- コンテナーと BLOB
	- ファイル共有とファイル
	- キュー
	- テーブルとテーブル エンティティの範囲

## SAS の URI の例

BLOB に読み書きアクセス許可を付与するサービス SAS URI の例を、次に示します。表では、SAS での機能がわかりやすいように、URI の部分ごとに説明しています。

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

名前|SAS の部分|説明
---|---|---
Blob URI|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |BLOB のアドレス。HTTPS の使用を強くお勧めします。
ストレージ サービスのバージョン|sv=2015-04-05|ストレージ サービス バージョン 2012-02-12 以降では、このパラメーターは、使用するバージョンを示します。
開始時刻|st=2015-04-29T22%3A18%3A26Z|ISO 8601 形式で指定されます。SAS をすぐに有効にする場合は、開始時刻を省略します。
有効期限|se=2015-04-30T02%3A23%3A26Z|ISO 8601 形式で指定されます。
リソース|sr=b|リソースは BLOB です。
アクセス許可|sp=rw|SAS で付与されるアクセス許可には、読み取り (r) および書き込み (w) が含まれます。
IP 範囲|sip=168.1.5.60-168.1.5.70|要求が受け入れられる IP アドレスの範囲です。
プロトコル|spr=https|HTTPS を使用する要求のみが許可されます。
署名|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|BLOB へのアクセスを認証するために使用します。署名は、SHA256 アルゴリズムを使用して署名対象文字列とキーを計算した後に、Base 64 エンコードを使用してエンコードした HMAC 値です。

トークンで同じ共通のパラメーターを使用するアカウント SAS の例を次に示します。これらのパラメーターは上で説明済みのため、ここでは説明はしません。アカウント SAS に固有のパラメーターのみを次の表に示します。

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

名前|SAS の部分|説明
---|---|---
リソース URI|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The BLOB サービス エンドポイントと、(GET を使用して呼び出された場合は) サービスのプロパティを取得するパラメーターまたは (SET を使用して呼び出された場合は) サービスのプロパティを設定するパラメーターです。
サービス|ss = bf|SAS は BLOB およびファイル サービスに適用されます。
リソースの種類|srt = s|SAS は、サービスレベルの操作に適用されます。
アクセス許可|sp=rw|この許可は、読み書きの操作へのアクセスを付与します。  

許可はサービス レベルに制限されているため、この SAS を使用してアクセスできる操作は、**Get Blob Service Properties** (読み取り) および **Set BLOB Service Properties** (書き込み) になります。ただし、別のリソース URI に同じ SAS トークンを使用し、**Get BLOB Service Stats** (読み取り) へのアクセスを委任することもできます。

## 保存されているアクセス ポリシーを使用した SAS の制御 ##

Shared Access Signature の形式は、次の 2 つのいずれかです。

- **アドホック SAS:** アドホック SAS を作成すると、開始時刻、有効期限、および SAS へのアクセス許可がすべて、SAS URI で指定されます (または、開始時刻を省略した場合は、暗黙で指定されます)。この種類の SAS はアカウント SAS またはサービス SAS として作成できます。 

- **保存されているアクセス ポリシーのある SAS**: 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、キュー、ファイル共有) で定義されており、これを使用して、1 つ以上の Shared Access Signature のコンテナーを管理できます。保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

>[AZURE.NOTE]現時点では、アカウント SAS はアドホック SAS である必要があります。保存されているアクセス ポリシーは、まだアカウント SAS では使用できません。

1 つの重要なシナリオ、失効では、この 2 つの形式の相違点が重要です。SAS は URL であるため、取得したユーザーはだれでも、どのユーザーが最初に要求したかに関係なく、SAS を使用できます。SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。配布された SAS は、次の 4 つの状況のいずれかになるまで有効です。

1.	SAS に指定された有効期限に達した。
2.	保存されているアクセス ポリシーに指定された、SAS が参照する有効期限に達した (保存されているアクセス ポリシーが参照される場合、かつ有効期限が指定されている場合)。これは、期間が経過したため、または保存されているアクセス ポリシーの有効期限を過去の日時に変更したために発生します。このような有効期限の変更は、SAS を失効させる方法の 1 つです。
3.	SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。保存されているアクセス ポリシーを、完全に同じ名前で再作成すると、そのアクセス ポリシーに関連付けられたアクセス許可に従って、すべての既存の SAS トークンが再び有効になります (SAS の有効期限が過ぎていないことが前提です)。SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使用してください。
4.	SAS の作成に使用したアカウント キーが再度生成された。これを行うと、そのアカウント キーを使用するすべてのアプリケーション コンポーネントが、別の有効なアカウント キー、または新しく再生成されたアカウント キーを使用するよう更新されるまで、認証に失敗します。

>[AZURE.IMPORTANT]Shared Access Signature URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。保存済みのアクセス ポリシーが指定されていない場合、Shared Access Signature を取り消すにはアカウント キーを変更する以外に方法はありません。

## 例: 共有アクセス署名を作成して使用する

次に、アカウント SAS とサービス SAS の 2 種類の Shared Access Signature の例をいくつか示します。

これらの例を実行するには、次のパッケージをダウンロードし、参照する必要があります。

- [.NET 用 Azure Storage クライアント ライブラリ](http://www.nuget.org/packages/WindowsAzure.Storage)、バージョン 6.x 以降 (アカウント SAS を使用)。
- [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 

### 例: アカウント SAS

次のコード例では、BLOB およびファイル サービスに有効なアカウント SAS を作成します。これでは、クライアントに、サービスレベルの API にアクセスするための、読み取り、書き込み、および一覧表示のアクセス許可を付与します。アカウント SAS では、プロトコルを HTTPS に制限しているため、要求は HTTPS で行う必要があります。

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials. Modify for your account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

BLOB サービス用にサービスレベルの API にアクセスするためにアカウント SAS を使用するには、SAS を使用して BLOB クライアントを構築し、ストレージ アカウント用に BLOB ストレージ エンドポイントを構築します。

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### 例: 保存されているアクセス ポリシーによるサービス SAS

次のコード例では、保存されているアクセス ポリシーをコンテナーに作成し、次いでコンテナーのサービス SAS を生成します。その後、クライアントにこの SAS を提供し、このコンテナーの読み取りおよび書き込みアクセス許可を付与できます。独自のアカウント名を使用するようにコードを変更します。

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();

    // Clear the container's shared access policies to avoid naming conflicts.
    blobPermissions.SharedAccessPolicies.Clear();
    
    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Add
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the new stored access policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

このサービス SAS を所有するクライアントは、コンテナー内の BLOB に対する読み取りまたは書き込み要求をコードから認証できるようになります。たとえば、次のコードは SAS トークンを使用し、コンテナー内に新しいブロック BLOB を作成します。独自のアカウント名を使用するようにコードを変更します。

    Uri blobUri = new Uri("https://<myaccount>.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Temp\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## Shared Access Signature の使用のベスト プラクティス

アプリケーションで Shared Access Signature を使用する場合は、次の 2 つの潜在的なリスクに注意する必要があります。

- SAS が漏えいすると、取得した人はだれでも使用できるため、ストレージ アカウントの安全性が損なわれるおそれがあります。
- クライアント アプリケーションに提供された SAS が期限切れになり、アプリケーションがサービスから新しい SAS を取得できない場合は、アプリケーションの機能が損なわれる可能性があります。  

Shared Access Signature の使用に関する次の推奨事項に従うと、これらのリスクが軽減されます。

1. **常に HTTPS を使用**して SAS を作成または配布します。SAS が HTTP で渡され、傍受された場合、中間者攻撃を実行している攻撃者は、SAS を読み取って、宛先のユーザーと同様に使用することができます。このため、機密データの安全性が損なわれたり、悪意のあるユーザーによるデータ破損が発生したりする可能性があります。
2. **可能な場合は、保存されているアクセス ポリシーを参照します。** 保存されているアクセス ポリシーを使用すると、ストレージ アカウント キーを再生成せずに、アクセス許可を失効させることが可能になります。これらの有効期限を非常に長い期間 (または無期限) に設定し、それがさらに将来へ移動するように、定期的に更新されるようにします。
3. **アドホック SAS には、短期間の有効期限を使用します。** こうすると、知らないうちに SAS の安全性が損なわれていても、有効であるのは短期間だけになります。この方法は、保存されているアクセス ポリシーを参照できない場合に特に重要です。また、この方法では、BLOB にアップロード可能な時間が制限されるので、BLOB に書き込むことのできるデータの量を制限するために役立ちます。
4. **必要に応じて、クライアントに SAS を自動更新させます。** クライアントは、SAS を提供するサービスが利用不可である場合に再試行する時間を考慮して、予期される有効期限までに余裕を持って SAS を更新する必要があります。使用する SAS が、すぐに実行する短期間の少数の操作用であり、操作が、指定された有効期限の前に完了する予定である場合は、SAS が更新されないため、この方法は必要ありません。ただし、クライアントが SAS 経由で日常的に要求を実行する場合は、有効期限に注意が必要になる可能性があります。重要な考慮事項は、SAS の有効期限を短くする必要性 (前述のように) と、更新の完了前に SAS の期限が切れることによる中断を避けるためにクライアントが早めに更新を要求する必要性とのバランスです。
5. **SAS の開始時刻に注意します。** SAS の開始時刻を [**現在**] に設定すると、クロック スキュー (さまざまなコンピューター間での現在時刻の差) により、最初の数分間にエラーが断続的に表示される場合があります。一般に、開始時刻を少なくとも 15 分前に設定するか、まったく設定しないようにします。設定しないと、すべての場合に、すぐに有効になります。同じことが、一般的には有効期限にも適用されます。どの要求でも、前後 15 分以内のクロック スキューが発生する可能性があることを憶えておいてください。2012-02-12 より前の REST バージョンを使用するクライアントの場合、保存されているアクセス ポリシーを参照しない SAS の最長期間は 1 時間であり、それより長い期間を指定するポリシーはすべて失敗します。
6.	**アクセス先のリソースを具体的に指定します。** 一般的なセキュリティのベスト プラクティスは、必要最小限の権限をユーザーに付与することです。ユーザーに必要なのは、1 つのエンティティへの読み取りアクセスだけの場合は、すべてのエンティティへの読み取り/書き込み/削除アクセスではなく、その 1 つのエンティティへの読み取りアクセスだけをユーザーに許可します。こうすると、SAS の安全性に対する脅威の軽減にも役立ちます。攻撃者が入手した場合でも、SAS の効力が小さいためです。
7.	**アカウントは、SAS によるものも含め、すべての使用について課金されます。** BLOB への書き込みアクセスを許可した場合は、ユーザーが 200 GB の BLOB をアップロードする可能性があります。ユーザーに読み取りアクセスも許可すると、この BLOB を 10 回ダウンロードする可能性があります。この場合、2 TB の送信料金が発生します。したがって、悪意のあるユーザーによるリスクが軽減されるように、制限付きアクセス許可を付与してください。このような脅威が軽減されるように、短期間の SAS を使用してください (ただし、終了時刻のクロック スキューには注意してください)。
8.	**SAS を使用して書き込まれたデータを検証します。** クライアント アプリケーションがストレージ アカウントにデータを書き込む場合は、そのデータに問題がある可能性に注意してください。データが検証後または認証後に使用可能になることをアプリケーションが要求する場合は、書き込まれたデータをアプリケーションが使用する前に、この検証を実行する必要があります。これを実行すると、ユーザーが SAS を正当に入手している場合でも、漏えいした SAS を利用している場合でも、破損データまたは悪意によるデータの書き込みからアカウントが保護されます。
9. **場合によっては SAS を使用しないようにします。** ストレージ アカウントに対する特定の操作に関連するリスクが、SAS の利点より重大である場合もあります。このような操作については、ビジネス ルールの検証、認証、および監査を実行した後にストレージ アカウントに書き込む中間層サービスを作成します。また、別の方法でアクセスを管理した方が容易である場合もあります。たとえば、コンテナー内のすべての BLOB が一般ユーザーに読み取り可能である場合は、すべてのクライアントにアクセス用の SAS を提供するのではなく、コンテナーをパブリックにします。
10.	**Storage Analytics を使用してアプリケーションを管理します。** SAS プロバイダー サービスが中断したり、保存されているアクセス ポリシーを不注意で削除したりしたために発生する認証失敗の急増を、ログやメトリックを使用して監視できます。詳細については、[Microsoft Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)を参照してください。

## まとめ ##

Shared Access Signature は、アカウント キーを知らせずに、ストレージ アカウントへの制限付きアクセス許可をクライアントに付与する場合に便利です。したがって、Azure Storage を使用するあらゆるアプリケーションのセキュリティ モデルの重要な部分となります。ここに示すベスト プラクティスに従うと、アプリケーションのセキュリティを損なうことなく、SAS を使用して、ストレージ アカウントのリソースへのアクセスの柔軟性を高めることができます。

## 次のステップ ##

- [Shared Access Signature、第 2 部: BLOB サービスによる SAS の作成および使用](storage-dotnet-shared-access-signature-part-2.md)
- [Windows で Azure File ストレージを使用する方法](storage-dotnet-how-to-use-files.md)
- [Azure Storage リソースへのアクセスの管理](storage-manage-access-to-resources.md)
- [Shared Access Signature を使用したアクセスの委任](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [テーブルおよびキュー SAS についての MSDN ブログ](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=Nov15_HO4-->