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
	ms.date="03/14/2016"
	ms.author="marsma" />

# Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ

Azure Batch のアプリケーション パッケージ機能を使用すると、管理が容易になり、プール内のコンピューティング ノードにアプリケーションをデプロイしやすくなります。アプリケーション パッケージを使用すると、タスクで実行するアプリケーションのバイナリとサポート ファイルを複数バージョン分、アップロードして管理し、これらのアプリケーションを 1 つでも複数でも、プール内のコンピューティング ノードに自動でデプロイできます。

この記事では、Azure ポータルでアプリケーション パッケージをアップロードし、管理する方法と、その後、[Batch .NET][api_net] ライブラリを使用して、それらのパッケージをプールのコンピューティング ノードにインストールする方法を説明します。

> [AZURE.NOTE] ここで説明されているアプリケーション パッケージの機能は、以前のバージョンのサービスで利用できる "Batch Apps" 機能に優先します。

## アプリケーション パッケージの要件

この記事で取り上げるアプリケーション パッケージ機能は、2016 年 3 月 10 日以降に作成された Batch プールと*のみ*互換性があります。アプリケーション パッケージは、この日付以前に作成されたプール内のコンピューティング ノードにはデプロイされません。

この機能は、[Batch REST API][api_rest] バージョン 2015-12-01.2.2、および対応する [Batch .NET][api_net] ライブラリ バージョン 3.1.0 で導入されました。Batch の運用には、常に最新の API バージョンを使用することをお勧めします。

## アプリケーションとアプリケーション パッケージについて

Azure Batch では、**アプリケーション**という用語で、プール内のコンピューティング ノードに自動でダウンロード可能なバージョン付きバイナリのセットを指します。また、**アプリケーション パッケージ**という用語で、そうしたバイナリの*特定のセット*を指し、アプリケーションのいずれかの*バージョン*を表します。

![High-level diagram of applications and application packages][1]

### アプリケーション

Batch 内のアプリケーションには、1 つ以上のアプリケーション パッケージが含まれます。コンピューティング ノードにインストールする既定のアプリケーション パッケージのバージョンや、パッケージを更新または削除するかどうかなど、アプリケーション用の構成オプションを規定します。

### アプリケーション パッケージ

アプリケーション パッケージは、アプリケーション バイナリとタスクの実行に必要なサポート ファイルを含む ZIP ファイルです。各アプリケーション パッケージは特定のバージョンのアプリケーションを表します。Batch サービスでプールを作成するときに、これらのアプリケーションを 1 つ以上、(必要に応じて) バージョンと共に指定でき、そうしておくと各ノードがプールに参加するたびに、指定したアプリケーション パッケージが自動でダウンロードされます。

> [AZURE.IMPORTANT] Batch アカウント内のアプリケーションとアプリケーション パッケージの数には、最大アプリケーション パッケージ サイズと同様に制限があります。これらの制限の詳細については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

### アプリケーション パッケージの利点

アプリケーション パッケージは、Batch ソリューションのコードを簡略化すると共に、タスクを実行するアプリケーションの管理に必要なオーバーヘッドを軽減します。

アプリケーション パッケージを使用すると、プールのスタート タスクで、ノードにインストールする多数のリソース ファイルを指定せずに済みます。Azure Storage やノードで、これらのファイルを複数バージョンにまたがって管理する手間も省けます。さらに、Azure ストレージ アカウントでのファイル アクセスを提供する [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) の生成に苦労することもありません。

Batch はバックグラウンドで綿密に Azure Storage を運用し、アプリケーション パッケージをコンピューティング ノードに保存してデプロイするので、コードと管理オーバーヘッドの両方が簡略化されます。

## アプリケーションのアップロードと管理

Azure ポータルでは、アプリケーション パッケージを追加、更新、削除でき、各アプリケーションの既定のバージョンを構成することができます。現時点で、これらの操作は Azure ポータルでのみサポートされています。

以降の数セクションでは、ストレージ アカウントと Batch アカウントの関連付けについて説明し、Azure ポータルで利用できるパッケージ管理機能を確認します。次に、[Batch .NET][api_net] ライブラリを使用して、これらのパッケージをコンピューティング ノードにデプロイする方法について説明します。

### ストレージ アカウントのリンク

