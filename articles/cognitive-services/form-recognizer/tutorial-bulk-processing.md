---
title: チュートリアル:Azure Data Factory を使用したフォーム データの一括抽出 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: ドキュメントの大きなバックログをデジタル化するために、Form Recognizer モデルのトレーニングと実行をトリガーする Azure Data Factory アクティビティを設定します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606709"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>チュートリアル:Azure Data Factory を使用したフォーム データの一括抽出

このチュートリアルでは、各 Azure サービスを使用して大量のフォームをデジタル メディアに取り込む方法を確認します。 このチュートリアルでは、ドキュメントの Azure Data Lake から Azure SQL データベースへのデータ インジェストを自動化する方法について説明します。 数回クリックするだけで、すばやくモデルをトレーニングして新しいドキュメントを処理できるようになります。

## <a name="business-need"></a>ビジネス ニーズ

現在、ほとんどの組織では、異なる形式 (pdf、画像、ビデオ) で所有しているデータの価値が認識されています。 このような資産をデジタル化するためのベスト プラクティスおよびもっともコスト効果の高い方法が求められています。

また、多くの場合、お客様は、多数のクライアントおよび顧客から送信されるさまざまな種類のフォームを使用しています。 [クイックスタート](./quickstarts/client-library.md)とは異なり、このチュートリアルではメタデータ駆動型のアプローチを使用して、新しい種類および異なる種類のフォームでモデルを自動的にトレーニングする方法について説明します。 特定の種類のフォームに対して既存のモデルがない場合は、システムによってモデルが作成され、モデル ID が付与されます。 

フォームからデータを抽出し、既存の運用システムやデータ ウェアハウスと組み合わせることにより、ビジネスでは分析情報を得たり、顧客やビジネス ユーザーに価値を提供したりすることができます。

Azure Form Recognizer を使用することにより、組織ではデータを活用し、プロセス (請求書の支払い、税の処理など) を自動化し、コストと時間を節約し、データの精度を向上させることができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * フォームを保存するための Azure Data Lake を設定する
> * Azure データベースを使用してパラメーター化テーブルを作成する
> * Azure Key Vault を使用して機密性の高い資格情報を保存する
> * Databricks ノートブックで Form Recognizer モデルをトレーニングする
> * Databricks ノートブックを使用してフォーム データを抽出する
> * Azure Data Factory を使用してフォームのトレーニングと抽出を自動化する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
    * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 同じ種類の少なくとも 5 つのフォームのセット。 このワークフローは、大量のドキュメントをサポートするためのものであることが理想的です。 トレーニング データ セットをまとめるためのヒントとオプションについては、[トレーニング データ セットの作成](./build-training-data-set.md)に関するページを参照してください。 このチュートリアルでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2128080)の **Train** フォルダーにあるファイルを使用できます。

## <a name="project-architecture"></a>プロジェクト アーキテクチャ 

このプロジェクトでは、Azure Data Lake ストレージ アカウント内のドキュメントからデータをトレーニング、分析、および抽出する Python ノートブックをトリガーするための、一連の Azure Data Factory パイプラインを設定します。

Form Recognizer REST API には、入力としていくつかのパラメーターが必要です。 セキュリティ上の理由により、これらのパラメーターの一部は Azure キー コンテナーに格納されますが、ストレージ BLOB フォルダー名など、機密性が低いその他のパラメーターは、Azure SQL データベース内のパラメーター化テーブルに格納されます。

分析するフォームの種類については、データ エンジニアまたはデータ サイエンティストが、パラメーター テーブルの行を設定します。 次に、Azure Data Factory を使用して検出されたフォームの種類のリストを反復処理し、関連するパラメーターを Databricks ノートブックに渡して、Form Recognizer モデルをトレーニングまたは再トレーニングします。 ここでは、Azure 関数を使用することもできます。

その後、Azure Databricks ノートブックでは、トレーニング済みのモデルを使用してフォーム データを抽出し、そのデータを Azure SQL データベースにエクスポートします。

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="プロジェクト アーキテクチャ":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake の設定

フォームのバックログは、オンプレミスの環境にある場合や、(s)FTP サーバー内にある場合があります。 このチュートリアルでは、Azure Data Lake Gen 2 ストレージ アカウント内のフォームを使用します。 そこには、Azure Data Factory、Azure Storage Explorer、または AzCopy を使用してファイルを転送できます。 トレーニング データセットとスコアリング データセットは異なるコンテナーに配置できますが、すべてのフォームの種類のトレーニング データセットは同じコンテナー内に配置する必要があります (ただし、異なるフォルダーでも構いません)。

新しい Data Lake を作成するには、「[Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account)」の手順に従います。

## <a name="create-a-parameterization-table"></a>パラメーター化テーブルを作成する

