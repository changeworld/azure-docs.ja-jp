---
title: VNet インジェクション キャッシュから Private Link キャッシュに移行する
description: Azure Cache for Redis Virtual Network (VNet) を Private Link キャッシュに移行する方法について説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 8ab6acaf1ff6742db0b6dc750e39803123829742
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781114"
---
# <a name="migrate-from-vnet-injection-caches-to-private-link-caches"></a>VNet インジェクション キャッシュから Private Link キャッシュに移行する
この記事では、Azure Cache for Redis Virtual Network (VNet) のインジェクションされたキャッシュ インスタンスを Azure Cache for Redis Private Link キャッシュ インスタンスに移行するいくつかのアプローチについて説明します。 

[Azure Private Link](../private-link/private-link-overview.md) により、ネットワーク アーキテクチャが簡素化され、Azure 内のエンドポイント間の接続がセキュリティで保護されます。 仮想ネットワーク内のサブネットでプライベート IP アドレスが割り当てられているプライベート エンドポイント経由で、仮想ネットワークから Azure Cache インスタンスに接続できます。 Azure Cache for Redis に対して Azure Private Link を使用する利点は次のとおりです。 

* **レベルの柔軟性** – Azure Private Link は、すべてのレベル (Basic、Standard、Premium、Enterprise、Enterprise Flash) でサポートされています。 Premium レベルでのみ提供される仮想ネットワーク インジェクションとは対照的です。   

* **簡略化されたネットワーク セキュリティ グループ (NSG) ルール管理** - NSG ルールは、Azure Cache for Redis の要件に準拠するように構成する必要がありません。

* **Azure Policy サポート** – 組織内のすべてのキャッシュが Private Link を使用して作成されていることを確認し、組織の既存のキャッシュを監査して、それらすべてで Private Link が利用されていることを確認します。

## <a name="migration-options"></a>移行オプション

いくつかの異なる方法を使用して、VNet インジェクションから Private Link に切り替えることができます。 キャッシュの場所やアプリケーションからの操作方法に応じて、ほかの方法よりも役立ちます。 頻繁に使用されるいくつかの移行戦略について、以下で詳しく説明します。 

### <a name="if-youre-using-any-combination-of-geo-replication-clustering-or-arm-vnet"></a>geo レプリケーション、クラスタリング、または ARM VNet の任意の組み合わせを使用している場合:

   | オプション       | 長所 | 短所 |
   | ------------ | ---------- | ------------- |
   | 2 つのキャッシュにデータを二重に書き込む | データの損失やダウンタイムはありません 既存のキャッシュが中断されない操作です。 新しいキャッシュのテストが簡単になります。 | 長期間にわたって 2 つのキャッシュが必要です。 | 
   | 新しいキャッシュを作成する | 実装が非常に簡単です。 | 新しいキャッシュにデータを再入力する必要があります。多くのアプリケーションで動作しない可能性があります。 |
   | RDB ファイルを使用してデータをエクスポートおよびインポートする | データ移行が必要です。 | RDB ファイルの生成後に既存のキャッシュに書き込まれると、一部のデータが失われる可能性があります。 | 
   | プログラムでデータを移行する | データの移動方法を完全に制御できます。 | カスタム コードが必要です。 | 

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>移行期間内に 2 つの Redis キャッシュに同時に書き込む

キャッシュ間で直接データを移動するのではなく、アプリケーションを使用して、既存のキャッシュと設定中の新しいキャッシュの両方にデータを書き込むことができます。 最初に既存のキャッシュからデータが読み取られます。 新しいキャッシュに必要なデータがある場合は、そのキャッシュにアプリケーションを切り替え、古いキャッシュを削除します。 たとえば、Redis をセッション ストアとして使用し、アプリケーション セッションが 7 日間有効であるとします。 1 週間で 2 つのキャッシュに書き込むと、新しいキャッシュに期限切れではないセッション情報がすべて含まれていることがわかります。 その時点以降は、データが失われることを心配せずに安全に利用できます。

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュと同じ (またはより大きい) サイズの、[プライベート エンドポイントを持つ Azure Cache for Redis インスタンス](cache-private-link.md)を新規作成します。

2. 新しいインスタンスと元のインスタンスの両方に書き込まれるようにアプリケーション コードを変更します。

3. 新しいインスタンスに十分なデータが入力されるまで、元のインスタンスからのデータの読み取りを続行します。

4. 新しいインスタンスからの読み取りと書き込みのみが行われるようにアプリケーション コードを更新します。

5. 元のインスタンスを削除します。

### <a name="create-a-new-azure-cache-for-redis"></a>新しい Azure Cache for Redis を作成する

