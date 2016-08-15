<properties
	pageTitle="Azure Batch | Microsoft Azure でのアプリケーションの手軽なインストールと管理"
	description="Azure Batch のアプリケーション パッケージ機能を使用すると、Batch コンピューティング ノードにインストールされる複数のアプリケーションとバージョンを簡単に管理できます。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/30/2016"
	ms.author="marsma" />

# Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ

Azure Batch のアプリケーション パッケージ機能を使用すると、管理が容易になり、プール内のコンピューティング ノードにアプリケーションをデプロイしやすくなります。アプリケーション パッケージを使用すると、複数のバージョンのアプリケーションをアップロードして管理できます。タスクによって実行されるバイナリやそのサポート ファイルも同様です。また、アップロードしたアプリケーションのうち 1 つ以上をプール内のコンピューティング ノードに自動的にデプロイできます。

この記事では、Azure ポータルを使用してアプリケーション パッケージをアップロードおよび管理する方法を学習します。その後、[Batch .NET][api_net] ライブラリを使用して、プールのコンピューティング ノードにそれらをインストールする方法を学習します。

> [AZURE.NOTE] ここで説明されているアプリケーション パッケージの機能は、以前のバージョンのサービスで利用できる "Batch Apps" 機能に優先します。

## アプリケーション パッケージの要件

この記事で取り上げるアプリケーション パッケージ機能は、2016 年 3 月 10 日以降に作成された Batch プールと "*のみ*" 互換性があります。アプリケーション パッケージは、この日付以前に作成されたプール内のコンピューティング ノードにはデプロイされません。

この機能は、[Batch REST API][api_rest] バージョン 2015-12-01.2.2、および対応する [Batch .NET][api_net] ライブラリ バージョン 3.1.0 で導入されました。Batch の運用には、常に最新の API バージョンを使用することをお勧めします。

