---
title: リソース ファイルを作成して使用する
description: さまざまな入力ソースから Batch リソースファイルを作成する方法について説明します。 この記事では、VM 上に作成して配置する方法について、いくつかの一般的な方法について説明します。
ms.date: 08/18/2021
ms.topic: how-to
ms.openlocfilehash: a4939cc6c60d226d8b75569ab08447973968735a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446570"
---
# <a name="creating-and-using-resource-files"></a>リソース ファイルを作成して使用する

Azure Batch タスクには多くの場合、何らかの形の処理するデータが必要です。 リソース ファイルは、このデータをタスク経由でバッチ仮想マシン (VM) に提供する手段です。 タスク、開始タスク、ジョブ準備タスク、ジョブ解放タスクなど、すべてのタイプのタスクがリソース ファイルをサポートします。この記事は、リソース ファイルを作成し、それらを VM 上に配置するいくつかの一般的な方法について説明します。  

リソース ファイルはバッチ内の VM にデータを格納しますが、データの型とその使用方法は柔軟に選択できます。 ただし、いくつかの一般的なユース ケースがあります。

- 共通ファイルを開始タスクでリソース ファイルを使用して各 VM 上にプロビジョニングします。
- タスクによって処理される入力データをプロビジョニングします。

共通ファイルとは、たとえば、タスクが実行するアプリケーションをインストールするために使用される開始タスクのファイルです。 入力データは、バッチにより処理される raw 画像やビデオ データ、その他の任意の情報が可能です。

## <a name="types-of-resource-files"></a>リソース ファイルの種類

