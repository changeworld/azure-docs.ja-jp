---
title: Azure Synapse lake データベースの概念
description: Lake database の概念と、それがデータの構造にどのように役立つかについて説明します。
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5b030bc1551bc8d21101b127c123507f08abf62c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510424"
---
# <a name="lake-database"></a>Lake データベース

Azure Synapse Analytics の lake database を使用すると、顧客はデータベースの設計、格納されているデータに関するメタ情報、およびデータを格納する方法と場所を記述できる可能性があります。 Lake database では、今日のデータ湖の課題に対処しています。データがどのように構造化されているかを理解するのは困難です。  

![Lake database の概要](./media/concepts-lake-database/lake-database-overview.png)


## <a name="database-designer"></a>データベース デザイナー

新しいデータベースデザイナーを使用すると、lake データベースのデータモデルを作成し、追加情報を追加することができます。 すべてのエンティティと属性を記述して、モデルに関する詳細情報を提供することができます。これには、エンティティだけでなく、リレーションシップも含まれます。 特に、モデルリレーションシップの欠如は、data lake での相互作用に関する課題でした。 この課題は、データベースでは使用できるが、レイクでは利用できない可能性を提供する統合されたデザイナーで対応できるようになりました。 また、説明と考えられるデモの値をモデルに追加する機能により、そのデータについての理解を深めるために必要な情報が得られるようになります。 

## <a name="data-storage"></a>データ ストレージ 

Lake データベースは、Azure Storage アカウントの data lake を使用して、データベースのデータを格納します。 データは CSV 形式の Parquet に格納することができ、さまざまな設定を使用してストレージを最適化できます。 各 lake database は、リンクされたサービスを使用して、ルートデータフォルダーの場所を定義します。 すべてのエンティティについて、data lake のこのデータベースフォルダー内に、既定で個別のフォルダーが作成されます。 既定では、lake データベース内のすべてのテーブルで同じ形式が使用されますが、要求された場合、エンティティごとにデータの形式と場所を変更できます。 


## <a name="database-compute"></a>データベースコンピューティング

lake database は、Synapse SQL サーバーレス SQL プールで公開され、ユーザーがコンピューティングからストレージを分離する機能を提供 Apache Spark ます。 Lake database に関連付けられているメタデータを使用すると、さまざまなコンピューティングエンジンが、統合されたエクスペリエンスを提供するだけでなく、data lake で元々サポートされていなかった追加情報 (リレーションシップなど) も使用できます。 

## <a name="next-steps"></a>次のステップ

次のリンクを使用して、データベース デザイナーの機能の詳細を引き続き確認します。
- [Lake database のクイックスタートを作成する](quick-start-create-lake-database.md)
- [データベーステンプレートの概念](concepts-database-templates.md)