> [AZURE.IMPORTANT] 現在のところ、*CloudServiceConfiguration* プールだけがアプリケーション パッケージをサポートしています。VirtualMachineConfiguration イメージを使用して作成されたプールではアプリケーション パッケージを使用できません。2 つの異なる構成の詳細については、「[Azure Batch プールの Linux コンピューティング ノードのプロビジョニング](batch-linux-nodes.md)」の「[仮想マシンの構成](batch-linux-nodes.md#virtual-machine-configuration)」セクションを参照してください。

## アプリケーションとアプリケーション パッケージについて

Azure Batch では、"*アプリケーション*" という用語は、プール内のコンピューティング ノードに自動でダウンロード可能なバージョン付きバイナリのセットを指します。また、"*アプリケーション パッケージ*" という用語は、そうしたバイナリの "*特定のセット*" を指し、アプリケーションの特定の "*バージョン*" を表します。

![High-level diagram of applications and application packages][1]

### アプリケーション

Batch 内のアプリケーションは、1 つ以上のアプリケーション パッケージを含んでおり、アプリケーションの構成オプションを指定します。たとえば、アプリケーションは、コンピューティング ノードにインストールする既定のアプリケーション パッケージのバージョンや、パッケージを更新または削除できるかどうかなどを指定します。

### アプリケーション パッケージ

アプリケーション パッケージは、タスクで実行するために必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイルです。各アプリケーション パッケージは特定のバージョンのアプリケーションを表します。Batch サービス内にプールを作成するときに、これらのアプリケーションの 1 つ以上と、必要に応じてバージョンを指定できます。これらのアプリケーション パッケージは自動的にダウンロードされ、各ノードがプールに参加するときにそのノードに抽出されます。

> [AZURE.IMPORTANT] Batch アカウント内のアプリケーションとアプリケーション パッケージの数には、最大アプリケーション パッケージ サイズと同様に制限があります。これらの制限の詳細については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

### アプリケーション パッケージの利点

アプリケーション パッケージは、Batch ソリューションのコードを簡略化すると共に、タスクが実行されるアプリケーションの管理に必要なオーバーヘッドを軽減します。

アプリケーション パッケージを使用すると、プールのスタート タスクで、ノードにインストールする多数のリソース ファイルを指定せずに済みます。Azure Storage やノードで、これらのファイルを複数バージョンにまたがって管理する手間も省けます。さらに、Azure ストレージ アカウントでのファイル アクセスを提供する [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) の生成に苦労することもありません。

Batch はバックグラウンドで Azure Storage と連携して、アプリケーション パッケージをコンピューティング ノードに保存してデプロイすることで、コードと管理オーバーヘッドの両方を簡略化します。

## アプリケーションのアップロードと管理

Azure ポータルでは、アプリケーション パッケージを追加、更新、削除できます。各アプリケーションの既定のバージョンを構成できます。

以降のセクションでは、まず、ストレージ アカウントを Batch アカウントに関連付ける方法を説明してから、Azure ポータルで利用できるパッケージ管理機能を確認する方法を説明します。その後、[Batch .NET][api_net] ライブラリを使用してこれらのパッケージをコンピューティング ノードにデプロイする方法について説明します。

### ストレージ アカウントのリンク

アプリケーション パッケージを使用するには、最初に Azure ストレージ アカウントを Batch アカウントにリンクする必要があります。Batch アカウントに対してストレージ アカウントを構成していない場合は、**[Batch アカウント]** ブレードで初めて **[アプリケーション]** タイルをクリックしたときに、Azure ポータルに警告が表示されます。

> [AZURE.IMPORTANT] 「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の手順 5.「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」で説明されているように、Batch では、現時点で**汎用**のストレージ アカウントの種類 "*のみ*" がサポートされています。Azure ストレージ アカウントを Batch アカウントにリンクする場合は、**汎用**のストレージ アカウント "*のみ*" をリンクしてください。

![No storage account configured warning in Azure portal][9]

Batch サービスは関連付けられたストレージ アカウントを使用して、アプリケーション パッケージのストレージと取得を行います。2 つのアカウントをリンクした後、Batch は、リンクされたストレージ アカウントに保存されているパッケージをコンピューティング ノードに自動的にデプロイできるようになります。**[警告]** ブレードで **[ストレージ アカウントの設定]** をクリックし、次に **[ストレージ アカウント]** ブレードの **[ストレージ アカウント]** をクリックして、ストレージ アカウントを Batch アカウントにリンクします。

![Choose storage account blade in Azure portal][10]

Batch アカウントで使用するストレージ アカウントは "*専用*" のものを作成することをお勧めします。ここでは、そのようにします。ストレージ アカウントの作成方法の詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「ストレージ アカウントの作成」セクションを参照してください。ストレージ アカウントを作成したら、**[ストレージ アカウント]** ブレードを使用して Batch アカウントにリンクすることができます。

> [AZURE.WARNING] Batch は Azure Storage を使用してアプリケーション パッケージを保存するため、ブロック BLOB データの[通常料金][storage_pricing]が課金されます。アプリケーション パッケージのサイズと数に気を配り、使用していないパッケージを定期的に削除して、コストを最小限に抑えてください。

### 現在のアプリケーションの表示

Batch アカウントに含まれているアプリケーションを表示するには、**[Batch アカウント]** ブレードで **[アプリケーション]** タイルをクリックします。

![Applications tile][2]

次のように **[アプリケーション]** ブレードが開きます。

![List applications][3]

**[アプリケーション]** ブレードには、アカウント内に存在する各アプリケーションの ID と以下のプロパティが表示されます。

* **[パッケージ]** -- このアプリケーションに関連するバージョン数。
* **[既定のバージョン]** -- プールにアプリケーションを設定する際にバージョンを指定しない場合にインストールされるバージョン。この設定はオプションです。
* **[更新を許可する]** -- パケージの更新、削除、および追加を許可するかどうかを指定する値。**[いいえ]** に設定されている場合は、アプリケーションのパッケージの更新と削除が無効になります。新しいアプリケーション パッケージ バージョンの追加のみが可能です。既定では、**[はい]** です。

### アプリケーションの詳細の表示

**[アプリケーション]** ブレードでアプリケーションをクリックすると、そのアプリケーションの詳細を含むブレードが表示されます。

![Application details][4]

このアプリケーションの詳細ブレードでは、アプリケーションに関する以下の設定を構成することができます。

* **[更新を許可する]** -- アプリケーション パッケージを更新または削除できるかどうかを指定します。この記事の後半にある「アプリケーション パッケージの更新または削除」を参照してください。
* **[既定のバージョン]** -- コンピューティング ノードにデプロイする既定のアプリケーション パッケージを指定します。
* **[表示名]** -- Batch を介して顧客に提供するサービスの UI など、アプリケーションの情報を表示するときに Batch ソリューションが使用できる "わかりやすい" 名前を指定します。

### 新しいアプリケーションの追加

新しいアプリケーションを作成するには、アプリケーション パッケージを追加し、新しい一意のアプリケーション ID を指定します。新しいアプリケーション ID を使用して最初のアプリケーション パッケージを追加すると、新しいアプリケーションも作成されます。

**[アプリケーション]** ブレードで **[追加]** をクリックし、**[新しいアプリケーション]** ブレードを開きます。

![New application blade in Azure portal][5]

**[新しいアプリケーション]** ブレードには、新しいアプリケーションとアプリケーション パッケージの設定を指定できる以下のフィールドがあります。

**[アプリケーション ID]**

このフィールドでは、以下に挙げる標準の Azure Batch ID 検証ルールに従って、新しいアプリケーションの ID を指定します。

* 英数字の組み合わせを使用し、ハイフンとアンダースコアを含めてもよい。
* 64 文字以内にする必要がある。
* Batch アカウント内で一意にする必要がある。
* 大文字小文字は保持されるが、区別されない。

**バージョン**

アップロードするアプリケーション パッケージのバージョンを指定します。バージョン文字列は以下の検証ルールに従うようにします。

* 英数字の組み合わせを使用し、ハイフン、アンダースコア、ピリオドを含めてもよい。
* 64 文字以内にする必要がある。
* アプリケーション内で一意にする必要がある。
* 大文字小文字は保持されるが、区別されない。

**[Application package (アプリケーション パッケージ)]**

このフィールドは、アプリケーションの実行に必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイルを指定します。**[ファイルの選択]** ボックスかフォルダー アイコンをクリックして、アプリケーションのファイルが格納されている .zip ファイルを参照して選択します。

ファイルを選択したら、**[OK]** をクリックして Azure Storage へのアップロードを開始します。アップロードが完了すると、通知が表示され、ブレードが閉じられます。アップロードするファイルのサイズやネットワーク接続の速度によっては、アップロードに時間がかかることもあります。

> [AZURE.WARNING] アップロードが完了するまで、**[新しいアプリケーション]** ブレードを閉じないようにしてください。途中で閉じると、アップロード プロセスが停止します。

### 新しいアプリケーション パッケージの追加

既存のアプリケーションに対して、新しいアプリケーション パッケージ バージョンを追加するには、**[アプリケーション]** ブレードでアプリケーションを選択し、**[パッケージ]**、**[追加]** の順にクリックして、**[パッケージの追加]** ブレードを開きます。

![Add application package blade in Azure portal][8]

上の図のように、**[新しいアプリケーション]** ブレードの場合とフィールドは同じですが、**[アプリケーション ID]** ボックスは使用できなくなっています。新しいアプリケーションの場合と同様に、新しいパッケージの**バージョン**を指定して、**アプリケーション パッケージ**の .zip ファイルを参照し、**[OK]** をクリックしてパッケージをアップロードします。

### アプリケーション パッケージの更新または削除

既存のアプリケーション パッケージを更新または削除するには、対象アプリケーションの詳細ブレードを開き、**[パッケージ]** をクリックして **[パッケージ]** ブレードを開きます。次に、変更するアプリケーション パッケージの行の**省略記号**をクリックして、実行する操作を選択します。

![Update or delete package in Azure portal][7]

**更新**

**[更新]** をクリックすると、*[パッケージの更新]* ブレードが表示されます。このブレードは *[New application package (新しいアプリケーション パッケージ)]* ブレードと似ていますが、パッケージ選択フィールドのみが有効になっていて、アップロードする新しい ZIP ファイルを指定できます。

![Update package blade in Azure portal][11]

**削除**

**[削除]** をクリックすると、当該パッケージ バージョンを削除するかどうかを確認するメッセージが表示され、承諾すると Batch がそのパッケージを Azure Storage から削除します。アプリケーションの既定のバージョンを削除すると、そのアプリケーションの**既定のバージョン**の設定も削除されます。

![Delete application][12]

## コンピューティング ノードへのアプリケーションのインストール

ここまで、Azure ポータルにおけるアプリケーション パッケージのアップロードと管理について説明してきました。これでコンピューティング ノードにアプリケーション パッケージをデプロイし、Batch タスクを使用して実行する方法について説明する準備ができました。

プール内のコンピューティング ノードにアプリケーション パッケージをインストールするには、対象のプールに対してアプリケーション パッケージへの "*参照*" を 1 つ以上指定します。Batch .NET で、作成する新しいプールまたは既存のプールに 1 つ以上の [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] を追加します。

[ApplicationPackageReference][net_pkgref] クラスは、プールのコンピューティング ノードにインストールするアプリケーション ID とバージョンを指定します。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

プールに指定したアプリケーション パッケージは、コンピューティング ノードがプールに参加したときや再起動されたとき、再イメージ化されたときに各ノードにインストールされます。アプリケーション パッケージのデプロイがなんらかの理由で失敗した場合、そのノードは、Batch サービスによって[使用不可][net_nodestate]としてマークされます。そのノードに対しては、タスクの実行がスケジュールされません。この場合、パッケージのデプロイを再開するには、ノードを**再起動**する必要があります。ノードを再起動すると、そのノードでのタスクのスケジュールももう一度有効になります。

## インストールしたアプリケーションの実行

各コンピューティング ノードがプールに参加したり、再起動または再イメージ化されたりすると、指定したパッケージがダウンロードされ、ノードの `AZ_BATCH_ROOT_DIR` 内の名前付きディレクトリに抽出されます。また、Batch により、タスクのコマンド ラインでアプリケーション バイナリを呼び出す際に使う環境変数も作成されます。この変数は、次の名前付けスキームに従います。

`AZ_BATCH_APP_PACKAGE_appid#version`

たとえば、*blender* というアプリケーションのバージョン 2.7 をインストールするように指定した場合、タスクはコマンド ラインで次の環境変数を参照し、対象のバイナリにアクセスします。

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

既定バージョンのアプリケーションを指定するときは、バージョン文字列のサフィックスを付けずに環境変数を参照できます。たとえば、Azure ポータルで *blender* アプリケーションの既定バージョン 2.7 を指定している場合、タスクは次の環境変数を参照できます。

`AZ_BATCH_APP_PACKAGE_BLENDER`

次のコード スニペットは、*blender* アプリケーションに既定バージョンを指定した場合のタスク構成例を示しています。

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] コンピューティング ノードの環境設定の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページの「タスクの環境設定」を参照してください。

