---
title: Team Data Science Process ライフサイクルの顧客による受け入れステージ - Azure | Microsoft Docs
description: データ サイエンス プロジェクトの顧客による受け入れステージの目標、タスク、成果物
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 0030698bafc3d92a1c6daf944ba496421080912c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="customer-acceptance"></a>顧客による受け入れ

この記事では、Team Data Science Process (TDSP) の顧客による受け入れステージに関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

   1. **ビジネスの把握**
   2. **データの取得と理解**
   3. **モデリング**
   4. **デプロイ**
   5. **顧客による受け入れ**

TDSP ライフサイクルを視覚化すると、次のようになります。 

![TDSP ライフサイクル](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
**プロジェクトの成果物を完成させる**: パイプライン、モデル、およびそれらの運用環境でのデプロイが顧客の目標を満たしていることを確認します。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 2 つの主な課題に取り組みます。

   * **システムの検証**: デプロイ済みのモデルとパイプラインが顧客のニーズを満たしていることを確認します。
   * **プロジェクトのハンドオフ**: 運用環境でシステムを運用することになっている組織にプロジェクトをハンドオフします。

顧客は、システムを運用環境にデプロイして独自のクライアント アプリケーションで利用できるようにするため、そのシステムがビジネス ニーズを満たし、許容可能な精度で質問に回答できるかどうかを検証します。 すべてのドキュメントに対して最終処理とレビューが行われます。 運用を担う組織にプロジェクトがハンドオフされます。 この場合の組織とは、たとえば運用環境でのシステム運用を担当する IT 部門、顧客のデータ サイエンス チーム、顧客の代理業者などです。 

## <a name="artifacts"></a>アーティファクト
この最終ステージで生成される主なアーティファクトは、**顧客向けプロジェクトの終了レポート**です。 この技術的なレポートには、システムの運用方法を学習するために役立つプロジェクトのすべての詳細が含まれています。 TDSP には、[終了レポート](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) テンプレートが用意されています。 このテンプレートをそのまま使用することも、特定のクライアントのニーズに合わせてカスタマイズすることもできます。 


## <a name="next-steps"></a>次の手順

TDSP のライフサイクルの各ステップへのリンクを次に示します。

   1. [ビジネスの把握](lifecycle-business-understanding.md)
   2. [データの取得と理解](lifecycle-data.md)
   3. [モデリング](lifecycle-modeling.md)
   4. [デプロイ](lifecycle-deployment.md)
   5. [顧客による受け入れ](lifecycle-acceptance.md)

特定のシナリオ のプロセスに伴うすべての手順を初めから終わりまで完全に説明するチュートリアルが用意されています。 [例を含むチュートリアル](walkthroughs.md)の記事に、各シナリオが、簡潔な説明とリンク付きで示されています。 これらのチュートリアルは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明しています。 

Azure Machine Learning Studio を使用して TDSP の手順を実行する方法の例については、「[Azure Machine Learning での Team Data Science Process の使用](http://aka.ms/datascienceprocess)」を参照してください。
