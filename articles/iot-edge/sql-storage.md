---
title: Azure IoT Edge SQL モジュール | Microsoft Docs
description: SQL Server モジュールを使用してエッジでデータを格納し、Azure Functions でデータをフォーマットする方法を説明します。
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c90cc28956e4dd7730cc7ba09a173f505f056fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632402"
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>SQL Server データベースを使用したエッジでのデータの格納

Azure IoT Edge デバイスを使用して、エッジで生成されるデータを格納します。 インターネット接続が断続的なデバイスで独自のデータベースを維持し、接続されている場合にのみ変更をクラウドに報告することができます。 重要なデータのみをクラウドに送信するようプログラムされたデバイスに、通常の一括アップロード用の残りのデータを保存できます。 クラウドでは、構造化データを他の Azure サービスと共有して、機械学習モデルの構築などを行えます。 

この記事では、SQL Server データベースを IoT Edge デバイスにデプロイするための手順を説明します。 IoT Edge デバイスで実行される Azure Functions は受信データを構造化してデータベースに送信します。 この記事の手順は、MySQL、PostgreSQL などのコンテナーで動作するその他のデータベースにも適用できます。 

## <a name="prerequisites"></a>前提条件 

この記事の手順を開始する前に、次のチュートリアルを完了していることを確認してください。
* [Windows](tutorial-simulate-device-windows.md) または [Linux](tutorial-simulate-device-linux.md) のシミュレートされたデバイスに Azure IoT Edge をデプロイする
* [Azure Functions を IoT Edge モジュールとしてデプロイする](tutorial-deploy-function.md)

次の記事は、このチュートリアルの正常な完了には必要ありませんが、コンテキストの確認に役立つ場合があります。
* [Run the SQL Server 2017 container image with Docker (Docker を使用した SQL Server 2017 コンテナー イメージの実行)](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Visual Studio Code を使用して Azure IoT Edge で Azure Functions を開発およびデプロイする](how-to-vscode-develop-azure-function.md)

