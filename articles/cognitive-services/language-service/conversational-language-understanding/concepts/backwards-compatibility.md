---
title: Conversational Language Understanding の下位互換性
titleSuffix: Azure Cognitive Services
description: LUIS と Conversational Language Understanding の間の下位互換性について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: f4c354780cd5d32c2c801b76bde2b8b6abd2d555
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092207"
---
# <a name="backwards-compatibility-with-luis-applications"></a>LUIS アプリケーションとの下位互換性

既存の LUIS アプリケーションのコンテンツの一部を Conversational Language Understanding で再利用できます。 Conversational Language Understanding プロジェクトを使うと、次のことができます。
* LUIS アプリケーションの JSON ファイルから CLU 会話プロジェクトを作成します。
* オーケストレーション ワークフロー プロジェクトに接続できる LUIS アプリケーションを作成します。  
  
> [!NOTE]
> このガイドでは、言語リソースを作成してあるものとします。 サービスを使い始める場合は、[クイックスタートの記事](../quickstart.md)をご覧ください。 

## <a name="import-a-luis-application-json-file-into-conversational-language-understanding"></a>LUIS アプリケーションの JSON ファイルを Conversational Language Understanding にインポートする

LUIS アプリケーションの JSON ファイルをインポートするには、 **[新しいプロジェクトの作成]** の横にあるアイコンをクリックして、 **[インポート]** を選びます。 次に、LUIS ファイルを選びます。 Conversational Language Understanding に新しいプロジェクトをインポートするときに、エクスポートした LUIS アプリケーションの JSON ファイルを選ぶと、現在利用可能な機能を備えたプロジェクトがサービスによって自動的に作成されます。

:::image type="content" source="../media/import.png" alt-text="会話プロジェクトのインポート ボタンを示すスクリーンショット。" lightbox="../media/import.png":::

### <a name="supported-features"></a>サポートされている機能
LUIS の JSON アプリケーションを CLU にインポートすると、次の機能が選ばれた **会話** プロジェクトが作成されます。

|**機能**|**ノート**|
| :- | :- |
|意図|すべての意図は、同じ名前で CLU の意図として転送されます。|
|ML のエンティティ|ML のすべてのエンティティは、同じ名前で CLU のエンティティとして転送されます。 ML のラベルは維持され、エンティティの学習済みコンポーネントのトレーニングに使われます。 構造化された ML のエンティティは、最上位レベルのエンティティとしてのみ転送されます。 個々のサブエンティティは無視されます。|
|発話|LUIS のすべての発話は、意図とエンティティのラベルを含む CLU の発話として転送されます。 構造化された ML のエンティティ ラベルでは、最上位レベルのエンティティ ラベルのみが考慮され、個々のサブエンティティ ラベルは無視されます。|
|culture|会話プロジェクトのプライマリ言語は、LUIS アプリのカルチャです。 カルチャがサポートされていない場合、インポートは失敗します。 |

### <a name="unsupported-features"></a>サポートされていない機能

LUIS の JSON アプリケーションを CLU にインポートすると、特定の機能は無視されますが、アプリケーションのインポートはブロックされません。 次の機能は無視されます。

|**機能**|**ノート**|
| :- | :- |
|アプリケーションの設定|句読点の正規化、分音記号の正規化、すべてのトレーニング データの使用などの設定は、意図とエンティティの予測の向上を目的としたものでした。 CLU の新しいモデルは、句読点などの小さな変更の影響を受けないため、設定として使用できません。|
|特徴|フレーズ リストの特徴と意図に対する特徴はすべて無視されます。 特徴は LUIS にセマンティックの理解を導入するためのものでしたが、CLU の新しいモデルでは標準で提供できます。|
|パターン|パターンは、意図の分類での品質の欠如をカバーするために使われました。 CLU の新しいモデルは、パターンを使わなくてもパフォーマンスが向上すると予想されます。|
|Pattern.Any エンティティ|Pattern.Any エンティティは、ML のエンティティ抽出での品質の欠如をカバーするために使われました。 CLU の新しいモデルは、Pattern.Any エンティティを使わなくてもパフォーマンスが向上すると予想されます。|
|正規表現エンティティ| 現在、サポートされていません |
|構造化された ML エンティティ| 現在、サポートされていません |
|エンティティの一覧表示 | 現在、サポートされていません |
|事前構築済みのエンティティ | 現在、サポートされていません |
|ML エンティティでのエンティティの必須の特徴 | 現在、サポートされていません |
|ML エンティティでのエンティティの必須ではない特徴 | 現在、サポートされていません |
|ロール | 現在、サポートされていません |

## <a name="use-a-published-luis-application-in-conversational-language-understanding-orchestration-projects"></a>Conversational Language Understanding オーケストレーション プロジェクトで発行済み LUIS アプリケーションを使用する

Conversational Language Understanding に使うのと同じ言語リソースによって所有されている発行済み LUIS アプリケーションにのみ接続できます。 作成リソースを、**西ヨーロッパ** アプリケーションの言語 **S** リソースに変更できます。 LUIS アプリケーションに別のリソースを割り当てる手順については、[LUIS のドキュメント](../../../luis/luis-how-to-azure-subscription.md#assign-luis-resources)をご覧ください。 LUIS アプリケーションをエクスポートして言語リソースにインポートすることもできます。 LUIS アプリケーションをオーケストレーション プロジェクトに接続したときにそれらが Conversational Language Understanding に表示されるようにするには、それらをトレーニングして発行する必要があります。


## <a name="next-steps"></a>次のステップ

[Conversational Language Understanding の概要](../overview.md)