リソース ファイルの生成に使用できるいくつかの異なるオプションがあり、それぞれの[方法](/dotnet/api/microsoft.azure.batch.resourcefile#methods)は異なります。 リソース ファイルの作成プロセスは、元のデータが格納される場所と、複数のファイルを作成するかどうかによって異なります。

- [ストレージ コンテナーの URL](#storage-container-url): Azure で任意のストレージ コンテナーからリソース ファイルを生成します。
- [ストレージ コンテナー名](#storage-container-name-autostorage): Batch アカウント (autostorage アカウント) にリンクされている Azure ストレージ アカウントで、コンテナーの名前からリソース ファイルを生成します。
- [Web エンドポイントからの単一のリソース ファイル](#single-resource-file-from-web-endpoint): 任意の有効な HTTP URL から 1 つのリソース ファイルを生成します。

### <a name="storage-container-url"></a>ストレージ コンテナーの URL

ストレージ コンテナーの URL を使用するということは、適切なアクセス許可があれば、Azure にあるどのストレージ コンテナー内のファイルにもアクセスできることを意味します。

この C# の例では、ファイルは、Blob ストレージとして Azure ストレージ コンテナーに既にアップロードされています。 リソース ファイルを作成するために必要なデータにアクセスするには、まずストレージ コンテナーにアクセスする必要があります。 これはいくつかの方法で実行できます。

#### <a name="shared-access-signature"></a>Shared Access Signature

ストレージ コンテナーにアクセスするための正しいアクセス許可を使用して、共有アクセス署名 (SAS) URI を作成します。 SAS に有効期限の時間とアクセス許可を設定します。 この場合、開始時刻が指定されていないため、SAS がすぐに有効になり、生成後 2 時間で有効期限が切れます。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> コンテナーへのアクセスでは、`Read` と `List` の両方のアクセス許可が必要ですが、BLOB へのアクセスでは、必要なのは `Read` アクセス権限のみです。

アクセス許可を構成したら、SAS トークンを作成し、ストレージ コンテナーにアクセスするための SAS URL を書式設定します。 ストレージ コンテナーの書式設定された SAS URL を使用して、[FromStorageContainerUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) でリソース ファイルを生成します。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

必要に応じて、[blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) プロパティを使用して、名前が特定のプレフィックスで始まる BLOB のみにダウンロードを制限することができます。

```csharp
ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl, blobPrefix = yourPrefix);
```

#### <a name="managed-identity"></a>マネージド ID

[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を作成し、Azure Storage コンテナーの `Storage Blob Data Reader` ロールにそれを割り当てます。 次に、VM で ID にアクセスできるよう、[マネージド ID をプールに割り当てます](managed-identity-pools.md)。 最後になりますが、使用する Batch の ID を指定することでコンテナー内のファイルにアクセスできます。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(container.Uri, identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name" });
```

#### <a name="public-access"></a>パブリック アクセス

SAS URL を生成する代わりに、あるいはマネージド ID を使用する代わりに、Azure Blob Storage のコンテナーとその BLOB に対する匿名のパブリック読み取りアクセスを有効にすることもできます。 そうすることで、アカウント キーを共有せず、SAS も必要とせずに、これらのリソースに対する読み取り専用のアクセスを付与できます。 パブリック アクセスは、特定の BLOB を匿名読み取りアクセスで常に使用できるようにするシナリオに一般的に使用されます。 このシナリオがご自分ソリューションに適している場合、BLOB データへのアクセスを管理する方法の詳細については、「[コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](../storage/blobs/anonymous-read-access-configure.md)」を参照してください。

### <a name="storage-container-name-autostorage"></a>ストレージ コンテナー名 (autostorage)

SAS URL を構成して作成する代わりに、BLOB データにアクセスするために Azure ストレージ コンテナーの名前を使用できます。 使用するストレージ コンテナーは、*autostorage アカウント* とも呼ばれる Batch アカウントにリンクされている Azure ストレージ アカウントにある必要があります。 autostorage コンテナーを使用すると、ストレージ コンテナーにアクセスするための SAS URL の構成と作成をバイパスすることができます。 代わりに、リンクされたストレージ アカウントでストレージ コンテナーの名前を指定します。

Autostorage アカウントをまだ持っていない場合、ストレージ アカウントを作成してリンクする方法の詳細については、[Batch アカウントの作成](batch-account-create-portal.md)の手順を参照してください。

次の例では、[AutoStorageContainer](/dotnet/api/microsoft.azure.batch.resourcefile.fromautostoragecontainer) を使用して、autostorage アカウントのデータからファイルを生成します。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

ストレージ コンテナーの URL と同様に、[blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) プロパティを使用して、ダウンロードする BLOB を指定できます。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName, blobPrefix = yourPrefix);
```

### <a name="single-resource-file-from-web-endpoint"></a>Web エンドポイントからの単一のリソース ファイル

1 つのリソース ファイルを作成するには、入力データを含む有効な HTTP URL を指定します。 URL は Batch API に提供され、データはリソース ファイルを作成するために使用されます。 このメソッドは、リソース ファイルを作成するデータが Azure Storage 内にあるか、GitHub エンドポイントなどの他の Web の場所にある場合でも使用できます。

次の例では、[FromUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromurl) を使用して、有効な URL を含む文字列からファイルを取得し、タスクで使用するリソース ファイルを生成します。 このシナリオには資格情報は必要ありません。 (BLOB コンテナーでパブリック読み取りアクセスが有効になっていない限り、BLOB ストレージを使用する場合は資格情報が必要です)。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURL, filePath);
```

また、URL として定義する文字列 (または、ファイルの完全な URL となる文字列の組み合わせ) を使用することもできます。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourDomain + yourFile, filePath);
```

ファイルが Azure Storage にある場合は、リソース ファイルの Shared Access Signature を生成する代わりにマネージド ID を使用できます。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURLFromAzureStorage, 
    identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"},
    filePath: filepath
);
```

> [!Note]
> マネージド ID 認証は、Azure Storage 内のファイルでのみ機能します。 マネージド ID には、ファイルが入っているコンテナーの `Storage Blob Data Reader` ロール割り当てが必要です。また、[Batch プールに割り当てる](managed-identity-pools.md)必要もあります。

## <a name="tips-and-suggestions"></a>ヒントと検索候補

Azure Batch のタスクでは、さまざまな方法でファイルが使用されます。そのため、Batch ではタスクでファイルを管理するためのさまざまなオプションを提供しています。 次のシナリオは、包括的なものとなるようには意図しておらず、いくつかの一般的な状況を扱い、推奨事項を提供することを意図しています。

### <a name="many-resource-files"></a>多くのリソース ファイル

Batch ジョブ内の多くのタスク間で共通のタスク ファイルが共有されている場合は、[アプリケーション パッケージ](batch-application-packages.md)を使用してそれらのファイルを含めることもできます。 アプリケーション パッケージはダウンロード速度の最適化を提供し、アプリケーション パッケージ内のデータはタスク間でキャッシュされます。 アプリケーション パッケージを使用すると、複数のリソース ファイルを手動で管理したり、Azure Storage 内のファイルにアクセスするために SAS URL を生成したりする必要はありません。 Batch は、バックグラウンドで Azure Storage と連携して、アプリケーション パッケージを格納したりコンピューティング ノードにデプロイしたりします。 タスク ファイルが頻繁に変更されていない場合は、アプリケーション パッケージがソリューションに適している可能性があります。

逆に、タスクに固有のファイルが多い場合は、リソース ファイルが最適なオプションです。 一意のファイルを使用するタスクは多くの場合、更新または交換する必要があるため、アプリケーション パッケージのコンテンツを使用して実行するほど簡単ではありません。 リソース ファイルには、個々のファイルの更新、追加、または編集のためのさらに高い柔軟性があります。

### <a name="number-of-resource-files-per-task"></a>タスクあたりのリソース ファイルの数

タスクに数百のリソース ファイルが指定されている場合、Batch はそのタスクが大きすぎるために拒否することがあります。 タスク自体のリソース ファイルの数を最小限に抑えることで、タスクを小さくしておくことをお勧めします。

タスクが必要とするファイル数を最小限に抑える方法がない場合、リソース ファイルのストレージ コンテナーを参照する 1 つのリソース ファイルを作成することで、タスクを最適化できます。 これを行うには、リソース ファイルを Azure Storage コンテナーに配置し、前述のいずれかの方法を使用して、必要に応じてリソース ファイルを生成します。

## <a name="next-steps"></a>次のステップ

- リソース ファイルの代替としての[アプリケーション パッケージ](batch-application-packages.md)について説明します。
- リソース ファイルに[コンテナーを使用する](batch-docker-container-workloads.md)方法について説明します。
- [タスクからの出力データを収集して保存する](batch-task-output.md)方法について参照してください。
- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
