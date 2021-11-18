---
title: Form Recognizer の新機能
titleSuffix: Azure Applied AI Services
description: Form Recognizer API の最新の変更点を把握します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 920f9191f92f62fad9a7d72b249bf16c4ec2e558
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708877"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-azure-form-recognizer"></a>Azure Form Recognizer の新機能

Form Recognizer サービスは、継続的に更新されます。 このページにブックマークを設定して、リリースノート、機能の強化、およびドキュメントの更新についての最新情報を入手してください。

## <a name="october-2021"></a>2021 年 10 月

### <a name="form-recognizer-new-preview-release"></a>Form Recognizer の新しいプレビュー リリース

 Form Recognizer の新しいプレビュー リリースでは、いくつかの新機能が導入されています。

* [**一般的なドキュメント**](concept-general-document.md) モデルは新しい API であり、事前トレーニング済みのモデルを使用して、テキスト、テーブル、構造、キーと値のペア、名前付きエンティティがフォームとドキュメントから抽出されます。
* [**ホテルの領収書**](concept-receipt.md)モデルが、構築済みの受信確認処理に追加されました。
* [**身分証明書の展開されたフィールド**](concept-id-document.md) ID モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。
* [**署名フィールド**](concept-custom.md)は、フォーム フィールド内の署名の有無を検出するカスタム フォームの新しいフィールドの種類です。

* [**言語の展開**](language-support.md) 122 言語 (印刷) と 7 言語 (手書き) のサポート。 Form Recognizer のレイアウトとカスタム フォームでは、最新のプレビューで[サポートされる言語](language-support.md)が 122 個に拡大されました。 これには、49 個の新しい言語 (ロシア語、ブルガリア語、その他のキリル語系やラテン語系など) での印刷テキストに対するテキスト抽出が含まれています。 また、手書きテキストの抽出では、英語を含む 7 個の言語と、新たに簡体字中国語、フランス語、ドイツ語、イタリア語、ポルトガル語、スペイン語の新しいプレビューがサポートされるようになりました。

* **テーブルとテキスト抽出の機能強化** レイアウトは、キーと値のテーブルとも呼ばれる単一行テーブルの抽出をサポートするようになりました。 テキスト抽出の機能強化には、デジタル PDF や身分証明書内の機械可読ゾーン (MRZ) テキストの処理の向上、全般的なパフォーマンスの向上が含まれます。

* [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com) サービスの使用を簡単にするために、Form Recognizer Studio にアクセスして、さまざまな事前構築済みモデルをテストしたり、カスタム モデルにラベルを付けてトレーニングしたりすることができるようになりました。

v3.0 プレビュー API の新しい [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)、[Python](quickstarts/try-v3-python-sdk.md)、または [.NET](quickstarts/try-v3-csharp-sdk.md) SDK を利用できます。

 #### <a name="form-recognizer-model-data-extraction"></a>Form Recognizer モデルのデータ抽出

  | **Model**   | **テキストの抽出** |**キーと値のペア** |**選択マーク**   | **テーブル**   |**エンティティ** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕一般的なドキュメント  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | 請求書  | ✓ | ✓  | ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |   |  ||
  | 身分証明書 | ✓  |   ✓  |   |   ||
  | 名刺    | ✓  |   ✓ |   |   ||
  | Custom             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="september-2021"></a>2021 年 9 月

* Azure portal の Form Recognizer リソースの概要ページで、[Azure メトリックス エクスプローラーの高度な機能](../../azure-monitor/essentials/metrics-charts.md)を使用できます。

    ### <a name="monitoring-menu"></a>監視メニュー

    :::image type="content" source="media/portal-metrics.png" alt-text="Azure portal の監視メニューを示すスクリーンショット":::

    ### <a name="charts"></a>グラフ

    :::image type="content" source="media/portal-metrics-charts.png" alt-text="Azure portal のサンプルのメトリック グラフを示すスクリーンショット。":::

*  **身分証明書** モデルの更新: サフィックスを含む名前がある場合、ピリオド (フル ストップ) の有無にかかわらず、正常に処理されます。

    |入力テキスト | 更新された結果 |
    |------------|-------------------------------------------|
    | William Isaac Kirby Jr. |**FirstName**: William Isaac</br></br>**LastName**: Kirby Jr. |
    | Henry Caleb Ross Sr | **FirstName**: Henry Caleb </br></br> **LastName**: Ross Sr |

## <a name="july-2021"></a>2021 年 7 月

### <a name="system-assigned-managed-identity-support"></a>システム割り当てマネージド ID のサポート

 システム割り当てマネージド ID を有効にして、プライベート ストレージ アカウントへの制限付きアクセスを Form Recognizer に許可できるようになりました。これには、Virtual Network (VNet) またはファイアウォールで保護されているものや、BYOS (Bring Your Own Storage) が有効になっているものが含まれます。 *詳細については、「* [Form Recognizer リソース用のマネージド ID を作成して使用する](managed-identity-byos.md)」を参照してください。

## <a name="june-2021"></a>2021 年 6 月

### <a name="form-recognizer-containers-v21-released-in-gated-preview"></a>Form Recognizer コンテナー v2.1 が限定的なプレビューとしてリリースされました