## プールに含まれるアプリケーション パッケージの更新

アプリケーション パッケージで構成済みの既存プールに対して、新しいパッケージを指定することができます。プールに対して新しいパッケージ参照を指定すると、次のようになります。

* プールに参加する新しいノードすべてと、再起動または再イメージ化される既存のノードにより、新しく指定されたパッケージがインストールされます。
* パッケージへの参照を更新したときに、既にプールに参加していたコンピューティング ノードに対し、新しいアプリケーション パッケージが自動でインストールされることはありません。新しいパッケージを受け取るには、これらのコンピューティング ノードを再起動または再イメージ化する必要があります。
* 新しいパッケージがデプロイされると、作成された環境変数に新しいアプリケーション パッケージ参照が反映されます。

この例では、既存のプールに、[CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] の 1 つとして *blender* アプリケーションのバージョン 2.7 が構成されています。プールのノードをバージョン 2.76b で更新するには、新しい [ApplicationPackageReference][net_pkgref] で新しいバージョンを指定し、変更をコミットします。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

新しいバージョンを構成したので、"*新たに*" プールに参加するノードでは、バージョン 2.76b がデプロイされます。プール内の "*既存*" のノードに 2.76b をインストールするには、ノードを再起動または再イメージ化します。再起動されたノードは、以前のパッケージ デプロイのファイルを保持することに注意してください。

