---
title: カスタム固有表現認識 (NER) に使用される定義と用語
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) の使用時に遭遇する可能性のあるいくつかの定義と用語について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c57d4efffc207759b8d3da343bda397ee885dc0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439084"
---
# <a name="custom-named-entity-recognition-ner-definitions-and-terms"></a>カスタム固有表現認識 (NER) の定義と用語

この記事を使用して、カスタム NER の使用時に遭遇する可能性のあるいくつかの定義と用語について学習してください。

## <a name="project"></a>プロジェクト

プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使用する Azure リソースへの共同作成者アクセス権を持つ他のユーザーのみです。
カスタム エンティティ抽出プロジェクトを作成するための前提条件として、[新しいプロジェクトを作成](how-to/create-project.md)するときに、データセットを含むストレージ アカウントにリソースを接続する必要があります。 プロジェクトには、コンテナー内にあるすべての `.txt` ファイルが自動的に組み込まれます。

プロジェクト内で、以下を実行できます。

* **データにタグを付ける**: モデルのトレーニング時にそのモデルが抽出する内容を学習するように、データにタグを付けるプロセスです。
* **モデルを構築してトレーニングする**: モデルがタグ付けされたデータから学習を開始する、プロジェクトのコア ステップです。 
* **モデルの評価の詳細を表示する**: モデルのパフォーマンスを確認して、改善の余地があるかどうか、または結果に満足できるかどうかを判断します。
* **モデルを改善する**: モデルの問題点が判明し、パフォーマンスを向上させる方法がわかった場合にこれを行います。 
* **モデルをデプロイする**: モデルを使用できるようにします。 
* **モデルをテストする**: データセットでモデルをテストします。

## <a name="model"></a>モデル

モデルは、特定のタスク (ここでは、カスタムの固有表現認識) を実行するためにトレーニングされたオブジェクトです。

* **モデルのトレーニング** は、タグ付けされたデータに基づいて何を抽出するかをモデルに教えるプロセスです。
* **モデルの評価** は、モデルがどの程度適切に実行されるかを把握するために、トレーニングの直後に行うプロセスです。
* **モデルのデプロイ** は、モデルを使用できるようにするプロセスです。

## <a name="entity"></a>Entity

エンティティは、特定の種類の情報を示すテキスト範囲です。 このテキスト範囲は、1 つ以上の単語 (またはトークン) で構成されます。 カスタム固有表現認識 (NER) では、エンティティはテキストから抽出する情報を表します。 

たとえば、"*John borrowed 25,000 USD from Fred. (John が Fred から 25,000 USD 借りました。)* " という文があるとします。 エンティティは以下になります。 

| エンティティ名/型 | Entity |
| -- | -- |
| Borrower Name (借り手名) | *John* |
| Lender Name (貸し手名) | *Fred* |
| Loan Amount (貸し付け金額) | *25,000 USD* |

## <a name="next-steps"></a>次のステップ

* [データとサービスの制限](service-limits.md)。
* [カスタム NER の概要](../overview.md)。