アプリケーション パッケージを使用するには、最初に Azure ストレージ アカウントを Batch アカウントにリンクする必要があります。Batch アカウントに対してストレージ アカウントを構成していない場合は、[Batch アカウント] ブレードで最初に *[アプリケーション]* タイルをクリックしたときに、Azure ポータルに警告が表示されます。

![No storage account configured warning in Azure portal][9]

Batch サービスは関連付けられたストレージ アカウントを使用して、アプリケーション パッケージのストレージと取得を行います。2 つのアカウントをリンクすると、Batch はリンクされたストレージ アカウントで保存しているパッケージをコンピューティング ノードに自動でデプロイできるようになります。*[警告]* ブレードで **[ストレージ アカウント設定]** をクリックし、次に *[ストレージ アカウント]* ブレードの **[ストレージ アカウント]** をクリックして、ストレージ アカウントを Batch アカウントにリンクします。

![Choose storage account blade in Azure portal][10]

Batch アカウントで使用するストレージ アカウントは*専用*のものを作成することをお勧めします。ここでは、そのようにします。ストレージ アカウントの作成の詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「ストレージ アカウントの作成」セクションを参照してください。ストレージ アカウントを作成したら、[*ストレージ アカウント*] ブレードを使用して Batch アカウントにリンクすることができます。

> [AZURE.WARNING] Batch は Azure Storage を使用してアプリケーション パッケージを保存するので、ブロック BLOB データの[通常料金][storage_pricing]が課金されます。アプリケーション パッケージのサイズと数に気を配り、使用していないパッケージを定期的に削除して、コストを最小限に抑えてください。

### 現在のアプリケーションの表示

Batch アカウントに含まれているアプリケーションを表示するには、[Batch アカウント] ブレードで **[アプリケーション]** タイルをクリックします。

![Applications tile][2]

次のように *[アプリケーション]* ブレードが開きます。

![List applications][3]

*[アプリケーション]* ブレードには、アカウント内に存在する各アプリケーションの ID と以下のプロパティが表示されます。

* **[パッケージ]** – このアプリケーションに関連するバージョン数。
* **[既定のバージョン]** – プールに対するアプリケーションの設定時にバージョンを指定しない場合は、このバージョンがインストールされます。この設定はオプションです。
* **[更新を許可する]** – この設定を *[いいえ]* にすると、そのアプリケーションではパッケージを更新または削除できなくなり、新しいアプリケーション パッケージ バージョンの追加のみが可能になります。既定では、*[はい]* です。

### アプリケーションの詳細の表示

*[アプリケーション]* ブレードでいずれかのアプリケーションをクリックすると、そのアプリケーションの詳細を示すブレードが表示されます。

![Application details][4]

このアプリケーションの詳細ブレードでは、アプリケーションに関する以下の設定を構成することができます。

* **[更新を許可する]** - アプリケーション パッケージを更新または削除できるかどうかを指定します (下記「アプリケーション パッケージの更新または削除」を参照してください)。
* **[既定のバージョン]** - コンピューティング ノードにデプロイする既定のアプリケーション パッケージを指定します。
* **[表示名]** - これは、Batch を介して顧客に提供するサービスの UI など、アプリケーションの情報を表示するときに Batch ソリューションが使用できる "わかりやすい" 名前です。

### 新しいアプリケーションの追加

新しいアプリケーションを作成するには、新しい一意のアプリケーション ID を使用してアプリケーション パッケージを追加します。新しいアプリケーション ID を使用して最初のアプリケーション パッケージを追加する際に、新しいアプリケーションも作成します。

*[アプリケーション]* ブレードで **[追加]** をクリックし、*[新しいアプリケーション]* ブレードを開きます。

![New application blade in Azure portal][5]

*[新しいアプリケーション]* ブレードには、新しいアプリケーションとアプリケーション パッケージを設定できる以下のフィールドがあります。

**Metadata**

アプリケーション メタデータの入力には、**[アプリケーション ID]** と **[バージョン]** の各ボックスに値を直接入力する方法と、メタデータを含む JSON ファイルをアップロードする方法があります。アプリケーション ID とバージョンを直接入力するには、**[メタデータ]** ドロップダウン セレクターの設定を **[メタデータを入力]** (既定) のままにして、**[アプリケーション ID]** と **[バージョン]** の各ボックスに手動で値を入力します。

