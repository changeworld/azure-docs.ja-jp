---
title: Azure SQL Edge での IoT Edge モジュールの設定
description: 鉄鉱石の不純物を予測する、この 3 部構成の Azure SQL Edge チュートリアルの第 2 部では、IoT Edge のモジュールと接続を設定します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d768fdc2540496769883d839cfbb4f009a2000c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077637"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge のモジュールと接続を設定する

Azure SQL Edge で鉄鉱石の不純物を予測する、この 3 部構成チュートリアルの第 2 部では、IoT Edge の次のモジュールを設定します。

- Azure SQL Edge
- データ ジェネレーター IoT Edge モジュール

## <a name="specify-container-registry-credentials"></a>コンテナー レジストリの資格情報の指定

モジュール イメージをホストするコンテナー レジストリに対する資格情報を指定する必要があります。 これは、リソース グループに作成されているコンテナー レジストリで見つかります。 **[アクセス キー]** セクションに移動します。 次のフィールドをメモします。

- レジストリ名
- ログイン サーバー
- ユーザー名
- Password

ここで、IoT Edge モジュールにコンテナーの資格情報を指定します。

1. リソース グループに作成された IoT ハブに移動します。

2. **[IoT Edge]** セクションの **[デバイスの自動管理]** で **[デバイス ID]** をクリックします。 このチュートリアルでは、ID は `IronOrePredictionDevice` です。

3. **[モジュールの設定]** セクションを選択します。

4. **[コンテナー レジストリの資格情報]** で、次の値を入力します。

   | _フィールド_   | _Value_       |
   | -------   | -------       |
   | 名前      | レジストリ名 |
   | Address   | ログイン サーバー  | 
   | [ユーザー名] | ユーザー名      | 
   | Password  | Password      | 
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>データ ジェネレーター モジュールのビルド、プッシュ、およびデプロイ

1. [プロジェクト ファイル](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica)をお使いのマシンにクローンします。
2. Visual Studio 2019 を使用して、ファイル **IronOre_Silica_Predict.sln** を開きます
3. **deployment.template.json** 内のコンテナー レジストリの詳細を更新します 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. **modules.json** ファイルを更新して、ターゲット コンテナー レジストリ (またはモジュールのリポジトリ) を指定します
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. デバッグ モードまたはリリース モードでプロジェクトを実行し、プロジェクトが問題なく実行できることを確認します 
6. プロジェクト名を右クリックし、 **[Build and Push IoT Edge Modules]\(IoT Edge モジュールをビルドしてプッシュする\)** を選択して、プロジェクトをコンテナー レジストリにプッシュします。
7. データ ジェネレーター モジュールを IoT Edge モジュールとしてエッジ デバイスにデプロイします。 

## <a name="deploy-the-azure-sql-edge-module"></a>Azure SQL Edge モジュールのデプロイ

1. **[+ 追加]** 、 **[Marketplace モジュール]** の順にクリックして、Azure SQL Edge モジュールをデプロイします。 

2. **[IoT Edge モジュールの Marketplace]** ブレードで、*Azure SQL Edge* を検索して *[Azure SQL Edge Developer]* を選択します。 

3. **[IoT Edge モジュール]** に新しく追加された *Azure SQL Edge* モジュールをクリックして、Azure SQL Edge モジュールを構成します。 構成オプションの詳細については、「[Azure SQL Edge のデプロイ](./deploy-portal.md)」を参照してください。

4. *Azure SQL Edge* モジュールのデプロイに `MSSQL_PACKAGE` 環境変数を追加して、このチュートリアルの [パート 1](tutorial-deploy-azure-resources.md) の手順 8 で作成したデータベース DACPAC ファイルの SAS URL を指定します。

5. **[update]\(更新\)** をクリックします。

6. **[デバイスのモジュールを設定してください]** ページで **[次へ: ルート] >** をクリックします。

7. **[デバイスのモジュールを設定してください]** ページのルート ペインで、IoT Edge ハブ通信へのモジュールのルートを次のように指定します。 下記のルート定義でモジュール名を忘れずに更新してください。

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   次に例を示します。

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. **[デバイスのモジュールを設定してください]** ページで、 **[次へ: 確認と作成] >** をクリックします。

8. **[デバイスのモジュールを設定してください]** ページで **[作成]** をクリックします

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Azure SQL Edge で T-SQL ストリーミング ジョブを作成して開始します。

1. Azure Data Studio を開きます。

2. **[ようこそ]** タブで、次の詳細情報を使用して新しい接続を開始します。

   |_フィールド_|_Value_|
   |-------|-------|
   |接続の種類| Microsoft SQL Server|
   |サーバー|このデモのために作成された VM で示されているパブリック IP アドレス|
   |ユーザー名|sa|
   |Password|Azure SQL Edge インスタンスの作成時に使用された強力なパスワード|
   |データベース|Default|
   |[サーバー グループ]|Default|
   |Name (名前) (省略可能)|必要に応じて名前を付けます。|

3. **[接続]**

4. **[ファイル]** メニュー タブで新しいノートブックを開くか、キーボード ショートカット Ctrl + N を使用します。

5. [新しいクエリ] ウィンドウで、T-SQL ストリーミング ジョブを作成する次のスクリプトを実行します。 スクリプトを実行する前に、次の変数を変更してください。 
   - *SQL_SA_Password:* Azure SQL Edge モジュールのデプロイ時に指定した MSSQL_SA_PASSWORD 値。 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. 次のクエリを使用して、データ生成モジュールからデータベースにデータがストリーム配信されていることを確認します。 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


このチュートリアルでは、データ ジェネレーター モジュールと SQL Edge モジュールをデプロイしました。 その後、データ生成モジュールによって生成されたデータを SQL にストリーム配信するストリーミング ジョブを作成しました。 

## <a name="next-steps"></a>次の手順

- [ONNX を使用して Azure SQL Edge に ML モデルをデプロイする](tutorial-run-ml-model-on-sql-edge.md)
