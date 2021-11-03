---
title: Azure Data Lake Storage Gen2 への Azure Blob Storage のアップグレード | Microsoft Docs
description: 説明がここに表示されます。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: 70f191a33eeb4d09ea8e4f14b17cd6ceadcec76e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003582"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 の機能による Azure Blob Storage のアップグレード

この記事は、ファイルやディレクトリ レベルのセキュリティや、より高速な操作などの機能を利用できるようにするのに役立ちます。 これらの機能は、ビッグ データ分析ワークロードで広く使用されており、まとめて Azure Data Lake Storage Gen2 と呼ばれます。 最も一般的な機能は次のとおりです。

- スループット、1 秒あたりの入出力操作数 (IOPS)、ストレージ容量制限の向上。

- 個々のノードの URI を操作できることによる、操作 (名前の変更操作など) の高速化。
 
- 指定された操作を実行するために必要なデータのみを転送する効率的なクエリ エンジン。

- コンテナー、ディレクトリ、ファイル レベルのセキュリティ。

これらの詳細については、「[Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)」を参照してください。

この記事は、ワークロード、アプリケーション、コスト、サービス統合、ツール、機能、ドキュメントへの影響を評価するのに役立ちます。 これらの影響を慎重に確認してください。 アカウントをアップグレードする準備ができたら、手順ガイド「[Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)」を参照してください。

> [!IMPORTANT]
> アップグレードは一方向です。 アップグレードを実行した後で、アカウントを元に戻す方法はありません。 非運用環境でアップグレードを検証することをお勧めします。

## <a name="impact-on-availability"></a>可用性への影響

アップグレード プロセスが行われている間、アカウントで若干のダウンタイムが発生することを必ず計画してください。 アカウントがアップグレードされている間、書き込み操作は無効になります。 読み取り操作は無効になりませんが、それらの操作によってアップグレード プロセスが不安定になる可能性があるため、読み取り操作を中断することを強く推奨します。

## <a name="impact-on-workloads-and-applications"></a>ワークロードとアプリケーションへの影響

Blob API は、階層型名前空間を使用するアカウントで動作するため、これらの API を使用してアカウントと対話するほとんどのアプリケーションは、変更しなくても引き続き動作します。

