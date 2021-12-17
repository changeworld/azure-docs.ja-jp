---
title: MongoDB 用 Azure Cosmos DB API の概要
description: Azure Cosmos DB の MongoDB 用 API を使用して、Azure Cosmos DB で膨大な量のデータを格納し、それに対してクエリを実行する方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9a142dfcba67b80a8293a15d03ea2b389bd297d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593897"
---
# <a name="azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Cosmos DB は、MongoDB 用 Azure Cosmos DB API を介すことで、あたかも MongoDB データベースであるかのように使用しやすくなります。 アプリケーションから MongoDB 用 API アカウントの接続文字列を参照することで、MongoDB で培った経験を活かすと共に、お気に入りの MongoDB ドライバー、SDK、ツールを使い続けることが可能です。

## <a name="why-choose-the-api-for-mongodb"></a>MongoDB 用 API を選ぶ理由

MongoDB Atlas などのサービス オファリングと比較して、MongoDB 用 API は [Azure Cosmos DB](../introduction.md) をベースにしていることから、さまざまな付加価値があります。

* **即時のスケーラビリティ**: [自動スケーリング](../provision-throughput-autoscale.md)機能を有効にすると、ウォームアップ期間をまったく設けずに、データベースのスケールアップとスケールダウンを行うことができます。
* **自動かつ透過的なシャーディング**: MongoDB 用 API では、すべてのインフラストラクチャが自動的に管理されます。 シャーディングやシャード数もその対象です。水平スケーリングを行う場合にシャーディングを自分で指定して管理しなければならない他の MongoDB オファリング (MongoDB Atlas など) とは異なります。 そのため、ユーザー向けのアプリケーションの開発に、より多くの時間を増やすことができます。
* **ファイブ ナインの可用性**: [99.999% の可用性](../high-availability.md)を簡単に構成でき、データの喪失を確実に防ぐことができます。  
* **コスト効率に優れた、粒度の細かい、無制限のスケーラビリティ**: 他の MongoDB サービス オファリングとは異なり、シャード コレクションを任意のサイズにスケーリングできます。 今日、MongoDB 用 API ユーザーは、600 TB を超えるストレージでデータベースを実行しています。 スケーリングは、コスト効率のよい方法で実行されます。Cosmos DB プラットフォームは、他の MongoDB サービス オファリングとは異なり、スケールとリソース ガバナンスの効率利用によって、VM の 1/100 単位でのスケーリングが可能です。
* **サーバーレスのデプロイ**: MongoDB Atlas とは異なり、MongoDB 用 API は、[サーバーレス容量モード](../serverless.md)を備えたクラウド ネイティブのデータベースです。 [サーバーレス](../serverless.md)であるため、料金はあくまで操作に応じて発生し、データベースを使用していないときには課金されません。
* **Free レベル**: Azure Cosmos DB の Free レベルでは、アカウント レベルで適用されている最初の 1000 RU/s と 25 GB のストレージを、お使いのアカウントで無期限かつ無料でご利用いただけます。
* **数秒で終わるアップグレード**: すべての API バージョンが 1 つのコードベース内に存在するため、バージョンの変更が [スイッチの切り替え](upgrade-mongodb-version.md)のように簡単です。ダウンタイムは発生しません。
* **あらゆる規模でのリアルタイム分析 (HTAP)** : データベースに影響を与えることなくそのデータにリアルタイムでビジネス インテリジェンスを実行するようなユース ケースに対応するため、MongoDB 用 API には、複雑な分析クエリを実行する機能が備わっています。 ETL パイプラインなしで、クラウド ネイティブの分析列ストアが利用されているため、高速でありながら、大きなコストがかかりません。 [Azure Synapse Link](../synapse-link.md) についての詳しい情報をご覧ください。

> [!NOTE]
> [Free レベルをご利用いただくと、MongoDB 用 Azure Cosmos DB API を無料で使用できます](../free-tier.md)。 Azure Cosmos DB の Free レベルでは、アカウント レベルに適用されている最初の 1000 RU/s と 25 GB のストレージを、お使いのアカウントで無料でご利用いただけます。