このアプローチは、技術的には移行ではありません。 データが失われる心配がない場合、Azure Cache for Redis に移動する最も簡単な方法は、キャッシュ インスタンスを作成し、アプリケーションを接続することです。 たとえば、Redis をデータベース レコードのルックアサイド キャッシュとして使用すると、キャッシュをゼロから簡単に再構築できます。

このオプションを実装する一般的な手順は、次のとおりです。

1. [プライベート エンドポイントを持つ Azure Cache for Redis インスタンス](cache-private-link.md)を新規作成します。

2. 新しいインスタンスが使用されるようにアプリケーションを更新します。

3. 古い Redis インスタンスを削除します。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis-premium-tier-only"></a>データを RDB ファイルにエクスポートし、Azure Cache for Redis にインポートする (Premium レベルのみ)

オープンソースの Redis は、キャッシュのメモリ内データセットのスナップショットを取得してファイルに保存するための標準的なメカニズムが定義されます。 この (RDB と呼ばれる) ファイルは、別の Redis キャッシュで読み取ることもできます。 [Azure Cache for Redis Premium レベル](cache-overview.md#service-tiers)では、RDB ファイルを使用したキャッシュ インスタンスへのデータのインポートがサポートされています。 RDB ファイルを使用すると、既存のキャッシュから Azure Cache for Redis にデータを転送できます。

> [!IMPORTANT]
> RDB ファイルの形式は、Redis のバージョン間で変更されることがあるため、下位互換性が確保されない場合があります。 エクスポート元のキャッシュの Redis バージョンは、Azure Cache for Redis で提供されるバージョン以下である必要があります。
>

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュと同じ (またはより大きい) サイズの、[プライベート エンドポイントを持つ Azure Cache for Redis インスタンス](cache-private-link.md)を Premium レベルで新規作成します。

2. 既存の Redis キャッシュのスナップショットを保存します。 [スナップショット](https://redis.io/topics/persistence)が定期的に保存されるように Redis を構成することも、[SAVE](https://redis.io/commands/save) または [BGSAVE](https://redis.io/commands/bgsave) コマンドを使用して手動でプロセスを実行することもできます。 既定の RDB ファイル名は "dump.rdb" であり、このファイルは *redis.conf* 構成ファイルで指定されたパスに配置されています。

    > [!NOTE]
    > Azure Cache for Redis 内でデータを移行する場合は、[RDB ファイルをエクスポートするための手順](cache-how-to-import-export-data.md)を参照するか、代わりに [PowerShell エクスポート コマンドレット](/powershell/module/azurerm.rediscache/export-azurermrediscache)を使用してください。
    >

3. RDB ファイルを新しいキャッシュが配置されているリージョンの Azure ストレージ アカウントにコピーします。 このタスクでは、AzCopy を使用できます。

4. これらの[インポート手順](cache-how-to-import-export-data.md)または [PowerShell インポート コマンドレット](/powershell/module/azurerm.rediscache/import-azurermrediscache)を使用して、RDB ファイルを新しいキャッシュにインポートします。

5. 新しいキャッシュ インスタンスが使用されるようにアプリケーションを更新します。

### <a name="migrate-programmatically"></a>プログラムで移行する

プログラムで既存のキャッシュからデータを読み込み、Azure Cache for Redis に書き込むことで、カスタム移行プロセスを作成します。 この[オープンソース ツール](https://github.com/deepakverma/redis-copy)を使用すると、別の Azure Cache for Redis インスタンスにデータをコピーできます。 このツールは、さまざまな Azure Cache for Redis リージョンのキャッシュ インスタンス間でデータを移動する場合に役立ちます。 [コンパイル済みのバージョン](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)も入手できます。 また、独自の移行ツールを作成する際に役立つガイドとなるソース コードが見つかることもあります。

> [!NOTE]
> このツールは、正式には Microsoft でサポートされていません。 
>

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュが配置されているリージョンで VM を作成します。 データセットが大きい場合は、コピー時間が削減されるように、比較的強力な VM を選択してください。

2. [プライベート エンドポイントを持つ Azure Cache for Redis インスタンス](cache-private-link.md)を新規作成します

3. 新しいキャッシュからデータをフラッシュして、空になったことを確認します。 このステップは、コピー ツール自体ではターゲット キャッシュの既存のキーが上書きされないため必須です。

    > [!IMPORTANT]
    > 絶対にソース キャッシュからフラッシュしないでください。
    >

4. 上記のオープンソース ツールなどのアプリケーションを使用して、ソース キャッシュからターゲットへのデータのコピーを自動化します。 データセットのサイズによっては、コピー処理が完了するまでに時間がかかる場合があることに注意してください。


## <a name="next-steps"></a>次の手順
* [ネットワークの分離オプション](cache-network-isolation.md)についてさらに学習してください。 
* [すべてのレベルの Azure Cache for Redis に対するプライベート エンドポイントを構成する](cache-private-link.md)方法について学習します。