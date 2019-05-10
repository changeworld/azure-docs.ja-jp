---
title: Form Recognizer とは
titleSuffix: Azure Cognitive Services
description: Form Recognizer を使用してフォームとテーブルのデータを解析する方法を学習します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143213"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer とは

Azure Form Recognizer は、機械学習テクノロジを使用して、フォーム ドキュメントからキーと値のペアおよびテーブル データを識別して抽出するコグニティブ サービスです。 Form Recognizer は、元のファイル内の関係を含む構造化データを出力します。 複雑さを軽減し、ワークフローまたはアプリケーションに簡単に統合するために、単純な REST API を使用してカスタム Form Recognizer モデルを呼び出すことができます。 開始するうえで必要になるのは、入力素材と同じ種類の 5 つのフォーム ドキュメントまたは 1 つの空のフォームだけです。 手作業による操作やデータ サイエンスに関する専門知識をあまり必要とすることなく、特定のコンテンツに合わせた結果をすばやく正確に得ることができます。

## <a name="request-access"></a>アクセスの要求
Form Recognizer は、アクセスが制限されたプレビューとして利用可能です。 プレビューへのアクセスを取得するには、[Cognitive Services Form Recognizer アクセス要求](https://aka.ms/FormRecognizerRequestAccess)フォームに記入して送信します。 このフォームでは、ユーザー、会社、Form Recognizer を使用するユーザー シナリオに関する情報が要求されます。 要求が Azure Cognitive Services チームによって承認されると、サービスへのアクセス方法を説明したメールが届きます。

## <a name="what-it-does"></a>実行内容

入力データが送信されると、アルゴリズムに基づいてトレーニングが行われ、種類別にフォームが分類されます。さらに、どのような種類のキーとテーブルが存在するかの発見に加えて、値をキーに関連付け、エントリをテーブルに関連付けための学習が行われます。 教師なし学習では、手作業によるデータのラベル付けまたは集中的なコーディングとメンテナンスを行うことなく、モデルはフィールドとエントリ間のレイアウトと関係を理解できます。 これに対し、トレーニング済み機械学習モデルは、標準化されたデータを必要とし、業界固有のフォームのような従来の形式から逸脱する入力素材では正確性が低下します。

モデルのトレーニング後、テスト、再トレーニングを行い、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

## <a name="what-it-includes"></a>備えている機能

Form Recognizer は、REST API として利用できます。 API を呼び出して、モデルを作成、トレーニング、およびスコア付けすることができます。また、オプションでローカルの Docker コンテナーでモデルを実行することもできます。

## <a name="input-requirements"></a>入力の要件

Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* JPG、PNG、または PDF 形式 (テキストまたはスキャン済み)。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、推奨されます。
* ファイル サイズは 4 メガバイト (MB) 未満である必要があります
* 画像の場合、寸法は 50 x 50 から 4,200 x 4,200 ピクセルの範囲内である必要があります
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません
* ラテン アルファベット (英語の文字) を使用する必要があります
* 印刷されたデータ (手書きではありません)
* キーと値を含める必要があります
* キーは値の上または左に配置されていてかまいませんが、下または右に配置されていてはいけません。

さらに、Form Recognizer は、次の種類の入力データをまだサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど) 
* チェックボックスまたはラジオ ボタン
* 50 ページを超える PDF ドキュメント

## <a name="where-do-i-start"></a>どこから始めるか

**手順 1:** Azure portal で Form Recognizer リソースを作成します。

**手順 2:** 実際に体験するためのクイック スタートを試します。
* [クイック スタート:cURL で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する](quickstarts/curl-train-extract.md)
* [クイック スタート:Python で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する](quickstarts/python-train-extract.md)

学習目的には無料のサービスをお勧めしますが、無料のページの数は 1 か月あたり 500 ページに制限されることに注意してください。

**手順 3:** REST API を確認します。次の API を使用して、トレーニングを行い、フォームから構造化データを抽出します。

| REST API | 説明 |
|-----|-------------|
| トレーニング | 同じ種類の 5 つのフォームまたは 1 つの空のフォームを使用して、フォームを分析する新しいモデルをトレーニングします。  |
| 分析  |ストリームとして渡された単一のドキュメントを分析し、カスタム モデルを使用してフォームからキーと値のペアとテーブルを抽出します。  |

[REST API リファレンス ドキュメント](https://aka.ms/form-recognizer/api)を参照してください。 

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

このサービスは、[オンライン サービス条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)に基づいて、Azure サービスの[プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)として提供されます。 Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

[クイック スタート](quickstarts/curl-train-extract.md)に従って [Form Recognizer API シリーズ](https://aka.ms/form-recognizer/api)の使用を開始します。