## <a name="how-the-api-works"></a>API の動作

MongoDB 用 Azure Cosmos DB API には MongoDB 用のワイヤ プロトコルが実装されています。 この実装により、ネイティブの MongoDB クライアント SDK、ドライバー、およびツールとの透過的な互換性が実現されます。 Azure Cosmos DB は、MongoDB データベース エンジンをホストしません。 ご使用の API バージョンと互換性のある MongoDB クライアント ドライバーであれば、通常、特別な構成なしに接続できます。

MongoDB 機能の互換性:

MongoDB 用 Azure Cosmos DB API は、次のバージョンの MongoDB サーバーと互換性があります。
- [Version 4.0](feature-support-40.md)
- [バージョン 3.6](feature-support-36.md)
- [Version 3.2](feature-support-32.md)

MongoDB 用 API の各バージョンはいずれも同じコードベースで実行されます。そのためアップグレード作業は簡単で、ダウンタイムが発生することなく数秒で完了できます。 Azure Cosmos DB でいくつかの機能フラグを反転させるだけで、バージョンが切り替わります。  また、この機能フラグにより、以前の API バージョン (3.2、3.6 など) が引き続きサポートされます。 自分にとって最適なサーバー バージョンを選択できます。

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB の MongoDB 用 API" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>最初に知っておくべきこと

* クラスター内の仮想マシンに対しては課金されません。 [価格](../how-pricing-works.md)は、データベース単位またはコレクション単位で構成された要求ユニット (RU) あたりのスループットに基づきます。 [Free レベル](../free-tier.md)では、最初の 1000 RU/s が無料になります。

* MongoDB 用 Azure Cosmos DB API には、次の 3 とおりのデプロイ方法があります。
     * [プロビジョニング スループット](../set-throughput.md): 1 秒あたりの RU 数を設定して手動で変更します。 このモデルは、一貫性のあるワークロードに最も適しています。
     * [自動スケーリング](../provision-throughput-autoscale.md): 必要なスループットの上限を設定します。 ニーズに合わせてスループットは即時にスケーリングされます。 このモデルは、頻繁に変化するワークロードに最も適しており、そのコストが最適化されます。
     * [サーバーレス](../serverless.md): 使用したスループット (期間) に対してのみ課金されます。 このモデルは、開発とテストのワークロードに最も適しています。 

* シャード クラスターのパフォーマンスは、コレクションの作成時に選択したシャード キーに依存します。 データがシャード全体に均等に分散されるよう、シャード キーは慎重に選択してください。

### <a name="capacity-planning"></a>容量計画

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](../estimate-ru-with-capacity-planner.md)に関するページを参照してください

## <a name="quickstart"></a>クイック スタート

* [既存の MongoDB Node.js Web アプリを移行する](create-mongodb-nodejs.md)
* [Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する](create-mongodb-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API と Java SDK を使用してコンソール アプリを構築する](create-mongodb-java.md)
* [仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md) 
* [Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](../estimate-ru-with-capacity-planner.md)

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください
* チュートリアル「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」に従って、アカウントの接続文字列の情報を取得する方法について学習します。
* [Azure Cosmos DB での Studio 3T の使用](connect-using-mongochef.md)に関するチュートリアルに従って、Studio 3T で Cosmos データベースと MongoDB アプリの間の接続を作成する方法を学習します。
* [Azure Cosmos DB への MongoDB データのインポート](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)に関するチュートリアルに従って、データを Cosmos データベースにインポートします。
* [Robo 3T](connect-using-robomongo.md) を使用して Cosmos アカウントに接続します。
* [グローバル分散アプリの読み取り設定を構成する](tutorial-global-distribution-mongodb.md)方法について学習します。
* よく見られるエラーの解決策については、[トラブルシューティング ガイド](error-codes-solutions.md)を参照してください


<sup>注意事項: この記事では、MongoDB データベースとのワイヤ プロトコルの互換性を提供する Azure Cosmos DB の機能について説明します。Microsoft は、このサービスを提供するための MongoDB データベースの運営は行いません。Azure Cosmos DB は MongoDB, Inc. には所属していません。</sup>