パッケージ用の ID とバージョンを含む JSON 形式のメタデータ ファイルを指定するには、**[メタデータ]** ドロップダウンから **[メタデータ ファイルをアップロード]** を選択します。

![Upload metadata file drop-down selector][6]

次に、**[メタデータ ファイル]** ボックスの隣に表示されるフォルダー アイコンをクリックし、JSON データを含むローカル ファイルを参照します。この例では、アップロードするファイルとして `litware_1.1001.2b.json` を選択しました。すると、**[アプリケーション ID]** と **[バージョン]** の両ボックスに自動でファイルの情報が入力されました。

![Metadata file selection detail][13]

ファイルでアプリケーション パッケージのメタデータを指定するには、次の JSON 形式を使用します。

```
{
    "id": "litware",
    "version": "1.1001.2b"
}
```

> [AZURE.NOTE] ID とバージョンを含む JSON メタデータ ファイルをアップロードする場合は、[アプリケーション ID] または [バージョン] ボックスを編集する必要は*ありません*。これらには、JSON ファイルのデータが自動で入力されます。

**[アプリケーション ID]**

以下に挙げる標準の Azure Batch ID 検証ルールに従って、新しいアプリケーションの ID を指定します。

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

アプリケーションの実行に必要なアプリケーション バイナリとサポート ファイルを含む ZIP ファイルを指定します。**[ファイルの選択]** ボックスかフォルダー アイコンをクリックして、アプリケーションのファイルを格納している ZIP ファイルを参照し、選択します。

ファイルを選択したら、**[OK]** をクリックして Azure Storage へのアップロードを開始します。アップロードが完了したら、通知が表示され、ブレードが閉じます。アップロードするファイルのサイズやネットワーク接続の速度によっては、アップロードに時間がかかることもあります。

> [AZURE.WARNING] アップロードが完了するまで、*[新しいアプリケーション]* ブレードを閉じないようにしてください。途中で閉じると、アップロード プロセスが中断されます。

### 新しいアプリケーション パッケージの追加

既存のアプリケーションに対して、新しいアプリケーション パッケージ バージョンを追加するには、*[アプリケーション]* ブレードでアプリケーションを選択し、**[パッケージ]**、**[追加]** の順にクリックして、*[パッケージの追加]* ブレードを表示します。

![Add application package blade in Azure portal][8]

上の図のように、*[新しいアプリケーション]* ブレードの場合とフィールドは同じですが、唯一、[アプリケーション ID] ボックスが無効になっている点が異なります。上記の手順で、新しいパッケージの**バージョン**を指定して、**アプリケーション パッケージ**の ZIP ファイルを選択し、**[OK]** をクリックしてパッケージをアップロードします。

### アプリケーション パッケージの更新または削除

既存のアプリケーション パッケージを更新または削除するには、対象アプリケーションの詳細ブレードを開き、**[パッケージ]** をクリックして *[パッケージ]* ブレードを表示します。次に、編集したいアプリケーション パッケージの行で**省略記号**をクリックして、操作を選択します。

![Update or delete package in Azure portal][7]

**更新**

**[更新]** をクリックすると、*[パッケージの更新]* ブレードが表示されます。このブレードは *[新しいアプリケーション パッケージ]* ブレードと似ていますが、パッケージ選択フィールドだけが有効になっていて、アップロードする新しい ZIP ファイルを指定できます。

![Update package blade in Azure portal][11]

**削除**

**[削除]** をクリックすると、当該パッケージ バージョンを本当に削除するかどうかの確認メッセージが表示され、承認すると Batch がそのパッケージを Azure Storage から削除します。アプリケーションの既定バージョンを削除すると、その既定バージョン設定も削除されます。

![Delete application][12]

## コンピューティング ノードへのアプリケーションのインストール

ここまで、Azure ポータルにおけるアプリケーション パッケージのアップロードと管理について説明してきました。これでコンピューティング ノードにアプリケーション パッケージをデプロイし、Batch タスクで実行する準備ができました。

プール内のコンピューティング ノードにアプリケーション パッケージをインストールするには、対象のプールに対してアプリケーション パッケージへの*参照*を 1 つ以上指定します。この指定を行うには、Batch .NET でプールの作成時に、または既存のプールに対して、1 つ以上の [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] を追加します。

[ApplicationPackageReference][net_pkgref] クラスは、プールのコンピューティング ノードにインストールするアプリケーション ID とバージョンを指定します。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(poolId: "myPool",
                                          osFamily: "4",
                                          virtualMachineSize: "small",
                                          targetDedicated: "1");

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

