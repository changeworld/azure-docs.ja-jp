---
title: Azure Cosmos DB Cassandra API での Glowroot の実行 (プレビュー)
description: この記事では Azure Cosmos DB Cassandra API で Glowroot を実行する方法について詳しく説明します。
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 1808e3df0b70f61c3cfabf96e61b6675dff6b011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092516"
---
# <a name="run-glowroot-on-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API で Glowroot を実行する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Glowroot は、アプリケーションのパフォーマンスを最適化および監視するために使用されるアプリケーション パフォーマンス管理ツールです。 この記事では、Azure Cosmos DB Cassandra API 内で Glowroot を使用して、アプリケーションのパフォーマンスを監視する方法について説明します。

## <a name="prerequisites-and-setup"></a>前提条件と設定

* [Azure Cosmos DB Cassandra API アカウントを作成する](manage-data-java.md#create-a-database-account)。
* [Windows 用の JAVA (バージョン 8) をインストールする](https://developers.redhat.com/products/openjdk/download)
> [!NOTE]
> 特定のビルド ターゲットは、新しいバージョンと互換性がないことが判明していますので注意してください。 JAVA の新しいバージョンを既にお持ちの場合でも、まだ JDK8 をダウンロードできます。
> JDK8 に加えて新しい JAVA がインストールされている場合は、ローカルのコマンド プロンプトで %JAVA_HOME% 変数を JDK8 に設定します。 これは、現在のセッションの java バージョンのみを変更し、グローバルのマシン設定はそのまま維持されます。 
* [Maven をインストールする](https://maven.apache.org/download.cgi)
    * `mvn --version` を実行してインストールの成功を確認します。

## <a name="run-glowroot-central-collector-with-cosmos-db-endpoint"></a>Cosmos DB エンドポイントを使用して Glowroot central collector を実行する
エンドポイント構成が完了したら、以下を実行します。 
1. [Glowroot central collector ディストリビューションをダウンロードします](https://github.com/glowroot/glowroot/wiki/Central-Collector-Installation#central-collector-installation)
2. glowroot-central.properties ファイルで、Cosmos DB Cassandra API エンドポイントの次のプロパティを設定します。
    * cassandra.contactPoints
    * cassandra.username
    * cassandra.password
3. プロパティ `cassandra.ssl=true`、`cassandra.gcGraceSeconds=0`、`cassandra.port=10350` を設定します。
4. glowroot-central.properties が glowroot-central.jar と同じフォルダーにあることを確認します。
5. `java -jar glowroot-central.jar` を実行して Glowroot の実行を開始します。

## <a name="faqs"></a>FAQ
Glowroot の実行またはテストで問題が発生した場合は、サポート チケットを開いてください。 Glowroot のテストを実行するサブスクリプション ID とアカウント名を指定します。

## <a name="next-steps"></a>次のステップ
- Java アプリケーションを使用した[ Cassandra API アカウント、データベースおよびテーブルの作成](create-account-java.md)の開始
- Azure Cosmos DB の Cassandra API で[サポートされている機能](cassandra-support.md)について確認します。