Form Recognizer 機能は、**レイアウト**、**名刺**、**身分証明書**、**レシート**、**請求書**、**カスタム** の 6 つの機能コンテナーでサポートされるようになりました。 これらを使用するには、[オンライン リクエスト](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)を送信し、承認を受ける必要があります。

*「* [**Form Recognizer の Docker コンテナーのインストールと実行**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout)」と「[**Form Recognizer コンテナーの構成**](containers/form-recognizer-container-configuration.md?branch=main)」を参照してください

### <a name="form-recognizer-connector-released-in-preview"></a>Form Recognizer コネクタがプレビューとしてリリースされました

  [**Form Recognizer コネクタ**](/connectors/formrecognizer)は、[Azure Logic Apps](../../logic-apps/logic-apps-overview.md)、[Microsoft Power Automate](/power-automate/getting-started)、[Microsoft Power Apps](/powerapps/powerapps-overview) と統合されます。 このコネクタは、カスタムおよび事前構築済みのフォーム、請求書、レシート、名刺、身分証明書から、ドキュメントのデータと構造を抽出および分析するワークフローのアクションとトリガーをサポートしています。

### <a name="form-recognizer-sdk-v310-patched-to-v311-for-c-java-and-python"></a>Form Recognizer SDK v3.1.0 に、C#、Java、Python 用のパッチが適用され、v3.1.1 になりました

このパッチは、`FormField` に `Text` があるが `BoundingBox` や `Page` の情報がないなど、サブ行項目フィールドが検出されない請求書に対処しています。

### <a name="c"></a>[**C#**](#tab/csharp)