必要なチュートリアルを完了すると、すべての必要な前提条件がコンピューター上に整います。 
* アクティブな Azure IoT Hub
* 最低 2 GB の RAM と 2 GB のディスク ドライブを持つ IoT Edge デバイス
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT Edge コントロール スクリプト](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* AzureIoTEdgeFunction テンプレート (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT ハブ

x64 プロセッサ アーキテクチャでは Windows と Linux の両方のコンテナーがこのチュートリアルで有効です。 SQL Server は、ARM プロセッサをサポートしていません。

## <a name="deploy-a-sql-server-container"></a>SQL Server コンテナーのデプロイ

このセクションでは、MS SQL データベースをシミュレートされた IoT Edge デバイスに追加します。 [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) コンテナーおよび [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) コンテナーとして利用できる SQL Server 2017 Docker コンテナー イメージを使用します。 

### <a name="deploy-sql-server-2017"></a>SQL Server 2017 のデプロイ

既定では、このセクションのコードは SQL Server 2017 の無料の Developer エディション付きコンテナーを作成します。 代わりに運用エディションを実行する場合は、詳細について、「[Run production container images (運用コンテナー イメージの実行)](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production)」を参照してください。 

手順 3 で、作成オプションを SQL Server コンテナーに追加します。これは、環境変数や永続的なストレージの確立のために重要です。 構成された[環境変数](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables)は使用許諾契約に同意し、パスワードを定義します。 [永続的なストレージ](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist)は、[マウント](https://docs.docker.com/storage/volumes/)を使用して構成されます。 マウントでは、*sqlvolume* ボリューム コンテナーがアタッチされた SQL Server 2017 コンテナーが作成されます。これにより、コンテナーが削除されてもデータは保持されます。 

1. Visual Studio Code で `deployment.json` ファイルを開きます。 
2. ファイルの **modules** セクションを次のコードに置き換えます。 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
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
        }
   ```

3. `<docker registry address>` を、終了したチュートリアル「[Azure Function を IoT Edge モジュールとしてデプロイする](tutorial-deploy-function.md)」で入力したアドレスに置き換えます。

   >[!NOTE]
   >コンテナー レジストリ アドレスは、レジストリからコピーしたログイン サーバーと同じです。 `<your container registry name>.azurecr.io` の形式にする必要があります。

4. 実行しているオペレーティング システムに応じて、次のコードで SQL モジュールの設定を更新します。 

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

5. ファイルを保存します。 
6. VS Code コマンド パレットで、**[Edge: Create deployment for Edge device]\(Edge: Edge デバイスのデプロイの作成\)** を選択します。 
7. IoT Edge デバイス ID を選択します。
8. 更新した `deployment.json` ファイルを選択します。 出力ウィンドウに、デプロイの対応する出力が表示されます。 
9. Edge ランタイムを開始するには、コマンド パレットで **[Edge: Start Edge]\(Edge: Edge の開始\)** を選択します。

>[!TIP]
>運用環境で SQL Server コンテナーを作成するときに、[既定のシステム管理者パスワードを変更](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)する必要があります。

## <a name="create-the-sql-database"></a>SQL データベースの作成

このセクションでは、IoT Edge デバイスに接続されたセンサーから受信した温度データを格納するするように SQL データベースを設定する手順を説明します。 シミュレートされたデバイスを使用する場合、このデータは *tempSensor* コンテナーから取得されます。 

コマンドライン ツールで、データベースに接続します。 

* Windows コンテナー
   ```cmd
   docker exec -it sql cmd
   ```

* Linux コンテナー
   ```bash
   docker exec -it sql bash
   ```

SQL コマンド ツールを開きます。 

* Windows コンテナー
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux コンテナー
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

データベースを作成します。 

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

テーブルを定義します。 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server が複数の IoT Edge デバイスにデプロイされるよう自動的に設定するように SQL Server ドッカー ファイルをカスタマイズします。 詳しくは、[Microsoft SQL Server コンテナーのデモ プロジェクト](https://github.com/twright-msft/mssql-node-docker-demo-app)に関する記事を参照してください。

## <a name="understand-the-sql-connection"></a>SQL 接続を理解する

他のチュートリアルでは、ルートを使用してコンテナーを互い分離された状態で通信できるようにします。 ただし、SQL Server データベースを操作する場合は、より近い関係が必要です。 

IoT Edge は起動時にブリッジ (Linux) または NAT (Windows) ネットワークを自動的に構築します。 ネットワークは **azure iot-edge** と呼ばれます。 この接続をデバッグする必要が生じた場合、コマンドラインでそのプロパティを調べることができます。

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

また、IoT Edge ではドッカーを通じてコンテナー名の DNS を解決できるため、その IP アドレスによって SQL Server データベースを参照する必要がありません。 

例として、次のセクションでは次の接続文字列を使用します。 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

接続文字列がコンテナーをその名前 **sql** で参照していることがわかります。 モジュール名を変更した場合、この接続文字列を更新します。 それ以外の場合は、次の手順に進みます。 

## <a name="update-your-azure-function"></a>Azure 関数の更新
データをデータベースに送信するには、前のチュートリアルで作成した FilterFunction Azure 関数を更新します。 このファイルを、センサーが受信したデータを構造化した後、SQL テーブルに格納するように変更します。 

1. Visual Studio Code で FilterFunction フォルダーを開きます。 
2. run.csx file を、前のセクションでの SQL 接続文字列を含む次のコードに置き換えます。 

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
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
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

## <a name="update-your-container-image"></a>コンテナー イメージの更新

加えた変更を適用するには、コンテナー イメージを更新し、発行して、IoT Edge を再起動します。

1. Visual Studio Code で、**Docker** フォルダーを展開します。 
2. 使用するプラットフォームに応じて、**windows nano** または **linux x64** フォルダーを展開します。 
3. **[Dockerfile]** ファイルを右クリックし、**[Build IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージのビルド\)** を選択します。
4. **FilterFunction** プロジェクト フォルダーに移動し、**[Select folder as EXE_DIR]\(EXE_DIR としてフォルダーを選択\)** をクリックします。
5. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「`<your container registry address>/filterfunction:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、名前は `<localhost:5000/filterfunction:latest>` のようになります。
6. VS Code コマンド パレットで、**[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** を選択します。 
7. ポップアップ テキスト ボックスに、同じイメージ名を入力します。 
8. VS Code コマンド パレットで、**[Edge: Restart Edge]\(Edge: Edge の再起動\)** を選択します。

## <a name="view-the-local-data"></a>ローカル データの表示

コンテナーが再起動したら、温度センサーから受信したデータは IoT Edge デバイス上のローカルの SQL Server 2017 データベースに格納されます。 

コマンドライン ツールで、データベースに接続します。 

* Windows コンテナー
   ```cmd
   docker exec -it sql cmd
   ```

* Linux コンテナー
   ```bash
   docker exec -it sql bash
   ```

SQL コマンド ツールを開きます。 

* Windows コンテナー
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux コンテナー
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

データを表示します。 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>次の手順

* [Docker で SQL Server 2017 コンテナー イメージを構成する](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker)方法を確認します。

* リソース、フィードバック、既知の問題については、[mssql docker GitHub リポジトリ](https://github.com/Microsoft/mssql-docker)をご覧ください。