次に、Azure SQL データベースにメタデータ テーブルを作成します。 このテーブルには、Form Recognizer REST API に必要な機密性の低いデータを含めます。 データセットに新しい種類のフォームがある場合は常に、このテーブルに新しいレコードを挿入し、トレーニングおよびスコアリングのパイプライン (後で実装します) をトリガーします。

このテーブルでは、次のフィールドが使用されます。

* **form_description**:このフィールドは、トレーニングの一部として必須ではありません。 モデルをトレーニングするフォームの種類の説明 ("client A forms"、"Hotel B forms" など) を指定します。
* **training_container_name**:このフィールドは、トレーニング データセットを格納したストレージ アカウントのコンテナー名です。 **scoring_container_name** と同じコンテナーにすることができます。
* **training_blob_root_folder**:モデルのトレーニング用のファイルを格納するストレージ アカウント内のフォルダー。
* **scoring_container_name**:このフィールドは、キーと値のペアを抽出するファイルを格納したストレージ アカウントのコンテナー名です。 **training_container_name** と同じコンテナーにすることができます。
* **scoring_input_blob_folder**:データの抽出元のファイルを格納するストレージ アカウント内のフォルダー。
* **model_id**:再トレーニングするモデルの ID。 最初の実行では、値を -1 に設定する必要があります。これにより、トレーニング ノートブックでトレーニングする新しいカスタム モデルが作成されます。 トレーニング ノートブックによって、新しく作成されたモデル ID が Azure Data Factory インスタンスに返されます。ストアド プロシージャ アクティビティを使用して、Azure SQL データベースでこの値を更新します。

  新しいフォームの種類を取り込む場合は常に、モデルのトレーニングを行う前に、モデル ID を -1 に手動でリセットする必要があります。

* **file_type**:サポートされているフォームの種類は、`application/pdf`、`image/jpeg`、`image/png`、および `image/tif` です。

  ファイルの種類が異なるフォームがある場合は、新しいフォームの種類をトレーニングするときに、この値と **model_id** を変更する必要があります。
* **form_batch_group_id**:時間の経過と共に、同じモデルに対して複数のフォームの種類をトレーニングすることがあります。 **form_batch_group_id** を使用すると、特定のモデルを使用してトレーニングしてきたすべてのフォームの種類を指定できます。

### <a name="create-the-table"></a>テーブルの作成

[Azure SQL データベースを作成](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)した後、[クエリ エディター](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal)で次の SQL スクリプトを実行して、必要なテーブルを作成します。

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

次のスクリプトを実行して、トレーニング後に **model_id** を自動的に更新するプロシージャを作成します。

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Azure Key Vault を使用して機密性の高い資格情報を保存する

セキュリティ上の理由から、特定の機密性の高い情報は Azure SQL データベース内のパラメーター化テーブルに格納したくありません。 機密性の高いパラメーターは Azure Key Vault シークレットとして格納します。

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

