---
title: オープン ソースの NoSQL データベースでの Azure Cosmos DB のマルチマスターの使用
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963902"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>オープン ソースの NoSQL データベースでの Azure Cosmos DB のマルチマスターの使用

Azure Cosmos DB のマルチマスター サポートは、Cosmos DB でサポートされているすべてのオープン ソースの NoSQL 製品で使用可能なネイティブのサーバー側の実装です。 これは、Cosmos DB がサポートしているすべての SDK からもアクセスできます。

Azure Cosmos DB は、これらのオープン ソースの NoSQL データベースのマルチマスターのサポートを提供する、世界初のサービスです。

|モデル  |サポート  |
|---------|---------|
|MongoDB  | アクティブ/アクティブ  |
|Graph  | アクティブ/アクティブ |
|Cassandra  | アクティブ/アクティブ   |

## <a name="use-mongodb-clients-with-multi-master"></a>マルチマスターでの MongoDB クライアントの使用

マルチマスターの機能は、他の Azure Cosmos DB API に対して有効にする方法と同じ方法で MongoDB API アカウントに対して有効にされます。 MongoDB API アカウントに対してマルチマスターを有効にした後、クライアント アプリケーションの各インスタンスは、読み取りと書き込みに対して優先リージョンを選択できます。 MongoDB ドライバーの観点から、優先リージョンがレプリカ セット プライマリとしてクライアントに表示されます。 この方法では、分散データベースのすべてのリージョンは、レプリカ セット プライマリとして動作できます。 Azure Cosmos DB のマルチマスターを使用すると、グローバル分散型の MongoDB アプリケーションの書き込みの待機時間を大幅に短縮することができます。 

### <a name="set-the-primary-region"></a>プライマリ リージョンの設定

MongoDB のクライアントの各インスタンスは、MongoDB クライアント ドライバーによって受け入れられた「アプリケーション名」フィールドに `@<preferred_primary_region_name>` を追加することによってプライマリ リージョンを選択できます。 ほとんどのドライバーは、次のように接続文字列でこれを受け入れます。

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

接続した後、次のいずれかが発生します。

* 優先リージョン名を書き込みリージョンとして使用できる場合、Azure Cosmos DB はこれをプライマリ リージョンとして選択します。

* 優先リージョン名が指定されていない場合、Azure Cosmos DB は、プライマリ リージョンとして既定のリージョンを選択します。

* 優先リージョン名が指定されているが、データベース アカウントの書き込みリージョンとして使用できない場合、Azure Cosmos DB は、プライマリ リージョンとして利用可能な最も近い書き込みリージョンを選択します。

NodeJS ドライバーなどの特定のドライバーは常に、初期接続文字列を指定したホストに書き込む最初の問題です。 このようなドライバーでは、必ず優先リージョンに書き込まれるように、アプリ名だけでなく接続文字列内の DNS 名を変更してリージョン名を含める必要があります。 スペースを入れず、リージョン名を指定してください。 例: 

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>競合解決モード

Azure Cosmos DB MongoDB API アカウントの競合解決モードは、常に最後の書き込みが優先されます。その際、書き込みを受け入れるリージョン サーバーのタイムスタンプを使用します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Cosmos DB MongoDB API アカウントのマルチマスターのサポートについて説明しました。 次に、以下のリソースをご覧ください。

* [Azure Cosmos DB アカウントのマルチ マスターを有効にする方法](enable-multi-master.md)

* [Azure Cosmos DB の競合解決の理解](multi-master-conflict-resolution.md)
