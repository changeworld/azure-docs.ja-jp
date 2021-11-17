---
title: 'チュートリアル: サーバーレス Apache Spark プールで PREDICT を使用して機械学習モデルをスコア付けする'
description: 機械学習モデルを使用してスコアを予測するために、サーバーレス Apache Spark プールで PREDICT 機能を使用する方法について説明します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 45338fa4aed963d2172af25fec680e2017c98e88
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549890"
---
# <a name="tutorial-score-machine-learning-models-with-predict-in-serverless-apache-spark-pools"></a>チュートリアル: サーバーレス Apache Spark プールで PREDICT を使用して機械学習モデルをスコア付けする

スコア予測のために、Azure Synapse Analytics 内のサーバーレス Apache Spark プールの PREDICT 機能を使用する方法について説明します。 Synapse ワークスペースの Azure Machine Learning (AML) または既定の Azure Data Lake Storage (ADLS) に登録されているトレーニング済みのモデルを使用できます。

Synapse PySpark ノートブックの PREDICT には、SQL 言語、ユーザー定義関数 (UDF)、または変換を使用して機械学習モデルをスコア付けする機能が用意されています。 PREDICT を使用すると、Synapse の外部でトレーニングされ、Azure Data Lake Storage Gen2 または Azure Machine Learning に登録された既存の機械学習モデルを取り込み、Azure Synapse Analytics のセキュリティで保護された境界内で履歴データをスコア付けできます。 PREDICT 関数は、モデルとデータを入力として受け取ります。 この機能により、重要なデータをスコア付けするために Synapse 外部に移動する手順が不要になります。 目的は、モデル コンシューマーが Synapse で機械学習モデルを簡単に推論できるだけでなく、モデル プロデューサーが自分のタスクに対して適切なフレームワークを使用してシームレスに共同作業を行う方法を提供することです。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Synapse の外部でトレーニングされ、Azure Machine Learning または Azure Data Lake Storage Gen2 に登録された機械学習モデルを使用して、サーバーレス Apache Spark プール内のデータのスコアを予測します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- Azure Synapse Analytics ワークスペースのサーバーレス Apache Spark プール。 詳細については、[Azure Synapse での Spark プールの作成](../get-started-analyze-spark.md)に関する記事を参照してください。
- Azure Machine Learning でモデルをトレーニングまたは登録する場合は、Azure Machine Learning ワークスペースが必要です。 詳細については、「[ポータルまたは Python SDK を使用して Azure Machine Learning ワークスペースを管理する](../../machine-learning/how-to-manage-workspace.md)」を参照してください。
- モデルが Azure Machine Learning に登録されている場合は、リンクされたサービスが必要です。 Azure Synapse Analytics では、このリンクされたサービスは、サービスへの接続情報を定義します。 このチュートリアルでは、Azure Synapse Analytics と Azure Machine Learning のリンクされたサービスに追加します。 詳しくは、「[Synapse で新しい Azure Machine Learning リンク サービスを作成する](quickstart-integrate-azure-machine-learning.md)」を参照してください。
- PREDICT 機能を使用するには、トレーニング済みのモデルが既に存在する必要があります。このモデルは、Azure Machine Learning に登録されている、または Azure Data Lake Storage Gen2 にアップロードされているものです。

> [!NOTE]
> - PREDICT 機能は、Azure Synapse Analytics 内の **Spark3** サーバーレス Apache Spark プールでサポートされます。 **Python 3.8** は、モデルの作成とトレーニングに推奨されるバージョンです。 
> - PREDICT では、このプレビューの **MLflow** 形式の **TensorFlow、ONNX、PyTorch、SkLearn、pyfunc** のほとんどの機械学習モデル パッケージがサポートされています。
> - PREDICT では、**AML and ADLS** モデル ソースがサポートされています。 ここで、ADLS アカウントは、**既定の Synapse ワークスペース ADLS アカウント** を参照します。 


## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="use-predict-for-mlflow-packaged-models"></a>MLFLOW パッケージ モデルに PREDICT を使用する

PREDICT を使用するためのこれらの手順に従う前に、すべての前提条件が満たされていることを確認してください。

1. **ライブラリのインポート**: Spark セッションで PREDICT を使用するには、次のライブラリをインポートします。

   ```python
      #Import libraries
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   ```

