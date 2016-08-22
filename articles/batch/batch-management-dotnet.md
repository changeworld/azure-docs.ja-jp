<properties
	pageTitle="Batch Management .NET でのアカウント管理 | Microsoft Azure"
	description="Batch Management .NET ライブラリを使用してアプリケーション内の Azure Batch アカウントを作成、削除、変更します。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/03/2016"
	ms.author="marsma"/>

# Azure Batch アカウントとクォータを Batch Management .NET で管理する

> [AZURE.SELECTOR]
- [Azure ポータル](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

[Batch Management .NET][api_mgmt_net] ライブラリを使用して Batch アカウントの作成、削除、キー管理、およびクォータ検出を自動化することにより、Azure Batch アプリケーションにおける保守の負担が減ります。

- リージョン内の **Batch アカウントを作成および削除します**。たとえば、独立系ソフトウェア ベンダー (ISV) が課金のためにクライアントごとに異なる Batch アカウントを割り当てる場合、顧客ポータルにアカウントの作成および削除機能を追加できます。
- プログラムで Batch アカウントの**アカウント キーを取得および再生成します**。これは、アカウント キーの定期的なロールオーバーまたは期限切れを実施するセキュリティ ポリシーへの準拠を維持する場合に特に便利です。さまざまな Azure リージョンに複数の Batch アカウントがある場合、このロールオーバー プロセスを自動化するとソリューションの効率が向上します。
- **アカウントのクォータを確認し**、Batch アカウントに対する制限の決定から試行錯誤の当て推量を排除します。ジョブ開始前にアカウントのクォータをチェックしたり、プールを作成したり、コンピューティング ノードを追加したりすることで、いつどこでコンピューティング リソースが作成されるのかを事前に調整できます。アカウントに追加リソースを割り当てる前に、クォータの増量が必要なアカウントを特定できます。
- Batch Management .NET、[Azure Active Directory][aad_about]、[Azure Resource Manager][resman_overview]を同じアプリケーションで使用することにより、完全な機能を備えた管理エクスペリエンスのために**他の Azure サービスの機能を組み合わせます**。これらの機能とその API を使用して、摩擦のない認証エクスペリエンスや、リソース グループの作成と削除の機能、そして既に説明した機能によってエンド ツー エンドの管理ソリューションを実現できます。

> [AZURE.NOTE] この記事では、プログラムによる Batch アカウント、キー、およびクォータの管理に重点を置いて説明しますが、これらのアクティビティの多くは [Azure ポータル][azure_portal]を使用して実行できます。詳細については、「[Azure ポータルでの Azure Batch アカウントの作成と管理](batch-account-create-portal.md)」および「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## Batch アカウントの作成と削除

前述のように、Batch Management API の主要な機能の 1 つは、Azure リージョン内の Batch アカウントの作成と削除です。これらを行うには、[BatchManagementClient.Account.CreateAsync][net_create] および [DeleteAsync][net_delete]、またはこれらの同期版を使用します。

次のコード例では、アカウントを作成し、新しく作成されたアカウントを Batch サービスから取得して、削除します。この記事の次のコードおよび他のコードでは、`batchManagementClient` は [BatchManagementClient][net_mgmt_client] の完全に初期化されたインスタンスです。

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
	"MyResourceGroup",
	"mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Batch Management .NET ライブラリとその BatchManagementClient クラスを使用するアプリケーションでは、管理対象の Batch アカウントを所有するサブスクリプションに対する**サービス管理者**または**共同管理者**のアクセスが必要です。詳細については、後述の[Azure Active Directory](#azure-active-directory)セクション、および [AccountManagement][acct_mgmt_sample] コード サンプルを参照してください。

## アカウント キーの取得と再生成

サブスクリプション内の Batch アカウントからプライマリおよびセカンダリのアカウント キーを取得するには [ListKeysAsync][net_list_keys] を使用します。アカウント キーを再生成するには [RegenerateKeyAsync][net_regenerate_keys] を使用します。

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
	await batchManagementClient.Account.ListKeysAsync(
		"MyResourceGroup",
		"mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
	await batchManagementClient.Account.RegenerateKeyAsync(
		"MyResourceGroup",
		"mybatchaccount",
		new BatchAccountRegenerateKeyParameters() {
			KeyName = AccountKeyType.Primary
			});
```

> [AZURE.TIP] 管理アプリケーションのための効率化された接続ワークフローを作成できます。まず、管理対象の Batch アカウントのアカウント キーを [ListKeysAsync][net_list_keys] で取得します。次に、このキーを使用して[BatchClient][net_batch_client] を初期化するときに使用されるBatch .NET ライブラリの [BatchSharedKeyCredentials][net_sharedkeycred]クラスを初期化します。

## Azure サブスクリプションと Batch アカウントのクォータの確認

Azure サブスクリプションおよび個別の Azure サービス (Batch など) はすべて、それらの中にある特定のエンティティの数を制限する既定のクォータを持っています。Azure サブスクリプションの既定のクォータについては、「[Azure サブスクリプションとサービスの制限、クォータ、制約](./../azure-subscription-service-limits.md)」を参照してください。Batch サービスの既定のクォータについては、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。これらのクォータは、アプリケーション内から Batch Management .NET ライブラリを使用して確認できます。アカウントやコンピューティング リソース (プールやコンピューティング ノードなど) を追加する前に、割り当ての意思決定を行うことができます。

### Azure サブスクリプションでの Batch アカウント クォータの確認

リージョンに Batch アカウントを作成する前に、Azure サブスクリプションを調べて、そのリージョンにアカウントを追加できるかどうかを確認できます。

次のコード スニペットでは、最初に [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] を使用してサブスクリプション内のすべての Batch アカウントのコレクションを取得しています。このコレクションを取得した後、対象リージョン内のアカウント数を調べています。その後、[BatchManagementClient.Subscriptions][net_mgmt_subscriptions] を使用して Batch アカウントのクォータを取得し、そのリージョンに作成できるアカウントの数 (作成できる場合) を調べます。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
		await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
	creds.SubscriptionId,
	accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

上のスニペットで、`creds` は [TokenCloudCredentials][azure_tokencreds] のインスタンスです。このオブジェクトを作成する例については、GitHub の[AccountManagement][acct_mgmt_sample] コード サンプルを参照してください。

### Batch アカウントでのコンピューティング リソース クォータの確認

Batch ソリューション内のコンピューティング リソースを増やす前に、リソースを割り当てることによって現在設定されているアカウント クォータを超えないことを確認できます。次のコード スニペットでは `mybatchaccount` という名前の Batch アカウントのクォータ情報を出力しているだけです。しかし、実際のアプリケーションでは、このような情報を使用して、作成する予定の追加リソースをアカウントが処理できるかどうかを判断できます。

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
	await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Azure サブスクリプションおよびサービスには既定のクォータがありますが、[Azure ポータル][azure_portal]で要求を発行することによってこれらの制限の多くを増やすことができます。たとえば、Batch アカウントのクォータを増やす方法については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## Batch Management .NET、Azure AD、リソース マネージャー

Batch Management .NET ライブラリを使用するときは通常、[Azure Active Directory][aad_about] \(Azure AD) と [Azure Resource Manager][resman_overview]の機能を利用します。以下で説明するサンプル プロジェクトでは、Azure Active Directory とリソース マネージャーの両方を利用して、Batch Management .NET API の動作を示しています。

### Azure Active Directory

Azure AD は、顧客、サービス管理者、組織のユーザーを認証するために、Azure 自体で使用されています。Batch Management .NET のコンテキストでは、サブスクリプション管理者または共同管理者の認証を行うために Azure AD を使用します。認証後、Batch サービスのクエリや、この記事で取り上げる操作の実行を管理ライブラリに許可します。

以下で説明するサンプル プロジェクトでは、Azure の [Active Directory Authentication Library][aad_adal] \(ADAL) を使用して、ユーザーに Microsoft の資格情報の入力を求めます。サービス管理者または共同管理者の資格情報が提供されると、それにより、アプリケーションは Azure にサブスクリプションのリストを照会し、リソース グループと Batch アカウントの両方を作成および削除できます。

### リソース マネージャー

Batch Management .NET ライブラリで Batch アカウントを作成するときは通常、[リソース グループ][resman_overview]内でそれらを作成します。リソース グループは、プログラムから [Resource Manager .NET][resman_api] ライブラリの [ResourceManagementClient][resman_client] クラスを使用して作成できます。または、[Azure ポータル][azure_portal]を使用して過去に作成した既存のリソース グループにアカウントを追加することもできます。

## GitHub のサンプル プロジェクト

GitHub の [AccountManagment][acct_mgmt_sample] サンプル プロジェクトで、Batch Management .NET ライブラリの動作を確認してください。このコンソール アプリケーションでは、[BatchManagementClient][net_mgmt_client] と [ResourceManagementClient][resman_client] の作成と使用を示しています。また、両方のクライアントで必要な Azure [Active Directory Authentication Library][aad_adal] \(ADAL) の使用も示しています。

サンプル アプリケーションを正常に実行するには、最初に Azure ポータルを使用して Azure AD にアプリケーションを登録する必要があります。「[Azure Active Directory とアプリケーションの統合][aad_integrate]」の「[アプリケーションの追加](../active-directory/active-directory-integrating-applications.md#adding-an-application)」の手順に従って、独自のアカウントで既定のディレクトリ内にサンプル アプリケーションを登録してください。アプリケーションの種類には必ず「**ネイティブ クライアント アプリケーション**」を選択してください。「**リダイレクト URI**」には任意の有効な URI を指定できます (`http://myaccountmanagementsample` など)。リアルエンドポイントにする必要はありません。

アプリケーションを追加したら、ポータルのアプリケーションの設定で、*Windows Azure サービス管理 API* アプリケーションに「**組織として Azure サービス管理にアクセスする**」許可を委任します。

![Azure ポータルのアプリケーション アクセス許可][2]

> [AZURE.TIP] *[他のアプリケーションに対するアクセス許可]*の下に **[Windows Azure Service Management API]** が表示されない場合、**[アプリケーションの追加]** をクリックして **[Windows Azure Service Management API]** を選択し、チェックボックスをオンにします。次に、上で指定したアクセス許可を委任します。

上記のようにアプリケーションを追加したら、アプリケーションのリダイレクト URI とクライアント ID を利用し、[AccountManagment][acct_mgmt_sample] サンプル プロジェクトで `Program.cs` を更新します。アプリケーションの **[構成]** タブにこれらの値があります。

![Azure ポータルのアプリケーション構成][3]

[AccountManagment][acct_mgmt_sample] サンプル アプリケーションでは、次の操作が示されています。

1. [ADAL][aad_adal] を使用して Azure AD からセキュリティ トークンを取得します。ユーザーがまだサインインしていない場合は、Azure 資格情報の指定を求められます。
2. Azure AD から取得したセキュリティ トークンを使用して、[SubscriptionClient][resman_subclient] を作成し、Azure に対して、アカウントに関連付けられているサブスクリプションの一覧を照会します。複数見つかった場合はユーザーがいずれかを選択できます。
3. 選択されたサブスクリプションに関連付けられた新しい資格情報オブジェクトを作成します。
4. 新しい資格情報を使用して [ResourceManagementClient][resman_client] を作成します。
5. [ResourceManagementClient][resman_client] を使用して、新しいリソース グループを作成します。
6. [BatchManagementClient][net_mgmt_client] を使用して、さまざまな Batch アカウント操作を実行します。
  - 新しく作成されたリソース グループに新しい Batch アカウントを作成します。
  - Batch サービスから新しく作成されたアカウントを取得します。
  - 新しいアカウントのアカウント キーを出力します。
  - アカウント用の新しいプライマリ キーを再生成します。
  - アカウントのクォータ情報を出力します。
  - サブスクリプションのクォータ情報を出力します。
  - サブスクリプション内のすべてのアカウントを出力します。
  - 新しく作成されたアカウントを削除します。
7. リソース グループを削除します。

新しく作成した Batch アカウントおよびリソース グループを削除する前に、[Azure ポータル][azure_portal]で両方を調べることができます。

![Azure portal displaying the resource group and Batch account][1] <br /> *Azure ポータルでの新しいリソース グループと Batch アカウントの表示*

[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory とは"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD の認証シナリオ"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory とアプリケーションの統合"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

<!---HONumber=AcomDC_0810_2016-->