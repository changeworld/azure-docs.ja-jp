---
title: チュートリアル:Azure Logic Apps で Form Recognizer を使用して請求書を分析する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Azure Logic Apps で Form Recognizer を 使用して、モデルのトレーニングとサンプル データを使用したテストのプロセスを自動化するワークフローを作成します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: 14affb2c2aa53fc7a2b1a5946e81ad124800f678
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981260"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>チュートリアル:Azure Logic Apps で Form Recognizer を使用して請求書を分析する

このチュートリアルでは、Form Recognizer (Azure Cognitive Services スイートの一部であるサービス) を使用する Azure Logic Apps のワークフローを作成し、請求書からデータを抽出します。 Form Recognizer を使用して、最初にサンプル データ セットを使用してモデルをトレーニングし、次に別のデータ セットを使用してモデルをテストします。 このチュートリアルで使用するサンプル データは、Azure Storage Blob コンテナーに格納されています。

このチュートリアルの内容:

> [!div class="checklist"]
> * Form Recognizer へのアクセスを要求する
> * Azure Storage Blob コンテナーを作成する
> * サンプル データを Azure Blob コンテナーにアップロードする
> * Azure Logic App を作成する
> * Form Recognizer リソースを使用するようにロジック アプリを構成する
> * ロジック アプリを実行してワークフローをテストする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。

## <a name="request-access-for-form-recognizer"></a>Form Recognizer へのアクセスを要求する

Form Recognizer は、アクセスが制限されたプレビューで利用可能です。 プレビューへのアクセスを取得するには、[Form Recognizer アクセス要求](https://aka.ms/FormRecognizerRequestAccess)フォームに記入して送信します。 要求が Azure Cognitive Services チームによって承認されると、サービスへのアクセス手順を説明したメールが届きます。

## <a name="understand-the-invoice-to-be-analyzed"></a>分析対象の請求書を理解する

モデルのトレーニングとモデルのテストに使用するサンプル データ セットは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) から .zip ファイルとして入手できます。 .zip ファイルをダウンロードして抽出し、 **/Train** フォルダーの下にある請求書 PDF ファイルを開きます。 請求書番号、請求日などが記載されたテーブルがあります。 

