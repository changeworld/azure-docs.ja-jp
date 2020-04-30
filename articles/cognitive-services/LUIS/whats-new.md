---
title: 新機能 - Language Understanding (LUIS)
description: この記事では、Azure Cognitive Services Language Understanding API に関するニュースが定期的に更新されています。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 8e3b6f729f69866cdb797cdfd1887de4f5e2be05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097729"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding の新機能

サービス内の新機能について説明します。 これらの項目には、リリース ノート、ビデオ、ブログ記事、およびその他の種類の情報が含まれています。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="release-notes"></a>リリース ノート

### <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

### <a name="november-4-2019---ignite"></a>2019 年 11 月 4 日 - Ignite

* ビデオ - [LUIS と Azure Cognitive Services を使用した高度な自然言語理解 (NLU) モデル | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 開発者の生産性の向上
    * [予測エンドポイント V3](luis-migration-api-v3.md) の一般提供。
    * .lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 形式のアプリをインポートおよびエクスポートする機能。 これにより、有効な CI/CD プロセスが可能になります。
* 言語の増加
    * [アラビア語とヒンディー語](luis-language-support.md) (パブリック プレビュー)。
* 事前構築済みのモデル
    * [事前構築済みドメイン](luis-reference-prebuilt-domains.md)の一般提供 (GA)
    * 日本語の[事前構築済みエンティティ](luis-reference-prebuilt-entities.md#japanese-entity-support) - 年齢、通貨、数値、割合は、V3 ではサポートされていません。
    * イタリア語の[事前構築済みエンティティ](luis-reference-prebuilt-entities.md#italian-entity-support) - 年齢、通貨、ディメンション、数値、割合、解像度が V2 から変更されました。
* [preview.luis.ai ポータル](https://preview.luis.ai)でのユーザー エクスペリエンスの強化 - 複雑なモデルの構築とデバッグを可能にするため、ラベル付けエクスペリエンスが改良されました。 プレビュー ポータルのチュートリアルをお試しください。
    * [意図のみ](tutorial-intents-only.md)
    * [分解可能な機械学習エンティティ](tutorial-machine-learned-entity.md)
* 高度な言語理解機能 - 少ない労力で[洗練された言語モデルを構築](luis-concept-entity-types.md)します。
* モデル レベルで機械学習機能を定義し、モデルを他のモデルへの信号として使用できるようにします。たとえば、エンティティを機能として、意図やその他のエンティティに使用します。
* 新しい、拡張された[制限](luis-limits.md) - フレーズ リストおよびフレーズの合計のより高い最大値、機能制限としての新しいモデル
* 深い階層構造の形式でテキストから情報を抽出することで、メッセージ交換アプリケーションをより強力にします。

    ![機械学習したエンティティの画像](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019 年 9 月 3 日

* Azure オーサリング リソース - [今すぐ移行](luis-migration-authoring.md)。
    * 1 つの Azure リソースにつき 500 個のアプリ
    * 1 つのアプリにつき 100 個のバージョン
* 事前に作成されたエンティティのトルコ語のサポート
* datetimeV2 のイタリア語のサポート

### <a name="july-23-2019"></a>2019 年 7 月 23 日

* [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) の 1.2.3 への更新
    * イタリア語での年齢、気温、サイズ、および通貨レコグナイザー。
    * 英語での感謝祭に基づく日付を正しく計算するための祝日認識機能の強化。
    * フランス語での日付と時刻以外のエンティティの偽陽性を減らすための DateTime の機能強化。
    * 英語での DateRange の暦年/学校度/会計年度と頭字語のサポート。
    * 中国語と日本語での PhoneNumber 認識機能の強化。
    * 英語での NumberRange のサポートの強化。
    * パフォーマンスの向上。

### <a name="june-24-2019"></a>2019 年 6 月 24 日

* 次へ、前へ、最後などの順序付けをサポートする [OrdinalV2 事前構築済みエンティティ](luis-reference-prebuilt-ordinal-v2.md)。 英語圏のみ。

### <a name="may-6-2019---build-conference"></a>2019 年 5 月 6 日- //Build Conference

Build 2019 Conference では、次の機能が公開されました。

* [V3 API 移行ガイドのプレビュー](luis-migration-api-v3.md)
* [改善された分析ダッシュ ボード](luis-how-to-use-dashboard.md)
* [改善された事前構築済みドメイン](luis-reference-prebuilt-domains.md)
* [動的なリスト エンティティ](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部エンティティ](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>ブログ

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>ビデオ

### <a name="2019-ignite-videos"></a>2019 Ignite ビデオ

[LUIS と Azure Cognitive Services を使用した高度な自然言語理解 (NLU) モデル | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Build のビデオ

[How to use Azure Conversational AI to scale your business for the next generation (Azure の対話型 AI を使用して次世代のビジネスを拡大する方法)](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>サービスの更新情報

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)
