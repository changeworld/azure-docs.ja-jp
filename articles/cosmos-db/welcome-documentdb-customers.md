---
title: "Azure Cosmos DB への DocumentDB ユーザーの自動移行 | Microsoft Docs"
description: "DocumentDB ユーザーが Azure Cosmos DB に自動的に移行されるという、//build 2017 で行われた発表の内容について詳しく説明します。"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>Azure Cosmos DB への DocumentDB ユーザーの自動移行

DocumentDB をご利用の皆さんに朗報です。今後は Azure Cosmos DB をご利用いただけます。 

Azure Cosmos DB は、[Microsoft Build 2017 カンファレンスで発表](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/)された、世界規模で分散される初のデータベース サービスです。地理的な垣根を越え、任意の数のリージョンにスループットとストレージをオンデマンドでスケールできます。その高可用性と整合性、短い待ち時間は、業界随一の充実した SLA によって保証され、また多数のデータ モデルと API がサポートされています。 

![Azure Cosmos DB は世界規模で分散される Microsoft のデータベース サービスであり、柔軟なスケールアウト、短い待ち時間の保証、5 つの整合性モデル、充実した保証を提供する SLA を特徴としています](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Azure Cosmos DB がサポートする API の 1 つに、DocumentDB API とドキュメント データ モデルがあります。 DocumentDB API については、皆さんもよくご存じかと思います。 現行の DocumentDB アプリケーションを実行するために皆さんが既に使用している API です。 これらの API は変更 "_されません_"。NuGet パッケージと名前空間、すべての依存関係がそのまま維持されます。 DocumentDB API を使って構築された既存のアプリは、**一切変更せずに**引き続き実行することができます。 Azure Cosmos DB とは、それらの API が加わったサービスの名前にすぎません。 

それではいくつかの事柄について質問形式で見ていきましょう。 

## <a name="why-move-to-azure-cosmos-db"></a>Azure Cosmos DB に移行する理由は何ですか? 

Azure Cosmos DB は、地理的な制約なく世界規模で分散できる次世代のクラウド データベースです。 DocumentDB をご利用のお客様には自動的に、Azure Cosmos DB が備えている革新的なシステムと機能が提供されます。

Azure Cosmos DB はもともと、Microsoft 社内の大規模なアプリケーションについて開発者が直面する問題に対処するため、2010 年に "Project Florence" として運用が開始されました。 その後、世界規模で分散されるアプリの構築に伴う難しさは Microsoft だけの問題ではないことがわかってきて、2015 年に、Azure の開発者を対象に、このテクノロジの第 1 世代を Azure DocumentDB という形で提供するようになりました。 

そのときからずっと Microsoft は新しい機能を追加してきており、重要な新機能も導入しています。  その結果誕生したのが Azure Cosmos DB です。  今回 Azure Cosmos DB がリリースされたことを受けて、DocumentDB を使用しているお客様は、(そのデータも含め) Azure Cosmos DB に自動で移行します。 その切り替えはシームレスに行われ、DocumentDB ユーザーには自動的に、Azure Cosmos DB が備えている革新的なシステムと機能が提供されます。 これらの機能は、核となるデータベース エンジンの領域のほか、世界規模での分散、伸縮自在のスケーラビリティ、業界随一の充実した SLA に及んでいます。 たとえば Azure Cosmos DB データベース エンジンは、広く使われているあらゆるデータ モデルと型システム、API を、Azure Cosmos DB に内在するデータ モデルに対して効率的にマッピングできるように刷新されています。 

この作業の成果は、現在開発者によって進められている [Gremlin](graph-introduction.md) や [Table Storage API](table-introduction.md) への対応として現れています。 そして、それはまだ始まりにすぎません。 今後、その他の普及している API や、より新しいデータ モデルが追加され、世界規模のパフォーマンスとストレージに向けて、さらに進化していくことでしょう。 

重要なことですが、DocumentDB の [SQL Dialect](../documentdb/documentdb-sql-query.md) は、その基になる Cosmos DB で提供されてきた多数の API の 1 つにすぎません。 Azure Cosmos DB のように、管理の完全に行き届いたサービスを使用する開発者にとって、そのサービスとの唯一のインターフェイスは、サービスによって公開されている API です。 そのため、既に DocumentDB を利用されている方にとっては、実際には何も変わりません。 Azure Cosmos DB では、DocumentDB とまったく同じ SQL API を利用できるのです。 ただし現在 (そしてこれから) は、今まで利用できなかった機能が利用できるようになります。 

Microsoft のたゆみない作業のもう 1 つの成果は、スループットとストレージを世界規模で伸縮自在にスケールする素地です。 その代表的な例の 1 つは [1 分あたりの要求ユニット](request-units-per-minute.md)ですが、この領域については、今後続々と機能が発表される予定です。 これらの新機能によって、お客様はさまざまなワークロードのコストを低減することができます。 Microsoft はグローバル分散サブシステムに対し、いくつかの根本的な機能強化を実施しました。 この作業が多くの開発者にもたらす成果の 1 つは、一貫性のあるプレフィックスの整合性モデルです (これで明確に定義された整合性モデルは、全部で 5 つとなります)。 しかし、有益な機能は他にも多数存在し、完成の域に達した時点でリリースしていく予定です。 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>DocumentDB リソースを今後 Azure Cosmos DB で確実に実行するために私は何をすべきでしょうか?

何もありません。 お客様が行うべき変更はありません。 ご利用の DocumentDB リソースは既に Azure Cosmos DB リソースになっています。また移行時にサービスの中断は生じていません。

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Azure Cosmos DB と連動させるために、既存のアプリにはどのような変更を加えればよいですか?

何も変更を加える必要はありません。 クラスや名前空間、NuGet パッケージの名前は変わっていません。 日頃から Microsoft が推奨しているように、最新の機能と改良点を最大限に活かすために SDK は最新の状態に保ってください。 

## <a name="whats-changed-in-the-azure-portal"></a>Azure Portal に変更はありますか?

Azure DocumentDB は今後、Azure サービスとしてはポータルに表示されません。 次の図に示すように Azure Cosmos DB は新しいアイコンで表示されます。 既存のコレクションはすべて従来と同じように利用できます。また、これまでどおり、スループットをスケールしたり、一貫性を変更したり、SLA を監視したりすることができます。 **データ エクスプローラー (プレビュー)** の機能は強化されています。 ドキュメントの表示と編集、クエリの作成と実行、さらにストアド プロシージャ、トリガー、UDF の操作を 1 つのページから行うことができるようになりました (下図参照)。 

![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>料金に変更はありますか?

いいえ。Azure Cosmos DB でアプリを実行するコストは以前と同じです。 ただし、Microsoft が新たに発表した "**1 分あたりの要求ユニット機能**" は利用価値がありますので、ぜひご検討ください。 詳細については、[スループットの分単位でのスケール](request-units-per-minute.md)に関する記事を参照してください。

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>サービス レベル アグリーメント (SLA) に変更はありますか?

いいえ。可用性、一貫性、待ち時間、スループットに関する SLA は未変更のままポータルに表示されています。 SLA の詳細については、[Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) を参照してください。
   
![todo アプリとサンプル データ](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>Azure Cosmos DB の今後はどうなりますか?

Azure Cosmos DB は、絶えず進化するデータベース サービスです。 新機能はすべて Microsoft 社内の大規模アプリケーションで検証され、その後、外部の主要顧客に公開された後、全世界に向けてリリースされます。 

Azure Cosmos DB では今後、広く使われている API とデータ モデルに対応していく予定です。新しい Gremlin と Table API のサポートは、その始まりにすぎません。新しいデータ モデルが発表された時点で皆さんにお知らせします。 世界規模でのパフォーマンスとストレージの点でも、これまで同様、大きく進化していきます。 

日頃より Azure DocumentDB をご利用いただき感謝いたします。私たちは、世界中の開発者やお客様に Azure Cosmos DB を提供できることを心から嬉しく思っています。 世界で最も信頼できるデータベース サービスとして、地球規模の強力なアプリを皆さんが簡単に開発できるようにすることが、私たちの使命です。 Azure Cosmos DB の新機能をぜひお試しください。皆さんからのご意見をお待ちしております。  Cosmos DB で皆さんがすばらしい成果を上げられることを期待して止みません。

— Azure Cosmos DB チーム一同[@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