## インストールしたアプリケーションの実行

それぞれのコンピューティング ノードがプールに参加すると (あるいは、再起動または再イメージ化されると)、指定したパッケージがダウンロードされ、ノードの `AZ_BATCH_ROOT_DIR` 内の名前付きディレクトリに抽出されます。Batch により、タスクのコマンド ラインでアプリケーション バイナリを呼び出す際に使う環境変数も作成されます。この変数は次の名前付けスキームに従います。

`AZ_BATCH_APP_PACKAGE_appid#version`

たとえば、*blender* というアプリケーションのバージョン 2.7 をインストールするよう指定した場合、タスクはコマンド ラインで次の環境変数を参照し、対象のバイナリにアクセスします。

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

既定バージョンのアプリケーションを指定するときは、バージョン文字列のサフィックスを付けずに環境変数を参照できます。たとえば、Azure ポータルで *blender* アプリケーションの既定バージョン 2.7 を指定している場合、タスクは次の環境変数を参照できます。

`AZ_BATCH_APP_PACKAGE_BLENDER`

次のコード スニペットは、*blender* アプリケーションに既定バージョンを指定した場合のタスク構成例を示しています。

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] コンピューティング ノードの環境設定の詳細については、「[Azure Batch 機能の概要](batch-api-basics.md)」の「タスクの環境設定」を参照してください。

## プールに含まれるアプリケーション パッケージの更新

アプリケーション パッケージで構成済みの既存プールに対して、新しいパッケージを指定することができます。新しいノードがプールに参加するたびに、あるいは既存ノードが再起動または再イメージ化されるたびに、新しく指定されたパッケージがインストールされます。パッケージへの参照を更新したときに、既にプールに参加していたコンピューティング ノードに対し、新しいアプリケーション パッケージが自動でインストールされることはありません。

この例では、既存のプールに、[CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] の 1 つとして *blender* アプリケーションのバージョン 2.7 が構成されています。プールのノードをバージョン 2.76b に更新するには、新しい [ApplicationPackageReference][net_pkgref] で新しいバージョンを指定し、変更をコミットします。

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

新しいバージョンを構成したら、それ以降、*新しく*プールに参加するノードには、バージョン 2.76b がデプロイされます。*既に*プールに参加しているノードに 2.76b をインストールするには、ノードを再起動 (または再イメージ化) します。再起動されたノードは、以前のパッケージ デプロイのファイルを保持することに注意してください。

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

アプリケーション パッケージを使用すると、ジョブ用のアプリケーションを選択できる環境を顧客に提供しやすくなり、Batch を有効にしたサービスでジョブを処理する場合の正確なバージョン指定も容易になります。さらに、顧客がサービスに自前のアプリケーションをアップロードし、トラッキングするための環境も用意できます。

## 次のステップ

* [Batch REST API][api_rest] も、アプリケーション パッケージの運用に役立ちます。たとえば、REST API を使用したインストール パッケージの指定については、「[Add a pool to an account (アカウントへのプールの追加)][rest_add_pool]」の [applicationPackageReferences][rest_add_pool_with_packages] 要素を参照してください。Batch REST API でのアプリケーション情報の取得の詳細については、「[Applications (アプリケーション)][rest_applications]」を参照してください。

* [Batch Management .NET でプログラムを使用して Azure Batch アカウントとクォータを管理する](batch-management-dotnet.md)方法を学習してください。[Batch Management .NET][api_net_mgmt] ライブラリで、Batch アプリケーションまたはサービス用のアカウント作成機能と削除機能を有効にすることができます。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Application packages high-level diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Applications tile in Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Applications blade in Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Application details blade in Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "New application blade in Azure portal"
[6]: ./media/batch-application-packages/app_pkg_06.png "Upload metadata file drop-down selector"
[7]: ./media/batch-application-packages/app_pkg_07.png "Update or delete packages drop-down in Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "New application package blade in Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "No linked Storage account alert"
[10]: ./media/batch-application-packages/app_pkg_10.png "Choose storage account blade in Azure portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Update package blade in Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Delete package confirmation dialog in Azure portal"
[13]: ./media/batch-application-packages/app_pkg_13.png "Metadata file selection detail"

<!---HONumber=AcomDC_0323_2016-->