2. **変数を使用したパラメーターの設定**: Synapse ADLS データ パスとモデル URI は、入力変数を使用して設定する必要があります。 また、"mlflow" であるランタイムと、モデル出力の戻り値のデータ型を定義する必要があります。 PySpark でサポートされているデータ型はすべて、PREDICT でもサポートされています。

   > [!NOTE]
   > このスクリプトを実行する前に、このスクリプトを ADLS Gen2 データ ファイルの URI、モデル出力の戻り値のデータ型、モデル ファイルの ADLS または AML URI で更新します。

   ```python
      #Set input data path
      DATA_FILE = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<file path>"
   
      #Set model URI
          #Set AML URI, if trained model is registered in AML
             AML_MODEL_URI = "<aml model uri>" #In URI ":x" signifies model version in AML. You can   choose which model version you want to run. If ":x" is not provided then by default   latest version will be picked.
   
          #Set ADLS URI, if trained model is uploaded in ADLS
             ADLS_MODEL_URI = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<model   mlflow folder path>"
   
      #Define model return type
      RETURN_TYPES = "<data_type>" # for ex: int, float etc. PySpark data types are supported
   
      #Define model runtime. This supports only mlflow
      RUNTIME = "mlflow"
   ```

3. **AML ワークスペースの認証方法**: モデルが Synapse ワークスペースの既定の ADLS アカウントに格納されている場合は、それ以上の認証設定は必要ではありません。 モデルが Azure Machine Learning に登録されている場合は、次の 2 つのサポートされる認証方法のいずれかを選択できます。

   > [!NOTE]
   > このスクリプトを実行する前に、このスクリプト内のテナント、クライアント、サブスクリプション、リソース グループ、AML ワークスペース、リンクされたサービスの詳細を更新します。

   - **サービス プリンシパルの使用**: サービス プリンシパルのクライアント ID とシークレットを、AML ワークスペースへの認証に直接使用できます。 サービス プリンシパルには、AML ワークスペースに対する "共同作成者" アクセスがあります。

      ```python
         #AML workspace authentication using service principal
         AZURE_TENANT_ID = "<tenant_id>"
         AZURE_CLIENT_ID = "<client_id>"
         AZURE_CLIENT_SECRET = "<client_secret>"
  
         AML_SUBSCRIPTION_ID = "<subscription_id>"
         AML_RESOURCE_GROUP = "<resource_group_name>"
         AML_WORKSPACE_NAME = "<aml_workspace_name>"
  
         svc_pr = ServicePrincipalAuthentication( 
             tenant_id=AZURE_TENANT_ID,
             service_principal_id=AZURE_CLIENT_ID,
             service_principal_password=AZURE_CLIENT_SECRET
         )
  
         ws = Workspace(
             workspace_name = AML_WORKSPACE_NAME,
             subscription_id = AML_SUBSCRIPTION_ID,
             resource_group = AML_RESOURCE_GROUP,
             auth=svc_pr
         )
      ```

   - **リンクされたサービスの使用**: リンクされたサービスを、AML ワークスペースへの認証に使用できます。 リンクされたサービスは、認証に "サービス プリンシパル" または Synapse ワークスペースの "マネージド サービス ID (MSI)" を使用できます。 "サービス プリンシパル" または "マネージド サービス ID (MSI)" には、AML ワークスペースへの "共同作成者" アクセスがある必要があります。

      ```python
         #AML workspace authentication using linked service
         from notebookutils.mssparkutils import azureML
         ws = azureML.getWorkspace("<linked_service_name>") #   "<linked_service_name>" is the linked service name, not AML workspace name. Also, linked   service supports MSI and service principal both
      ```

