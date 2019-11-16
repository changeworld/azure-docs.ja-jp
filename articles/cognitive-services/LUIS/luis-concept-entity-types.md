---
title: エンティティの種類 - LUIS
titleSuffix: Azure Cognitive Services
description: エンティティは、発話からデータを抽出します。 エンティティの種類では、データの予測可能な抽出が提供されます。 エンティティには、機械学習と非機械学習の 2 種類があります。 発話で使用しているエンティティの種類を把握していることが重要です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 8e91a475c7fd7f207c8b38d3da8abe7affd668b2
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013500"
---
# <a name="entities-and-their-purpose-in-luis"></a>エンティティと LUIS におけるその目的

エンティティの主な目的は、クライアント アプリケーションに予測可能な抽出データを提供することです。 "_オプション_" の 2 つ目の目的は、記述子により意図の予測を向上させることです。 

エンティティには、次の 2 つの種類があります。 

* 機械学習 - コンテキストより
* 非機械学習 - テキスト完全一致のため

データ抽出の選択肢の幅が最も広いため、常に機械学習エンティティから始めるようにしてください。

## <a name="entity-compared-to-intent"></a>エンティティと意図の比較

エンティティは、抽出しようとしている発話内のデータの概念を表します。 

次の 3 つの発話について考えます。

|発話|抽出されたデータ|説明|
|--|--|--|
|`Help`|-|何も抽出されません。|
|`Send Bob a present`|Bob、プレゼント|Bob はタスクを完了するうえで明らかに重要です。 プレゼントは十分な情報である可能性も、ボットがフォローアップの質問によりそのプレゼントは何であるかを明確にする必要がある可能性もあります。|
|`Send Bob a box of chocolates.`|Bob と箱入りのチョコレートという 2 つの重要なデータは、ユーザーの要求を完了するうえで重要です。|

発話には、多数のエンティティを含めることも、まったく含めないこともできます。 クライアント アプリケーションでは、そのタスクを実行するためにエンティティを必要とする "_可能性_" があります。 

一方、発話に対する意図の予測は "_必須_" で、発話全体を表します。 LUIS を使用するには、発話のサンプルが意図に含まれている必要があります。 発話の主な意図がクライアント アプリケーションにとって重要でない場合は、すべての発話を None 意図に追加します。 

アプリのライフサイクルの後半で、発話を中断する必要がある場合は、簡単に行うことができます。 作成中に発話を整理したり、クライアント アプリケーションで予測された意図を使用したりすることができます。 

クライアント アプリケーションで予測された意図を使用することは必須ではありませんが、予測エンドポイントの応答の一部として返されます。

## <a name="entities-represent-data"></a>エンティティはデータを表す

エンティティは、発話から引き出す必要があるデータです。 名前、日付、製品名、任意の単語のグループを指定できます。 

|発話|エンティティ|Data|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>場所.目的地|3<br>ニューヨーク|
|3 月 5 日のニューヨーク発ロンドン行きのチケットを購入する|場所.出発地<br>場所.目的地<br>事前構築済みの datetimeV2|ニューヨーク<br>ロンドン<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>エンティティは省略可能だが強く推奨される

意図は必須ですが、エンティティは省略可能です。 アプリ内のすべての概念を表すエンティティを作成する必要はありません。クライアント アプリケーションがアクションを実行するために必須のエンティティのみ作成します。 

ボットが継続するために必要な詳細が発話に含まれない場合は、エンティティを追加する必要はありません。 アプリが成熟するに従い、後で追加することもできます。 

情報の使用方法が不明の場合は、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序数](luis-reference-prebuilt-ordinal.md)、[電子メール アドレス](luis-reference-prebuilt-email.md)、[電話番号](luis-reference-prebuilt-phonenumber.md)などの一般的な事前構築済みのエンティティを追加します。

## <a name="design-entities-for-decomposition"></a>分解のためのエンティティの設計

機械学習エンティティを使用して、エンティティの設計を開始します。 これにより、時間の経過によるエンティティの成長と変化を簡単に設計できます。 **サブコンポーネント** (子エンティティ) を**制約**と**記述子**と共に追加して、エンティティの設計を完了します。 

分解のための設計により、LUIS では、クライアント アプリケーションに対してより深いレベルのエンティティ解決を返すことができます。 これにより、クライアント アプリケーションはビジネス ルールに集中して、データの解決を LUIS に任せることができます。

### <a name="machine-learned-entities-are-primary-data-collections"></a>機械学習エンティティは、プライマリ データ コレクションである

機械学習エンティティは、最上位レベルのデータ単位です。 サブコンポーネントは、機械学習エンティティの子エンティティです。 

**制約**は、データを識別して抽出するためのルールを適用するテキスト完全一致のエンティティです。 **記述子**は、予測のために単語や語句の関連性を高める目的で適用される機能です。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>エンティティの種類

データの抽出方法と抽出後にそれを表現する方法に基づいてエンティティを選択します。

|エンティティの種類|目的|
|--|--|
|[**機械学習**](tutorial-machine-learned-entity.md)|エンティティ型に関係なく、エンティティの親グループ。 機械学習エンティティは、発話内のコンテキストから学習します。 これにより、発話例での配置の変動が顕著になります。 |
|[**リスト**](reference-entity-list.md)|項目の一覧と、**テキスト完全一致**を使用して抽出されたそれらのシノニム。|
|[**Pattern.any**](reference-entity-pattern-any.md)|エンティティの末尾の特定が困難なエンティティ。 |
|[**事前構築済み**](luis-reference-prebuilt-entities.md)|URL や電子メールなど、特定の種類のデータを抽出するために既にトレーニングされています。 これらの事前構築済みエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。|
|[**正規表現**](reference-entity-regular-expression.md)|**テキスト完全一致**のために正規表現を使用します。|

### <a name="entity-role-defines-context"></a>エンティティ ロールがコンテキストを定義する

エンティティのロールは、発話内のコンテキストに基づく名前付きエイリアスです。 例として、出発地と到着地の 2 つの場所があるフライトを予約するための発話が挙げられます。

`Book a flight from Seattle to Cairo`

`location` エンティティの 2 つの例を抽出する必要があります。 チケットの購入を完了するには、クライアント アプリケーションがそれぞれの場所の種類を把握している必要があります。 `location` エンティティには `origin` と `destination` の 2 つのロールが必要であり、その両方が発話の例でマークされている必要があります。 

LUIS によって検出された `location` のロールが判断できない場合でも、場所のエンティティは返されます。 クライアント アプリケーションでは、ユーザーが意図した場所の種類を判断するために、フォローアップの質問をする必要があります。 

発話には複数のエンティティが存在する可能性があり、それらのエンティティはロールを使用せずに抽出できます。 文のコンテキストがエンティティの値を示している場合は、ロールを使用する必要があります。

発話に場所の一覧が含まれる場合 `I want to travel to Seattle, Cairo, and London.`、これは各項目に追加の意味がない一覧です。 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>最大数を超えるエンティティが必要な場合 

上限を超えて必要な場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 

## <a name="entity-prediction-status"></a>エンティティ予測の状態

LUIS ポータルは、発話の例のエンティティに、選択したエンティティと異なるエンティティ予測があるときを示します。 この異なるスコアは、現在のトレーニング済みのモデルに基づいています。 

## <a name="next-steps"></a>次の手順

正しい[発話](luis-concept-utterance.md)の概念について学習します。 

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