[Key Vault リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。 次に、作成された Key Vault リソースに移動し、 **[設定]** セクションで、 **[シークレット]** を選択してパラメーターを追加します。

新しいウィンドウが表示されたら、 **[生成/インポート]** を選択します。 パラメーターの名前とその値を入力し、[作成] をクリックします。 以下のパラメーターに対してこの操作を行ってください。

* **CognitiveServiceEndpoint**:Form Recognizer API のエンドポイント URL。
* **CognitiveServiceSubscriptionKey**:Form Recognizer サービスのアクセス キー。 
* **StorageAccountName**:トレーニング データセットとキーと値のペアを抽出するフォームが格納されているストレージ アカウント。 それらが異なるアカウントに配置されている場合は、それぞれのアカウント名を個別のシークレットとして入力します。 トレーニング データセットはすべてのフォームの種類に対して同じコンテナーに配置する必要がありますが、異なるフォルダーに配置しても構わないことを忘れないでください。
* **StorageAccountSasKey**: ストレージ アカウントの Shared Access Signature (SAS)。 SAS URL を取得するには、ストレージ リソースにアクセスし、 **[ストレージ エクスプローラー]** タブを選択します。コンテナーに移動して右クリックし、 **[Get shared access signature]\(Shared Access Signature の取得\)** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Databricks ノートブックで Form Recognizer モデルをトレーニングする

Form Recognizer サービスと対話する Python コードを格納して実行するために、Azure Databricks を使用します。

### <a name="create-a-notebook-in-databricks"></a>Databricks でノートブックを作成する

Azure portal で [Azure Databricks リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.Databricks)。 作成されたリソースに移動し、ワークスペースを起動します。

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Azure Key Vault によってサポートされるシークレットのスコープを作成する

上記で作成した Azure Key Vault のシークレットを参照するには、Databricks でシークレットのスコープを作成する必要があります。 「[Azure Key Vault を利用するシークレットのスコープを作成する](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)」の手順に従ってください。

### <a name="create-a-databricks-cluster"></a>Databricks クラスターを作成する

クラスターは、Databricks コンピュテーション リソースのコレクションです。 クラスターを作成するには:

1. サイド バーの **[クラスター]** ボタンをクリックします。
1. **[クラスター]** ページで、 **[クラスターの作成]** をクリックします。
1. **[クラスターの作成]** ページでクラスター名を指定し、[Databricks Runtime のバージョン] ドロップダウンで **[7.2 (Scala 2.12, Spark 3.0.0)]** を選択します。
1. **[クラスターの作成]** をクリックします。

### <a name="write-a-settings-notebook"></a>設定ノートブックを作成する

これで、Python ノートブックを追加する準備ができました。 まず、「**Settings**」という名前のノートブックを作成します。このノートブックにより、パラメーター化テーブル内の値をスクリプト内の変数に代入します。 この値は、後で Azure Data Factory によってパラメーターとして渡されます。 また、キー コンテナー内のシークレットの値も変数に代入します。 

1. **Settings** ノートブックを作成するには、 **[ワークスペース]** ボタンをクリックし、[新規] タブで、ドロップダウン リストをクリックして **[作成]** を選択した後、 **[ノートブック]** を選択します。
1. ポップアップ ウィンドウでノートブックに付ける名前を入力し、既定の言語として **[Python]** を選択します。 使用する Databricks クラスターを選択し、 **[作成]** を選択します。
1. 最初のノートブック セルでは、Azure Data Factory によって渡されたパラメーターを取得します。

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. 2 番目のセルでは、キー コンテナーからシークレットを取得し、変数に代入します。

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>トレーニング ノートブックを作成する

**Settings** ノートブックが完成したので、モデルをトレーニングするためのノートブックを作成できます。 前述のように、Azure Data Lake Gen 2 ストレージ アカウント内のフォルダー (**training_blob_root_folder**) に格納されているファイルを使用します。 フォルダー名は変数として渡されています。 フォームの種類の各セットは同じフォルダー内にあり、パラメーター テーブルをループ処理しながら、すべてのフォームの種類を使用してモデルをトレーニングします。 

1. 「**TrainFormRecognizer**」という名前の新しいノートブックを作成します。 
1. 最初のセルで、Settings ノートブックを実行します。

    ```python
    %run "./Settings"
    ```

1. 次のセルでは、**Settings** ファイルから変数を代入し、[REST のクイックスタート](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)に記載されているコードを応用して、フォームの種類ごとにモデルを動的にトレーニングします。

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. トレーニング プロセスの最後の手順は、トレーニング結果を json 形式で取得することです。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>ノートブックを使用してフォーム データを抽出する

### <a name="mount-the-azure-data-lake-storage"></a>Azure Data Lake ストレージをマウントする

次の手順は、トレーニング済みのモデルを使用して、さまざまなフォームをスコア付けすることです。 Azure Data Lake ストレージ アカウントを Databricks にマウントし、取り込みプロセス中にマウントを参照します。

トレーニング段階と同様に、Azure Data Factory を使用して、フォームからのキーと値のペアの抽出を呼び出します。 パラメーター テーブルで指定されているフォルダー内のフォームに対してループ処理を行います。

1. Databricks にストレージ アカウントをマウントするノートブックを作成しましょう。 ここでは「**MountDataLake**」という名前を付けます。 
1. 最初に **Settings** ノートブックを呼び出す必要があります。

    ```python
    %run "./Settings"
    ```

1. 2 番目のセルでは、機密性の高いパラメーター用の変数を定義します。これはキー コンテナーのシークレットから取得します。

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. 次に、以前にマウントされていた場合に備えて、ストレージ アカウントのマウントを解除します。

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. 最後に、ストレージ アカウントをマウントします。


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > マウントしたのはトレーニング ストレージ アカウントのみです&mdash;この場合、トレーニング ファイルとキーと値のペアを抽出するファイルは同じストレージ アカウント内にあります。 スコアリングとトレーニングのストレージ アカウントが異なる場合は、ここで両方のストレージ アカウントをマウントする必要があります。 

### <a name="write-the-scoring-notebook"></a>スコアリング ノートブックを作成する

これで、スコアリング ノートブックを作成できるようになりました。 トレーニング ノートブックと同様に、先ほどマウントした Azure Data Lake ストレージ アカウント内のフォルダーに格納されているファイルを使用します。 フォルダー名は変数として渡されます。 指定されたフォルダー内のすべてのフォームに対してループ処理を行い、キーと値のペアを抽出します。 

1. 新しいノートブックを作成し、「**ScoreFormRecognizer**」という名前を付けます。 
1. **Settings** ノートブックと **MountDataLake** ノートブックを実行します。

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. 次に、[Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API を呼び出す次のコードを追加します。

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 次のセルで、キーと値のペアの抽出の結果を取得します。 このセルによって結果が出力されます。 Azure SQL Database または Cosmos DB でさらに処理するために JSON 形式の結果が必要なので、結果は .json ファイルに書き込みます。 出力ファイル名は、スコアリングされたファイル名に "_output.json" を連結した名前になります。 ファイルは、ソース ファイルと同じフォルダーに格納されます。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. 新しいセルでファイルの書き込み手順を実行します。

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Azure Data Factory でトレーニングとスコアリングを自動化する

残っている最後の手順は、トレーニングとスコアリングのプロセスを自動化するために Azure Data Factory (ADF) サービスを設定することです。 まず、「[Data Factory の作成](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)」の手順に従います。 ADF リソースを作成したら、3 つのパイプラインを作成する必要があります。1 つはトレーニング用で、もう 2 つはスコアリング用です (以下で説明します)。

### <a name="training-pipeline"></a>トレーニング パイプライン

トレーニング パイプラインの最初のアクティビティは、Azure SQL データベース内のパラメーター化テーブルの値を読み取って返すためのルックアップです。 すべてのトレーニング データセットは同じストレージ アカウントとコンテナー (ただし、場合によっては異なるフォルダー) に存在するので、ルックアップ アクティビティの設定で既定値 **[First row only]\(先頭行のみ\)** 属性を維持します。 モデルをトレーニングするフォームの種類ごとに、**training_blob_root_folder** 内のすべてのファイルを使用してモデルをトレーニングします。

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="データ ファクトリのトレーニング パイプライン":::

ストアド プロシージャは、2 つのパラメーター **model_id** と **form_batch_group_id** を受け取ります。 Databricks ノートブックからモデル ID を返すコードは `dbutils.notebook.exit(model_id)`、データ ファクトリのストアド プロシージャ アクティビティでコードを読み取るコードは `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` です。

### <a name="scoring-pipelines"></a>スコアリング パイプライン

キーと値のペアを抽出するには、パラメーター化テーブル内のすべてのフォルダーをスキャンし、各フォルダーに対して、含まれているすべてのファイルのキーと値のペアを抽出します。 現在のところ、ADF では入れ子になった ForEach ループはサポートされていません。 そのため、代わりに 2 つのパイプラインを作成します。 最初のパイプラインでは、パラメーター化テーブルから参照を行い、フォルダーの一覧をパラメーターとして 2 番目のパイプラインに渡します。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="データ ファクトリの最初のスコアリング パイプライン":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="データ ファクトリの最初のスコアリング パイプライン (詳細)":::

2 番目のパイプラインでは、GetMeta アクティビティを使用してフォルダー内のファイルの一覧を取得し、それをパラメーターとして Databricks のスコアリング ノートブックに渡します。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="データ ファクトリの 2 番目のスコアリング パイプライン":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="データ ファクトリの 2 番目のスコアリング パイプライン (詳細)":::

### <a name="specify-a-degree-of-parallelism"></a>並列処理の次数を指定する

トレーニングとスコアリングの両方のパイプラインで、並列処理の次数を指定し、複数のフォームを同時に処理することができます。

ADF パイプラインで並列処理の次数を設定するには:

* Foreach アクティビティを選択します。
* **[シーケンシャル]** ボックスをオフにします。
* **[Batch count]\(バッチ カウント\)** テキスト ボックスで並列処理の次数を設定します。 スコアリングには、最大バッチ カウントを 15 にすることをお勧めします。

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="ADF のスコアリング アクティビティの並列処理の構成":::

## <a name="how-to-use"></a>使用方法

これで、フォームのバックログをデジタル化し、さらにいくつかの分析を実行する、自動化されたパイプラインが完成しました。 既存のストレージ フォルダーに使い慣れた種類の新しいフォームを追加するときは、スコアリング パイプラインを再実行するだけで、新しいフォームの出力ファイルを含むすべての出力ファイルが更新されます。 

新しい種類の新しいフォームを追加する場合は、適切なコンテナーにトレーニング データセットをアップロードする必要もあります。 次に、パラメーター化テーブルに新しい行を追加し、新しいドキュメントとそのトレーニング データセットの場所を入力します。 これらのフォームに対して新しいモデルをトレーニングする必要があることを示すために、**model_ID** に値 -1 を入力します。 その後、ADF でトレーニング パイプラインを実行します。 テーブルからの読み取り、モデルのトレーニング、テーブル内のモデル ID の上書きが行われます。 その後、スコアリング パイプラインを呼び出して、出力ファイルの書き込みを開始できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ファイルの大きなバックログをデジタル化するために、Form Recognizer モデルのトレーニングと実行をトリガーする Azure Data Factory パイプラインを設定しました。 次に、Form Recognizer API を調べて、他に何ができるかを確認します。

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