> [!div class="mx-imgBorder"]
> ![サンプル請求書](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

このチュートリアルでは、Azure Logic Apps と Form Recognizer を使用して作成したワークフローを使用して、このようなテーブルから JSON 形式に情報を抽出する方法について説明します。

## <a name="create-an-azure-storage-blob-container"></a>Azure Storage Blob コンテナーを作成する

このコンテナーを使用して、モデルのトレーニングに必要なサンプル データをアップロードします。

1. [Azure Storage アカウントの作成](../../storage/common/storage-account-create.md)に関するページの手順に従って、ストレージ アカウントを作成します。 ストレージ アカウント名として **formrecostorage** を使用します。
1. [Azure Blob コンテナーの作成](../../storage/blobs/storage-quickstart-blobs-portal.md)に関するページの手順に従って、Azure Storage アカウント内にコンテナーを作成します。 コンテナー名として **formrecocontainer** を使用します。 パブリック アクセス レベルを必ず **[コンテナー (コンテナーと BLOB の匿名読み取りアクセス)]** に設定します。

    > [!div class="mx-imgBorder"]
    > ![BLOB コンテナーの作成](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>サンプル データを Azure Blob コンテナーにアップロードする

[GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) で入手できるサンプル データをダウンロードします。 ローカル フォルダーにデータを抽出し、 **/Train** フォルダーの内容を、前に作成した **formrecocontainer** にアップロードします。 「[ブロック BLOB をアップロードする](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)」の手順に従って、コンテナーにデータをアップロードします。

コンテナーの URL をコピーします。 この情報は後でこのチュートリアルで必要になります。 このチュートリアルで示したものと同じ名前のストレージ アカウントとコンテナーを作成した場合、URL は *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* になります。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

Azure Logic Apps を使用すると、タスクとワークフローを自動化および調整できます。 このチュートリアルでは、分析対象の請求書をメールの添付ファイルとして受信することによってトリガーされるロジック アプリを作成します。 このワークフローでは、次のタスクを実行します。
* 請求書が添付されたメールを受信したときに自動的にトリガーされるようにロジック アプリを構成します。
* Azure Blob Storage にアップロードしたサンプル データを使用してモデルをトレーニングするために、Form Recognizer の **[モデルのトレーニング]** 操作を使用するようにロジック アプリを構成します。
* 既にトレーニング済みのモデルを使用するために、Form Recognizer の **[フォームの分析]** 操作を使用するようにロジック アプリを構成します。 このコンポーネントは、前にトレーニングしたモデルに基づいて、このロジック アプリに提供する請求書を分析します。

始めましょう。 ワークフローを設定するには、次の手順に従います。

1. Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

1. **[ロジック アプリの作成]** で、次に示すようにロジック アプリの詳細を入力します。 完了したら、 **[作成]** を選択します。

   | プロパティ | 値 | [説明] |
   |----------|-------|-------------|
   | **Name** | <*ロジック アプリ名*> | ロジック アプリの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、"My-First-Logic-App" を使用します。 |
   | **サブスクリプション** | <*Azure サブスクリプション名*> | お使いの Azure サブスクリプション名 |
   | **リソース グループ** | <*Azure-resource-group-name*> | 関連するリソースの整理に使用する[Azure リソース グループ](./../../azure-resource-manager/management/overview.md)の名前。 この例では、"My-First-LA-RG" を使用します。 |
   | **Location** | <*Azure-region*> | ロジック アプリの情報の保存先となるリージョン。 この例では "米国西部" を使用します。 |
   | **Log Analytics** | Off | 診断ログの場合は、この設定を**オフ**のままにしてください。 |
   ||||

1. Azure によってアプリがデプロイされた後、Azure ツール バーで、対象のデプロイされたロジック アプリに対して、 **[通知]**  >  **[リソースに移動]** の順に選択します。 または、検索ボックスに名前を入力して、目的のロジック アプリを見つけて選択することもできます。

   ロジック アプリ デザイナーが開き、紹介ビデオとよく使用されるトリガーが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![空のロジック アプリのテンプレートを選択する](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>メールが届いたときにワークフローをトリガーするようにロジック アプリを構成する

このチュートリアルでは、請求書が添付されたメールを受信したときにワークフローをトリガーします。 このチュートリアルでは、メール サービスとして Office 365 を選択しますが、他の任意のメール プロバイダーを使用することもできます。

1. タブから [すべて] を選択し、 **[Office 365 Outlook]** を選択します。次に、 **[トリガー]** で **[新しいメールが届いたとき]** を選択します。

    ![受信メールでロジック アプリをトリガーする](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. **[Office 365 Outlook]** ボックスで、 **[サインイン]** をクリックし、Office 365 アカウントにログインするための詳細を入力します。

1. 次のダイアログ ボックスで、次の手順に従います。
    1. 新しいメールを監視するフォルダーを選択します。
    1. **[添付ファイルあり]** で、 **[はい]** を選択します。 これにより、添付ファイル付きのメールのみがワークフローをトリガーするようになります。
    1. **[添付ファイルを含める]** で、 **[はい]** を選択します。 これにより、ダウンストリーム処理で添付ファイルのコンテンツが使用されるようになります。

        > [!div class="mx-imgBorder"]
        > ![ロジック アプリのメール トリガーを構成する](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. 上部にあるツール バーの **[保存]** をクリックします。

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Form Recognizer の [モデルのトレーニング] 操作を使用するようにロジック アプリを構成する

Form Recognizer サービスを使用して請求書を分析する前に、モデルで分析および学習できるサンプルの請求書データを提供することで、モデルをトレーニングする必要があります。

1. **[新しいステップ]** を選択し、 **[Choose an action] (アクションの選択)** で **[Form Recognizer]** を検索します。 表示された結果から **[Form Recognizer]** を選択し、Form Recognizer で使用できるアクションで **[モデルのトレーニング]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer モデルをトレーニングする](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. [Form Recognizer] ダイアログ ボックスで、接続の名前を指定し、エンドポイント URL と、Form Recognizer リソース用に取得したキーを入力します。

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer の接続名](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    **Create** をクリックしてください。

1. **[モデルのトレーニング]** ダイアログ ボックスで、 **[ソース]** に、サンプル データをアップロードしたコンテナーの URL を入力します。

    > [!div class="mx-imgBorder"]
    > ![サンプル請求書のストレージ コンテナー](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. 上部にあるツール バーの **[保存]** をクリックします。

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Form Recognizer の [フォームの分析] 操作を使用するようにロジック アプリを構成する

このセクションでは、 **[フォームの分析]** 操作をワークフローに追加します。 この操作では、既にトレーニング済みのモデルを使用して、ロジック アプリに提供されている新しい請求書を分析します。

1. **[新しいステップ]** を選択し、 **[Choose an action] (アクションの選択)** で **[Form Recognizer]** を検索します。 表示された結果から **[Form Recognizer]** を選択し、Form Recognizer で使用できるアクションで **[フォームの分析]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer モデルを分析する](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. **[フォームの分析]** ダイアログ ボックスで、次の操作を行います。

    1. **[モデル ID]** テキスト ボックスをクリックし、表示されるダイアログ ボックスで、 **[動的コンテンツ]** タブの **[modelId]** を選択します。 これを行うことで、前のセクションでトレーニングしたモデルのモデル ID をフロー アプリケーションに提供します。

        > [!div class="mx-imgBorder"]
        > ![Form Recognizer に ModelID を使用する](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. **[ドキュメント]** テキスト ボックスをクリックし、表示されるダイアログ ボックスで、 **[動的コンテンツ]** タブの **[添付ファイルのコンテンツ]** を選択します。 これを行うことで、ワークフローをトリガーするために送信されるメールに添付されているサンプルの請求書ファイルを使用するようにフローを構成します。

        > [!div class="mx-imgBorder"]
        > ![メールの添付ファイルを使用して請求書を分析する](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. 上部にあるツール バーの **[保存]** をクリックします。

### <a name="extract-the-table-information-from-the-invoice"></a>請求書からテーブル情報を抽出する

このセクションでは、請求書内のテーブルから情報を抽出するようにロジック アプリを構成します。

1. **[アクションの追加]** を選択し、 **[Choose an action] (アクションの選択)** で **[作成]** を検索して、使用できるアクションで再度 **[作成]** を選択します。
    ![請求書からテーブル情報を抽出する](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. **[作成]** ダイアログ ボックスで、 **[入力]** テキスト ボックスをクリックし、ポップアップ表示されたダイアログ ボックスで **[テーブル]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![請求書からテーブル情報を抽出する](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. **[保存]** をクリックします。

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

ロジック アプリをテストするには、[GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) からダウンロードしたサンプル データ セットの **/Test** フォルダーにあるサンプルの請求書を使用します。 次の手順に従います。

1. アプリの Azure Logic Apps デザイナーで、上部にあるツール バーの **[実行]** を選択します。 ワークフローがアクティブになり、請求書が添付されたメールを受信するのを待機します。
1. ロジック アプリの作成時に指定したメール アドレスに、サンプルの請求書が添付されたメールを送信します。 ロジック アプリの構成中に指定したフォルダーにメールが配信されていることを確認します。
1. メールがフォルダーに配信されるとすぐに、Logic Apps デザイナーに各ステージの進行状況を示す画面が表示されます。 次のスクリーンショットでは、添付ファイルを含むメールが受信され、ワークフローが進行中であることがわかります。

    > [!div class="mx-imgBorder"]
    > ![メールを送信してワークフローを開始する](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. ワークフローのすべてのステージの実行が完了すると、Logic Apps デザイナーでは、すべてのステージに対して緑色のチェック マークが表示されます。 デザイナー ウィンドウで、 **[For each 2]** を選択し、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ワークフローの完了](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    **[出力]** ボックスで出力をコピーし、任意のテキスト エディターに貼り付けます。

1. JSON 出力と、メールの添付ファイルとして送信したサンプルの請求書を比較します。 JSON データが、請求書内のテーブルのデータに対応していることを確認します。

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    これで、このチュートリアルは終了です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Form Recognizer を使用してモデルをトレーニングし、請求書の内容を抽出するように Azure Logic Apps ワークフローを設定しました。 次に、独自のフォームを使用して同様のシナリオを作成できるように、トレーニング データ セットを作成する方法について説明します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](build-training-data-set.md)