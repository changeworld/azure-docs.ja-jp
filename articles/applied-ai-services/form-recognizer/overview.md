---
title: Azure Form Recognizer とは? (更新済み)
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer サービスを使用すると、フォーム ドキュメントからキーと値のペアとテーブル データを識別して抽出することや、領収書や名刺から主要な情報を抽出することができます。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
keywords: 自動データ処理、ドキュメント処理、自動データ入力、フォーム処理
ms.openlocfilehash: 272104444ca3389f69c639fba0984552b93c5893
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712118"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>Azure Form Recognizer とは?

Azure Form Recognizer は、機械学習テクノロジを利用して自動ドキュメント処理ソフトウェアを構築することができる [Azure Applied AI Services](../../applied-ai-services/index.yml) です。 Form Recognizer は、フォームとドキュメントを分析し、テキストとデータを抽出して、フィールドのリレーションシップをキーと値のペアとしてマップし、構造化された JSON 出力を返します。 過剰な手動操作を行うことなく、広範なデータ サイエンスに関する専門知識なしで、特定のコンテンツに合わせた正確な結果をすばやく得られます。 Form Recognizer を使用して、アプリケーションとワークフローでのデータ処理を自動化し、データドリブン戦略を強化し、ドキュメント検索機能を強化します。

Form Recognizer では、次のドキュメント データを簡単に識別、抽出、分析します。

* テーブルの構造と内容。
* フォームの要素とフィールドの値。
* タイプ入力および手書きされた英数字テキスト。
* 要素間のリレーションシップ。
* キーと値のペア。
* 境界ボックス座標を含む要素の位置。

このドキュメントには、次の種類の記事が含まれています。

