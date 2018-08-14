---
title: Azure IoT Edge SQL モジュールを使用したデータの格納 | Microsoft Docs
description: SQL Server モジュールを使用して IoT Edge デバイスでデータをローカルに格納する方法について説明します
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fa01d2d3f4ab3923129ab1690477d5a8af82d4df
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448913"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>チュートリアル: SQL Server データベースを使用したエッジでのデータの格納

Azure IoT Edge と SQL Server を使用し、エッジでデータを格納してクエリを実行します。 Azure IoT Edge には、デバイスがオフラインになった場合にメッセージをキャッシュして、接続が再確立されるとそれらのメッセージを転送するという基本のストレージ機能が組み込まれています。 しかし、ローカルでデータのクエリを実行する機能など、より高度なストレージ機能が必要になる場合があります。 ローカルなデータベースを組み込むことで、IoT Hub への接続を維持することなく、IoT Edge デバイスでより複雑なコンピューティングを実行できます。 たとえば、機械学習モデルを改善するために月に 1 回のみセンサー データがクラウドにアップロードされる場合でも、現場の技術者はマシン上の過去数日のセンサー データをローカルで視覚化できます。

この記事では、SQL Server データベースを IoT Edge デバイスにデプロイするための手順を説明します。 IoT Edge デバイスで実行される Azure Functions は受信データを構造化してデータベースに送信します。 この記事の手順は、MySQL、PostgreSQL などのコンテナーで動作するその他のデータベースにも適用できます。

このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure 関数を作成する
> * SQL データベースを IoT Edge デバイスに配置する
> * Visual Studio Code を使用してモジュールを作成し、それらを IoT Edge デバイスに配置する
> * 生成されたデータを表示する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* IoT Edge 用 Python モジュールは、ARM プロセッサと Windows デバイスのいずれにも対応していません。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 拡張機能。 
* Visual Studio Code 用の [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 拡張機能。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。 
* [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

    ![コンテナー レジストリの作成](./media/tutorial-deploy-function/create-container-registry.png)

2. レジストリの名前を指定し、サブスクリプションを選択します。
3. リソース グループには、IoT ハブが含まれているのと同じリソース グループの名前を使用することをお勧めします。 すべてのリソースを同じグループにまとめておくことで、それらを一緒に管理できます。 たとえば、テストに使用されるリソース グループを削除すると、グループに含まれているすべてのテスト リソースが削除されます。 
4. SKU を **[Basic]** に設定し、**[管理者ユーザー]** を **[有効]** に切り替えます。 
5. **Create** をクリックしてください。
6. コンテナー レジストリが作成されたら、そこに移動し、**[アクセス キー]** を選択します。 
7. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値を、このチュートリアルで後ほど使用します。 

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

データベースにデータを送信するには、データを正しく構造化してテーブルに格納できるモジュールが必要です。 

次の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge 関数を作成する方法を説明します。

1. Visual Studio Code を開きます。
2. **[表示]** > **[統合ターミナル]** の順に選択して、VS Code 統合ターミナルを開きます。
3. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。
4. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、指示に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。
3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 
   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. **Azure Functions - C#** をモジュール テンプレートとして選択します。 
   4. モジュールに **sqlFunction** という名前を付けます。 
   5. 前のセクションで作成した Azure コンテナー レジストリを、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 文字列は最終的に、**\<レジストリ名\>.azurecr.io/sqlFunction** のようになります。

4. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 **modules** フォルダー、**.vscode** フォルダー、配置マニフェスト テンプレート ファイルがあります。 **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx** の順に開きます。

5. このファイルの内容を次のコードに置き換えます。

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "<sql connection string>";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

6. 行 24 で、文字列 **\<sql connection string\>** を次の文字列に置き換えます。 **Data Source** プロパティは SQL Server コンテナー名を参照します。これは、次のセクションで **SQL** という名前で作成します。 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. **run.csx** ファイルを保存します。 

## <a name="add-a-sql-server-container"></a>SQL Server コンテナーの追加

[配置マニフェスト](module-composition.md)では、IoT Edge ランタイムによって IoT Edge デバイスにインストールされるモジュールを宣言します。 前のセクションではカスタム関数モジュールを作成するコードを追加しましたが、SQL Server モジュールは既に作成されています。 IoT Edge ランタイムがそれを含み、デバイス上で構成するよう指定する必要があります。 

1. Visual Studio Code エクスプローラーで、**deployment.template.json** ファイルを開きます。 
2. **moduleContent.$edgeAgent.properties.desired.modules** セクションを探します。 2 つのモジュールが表示されています。1 つはシミュレートされたデータを生成する **tempSensor** というモジュールで、もう 1 つは **sqlFunction** というモジュールです。
3. 3 つ目のモジュールを宣言するために次のコードを追加します。

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

4. IoT Edge デバイスのオペレーティング システムに応じて、**sql.settings** パラメーターを次のコードに更新します。

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >運用環境で SQL Server コンテナーを作成するときに、[既定のシステム管理者パスワードを変更](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)する必要があります。

5. **deployment.template.json** ファイルを保存します。 

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

これまでのセクションでは、1 つのモジュールと共にソリューションを作成して、別のモジュールを配置マニフェスト テンプレートに追加しました。 次は、ソリューションをビルドし、モジュールのコンテナー イメージを作成してから、コンテナー レジストリにイメージをプッシュする必要があります。 

1. IoT Edge ランタイムがモジュール イメージにアクセスできるよう、deployment.template.json ファイルでこの IoT Edge ランタイムにレジストリ資格情報を付与します。 **moduleContent.$edgeAgent.properties.desired.runtime.settings** セクションを探します。 
2. **loggingOptions** の後に次の JSON コードを挿入します。

   ```JSON
   "registryCredentials": {
       "myRegistry": {
           "username": "",
           "password": "",
           "address": ""
       }
   }
   ```

3. **username**、**password**、**address** の各フィールドにレジストリ資格情報を入力します。 チュートリアルの冒頭で Azure コンテナー レジストリを作成した際にコピーした値を使用します。
4. **deployment.template.json** ファイルを保存します。
5. Visual Studio Code でコンテナー レジストリにサインインします。これで、イメージをレジストリにプッシュできます。 先ほど配置マニフェストに追加したのと同じ資格情報を使用します。 統合ターミナルで次のコマンドを入力します。 

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    パスワードを入力するように求められます。 パスワードをプロンプトに貼り付けて、**Enter** キーを押します。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

6. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、**[Build IoT Edge solution]\(IoT Edge ソリューションのビルド\)** を選択します。 

## <a name="deploy-the-solution-to-a-device"></a>デバイスへのソリューションの配置

IoT ハブを通じてデバイスにモジュールを設定できますが、Visual Studio Code を通じて IoT ハブとデバイスにアクセスすることもできます。 このセクションでは、IoT ハブへのアクセスを設定してから、VS Code を使用してソリューションを IoT Edge デバイスに配置します。 

1. VS Code コマンド パレットで、**Azure IoT Hub: Select IoT Hub** を選択します。
2. プロンプトに従って Azure アカウントにサインインします。 
3. コマンド パレットで、Azure サブスクリプション、IoT ハブの順に選択します。 
4. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 
5. 配置でターゲットにするデバイスを右クリックして、**[Create Deployment for IoT Edge device]\(IoT Edge デバイスの配置の作成\)** を選択します。 
6. エクスプローラーで、ソリューション内の **config** フォルダーに移動して、**deployment.json** を選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 

配置が成功したら、確認メッセージが VS Code 出力に表示されます。 デバイス上ですべてのモジュールが実行されているのを確認することもできます。 

IoT Edge デバイスで、次のコマンドを実行してモジュールの状態を表示します。 これには数分かかることがあります。

   ```bash
   sudo iotedge list
   ```

## <a name="create-the-sql-database"></a>SQL データベースの作成

配置マニフェストをデバイスに適用すると、3 つのモジュールが実行されます。 tempSensor モジュールによって、シミュレートされた環境データが生成されます。 sqlFunction モジュールによってデータが取得され、データベース用にその書式が設定されます。 

このセクションでは、温度データを格納するように SQL データベースを設定する手順を説明します。 

1. コマンドライン ツールで、データベースに接続します。 
   * Windows コンテナー:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Linux コンテナー: 

      ```bash
      docker exec -it sql bash
      ```

2. SQL コマンド ツールを開きます。
   * Windows コンテナー:

      ```cmd
      sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

   * Linux コンテナー: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. データベースを作成します。 

   * Windows コンテナー
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Linux コンテナー
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. テーブルを定義します。

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server が複数の IoT Edge デバイスにデプロイされるよう自動的に設定するように SQL Server ドッカー ファイルをカスタマイズします。 詳しくは、[Microsoft SQL Server コンテナーのデモ プロジェクト](https://github.com/twright-msft/mssql-node-docker-demo-app)に関する記事を参照してください。 

## <a name="view-the-local-data"></a>ローカル データの表示

テーブルが作成されると、IoT Edge デバイス上にあるローカル SQL Server 2017 データベースへのデータの格納が、sqlFunction モジュールによって開始されます。 

SQL コマンド ツール内から次のコマンドを実行して、書式設定されたテーブル データを表示します。 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![ローカル データの表示](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

IoT デバイスのプラットフォーム (Linux または Windows) に基づいて、IoT Edge サービス ランタイムを削除します。

#### <a name="windows"></a>Windows

IoT Edge ランタイムを削除します。

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

デバイス上に作成されたコンテナーを削除します。 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

#### <a name="linux"></a>Linux

IoT Edge ランタイムを削除します。

```bash
sudo apt-get remove --purge iotedge
```

デバイス上に作成されたコンテナーを削除します。 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

コンテナー ランタイムを削除します。

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスで生成された生データをフィルター処理するコードを含む、Azure Functions モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で Azure 関数を開発する](how-to-develop-csharp-function.md)方法の詳細をご覧ください。 

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でフローティング ウィンドウを使用して平均値を見つける](tutorial-deploy-stream-analytics.md)