4. **Spark セッションでの PREDICT の有効化**: ライブラリを有効にするには、Spark 構成 `spark.synapse.ml.predict.enabled` を `true` に設定します。

   ```python
      #Enable SynapseML predict
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

5. **Spark セッションでのモデルのバインド**: モデルを Spark セッションで参照できるよう、モデルと必要な入力をバインドします。 また、PREDICT 呼び出しで同じ別名を使用できるよう、別名も定義します。

   > [!NOTE]
   > このスクリプトを実行する前に、スクリプトのモデルの別名とモデル URI を更新します。

   ```python
      #Bind model within Spark session
       model = pcontext.bind_model(
        return_types=RETURN_TYPES, 
        runtime=RUNTIME, 
        model_alias="<random_alias_name>", #This alias will be used in PREDICT call to refer  this   model
        model_uri=ADLS_MODEL_URI, #In case of AML, it will be AML_MODEL_URI
        aml_workspace=ws #This is only for AML. In case of ADLS, this parameter can be removed
        ).register()
   ```

6. **ADLS からのデータの読み取り**: ADLS からデータを読み取ります。 データ フレームの上に Spark データ フレームとビューを作成します。

   > [!NOTE]
   > このスクリプトを実行する前に、このスクリプトのビュー名を更新します。

   ```python
      #Read data from ADLS
      df = spark.read \
       .format("csv") \
       .option("header", "true") \
       .csv(DATA_FILE,
           inferSchema=True)
      df.createOrReplaceTempView('<view_name>')
   ```

7. **PREDICT を使用したスコアの生成**: Spark SQL API を使用し、ユーザー定義関数 (UDF) を使用して、変換 API を使用して、PREDICT を 3 つの方法で呼び出します。 次に例を示します。

   > [!NOTE]
   > このスクリプトを実行する前に、このスクリプトのモデルの別名、ビュー名、およびコンマ区切りのモデル入力列名を更新します。 コンマ区切りのモデル入力列は、モデルのトレーニング中に使用される列と同じです。

   ```python
      #Call PREDICT using Spark SQL API
   
      predictions = spark.sql(
                     """
                         SELECT PREDICT('<random_alias_name>',
                                   <comma_separated_model_input_column_name>) AS predict 
                         FROM <view_name>
                     """
                 ).show()
   ```

   ```python
      #Call PREDICT using user defined function (UDF)
   
      df = df[<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
   
      df.withColumn("PREDICT",model.udf(lit("<random_alias_name>"),*df.columns)).show()
   ```

   ```python
      #Call PREDICT using Transformer API
      
      columns = [<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
      
      tranformer = model.create_transformer().setInputCols(columns).setOutputCol("PREDICT")
   
      tranformer.transform(df).show()
   ```

## <a name="sklearn-example-using-predict"></a>PREDICT を使用した Sklearn の例

1. ライブラリをインポートし、ADLS からトレーニング データセットを読み取ります。

   ```python
      # Import libraries and read training dataset from ADLS
      
      import fsspec
      import pandas
      from fsspec.core import split_protocol
      
      adls_account_name = 'xyz' #Provide exact ADLS account name
      adls_account_key = 'xyz' #Provide exact ADLS account key
      
      fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>',      account_name=adls_account_name, account_key=adls_account_key)
      
      with fsspec_handle.open() as f:
          train_df = pandas.read_csv(f)
   ```

1. モデルをトレーニングし、mlflow 成果物を生成します。

   ```python
      # Train model and generate mlflow artifacts
   
      import os
      import shutil
      import mlflow
      import json
      from mlflow.utils import model_utils
      import numpy as np
      import pandas as pd
      from sklearn.linear_model import LinearRegression
      
      
      class LinearRegressionModel():
        _ARGS_FILENAME = 'args.json'
        FEATURES_KEY = 'features'
        TARGETS_KEY = 'targets'
        TARGETS_PRED_KEY = 'targets_pred'
      
        def __init__(self, fit_intercept, nb_input_features=9, nb_output_features=1):
          self.fit_intercept = fit_intercept
          self.nb_input_features = nb_input_features
          self.nb_output_features = nb_output_features
      
        def get_args(self):
          args = {
              'nb_input_features': self.nb_input_features,
              'nb_output_features': self.nb_output_features,
              'fit_intercept': self.fit_intercept
          }
          return args
      
        def create_model(self):
          self.model = LinearRegression(fit_intercept=self.fit_intercept)
      
        def train(self, dataset):
      
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
      
          targets = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.TARGETS_KEY])], axis=0)
      
      
          self.model.fit(features, targets)
      
        def predict(self, dataset):
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
          targets_pred = self.model.predict(features)
          return targets_pred
      
        def save(self, path):
          if os.path.exists(path):
            shutil.rmtree(path)
      
          # save the sklearn model with mlflow
          mlflow.sklearn.save_model(self.model, path)
      
          # save args
          self._save_args(path)
      
        def _save_args(self, path):
          args_filename = os.path.join(path, LinearRegressionModel._ARGS_FILENAME)
          with open(args_filename, 'w') as f:
            args = self.get_args()
            json.dump(args, f)
      
      
      def train(train_df, output_model_path):
        print(f"Start to train LinearRegressionModel.")
      
        # Initialize input dataset
        dataset = train_df.to_numpy()
        datasets = {}
        datasets['targets'] = dataset[:, -1]
        datasets['features'] = dataset[:, :9]
      
        # Initialize model class obj
        model_class = LinearRegressionModel(fit_intercept=10)
        with mlflow.start_run(nested=True) as run:
          model_class.create_model()
          model_class.train(datasets)
          model_class.save(output_model_path)
          print(model_class.predict(datasets))
      
      
      train(train_df, './artifacts/output')
   ```

1. モデル MLFLOW ビルド成果物を ADLS に格納するか、または AML に登録します。

   ```python
      # Store model MLFLOW artifacts in ADLS
      
      STORAGE_PATH = 'abfs[s]://<container>/<path-to-store-folder>'
      
      protocol, _ = split_protocol(STORAGE_PATH)
      print (protocol)
   
      storage_options = {
          'account_name': adls_account_name,
          'account_key': adls_account_key
      }
      fs = fsspec.filesystem(protocol, **storage_options)
      fs.put(
          './artifacts/output',
          STORAGE_PATH, 
          recursive=True, overwrite=True)
   ```

   ```python
      # Register model MLFLOW artifacts in AML
      
      from azureml.core import Workspace, Model
      from azureml.core.authentication import ServicePrincipalAuthentication
      
      AZURE_TENANT_ID = "xyz"
      AZURE_CLIENT_ID = "xyz"
      AZURE_CLIENT_SECRET = "xyz"
      
      AML_SUBSCRIPTION_ID = "xyz"
      AML_RESOURCE_GROUP = "xyz"
      AML_WORKSPACE_NAME = "xyz"
      
      svc_pr = ServicePrincipalAuthentication( 
          tenant_id=AZURE_TENANT_ID,
          service_principal_id=AZURE_CLIENT_ID,
          service_principal_password=AZURE_CLIENT_SECRET
      )
      
      ws = Workspace(
          workspace_name = AML_WORKSPACE_NAME,
          subscription_id = AML_SUBSCRIPTION_ID,
          resource_group = AML_RESOURCE_GROUP,
          auth=svc_pr
      )
      
      model = Model.register(
          model_path="./artifacts/output",
          model_name="xyz",
          workspace=ws,
      )
   ```

1. 変数を使用して必須パラメーターを設定します。

   ```python
      # If using ADLS uploaded model
   
      import pandas as pd
      from pyspark.sql import SparkSession
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      ADLS_MODEL_URI_SKLEARN = "abfss://xyz@xyz.dfs.core.windows.net/mlflow/sklearn/     e2e_linear_regression/"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

   ```python
      # If using AML registered model
   
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      AML_MODEL_URI_SKLEARN = "aml://xyz"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

1. Spark セッションで SynapseML PREDICT 機能を有効にします。

   ```python
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

1. Spark セッションでモデルをバインドします。

   ```python
      # If using ADLS uploaded model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=ADLS_MODEL_URI_SKLEARN,
       ).register()
   ```

   ```python
      # If using AML registered model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=AML_MODEL_URI_SKLEARN,
       aml_workspace=ws
       ).register()
   ```

1. ADLS からテスト データを読み込みます。

   ```python
      # Load data from ADLS
   
      df = spark.read \
          .format("csv") \
          .option("header", "true") \
          .csv(DATA_FILE,
              inferSchema=True)
      df = df.select(df.columns[:9])
      df.createOrReplaceTempView('data')
      df.show(10)
   ```

1. PREDICT を呼び出してスコアを生成します。

   ```python
      # Call PREDICT
      
      predictions = spark.sql(
                        """
                            SELECT PREDICT('sklearn_linear_regression', *) AS predict FROM data
                        """
                    ).show()
   ```


## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)