* [**概念**](concept-layout.md)では、サービスの機能と特徴について詳しく説明します。
* [**クイックスタート**](quickstarts/try-sdk-rest-api.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](build-training-data-set.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [**チュートリアル**](tutorial-ai-builder.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="form-recognizer-features-and-development-options"></a>Form Recognizer の機能と開発オプション

### <a name="form-recognizer-ga-v21"></a>[Form Recognizer GA (v2.1)](#tab/v2-1)

Form Recognizer サービス v2.1 では、次の機能がサポートされています。 各機能の詳細を確認したり、API リファレンスを参照したりするには、表のリンクを使用してください。

| 特徴量 | 説明 | 開発オプション |
|----------|--------------|-------------------------|
|[**Layout API**](concept-layout.md) | フォームとドキュメントからのテキスト、選択マーク、テーブル構造、およびそれらの境界ボックス座標の抽出と分析。 | <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/layout-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-layout)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**名刺モデル**](concept-business-card.md) | 名刺の重要な情報の自動データ処理と抽出。| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**身分証明書モデル**](concept-id-document.md) | 米国の運転免許証と国際パスポートの重要な情報の自動データ処理と抽出。| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**請求書モデル**](concept-invoice.md) | 売上請求書の重要な情報の自動データ処理と抽出。 | <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**レシート モデル**](concept-receipt.md) | レシートの重要な情報の自動データ処理と抽出。| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**カスタム モデル**](concept-business-card.md) | 個別のビジネス データおよびユース ケースに固有のフォームおよびドキュメントからのデータの抽出と分析。| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[Form Recognizer プレビュー (v3.0)](#tab/v3-0)

>[!NOTE]
> Form Recognizer Studio は現在、パブリックプレビュー段階にあります。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。

Form Recognizer サービス v3.0 では、次の機能と開発オプションがサポートされています。 各機能の詳細を確認したり、API リファレンスを参照したりするには、表のリンクを使用してください。

| 特徴量 | 説明 | 開発オプション |
|----------|--------------|-------------------------|
|[🆕 **一般的なドキュメント モデル**](concept-general-document.md)|テキスト、テーブル、構造、キーと値のペア、および名前付きエンティティを抽出します。|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul> |
|[**レイアウト モデル**](concept-layout.md) | テキスト、選択マーク、およびテーブル構造を、対応する境界ボックスの座標と共にフォームとドキュメントから抽出します。</br></br> レイアウト API は、事前構築済みモデルに更新されました。 | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**カスタム モデル (更新済み)** ](concept-custom.md) | 個別のビジネス データおよびユース ケースに固有のフォームおよびドキュメントからのデータの抽出と分析。</br></br>カスタム モデル API v3.0 では、**カスタム フォームの署名の検出** がサポートされています。</li></ul>| <ul><li>[**Form Recognizer Studio**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**レシート モデル (更新済み)** ](concept-receipt.md) | レシートの重要な情報の自動データ処理と抽出。</br></br>レシート モデル v3.0 では、**単一ページのホテル領収書** の処理をサポートしています。| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**身分証明書モデル (更新済み)** ](concept-id-document.md) |米国の運転免許証と国際パスポートの重要な情報の自動データ処理と抽出。</br></br>事前構築された身分証明書 API では、**米国の運転免許証からの承認、制限、車種区分の抽出** がサポートされています。 |<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**請求書モデル**](concept-invoice.md) | 請求書の重要な情報の自動データ処理と抽出。 | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**名刺モデル**](concept-business-card.md) |名刺の重要な情報の自動データ処理と抽出。| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションが必要です - [**無料で作成する**](https://azure.microsoft.com/free/cognitive-services)。

* サブスクリプションを入手したら、Azure portal で [**Form Recognizer リソース**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)を作成します。 Free 価格レベル (F0) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

* 最後に、Form Recognizer サービスを試すために、Azure portal からリソースの **エンドポイント URL** と **API キー** を取得する必要があります。

  :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントを示すウィンドウ。":::

 ## <a name="form-recognizer-workflows"></a>Form Recognizer のワークフロー

| ドキュメントの種類 | 考慮事項 | 解決策 |
| -----------------|-------------------| ----------|
|<ul><li>**請求書**</li><li>**Receipt**</li><li>**名刺**</li></ul>| 請求書、レシート、または名刺のドキュメントは、英語のテキストで構成されていますか? | <ul><li>はい → [**請求書**](concept-invoice.md)、[**レシート**](concept-receipt.md)、または [**名刺**](concept-business-card.md)のモデル</li><li>いいえ → [**レイアウト**](concept-layout.md)または [**一般的なドキュメント (プレビュー)** ](concept-general-document.md) のモデル</li></ul>|
|<ul><li>**身分証明書**</li></ul>| ご利用の身分証明書は、米国の運転免許証または国際パスポートですか?| <ul><li>はい → [**身分証明書**](concept-id-document.md)モデル</li><li>いいえ → [**レイアウト**](concept-layout.md)または [**一般的なドキュメント (プレビュー)** ](concept-general-document.md) のモデル</li></ul>|
|<ul><li>**フォーム** または **ドキュメント**</li></ul>| フォームまたはドキュメントは、この事業または業界で一般的に使用される業界標準の形式ですか?| <ul><li>はい → [**レイアウト**](concept-id-document.md)または [**一般的なドキュメント (プレビュー)** ](concept-general-document.md) モデル</li><li>いいえ → [**カスタム モデルをトレーニングおよび構築する**](concept-layout.md) 

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

 Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 「[Form Recognizer のデータ、プライバシー、およびセキュリティ](/legal/cognitive-services/form-recognizer/fr-data-privacy-security)」を参照してください。 

## <a name="next-steps"></a>次の手順

### <a name="form-recognizer-v21"></a>[Form Recognizer v2.1](#tab/v2-1)

> [!div class="checklist"]
>
> * [**サンプルのラベル付けオンライン ツール**](https://aka.ms/fott-2.1-ga/)を試す
> * [**クライアント ライブラリと REST API のクイックスタート**](./quickstarts/try-sdk-rest-api.md)に従って、ドキュメントからのデータ抽出を開始します。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。
> * 詳細については、[**REST API のリファレンス ドキュメント**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)を参照してください。 
> * 前のバージョンの API に精通している場合は、「[**新機能**](./whats-new.md)」の記事を参照して、最近の変更点を確認してください。

### <a name="form-recognizer-v30"></a>[Form Recognizer v3.0](#tab/v3-0)

> [!div class="checklist"]
>
> * [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com) を試してみる
> * 詳細については、[**REST API のリファレンス ドキュメント**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)を参照してください。 
> * 前のバージョンの API に精通している場合は、「[**新機能**](./whats-new.md)」の記事を参照して、最近の変更点を確認してください。

---