問題と回避策の完全な一覧については、[Blob Storage API での既知の問題](data-lake-storage-known-issues.md#blob-storage-apis)に関する記事を参照してください。

Windows Azure Storage Blob ドライバーまたは [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) ドライバーを使用する Hadoop ワークロードは、[Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) ドライバーを使用するように変更する必要があります。 **Blob service** エンドポイントに対して要求を行う WASB ドライバーとは異なり、ABFS ドライバーはアカウントの **Data Lake Storage** エンドポイントに対して要求を行います。

### <a name="data-lake-storage-endpoint"></a>Data Lake Storage エンドポイント

アップグレード後のアカウントには、Data Lake Storage エンドポイントがあります。 このエンドポイントの URL は、Azure portal でアカウントの **[プロパティ]** ページを開くことにより確認できます。

> [!div class="mx-imgBorder"]
> ![汎用 v2 カテゴリ](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

そのエンドポイントを使用するために、既存のアプリケーションやワークロードを変更する必要はありません。 [Data Lake Storage でのマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)を使用すると、Blob service エンドポイントまたは Data Lake Storage エンドポイントを使用してデータを操作できるようになります。 

Azure のサービスとツール (AzCopy など) により、Data Lake Storage エンドポイントを使用して、ストレージ アカウント内のデータとのやり取りが行われることがあります。 また、[Data Lake Storage Gen2 SDK](data-lake-storage-directory-file-acl-dotnet.md)、[PowerShell コマンド](data-lake-storage-directory-file-acl-powershell.md)、または[Azure CLI コマンド](data-lake-storage-directory-file-acl-cli.md)を使用して実行するすべての操作には、この新しいエンドポイントを使用する必要があります。 

### <a name="directories"></a>ディレクトリ

階層型名前空間を使用しない BLOB ストレージ アカウントでは、ファイルは階層化されたパラダイムではなくフラットなパラダイムで編成されます。 BLOB を仮想ディレクトリに編成して、フォルダー構造を模倣することができます。 仮想ディレクトリは BLOB 名の一部を形成し、区切り文字によって示されます。 仮想ディレクトリは BLOB 名の一部であるため、独立したオブジェクトとして実際に存在してはいません。

新しいアカウントには、階層型名前空間があります。 つまり、ディレクトリは仮想ではありません。 これらは、直接操作できる具体的な独立したオブジェクトです。 ディレクトリは、ファイルが含まれていなくても存在できます。 ディレクトリを削除すると、そのディレクトリ内のすべてのファイルが削除されます。 ディレクトリを削除する前に、個々の BLOB を削除する必要はなくなりました。 

### <a name="blob-metadata"></a>BLOB のメタデータ

移行前の BLOB メタデータは、BLOB 名およびその仮想パス全体と関連付けられています。 移行後のメタデータは、BLOB にのみ関連付けられます。 BLOB への仮想パスは、ディレクトリのコレクションになります。 BLOB のメタデータは、それらのどのディレクトリにも適用されません。 

### <a name="put-operations"></a>Put 操作

BLOB をアップロードするとき、指定したパスに存在しないディレクトリが含まれていると、操作によってそのディレクトリが作成されてから、BLOB がそこに追加されます。 この動作は、階層型フォルダー構造のコンテキスト内の論理的なものです。 階層型名前空間を持たない BLOB ストレージ アカウントでは、操作によってディレクトリは作成されません。 代わりに、ディレクトリ名が BLOB の名前空間に追加されます。 

### <a name="list-operations"></a>操作の一覧表示

[List Blobs](/rest/api/storageservices/list-blobs) 操作からは、ディレクトリとファイルの両方が返されます。 それぞれが個別にリストされます。 ディレクトリは、長さが 0 の BLOB として一覧に表示されます。 階層型名前空間が使用されていない BLOB ストレージ アカウントでは、[List Blobs](/rest/api/storageservices/list-blobs) 操作から BLOB のみが返され、ディレクトリは返されません。 Data Lake Storage Gen2 の [Path - List](/rest/api/storageservices/datalakestoragegen2/path/list) 操作を使用すると、ディレクトリは、長さが 0 の BLOB としてではなく、ディレクトリ エントリとして表示されます。

リストの順序も異なります。 ディレクトリとファイルは、"*深さ優先検索*" の順序で表示されます。 階層型名前空間を持たない BLOB ストレージ アカウントでは、BLOB の一覧は "*辞書編集*" の順序で表示されます。 

### <a name="operations-to-rename-blobs"></a>BLOB の名前を変更する操作

クライアント アプリケーションでは 1 回の操作で BLOB の名前を変更できるため、BLOB の名前変更ははるかに効率的になります。 階層型名前空間を "*持たない*" アカウントでは、ツールやアプリケーションで BLOB をコピーした後、コピー元の BLOB を削除する必要があります。  

> [!NOTE]
> BLOB の名前を変更しても、BLOB の最終変更時刻は更新されません。 これは、BLOB の内容が変更されていないためです。 

## <a name="impact-on-costs"></a>コストへの影響

ストレージのコストは影響を受けませんが、トランザクションのコストは影響を受けます。 コストの比較を評価するには、次のページを使用してください。 

- [ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)。

- [Azure Data Lake Storage Gen2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

また、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)の **[ストレージ アカウント]** オプションを使用して、アップグレード後のコストの影響を見積もることもできます。 

価格の変更とは別に、Data Lake Storage Gen2 の機能によるコストの削減について検討してください。 一般に、全体的な総保有コストは、高いスループットと最適化された操作のために低下します。 スループットが高くなるほど、より短い時間でより多くのデータを転送できます。 階層型名前空間により、操作の効率が向上します。  

## <a name="impact-on-service-integrations"></a>サービス統合への影響

ほとんどの Azure サービスの統合は、これらの機能を有効にした後も引き続き機能しますが、一部の機能はプレビュー段階のままであり、まだサポートされていません。 Data Lake Storage Gen2 での Azure サービスの統合の現在のサポートについては、「[Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)」を参照してください。

## <a name="impact-on-tools-features-and-documentation"></a>ツール、機能、ドキュメントへの影響

アップグレードすると、一部の機能の操作方法が変わります。 このセクションでは、それらの変更について説明します。

### <a name="blob-storage-feature-support"></a>Blob Storage 機能のサポート

Blob Storage のほとんどの機能は、これらの機能を有効にした後も引き続き機能しますが、一部の機能はプレビュー段階のままであり、まだサポートされていません。 

Data Lake Storage Gen2 での Blob Storage の機能の現在のサポートについては、[Azure Data Lake Storage Gen2 で使用できる Blob Storage の機能](./storage-feature-support-in-storage-accounts.md)に関する記事を参照してください。 

### <a name="diagnostic-logs"></a>診断ログ

[Storage Analytics のログ](../common/storage-analytics-logging.md)を有効にしている場合は、バージョン 2.0 のログ形式を使用できるようになりました。  

この新しいバージョンを使用する必要はありません。 ただし、Data Lake Storage エンドポイントに適用される操作は、バージョン 2.0 のログにのみ記録されます。 使用するサービスやツール (AzCopy など) によっては、アカウントでの操作を実行するためにそのエンドポイントが使用されます。 すべてのアクティビティからログ情報を確実にキャプチャするには、バージョン 2.0 のログ形式の使用を検討してください。 

### <a name="azure-lifecycle-management"></a>Azure のライフサイクル管理

ディレクトリ内のすべての BLOB を移動または削除するポリシーでは、それらの BLOB を含むディレクトリは次の日まで削除されません。 これは、そのディレクトリにあるすべての BLOB が最初に削除されるまで、ディレクトリを削除できないためです。 翌日、ディレクトリは削除されます。 

### <a name="event-grid"></a>Event Grid

新しいアカウントには、Data Lake Storage エンドポイントと Blob service エンドポイントの 2 つのエンドポイントがあります。 サービス、ツール、アプリケーションでは、いずれかのエンドポイントを使用してデータを操作できます。 その結果、Event Grid によって返されるイベント応答で、影響を受けた BLOB を説明する **url** フィールドに、これら 2 つのエンドポイントのいずれかが示される可能性があります。 

次の JSON は、Blob service エンドポイントを使用して BLOB が作成されたときに、イベント応答で示される BLOB の url です。

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

次の JSON は、Data Lake Storage エンドポイントを使用して BLOB が作成されたときに、イベント応答で示される BLOB の url です。

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

アプリケーションで Event Grid が使用されている場合は、これを考慮するようにアプリケーションを変更することが必要な場合があります。 

### <a name="storage-explorer"></a>Storage Explorer

次のボタンは、Azure Storage Explorer のリボンにはまだ表示されていません。

|Button|理由|
|--|--|
|URL のコピー|まだ実装されていません|
|スナップショットを管理する|まだ実装されていません|
|削除の取り消し|Data Lake Storage Gen2 でまだサポートされていない Blob Storage の機能に依存します |


次のボタンは、新しいアカウントでは動作が異なります。

|Button|Blob Storage の動作|Data Lake Storage Gen2 の動作|
|---|---|---|
|フォルダー|フォルダーは仮想であり、ファイルが追加されていない場合は表示されません。 |ファイルが追加されていなくてもフォルダーは存在します。| 
|[名前の変更]|コピー元の BLOB がコピーされてから削除されます|同じ BLOB の名前が変更されます。 はるかに効率的です。|

### <a name="documentation"></a>ドキュメント

Data Lake Storage Gen2 の機能の使用に関するガイダンスについては、「[Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)」を参照してください。 

Blob Storage の既存のすべての機能に関するガイダンスがある場所に関しては、何も変更されていません。 そのがインダンスは、「[Azure Blob Storage の概要](storage-blobs-introduction.md)」にあります。 

コンテンツ セット間を移動すると、わずかな用語の違いがわかります。 たとえば、Data Lake Storage Gen2 に関するコンテンツでは、*BLOB* と "*コンテナー*" ではなく、"*ファイル*" と "*ファイル システム*" という用語が使用されている場合があります。 "*ファイル*" と "*ファイル システム*" という用語は、Data Lake Storage に長い歴史があるビッグ データ分析の世界に深く根差しています。 そのコンテンツには、これらの対象ユーザーへの関連を維持するためにこれらの用語が含まれています。 これらの用語で、別の "*事柄*" が説明されているわけではありません。

## <a name="next-steps"></a>次のステップ

Data Lake Storage Gen2 の機能を組み込むようにストレージ アカウントをアップグレードする準備ができたら、この手順ガイドを参照してください。 
> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)