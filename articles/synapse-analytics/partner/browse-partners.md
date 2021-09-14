---
title: Synapse Studio を使用して Azure Synapse パートナーのサードパーティ ソリューションを見つける
description: Azure Synapse パートナーと緊密に統合されている新しいサードパーティ ソリューションを見つける方法について説明します
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: c765fcce0b95027ffa28915752bd3f370070a894
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481534"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>Synapse Studio を使用してパートナー ソリューションを見つける

Synapse Studio により、Azure Synapse の機能を拡張するソリューション パートナーを見つけることができます。 データ コネクタから、データ ラングリング ツール、オーケストレーション エンジン、およびその他のワークロードを通じて、**パートナーの参照** ページは、Azure Synapse Analytics と連携することが確認されたサードパーティ ISV アプリケーションとソリューションを見つけるためのハブとして機能します。 Synapse Studio では、多くの場合に、パートナー プラットフォームへの初期接続の自動セットアップにより、これらのパートナーを簡単に使い始めることができます。

## <a name="participating-partners"></a>参加しているパートナー
次の表に、現在サポートされているパートナー ソリューションの一覧を示します。 この一覧には新しいパートナーが追加されていくため、頻繁に確認するようにしてください。 

| Partner | ソリューション名 |
| ------- | ------------- |
| ![Incorta](./media/data-integration/incorta-logo.png) | Incorta Intelligent Ingest for Azure Synapse |
| ![Informatica](./media/data-integration/informatica_logo.png) | Informatica Intelligent Data Management Cloud |
| ![Qlik Data Integration (旧称 Attunity)](./media/business-intelligence/qlik_logo.png) | Qlik Data Integration (旧称 Attunity) |

## <a name="requirements"></a>要件
パートナー アプリケーションを選択すると、Azure Synapse Studio によって、この試用版に使用できるサンドボックス環境がプロビジョニングされるため、パートナー ソリューションを運用データで使用することを決定する前に、すばやく実験することができます。 次のオブジェクトが作成されます。 

|  オブジェクト  |    詳細    |
| -------- | ------------- |
| **Partner_[PartnerName]_pool** という名前の [専用 SQL プール](../overview-what-is.md) | DW100c パフォーマンス レベル。 |
| **Partner_[PartnerName]_login** という名前の [SQL ログイン](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) | `master` データベース上に作成されます。 この SQL ログインのパスワードは、試用版の作成時にユーザーが指定します。|
| [データベース ユーザー](../../azure-sql/database/logins-create-manage.md) | 新しい SQL ログインにマップされる新しいデータベース ユーザー。 このユーザーは、新しく作成されたデータベースの db_owner ロールに追加されます。 |

すべての場合で、 **[PartnerName]** は、試用版を提供するサードパーティの ISV の名前です。 

### <a name="security"></a>セキュリティ 
必要なオブジェクトが作成されると、Synapse Studio によって、新しいサンドボックス環境に関する情報がパートナー アプリケーションに送信されるため、カスタマイズされた試用エクスペリエンスが可能になります。 次の情報がパートナーに送られます。 
- 名
- 姓
- [電子メール アドレス]
-  接続を確立するために必要な Synapse 環境の詳細:     
    - Synapse ワークスペースの DNS 名 (サーバー名)
    - SQL プールの名前 (データベース)
    - SQL ログイン (ユーザー名のみ)
    
新しく作成された SQL ログインのパスワードを含め、パートナー アプリケーションとパスワードを共有することはありません。 ユーザーがパートナー アプリケーションにパスワードを入力する必要があります。

### <a name="costs"></a>コスト
パートナー試用版用に作成された専用の SQL プールには継続的なコストが発生します。これは、DWU ブロック数と実行時間に基づきます。 このパートナーの試用版用に作成された SQL プールは、使用していないときに一時停止して、不要な料金が発生しないようにしてください。 

## <a name="starting-a-new-partner-trial"></a>新しいパートナー試用版の開始 

1) Synapse Studio のホーム ページの、 **[その他の検出]** で、 **[パートナーの参照]** を選択します。
2) [パートナーの参照] ページには、Azure Synapse との直接接続を許可する試用版を現在提供しているすべてのパートナーが表示されます。 パートナー ソリューションを選択します。
3) パートナーの詳細ページに、このアプリケーションに関する関連情報と、それらのソリューションの詳細についてのリンクが表示されます。 試用版を開始する準備ができたら、 **[パートナーに接続]** を選択します。
4) **[[PartnerName] ソリューションへの接続]** ページで、このパートナー接続の要件に注意します。 必要に応じて SQL プール名と SQL ログイン パラメーターを変更し (または既定値を受け入れ)、新しい SQL ログインのパスワードを入力し、 **[接続]** を選択します。

パートナーの試用版に必要なオブジェクトが作成されます。 次に、パートナー ページに転送され、追加情報 (必要な場合) を提供し、試用版を開始します。 

> [!NOTE]
> Microsoft は、パートナー試用版エクスペリエンスを制御することはありません。 パートナーは、独自の条件で製品の試用版を提供しているため、エクスペリエンス、試用版の使用可能性、機能はパートナーによって異なる場合があります。 Microsoft では、Synapse Studio で提供されているサードパーティ アプリケーションに対するサポートは行っていません。 

## <a name="next-steps"></a>次のステップ

その他のパートナーの詳細については、[データ統合パートナー](data-integration.md)、[データ管理パートナー](data-management.md)、[機械学習と AI のパートナー](machine-learning-ai.md)に関する記事を参照してください。