| [リファレンス ドキュメント](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet パッケージ バージョン 3.1.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

### <a name="java"></a>[**Java**](#tab/java)

 | [リファレンス ドキュメント](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven アーティファクト パッケージの依存関係バージョン 3.1.1](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer/3.1.1) |

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

> [!NOTE]
> JavaScript SDK v3.1.0 に対する更新はありません。

| [リファレンス ドキュメント](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm パッケージ依存関係 form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer) |

### <a name="python"></a>[**Python**](#tab/python)

| [リファレンス ドキュメント](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.1](https://pypi.org/project/azure-ai-formrecognizer/) |

---

## <a name="may-2021"></a>2021 年 5 月

### <a name="form-recognizer-21-api-generally-available-ga-release"></a>Form Recognizer 2.1 API 一般提供 (GA) リリース

* Form Recognizer 2.1 は、一般提供されています。 この一般提供 (GA) リリースは、前の 2.1 プレビュー パッケージ バージョンで導入された変更が安定していることを示しています。 このリリースでは、次から情報とデータを検出して抽出することができます。

* [ドキュメント](concept-layout.md)
* [Receipts](./concept-receipt.md)
* [名刺](./concept-business-card.md)
* [Invoices](./concept-invoice.md)
* [ID ドキュメント](./concept-id-document.md)
* [カスタム フォーム](concept-custom.md)

#### <a name="get-started"></a>はじめに

[Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)にアクセスし、[クイックスタート](./quickstarts/try-sample-label-tool.md)に従ってください

### <a name="layout-adds-table-headers"></a>レイアウトによってテーブル ヘッダーが追加される

更新された Layout API テーブル機能では、複数の行にまたがることができる列ヘッダーを使用してヘッダーが認識されます。 各テーブル セルには、ヘッダーの一部であるかどうかを示す属性があります。 これは、テーブル ヘッダーを構成する行を識別するために使用できます。

#### <a name="sdk-updates"></a>SDK の更新

### <a name="c"></a>[**C#**](#tab/csharp)

| [リファレンス ドキュメント](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet パッケージ バージョン 3.0.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

#### <a name="non-breaking-changes"></a>**非破壊的変更**

* **FormRecognizerModelFactory** クラスが、**TextAppearance** および **ReadingOrder** の更新と **TextStyle** モデルの削除に対応しました。 「[破壊的変更](#breaking-changes-may)」を参照してください

#### <a name="breaking-changes-may"></a>**破壊的変更 (5 月)**

* クライアントは、既定でサポートされている最新のサービス バージョン (現在は v2.1) になります。 **FormRecognizerClientOptions** オブジェクトの **Version** プロパティでバージョン 2.0 を指定できます。

* **StartRecognizeIdentityDocuments**。 関連するすべての ID ドキュメント認識 API 機能で、メソッドおよびメソッド パラメーターの名前が **Identity** を使用するように変更され、_Id_ キーワードから置き換えられました。

* **FormReadingOrder**。 *ReadingOrder* の名前が **FormReadingOrder** に変更されました。

* **AsCountryRegion**。 *AsCountryCode* の名前が **AsCountryRegion** に変更されました。

* **TextAppearance** に **StyleName** および **StyleConfidence** プロパティが含まれるようになりました (以前は **TextStyle** オブジェクトの一部でした)。

* **FieldValueType**。  値 **Gender** がモデルから削除されました。

* **TextStyle** モデルが削除されました。

* **FieldValueGender** の種類が削除されました。

### <a name="java"></a>[**Java**](#tab/java)

  | [リファレンス ドキュメント](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven アーティファクト パッケージの依存関係バージョン 3.1.0](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) |

#### <a name="non-breaking-changes"></a>**非破壊的変更**

* **FormRecognizerClientBuilder** と **FormTrainingClientBuilder**。 **clientOptions** および **getDefaultLogOptions** メソッドが追加されました。

* **FormRecognizerLanguage**。  さらに言語フィールドが追加されました。

#### <a name="breaking-changes-may"></a>**破壊的変更 (5 月)**

* クライアントは、既定でサポートされている最新のサービス バージョン (現在は v2.1) になります。 **FormRecognizerClientBuilder** オブジェクトの **serviceVersion** メソッドでバージョン 2.0 を指定できます。

* v2.1-preview.1 と v2.1-preview.2 のサポートが削除されました。

* **beginRecognizeIdentityDocuments**。  関連するすべての ID ドキュメント認識 API 機能で、メソッドおよびメソッド パラメーターの名前が **Identity** を使用するように変更され、_Id_ キーワードから置き換えられました。

* **FormReadingOrder**。 *ReadingOrder* の名前が **FormReadingOrder** に変更され、クラスが拡張可能な文字列クラスにリファクタリングされました。

* **asCountryRegion**。 *asCountry* の名前が **asCountryRegion** メソッドに変更されました。

* **FieldValueType**。 フィールド値 *COUNTRY* の名前が **COUNTRY_REGION** に変更されました。

* **TextAppearance** クラスに **styleName** および **styleConfidence** プロパティが含まれるようになりました (以前は **TextStyle** オブジェクトの一部でした)。

* **FieldValueType**。 値 *Gender* がモデルから削除されました。

* **TextStyle** モデルが削除されました。

* **FieldValueGender** クラス型が削除されました。

* **pollInterval**。 pollInterval メソッドが、**RecognizeBusinessCardsOptions**、**RecognizeContentOptions**、**RecognizeCustomFormsOptions** **RecognizeIdentityDocumentOptions**、**RecognizeInvoicesOptions**、**RecognizeReceiptsOptions** の各クラスから削除されました。 ポーリング間隔は、Azure Core の [**SyncPoller setPollInterval**](/java/api/com.azure.core.util.polling.syncpoller.setpollinterval?view=azure-java-stable&preserve-view=true) または [**PollerFlux setPollInterval**](/java/api/com.azure.core.util.polling.pollerflux.setpollinterval?view=azure-java-stable&preserve-view=true) メソッドを使用して、それぞれ同期的または非同期的に更新できます。

* **FormLine**、**FormPage**、**FormTable**、**FormSelectionMark**、**TextAppearance**、**CustomFormModel**、**CustomFormModelInfo**、**CustomFormModelProperties**、**CustomFormSubmodel**、および **TrainingDocumentInfo** が変更できないモデル クラスになりました。

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

| [リファレンス ドキュメント](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm パッケージ依存関係 form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |

#### <a name="non-breaking-changes"></a>**非破壊的変更**

* すべての REST API 呼び出しが、v2.1 エンドポイントに移行されました。

* **KnownFormLocale** 列挙型が、フォーム ロケールの使用可能な値にアクセスするために追加されました。

* **beginRecognizeIdDocuments...** 。関連するすべての ID ドキュメント認識 API 機能で、メソッドとメソッド パラメーターの名前が **Identity** を使用するように変更され、_Id_ キーワードから置き換えられました。

* **FormReadingOrder** および **FormLanguage**。 *ReadingOrder* の名前が *FormReadingOrder* に変更されました。 *Language* の名前が **FormLanguage** に変更されました。

* **FormCountryRegionField** および **countryRegion**。 *FormCountryField* 型の名前が **FormCountryRegionField** に変更され、valueType *country* の名前が **countryRegion** に変更されました。

* **TextAppearance** インターフェイスに **styleName** および **styleConfidence** プロパティが含まれるようになりました (以前は **TextStyle** インターフェイスの名前および信頼度プロパティでした)。

* **KnownStyleName**、**KnownSelectionMarkState**、および **KnownKeyValueType** 列挙型が削除されました。

* **FormGenderField** 型が削除されました。 これまで _FormGenderField_ として生成された認識値は、FormStringField 型として返されるようになりましたが、値は変わりません。

* **TextStyle** 型が削除されました。

#### <a name="breaking-changes-may"></a>**破壊的変更 (5 月)**

**破壊的変更はありません**

### <a name="python"></a>[**Python**](#tab/python)

| [リファレンス ドキュメント](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.0](https://pypi.org/project/azure-ai-formrecognizer/) |

#### <a name="non-breaking-changes"></a>**非破壊的変更**

* **to_dict** と **from_dict** メソッドがすべてのモデルに追加されました。

#### <a name="breaking-changes-may"></a>**破壊的変更 (5 月)**

* **begin_recognize_identity_documents** および **begin_recognize_identity_documents_from_url**。 メソッドおよびメソッド パラメーターの名前が **Identity** を使用するように変更され、_Id_ キーワードから置き換えられました。

* **FieldValueType**。 値の型 *country* の名前が **countryRegion** に変更されました。  値の型 *gender* が削除されました。

* **TextAppearance** モデルに **style_name** および **style_confidence** プロパティが含まれるようになりました (以前は **TextStyle** オブジェクトの名前および信頼度プロパティでした)。

* **TextStyle** モデルが削除されました。

---

## <a name="april-2021"></a>2021 年 4 月
<!-- markdownlint-disable MD029 -->

### <a name="sdk-preview-updates-for-api--version-21-preview3"></a>API バージョン 2.1-preview.3 の SDK プレビュー更新プログラム

### <a name="c"></a>[**C#**](#tab/csharp)

NuGet パッケージ バージョン 3.1.0-beta.4

* **身分証明書のデータ分析用の新しいメソッド**:

   **StartRecognizeIdDocumentsFromUriAsync**

   **StartRecognizeIdDocumentsAsync**

   フィールド値の一覧については、Form Recognizer ドキュメントの「[抽出されるフィールド](./concept-id-document.md)」を "_参照_" してください。

* **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** メソッドに指定できるドキュメント言語のセットが拡大されました。

* **新しいプロパティ `Pages` が次のクラスでサポートされました**:

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   `Pages` プロパティを使用すると、複数のページがある PDF および TIFF ドキュメントの個々のページまたはページの範囲を選択できます。 個々のページの場合は、「`3`」のようにページ番号を入力します。 ページの範囲 (ページ 2 とページ 5 - 7 など) の場合は、「`2, 5-7`」のようにページ番号と範囲をコンマで区切って入力します。

* **新しいプロパティ `ReadingOrder` が次のクラスでサポートされました**:

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  `ReadingOrder` プロパティは省略可能なパラメーターであり、これを使用すると、テキスト要素の抽出順序を決めるために、`basic` または `natural` のどちらの読み取り順序アルゴリズムを適用するかを指定できます。 指定しない場合は、既定値の `basic` が使用されます。

#### <a name="breaking-changes-april"></a>破壊的変更 (4 月)

* 既定により、クライアントは、サポートされている最新のサービス バージョンになります。現在は **2.1-preview.3** です。

* **[StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** メソッドで、無効なファイルが渡された場合に `RequestFailedException()` がスローされるようになりました。

### <a name="java"></a>[**Java**](#tab/java)

Maven アーティファクト パッケージの依存関係バージョン 3.1.0-beta.3

* **身分証明書のデータ分析用の新しいメソッド**:

  **[beginRecognizeIdDocumentsFromUrl]**

  **[beginRecognizeIdDocuments]**

   フィールド値の一覧については、Form Recognizer ドキュメントの「[抽出されるフィールド](./concept-id-document.md)」を "_参照_" してください。

* **`FormContentType` 列挙型でのカスタムのフォームおよびトレーニング方法を対象にしたビットマップ画像ファイル (.bmp) のサポート**:

* `image/bmp`

* **新しいプロパティ `Pages` が次のクラスでサポートされました**:

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  `Pages` プロパティを使用すると、複数のページがある PDF および TIFF ドキュメントの個々のページまたはページの範囲を選択できます。 個々のページの場合は、「`3`」のようにページ番号を入力します。 ページの範囲 (ページ 2 とページ 5 - 7 など) の場合は、「`2, 5-7`」のようにページ番号と範囲をコンマで区切って入力します。

* **[FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields) フィールドでのカスタムのフォームおよびトレーニング方法を対象にしたビットマップ画像ファイル (.bmp) のサポート**:

  `image/bmp`

* **新しいキーワード引数 `ReadingOrder` が次のメソッドでサポートされました**:

* **[beginRecognizeContent](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?preserve-view=true&view=azure-java-preview)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   `ReadingOrder` キーワード引数は省略可能なパラメーターであり、これを使用すると、テキスト要素の抽出順序を決めるために、`basic` または `natural` のどちらの読み取り順序アルゴリズムを適用するかを指定できます。 指定しない場合は、既定値の `basic` が使用されます。

* 既定により、クライアントは、サポートされている最新のサービス バージョンになります。現在は **2.1-preview.3** です。

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

npm パッケージ バージョン 3.1.0-beta.3

* **身分証明書のデータ分析用の新しいメソッド**:

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    フィールド値の一覧については、Form Recognizer ドキュメントの「[抽出されるフィールド](./concept-id-document.md)」を "_参照_" してください。

* **新しいフィールド値が FieldValue インターフェイスに追加されました**:

    `gender`—使用可能な値は `M` `F` `X` のいずれかです。</br>
   `country`—使用可能な値は [ISO alpha-3](https://www.iso.org/obp/ui/#search) の 3 文字の国番号文字列に従います。

* すべてのフォーム認識メソッド (カスタム フォームおよびすべての事前構築済みモデル) でサポートされている新しいオプション `pages`。 この引数を使用すると、複数のページがある PDF および TIFF ドキュメントの個々のページまたはページの範囲を選択できます。 個々のページの場合は、「`3`」のようにページ番号を入力します。 ページの範囲 (ページ 2 とページ 5 - 7 など) の場合は、「`2, 5-7`」のようにページ番号と範囲をコンマで区切って入力します。

* コンテンツ認識メソッドに **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/formreadingorder?view=azure-node-latest&preserve-view=true to the URL)** 型のサポートが追加されました。 このオプションを使用すると、認識されたテキスト行の順序を決定するためにサービスで使用されるアルゴリズムを制御できます。 テキスト要素の抽出順序を決めるために、`basic` または `natural` のどちらの読み取り順序アルゴリズムを適用するかを指定できます。 指定しない場合は、既定値の `basic` が使用されます。

* **[FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** 型が複数の異なるインターフェイスに分割されました。 この更新によって、特定のエッジ ケース (未定義の valueType) を除き、API 互換性の問題は発生しません。

* すべての REST API 呼び出しで **2.1-preview.3** の Form Recognizer サービス エンドポイントに移行しました。

### <a name="python"></a>[**Python**](#tab/python)

pip パッケージ バージョン 3.1.0b4

* **身分証明書のデータ分析用の新しいメソッド**:

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  フィールド値の一覧については、Form Recognizer ドキュメントの「[抽出されるフィールド](./concept-id-document.md)」を "_参照_" してください。

* **新しいフィールド値が [FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true) 列挙型に追加されました**:

   gender—使用可能な値は `M` `F` `X` のいずれかです。

  country—使用可能な値は [ISO alpha-3 国別コード](https://www.iso.org/obp/ui/#search)に従います。

* **[FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true) 列挙型でのカスタムのフォームおよびトレーニング方法を対象にしたビットマップ画像ファイル (.bmp) のサポート**:

    image/bmp

* **新しいキーワード引数 `pages` が次のメソッドでサポートされました**:

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `pages` キーワード引数を使用すると、複数のページがある PDF および TIFF ドキュメントの個々のページまたはページの範囲を選択できます。 個々のページの場合は、「`3`」のようにページ番号を入力します。 ページの範囲 (ページ 2 とページ 5 - 7 など) の場合は、「`2, 5-7`」のようにページ番号と範囲をコンマで区切って入力します。

* **新しいキーワード引数 `readingOrder` が次のメソッドでサポートされました**:

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `readingOrder` キーワード引数は省略可能なパラメーターであり、これを使用すると、テキスト要素の抽出順序を決めるために、`basic` または `natural` のどちらの読み取り順序アルゴリズムを適用するかを指定できます。 指定しない場合は、既定値の `basic` が使用されます。

---

## <a name="march-2021"></a>2021 年 3 月

**Form Recognizer v2.1 パブリック プレビュー 3 が利用可能になりました。** v2.1-preview.3 がリリースされました。次の機能を備えています。

* **新しい事前構築済み ID モデル** 新しい事前構築済み ID モデルを使用すると、お客様は ID を取得して構造化データを返し、処理を自動化できます。 強力な光学式文字認識 (OCR) 機能と ID 理解モデルを組み合わせて、パスポートや米国の運転免許証から、名前、生年月日、発行日、有効期限などの重要な情報を抽出することができます。

  [事前作成された ID モデルの詳細情報](./concept-id-document.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="パスポートの例" lightbox="./media/id-canada-passport-example.png":::

* **請求書モデルの品目抽出** - 事前構築済み請求書モデルは品目抽出をサポートするようになりました。完全な項目とその一部 (説明、金額、数量、製品 ID、日付など) が抽出されるようになりました。 簡単な API または SDK の呼び出しにより、請求書から有用なデータ (テキスト、テーブル、キーと値のペア、品目) を抽出できます。

   [請求書モデルの詳細について確認してください](./concept-invoice.md)

* **監視対象テーブルのラベル付けとトレーニング、空の値のラベル付け** - Form Recognizer の [最先端のディープ ラーニング自動テーブル抽出機能](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)に加えて、お客様がテーブルに対してラベル付けとトレーニングを行えるようになりました。 この新しいリリースには、品目とテーブル (動的と固定) にラベルを付けてトレーニングし、キーと値のペアと品目を抽出するカスタム モデルをトレーニングする機能が含まれています。 モデルのトレーニングが完了すると、モデルにより documentResults セクションの JSON 出力の一部として品目が抽出されます。

    :::image type="content" source="./media/table-labeling.png" alt-text="テーブルのラベル付け" lightbox="./media/table-labeling.png":::

    テーブルにラベルを付けることに加え、空の値と領域にラベルを付けることができるようになりました。トレーニング セット内の一部のドキュメントに特定のフィールドの値がない場合、それらにラベルを付けることで、分析対象のドキュメントから値を適切に抽出するようにモデルに認識させることができます。

* **66 の新しい言語のサポート** - Form Recognizer の Layout API とカスタム モデルは 73 の言語をサポートするようになりました。

  [Form Recognizer の言語サポートの詳細](language-support.md)

* **自然な読み取り順序、手書きの分類、およびページ選択** - この更新により、既定の左から右、また上から下の順序ではなく、自然な読み取り順序でテキスト行の出力を取得することを選択できます。 新しい readingOrder クエリ パラメーターを使用して "自然な" 値に設定すると、より人間にとってわかりやすい読み取り順序の出力が得られます。 さらに、ラテン語の場合、Form Recognizer により、テキスト行が手書きスタイルかどうかが分類され、信頼スコアが与えられます。

* **事前構築済み領収書モデルの品質改善** この更新には、特に品目の抽出に関する、事前構築済み領収書モデルの品質改善が多数含まれています。

## <a name="november-2020"></a>2020 年 11 月

### <a name="new-features"></a>新機能

**Form Recognizer v2.1 パブリック プレビュー 2 が利用可能になりました。** v2.1-preview.2 がリリースされました。次の機能を備えています。

* **新しい事前構築済み請求書モデル** - 新しい事前構築済み請求書モデルにより、お客様はさまざまな形式で請求書を取得し、構造化されたデータを返して、請求書の処理を自動化することができます。 強力な光学式文字認識 (OCR) 機能と、請求書を解釈するディープ ラーニング モデルを組み合わせて、英語の請求書から重要な情報を抽出します。 顧客、仕入先、請求書 ID、請求書の期限、合計、請求額、課税額、出荷先、請求先などの重要なテキスト、テーブル、情報が抽出されます。

  > [事前作成された請求書モデルの詳細情報](./concept-invoice.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="請求書の例" lightbox="./media/invoice-example.jpg":::

* **強化されたテーブル抽出** - Form Recognizer によって、強力な光学式文字認識 (OCR) 機能とディープ ラーニング テーブル抽出モデルを組み合わせた拡張テーブル抽出が提供されるようになりました。 Form Recognizer では、テーブル (結合された列、行、境界線を含む複雑なテーブルなど) からデータを抽出できます。

  :::image type="content" source="./media/tables-example.jpg" alt-text="テーブルの例" lightbox="./media/tables-example.jpg":::

  > [レイアウト抽出の詳細情報](concept-layout.md)

* **クライアント ライブラリの更新** - .NET、Python、Java、JavaScript 用の [クライアント ライブラリ](./quickstarts/try-sdk-rest-api.md)の最新バージョンでは、Form Recognizer 2.1 API がサポートされています。
* **サポートされている新しい言語: 日本語** - `AnalyzeLayout` と `AnalyzeCustomForm` に対して、新しい言語: 日本語 (`ja`) がサポートされるようになりました。 [言語サポート](language-support.md)
* **テキスト行のスタイルの表示 (手書き/その他) (ラテン言語のみ)** - Form Recognizer では、信頼度スコアと共に、各テキスト行が手書きスタイルであるかどうかを分類する `appearance` オブジェクトが出力されるようになりました。 この機能は、ラテン語系の言語でのみサポートされています。
* **品質の向上** - 1 桁の抽出の改善を含め、抽出の品質が向上しました。
* **Form Recognizer のサンプル ラベル付けツールの新しい試用機能** - Form Recognizer サンプル ラベル付けツールを使用して、事前作成された請求書、領収書、およびビジネス カード モデルと Layout API を試すことができます。 コードを記述せずにデータがどのように抽出されるかを確認してください。

  [**Form Recognizer のサンプル ラベル付けツールをお試しください**](https://fott-2-1.azurewebsites.net)

  ![サンプル ラベル付けツールのスクリーンショット。](./media/ui-preview.jpg)

* **フィードバック ループ** - サンプル ラベル付けツールを使用してファイルを分析するときに、それをトレーニング セットに追加し、必要に応じてラベルを調整して、モデルを改善するためにトレーニングできるようになりました。
* **ドキュメントの自動ラベル付け** - プロジェクト内のラベル付けされた以前のドキュメントに基づいて、追加のドキュメントが自動的にラベル付けされます。

## <a name="august-2020"></a>2020 年 8 月

### <a name="new-features"></a>新機能

**Form Recognizer v2.1 パブリック プレビューが利用可能になりました。** V2.1-preview.1 がリリースされました。次の機能を備えています。

* **REST API リファレンスが利用可能** - [v2.1-preview.1 リファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)を参照してください
* **英語の他に新たに言語をサポート**。次の [言語](language-support.md)がサポートされるようになりました。`Layout` および `Train Custom Model` の場合: 英語 (`en`)、簡体中国語 (`zh-Hans`)、オランダ語 (`nl`)、フランス語 (`fr`)、ドイツ語 (`de`)、イタリア語 (`it`)、ポルトガル語 (`pt`)、およびスペイン語 (`es`)。
* **チェックボックス、選択マークの検出** – Form Recognizer では、チェックボックスやオプション ボタンなどの選択マークの検出および抽出がサポートされています。 選択マークは `Layout` で抽出されます。また、`Train Custom Model`_ - [ラベルを使用したトレーニング]_ でラベル付けおよびトレーニングを行って、選択マーク用のキーと値のペアを抽出することもできるようになりました。
* **[モデルの作成]** を使用すると、1 つのモデル ID で複数のモデルを構成して呼び出すことができます。 作成済みのモデル ID を使用して分析するドキュメントを送信すると、分類ステップが最初に実行され、適切なカスタム モデルにルーティングされます。 モデルの作成は、`Train Custom Model` -  _[ラベルを使用したトレーニング]_ で使用できます。
* **モデル名**。管理と追跡が容易になるように、カスタム モデルにわかりやすい名前を追加します。
* **[名刺用の新しい事前構築されたモデル](./concept-business-card.md)** 。英語の名刺内の共通のフィールドを抽出するためのものです。
* **[事前に作成された領収書の新しいロケール](./concept-receipt.md)** 。EN-US に加えて、EN-AU、EN-CA、EN-GB、EN-IN でもサポートが利用できるようになりました。
* **品質向上**。`Layout`、`Train Custom Model` -  _[ラベルを使用しないトレーニング]_ 、および _[ラベルを使用したトレーニング]_ が対象となります。

**v2.0** には、次の更新が含まれています。

* NET、Python、Java、および JavaScript 用の[クライアント ライブラリ](./quickstarts/try-sdk-rest-api.md)が一般提供になりました。

**新しいサンプル** は GitHub で入手できます。

* [知識抽出レシピ - Forms Playbook](https://github.com/microsoft/knowledge-extraction-recipes-forms) では、Form Recognizer の実際のカスタマー エンゲージメントからベスト プラクティスが収集され、これらのプロジェクトの開発時に使用する便利なコード サンプル、チェックリスト、およびサンプル パイプラインが提供されます。
* [サンプルラベル付けツール](https://github.com/microsoft/OCR-Form-Tools) が、v2.1 の新機能をサポートするように更新されました。 ツールの概要については、こちらの[クイックスタート](label-tool.md)を参照してください。
* [インテリジェント キオスク](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Form Recognizer サンプルでは、`Analyze Receipt` と `Train Custom Model` -  _[ラベルを使用しないトレーニング]_ を統合する方法について示します。

## <a name="july-2020"></a>2020 年 7 月

### <a name="new-features"></a>新機能
<!-- markdownlint-disable MD004 -->
* **v2.0 リファレンスが使用可能** [v2.0 API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)と、[.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)、[Python](/python/api/overview/azure/)、[Java](/java/api/overview/azure/ai-formrecognizer-readme)、および [JavaScript](/javascript/api/overview/azure/) 用のアップデートされた SDK を参照できます。
* **テーブルの機能強化と抽出の機能強化** には、精度の向上とテーブル抽出の機能強化が含まれています。特に、"_ラベルのないカスタム トレーニング_" でテーブルのヘッダーと構造を学習する機能が追加されています。

* **通貨のサポート** - グローバル通貨記号の検出と抽出。
* **Azure Gov** - Form Recognizer が、Azure Gov でも使用できるようになりました。
* **強化されたセキュリティ機能**:
  * **Bring Your Own Key** - Form Recognizer は、クラウドに永続化されたときにデータを自動的に暗号化して保護し、セキュリティとコンプライアンスに関する組織のコミットメントを満たすのに役立つようにします。 サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 ユーザー独自の暗号化キーを使用してサブスクリプションを管理することもできるようになりました。 [カスタマー マネージド キー (Bring Your Own Key (BYOK) とも呼ばれます)](./encrypt-data-at-rest.md) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます また、データを保護するために使われる暗号化キーを監査することもできます。
  * **プライベート エンドポイント** – 仮想ネットワークを使用して、[Private Link を介してデータに安全にアクセスできるようになります。](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新機能

* **クライアント SDK への CopyModel API の追加** - クライアント SDK を使用して、サブスクリプション間でモデルをコピーできるようになりました。 この機能の全般的な情報については、[モデルのバックアップと復旧](./disaster-recovery.md)に関する記事を参照してください。
* **Azure Active Directory の統合** - Azure AD の資格情報を使用して、SDK で Form Recognizer クライアント オブジェクトを認証できるようになりました。
* **SDK 固有の変更** - この変更には、小さな機能追加と破壊的な変更の両方が含まれます。 詳細については、SDK の変更ログを参照してください。
  * [C# SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新機能

* **Form Recognizer API v2.0 パブリック プレビューの SDK サポート** - 今月、サービス サポートが拡張され、Form Recognizer v2.0 (プレビュー) リリースのプレビュー SDK が含まれるようになりました。 以下のリンクを使用して、任意の言語で作業を開始してください。
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  新しい SDK では、Form Recognizer 用の v2.0 REST API のすべての機能がサポートされています。 たとえば、モデルをラベルの有無にかかわらずトレーニングしたり、フォームからテキスト、キーと値のペア、およびテーブルを抽出したり、事前構築済みの領収書サービスを使用してレシートからデータを抽出したり、レイアウト サービスを使用してドキュメントからテキストやテーブルを抽出したりすることができます。 SDK に関するフィードバックは、[SDK フィードバック フォーム](https://aka.ms/FR_SDK_v1_feedback)を通じて共有できます。

* **カスタム モデルのコピー** 新しいカスタム モデルのコピー機能を使用して、リージョン間やサブスクリプション間でモデルをコピーできるようになりました。 Copy Custom Model API を呼び出す前に、ターゲット リソース エンドポイントに対してコピー承認操作を呼び出して、ターゲット リソースにコピーするための承認を取得する必要があります。

  * [コピー承認を生成する](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [カスタム モデルをコピーする](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>セキュリティの強化

* FormRecognizer でカスタマー マネージド キーが利用できるようになりました。 詳細については、[Form Recognizer による保存データの暗号化](./encrypt-data-at-rest.md)に関するページを参照してください。
* Azure Active Directory を使用した Azure リソースへのアクセスにマネージド ID を使用します。 詳細については、[マネージド ID に対するアクセスの承認](../../cognitive-services/authentication.md#authorize-access-to-managed-identities)に関するページを参照してください。

## <a name="march-2020"></a>2020 年 3 月

### <a name="new-features"></a>新機能

* **ラベル付けの値の種類** Form Recognizer サンプル ラベル付けツールを使用して、ラベル付けする値の種類を指定できるようになりました。 現在、次の値の型とバリエーションがサポートされています。
  * `string`
    * 既定値、`no-whitespaces`、`alphanumeric`
  * `number`
    * 既定値、`currency`
  * `date`
    * 既定値、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  この機能の使用方法については、[サンプル ラベル付けツール](label-tool.md#specify-tag-value-types) ガイドを参照してください。

* **テーブルの視覚化** サンプル ラベル付けツールで、ドキュメントで認識されたテーブルが表示されるようになりました。 この機能により、ラベル付けおよび分析を行う前に、ドキュメントから認識されて抽出されたテーブルを表示できます。 この機能は、レイヤー オプションを使用してオンとオフを切り替えることができます。

  次の図に、テーブルが認識されて抽出される方法の例を示します。

  > [!div class="mx-imgBorder"]
  > ![サンプル ラベル付けツールを使用したテーブルの視覚化](./media/whats-new/table-viz.png)

    抽出されたテーブルは、`"pageResults"` の JSON 出力で使用できます。

  > [!IMPORTANT]
  > テーブルのラベル付けはサポートされていません。 テーブルの認識と抽出が自動的に行われない場合は、キーと値のペアとしてのみラベルを付けることができます。 キーと値のペアとしてテーブルのラベル付けを行うときは、各セルに固有の値としてラベルを付けます。

### <a name="extraction-enhancements"></a>抽出の機能強化

このリリースでは、抽出の機能強化と精度の向上が行われています。具体的には、テキストの同じ行に複数のキーと値のペアが含まれる場合の、ラベル付けと抽出の機能です。

### <a name="sample-labeling-tool-is-now-open-source"></a>サンプル ラベル付けツールがオープンソースになりました

Form Recognizer のサンプル ラベル付けツールが、オープンソース プロジェクトとして使用できるようになりました。 ソリューションにそれを統合し、ニーズに合わせて顧客固有の変更を行うことができます。

Form Recognizer のサンプル ラベル付けツールの詳細については、[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) で入手できるドキュメントを参照してください。

### <a name="tls-12-enforcement"></a>TLS 1.2 の適用

TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../../cognitive-services/cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

このリリースでは、Form Recognizer 2.0 (プレビュー) が導入されています。 以下のセクションでは、新機能、機能強化、および変更の詳細について説明します。

### <a name="new-features"></a>新機能

* **カスタム モデル**
  * **ラベルを使用したトレーニング** 手動でラベル付けされたデータを使用して、カスタム モデルをトレーニングできるようになりました。 この方法より、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成することが可能です。
  * **非同期 API** 非同期 API 呼び出しを使用して、大規模なデータ セットおよびファイルをトレーニングして分析できます。
  * **TIFF ファイルのサポート** TIFF ドキュメントからのデータをトレーニングして、抽出できるようになりました。
  * **抽出精度の向上**

* **構築済みのレシート モデル**
  * **チップの金額** チップの金額とその他の手書きの値を抽出できるようになりました。
  * **品目の抽出** レシートから品目の値を抽出できます。
  * **信頼値** 抽出された各値のモデルの信頼度を表示できます。
  * **抽出精度の向上**

* **レイアウトの抽出** レイアウト API を使用して、フォームからテキスト データとテーブル データを抽出できるようになりました。

### <a name="custom-model-api-changes"></a>カスタム モデル API の変更点

カスタム モデルのトレーニングと使用のための API 名がすべて変更され、一部の同期メソッドは非同期になりました。 主な変更点は次のとおりです。

* モデルのトレーニング プロセスが非同期になりました。 **/custom/models** API 呼び出し経由で、トレーニングを開始します。 この呼び出しでは、操作 ID が返されます。この ID を **custom/models/{modelID}** に渡すと、トレーニング結果を返すことができます。
* キー/値の抽出は、 **/custom/models/{modelID}/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **custom/models/{modelID}/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。
* トレーニング操作の操作 ID は、**Operation-Location** ヘッダーではなく、HTTP 応答の **Location** ヘッダー内に配置されるようになりました。

### <a name="receipt-api-changes"></a>レシート API の変更点

販売レシートを読み取る API の名前が変更されました。

* レシート データの抽出は、 **/prebuilt/receipt/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **/prebuilt/receipt/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。

### <a name="output-format-changes"></a>出力形式の変更点

すべての API 呼び出しへの JSON 応答には、新しい形式が用意されています。 一部のキーと値は、追加、削除、または名前の変更が行われています。 現在の JSON 形式の例については、クイックスタートを参照してください。

## <a name="next-steps"></a>次のステップ

選択した開発言語で Form Recognizer を使用してフォーム処理アプリの作成を開始するには、[クイックスタート](./quickstarts/try-sdk-rest-api.md)を完了します。

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](./overview.md)