## Batch アカウント内のアプリケーションの一覧表示

[ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] メソッドを使用して、Batch アカウント内のアプリケーションとパッケージを一覧表示することができます。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## まとめ

アプリケーション パッケージを使用すると、顧客がジョブ用のアプリケーションを選択するのを支援したり、Batch を有効にしたサービスでジョブを処理する場合の正確なバージョン指定を行ったりすることができます。さらに、顧客がサービスに自前のアプリケーションをアップロードし、トラッキングするための環境も用意できます。

## 次のステップ

* [Batch REST API][api_rest] も、アプリケーション パッケージの運用をサポートしています。たとえば、REST API を使用したインストール パッケージの指定方法については、「[Add a pool to an account (アカウントへのプールの追加)][rest_add_pool]」の [applicationPackageReferences][rest_add_pool_with_packages] 要素を参照してください。Batch REST API を使用したアプリケーション情報の取得方法の詳細については、「[Applications (アプリケーション)][rest_applications]」を参照してください。

* [Batch Management .NET でプログラムを使用して Azure Batch アカウントとクォータを管理する](batch-management-dotnet.md)方法を学習してください。[Batch Management .NET][api_net_mgmt] ライブラリで、Batch アプリケーションまたはサービス用のアカウント作成機能と削除機能を有効にすることができます。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Application packages high-level diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Applications tile in Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Applications blade in Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Application details blade in Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "New application blade in Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Update or delete packages drop-down in Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "New application package blade in Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "No linked Storage account alert"
[10]: ./media/batch-application-packages/app_pkg_10.png "Choose storage account blade in Azure portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Update package blade in Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Delete package confirmation dialog in Azure portal"

<!---HONumber=AcomDC_0803_2016-->