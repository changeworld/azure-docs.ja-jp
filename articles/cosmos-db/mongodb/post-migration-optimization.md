---
title: Azure Cosmos DB の MongoDB 用 API での移行後の最適化手順
description: このドキュメントでは、MongoDB から Azure Cosmos DB の MongoDB 用 API に移行した後の最適化手法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 6dbcb5e65c99c2009602d660e3cd0cc9e22efdbf
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031569"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API 使用時の移行後の最適化手順
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 移行後の手順を実行する前に、このガイド全体をお読みください。
>

この MongoDB 移行後ガイドは、MongoDB の移行に関するシリーズの一部です。 MongoDB 移行の重要な手順は、次に示すように、[移行前](pre-migration-steps.md)、移行、そして移行後です。

![移行手順の図。](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>移行後の概要

MongoDB データベースに格納されているデータを Azure Cosmos DB の MongoDB 用 API に移行した後、Azure Cosmos DB に接続してデータを管理できます。 このガイドでは、移行後に考慮すべき手順を提供します。 移行手順については、[MongoDB を Azure Cosmos DB の MongoDB 用 API に移行するためのチュートリアル](../../dms/tutorial-mongodb-cosmos-db.md)をご覧ください。

次の手順に従って、移行後の手順を実行します

- [インデックス作成ポリシーを最適化する](#optimize-the-indexing-policy)
- [Azure Cosmos DB の MongoDB 用 API のグローバル配布を構成する](#globally-distribute-your-data)
- [整合性レベルを設定する](#set-consistency-level)
- [アプリケーションを接続 (カットオーバー) する](#connect-or-cutover-your-application)
- [最適なパフォーマンスを得るチューニング](#tune-for-optimal-performance)

> [!NOTE]
> 唯一の必須移行後手順は、新しい Azure Cosmos DB アカウントを指すようにアプリケーションの接続文字列を変更することです。 その他の移行後手順はすべて、データ層のパフォーマンスを向上させるために推奨される最適化です。 "*ただし*"、他の手順を行わずにアプリケーションのカットオーバーをすぐに実行すると、アプリケーションは、最適でないインデックス作成と一貫性の影響をすぐに受けることになります。 具体的には、インデックスを構成する前にカットオーバーを実行した場合、アプリケーションの価格性能比がすぐに低下する可能性があります。 これは改善される可能性があることに注意してください。[インデックスが最適化されると](#optimize-the-indexing-policy)、多くの場合、Azure Cosmos DB は価格性能比の面で現状のソリューションを上回ることができると考えられます。
>

## <a name="pre-requisites"></a>前提条件

このガイドでは、スプレッドシートなど何らかの追跡の成果物を使用して、移行の進行状況を記録していることを前提としています。 まだ「[移行前ガイド](pre-migration-steps.md)」を読んでいない場合は、"*データ資産の移行スプレッドシート*" の構築、既存の MongoDB リソースの検出、移行の計画に関するガイダンスについて確認することをお勧めします。

## <a name="optimize-the-indexing-policy"></a>インデックス作成ポリシーを最適化する

価格とパフォーマンスを最適化するために、データ資産の移行スプレッドシートをステップ実行し、各リソースのインデックス構成を設計することをお勧めします。 
1. 実際には、[移行前フェーズでインデックスを計画する](pre-migration-steps.md#post-migration)ことをお勧めします。 インデックス設定用の列をデータ資産の移行スプレッドシートに追加します。 
   * MongoDB サーバー バージョン 3.6 以降を対象とする Azure Cosmos DB の API では、_id フィールドのみ、インデックスが自動的に作成されます。 このフィールドは削除できません。 シャード キーごとに _id フィールドの一意性が自動的に適用されます。 その他のフィールドのインデックスを作成するには、MongoDB インデックス管理コマンドを適用します。 この既定のインデックス作成ポリシーは Azure Cosmos DB の SQL API とは異なり、既定ですべてのフィールドのインデックスが作成されます。

   * MongoDB サーバー バージョン 3.2 を対象とする Azure Cosmos DB API では、データを Azure Cosmos DB に移行するときに、既定ですべてのデータ フィールドに自動的にインデックスが作成されます。 多くの場合、この既定のインデックス作成ポリシーは許容されます。 一般的に、インデックスを削除すると書き込み要求が最適化され、既定のインデックス作成ポリシー (インデックス自動作成など) を取得すると読み取り要求が最適化されます。

   * Azure Cosmos DB によって提供されるインデックス作成機能には、複合インデックス、一意のインデックス、Time-to-Live (TTL) インデックスの追加が含まれます。 インデックス管理インターフェイスは、createIndex() コマンドにマップされます。 詳細については、Azure Cosmos DB のインデックス作成と Azure Cosmos DB の MongoDB 用 API のインデックス作成に関するページを参照してください。
2. 移行後にこれらのインデックス設定を適用します。
   * [Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db.md) では、一意のインデックスがある MongoDB のコレクションは自動的に移行されます。 ただし、一意のインデックスは移行前に作成する必要があります。 Azure Cosmos DB では、コレクションにデータが既にある場合、一意のインデックスの作成をサポートしていません。 詳細については、[Azure Cosmos DB における一意のキー](../unique-keys.md)に関するページをご覧ください。

## <a name="globally-distribute-your-data"></a>データをグローバルに分散する

Azure Cosmos DB は世界中のすべての [Azure リージョン](https://azure.microsoft.com/regions/#services)で利用できます。 
1. データをグローバルに分散するには、[Azure Cosmos DB の MongoDB 用 API でデータを グローバルに分散する](tutorial-global-distribution-mongodb.md)記事のガイダンスに従います。 Azure Cosmos DB アカウントに既定の一貫性レベルを選択したら、(グローバル配信の必要性に基づいて) 1 つまたは複数の Azure リージョンを関連付けることができます。 高可用性とビジネス継続性のために、少なくとも 2 つのリージョンで実行することを常にお勧めします。 Azure Cosmos DB でマルチリージョン デプロイのコストを最適化するためのヒントは[こちら](../optimize-cost-regions.md)でご確認いただけます。

## <a name="set-consistency-level"></a>整合性レベルを設定する

Azure Cosmos DB では、5 つの[整合性レベル](../consistency-levels.md)が明確に定義されています。 MongoDB と Azure Cosmos DB の各種整合性レベル間でマッピングする方法については、「[整合性レベルと Azure Cosmos DB API](../consistency-levels.md)」を参照してください。 既定の整合性レベルは、セッション整合性レベルです。 整合性レベルの変更は任意であり、アプリに合わせて最適なレベルを選択できます。 Azure portal を使用して整合性レベルを変更する方法:

1. [設定] の **[既定の整合性]** に進みます。
2. [整合性レベル](../consistency-levels.md)を選択します。

ほとんどのユーザーは、整合性レベルを既定のセッション整合性設定のままにします。 しかしながら、[さまざまな整合性レベルでの可用性とパフォーマンスのトレードオフ](../consistency-levels.md)があります。

## <a name="connect-or-cutover-your-application"></a>アプリケーションの接続またはカットオーバー

アプリケーションのカットオーバーまたは接続の処理により、移行が完了したら、アプリケーションで Azure Cosmos DB を使用するように切り替えることができます。 次の手順に従います。

1. 新しいウィンドウで、[Azure portal](https://www.portal.azure.com/) にサインインします。
2. [Azure portal](https://www.portal.azure.com/) の左側のウィンドウで **[すべてのリソース]** メニューを開き、データを移行した Azure Cosmos DB アカウントを見つけます。
3. **[接続文字列]** ブレードを開きます。 右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。
4. アプリケーションの構成 (または、その他の関連する場所) で接続情報を使用し、アプリで Azure Cosmos DB の MongoDB 用 API 接続を反映させます。
:::image type="content" source="./media/post-migration-optimization/connection-string.png" alt-text="接続文字列の設定が示されているスクリーンショット。":::

詳細については、「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」ページを参照してください。

## <a name="tune-for-optimal-performance"></a>最適なパフォーマンスを得るチューニング

[インデックス作成](#optimize-the-indexing-policy)、[グローバル分散](#globally-distribute-your-data)、[整合性](#set-consistency-level)について、これらはすべて簡単に構成でき、必要に応じて再構成することができます。 そのため、アプリケーションのカットオーバーが完了したら、アプリケーションのパフォーマンスを監視し、必要に応じてこれらの設定を調整して、アプリケーションの要件を満たすようにすることをお勧めします。

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか?
    * 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください
* [Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)
* [Studio 3T を使用して Azure Cosmos DB アカウントに接続する](connect-using-mongochef.md)
* [Azure Cosmos DB の MongoDB 用 API を使用して読み取りをグローバルに配布する方法](readpreference-global-distribution.md)
* [Azure Cosmos DB の MongoDB 用 API でデータを期限切れにする](mongodb-time-to-live.md)
* [Azure Cosmos DB の整合性レベル](../consistency-levels.md)
* [Azure Cosmos DB のインデックス作成](../index-overview.md)
* [Azure Cosmos DB の要求ユニット](../request-units.md)