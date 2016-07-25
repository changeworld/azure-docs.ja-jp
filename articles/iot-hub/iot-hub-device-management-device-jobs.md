<properties
	pageTitle="IoT Hub デバイス管理のデバイス ジョブ |Microsoft Azure"
	description="Azure IoT Hub デバイス管理チュートリアルは、デバイス ジョブを使用してリモート ファームウェア更新などの操作を実行する方法について説明します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# チュートリアル: デバイスのジョブを使用して、デバイスのファームウェア を更新する方法 (プレビュー)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## はじめに
Azure の IoT デバイス管理では、デバイスのジョブを使用して物理デバイスと対話することができます。デバイス ツイン (物理デバイスのサービスの表現) を識別すると、デバイスのジョブを使用して、対応する物理デバイスと対話できるようになります。デバイス ジョブは、複数のデバイス上の複雑なプロセスの調整を有効にします。このプロセスには、複数のステップと実行時間の長い操作が含まれることがあります。

現時点では、Azure IoT Hub デバイス管理によって用意されている 6 種類のデバイスジョブがあります (顧客の必要に応じてジョブは今後追加されます)。

- **ファームウェアの更新**: 物理デバイスのファームウェア (または OS イメージ) を更新します。
- **再起動**: 物理デバイスを再起動します。
- **工場出荷時の設定へのリセット**: 物理デバイスのファームウェア (または OS イメージ)を、デバイスに格納されている工場出荷時のバックアップ イメージに戻します。
- **構成の更新**: 物理デバイスで実行されている IoT Hub クライアント エージェントを構成します。
- **デバイス プロパティの読み取り**: 物理デバイスのデバイス プロパティの最新の値を取得します。
- **デバイス プロパティの書き込み**: 物理デバイスのデバイス プロパティを変更します。

これらの各ジョブの使用方法の詳細については、[API ドキュメント][lnk-apidocs]を参照してください。

ジョブ履歴のクエリを実行して、開始したジョブの状態を理解できます。サンプル クエリについては、[クエリ式のライブラリ][lnk-query-samples]を参照してください。

## デバイスのジョブを使用して、ファームウェアの更新を実行する: アーキテクチャ

次の図は、単一の物理デバイスと対話しているファームウェアの更新デバイス ジョブを示しています。

![][img-architecture]

ファームウェア更新デバイス ジョブの手順を以下に示します。

1.  クラウド ベースの IoT ソリューションがファームウェア更新デバイス ジョブを開始し、ファームウェア パッケージの場所の URI を提供します。デバイスがダウンロードできる場所にファームウェア パッケージを配置するのは、IoT ソリューションの役目です。

2.  物理デバイスはこの URI を受信すると、指定された URI からのダウンロードを自動的に開始します。

3.  デバイス上のコードは、IoT Hub から要求を受信し、指定された場所の URI からファームウェア パッケージをダウンロードします。

4.  ダウンロードが完了したデバイスのステータス メッセージを受信した後、デバイス ジョブはダウンロードしたファームウェア イメージを適用するようにデバイスに指示します。

5.  デバイスがファームウェア イメージを適用すると再起動が行われ、デバイスは IoT Hub に再接続し、新しいファームウェアが適用されてデバイス ジョブが完了したことを IoT Hub に通知します。

## ファームウェア更新サンプルの実行

次の例では、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」チュートリアル機能が拡張されます。異なるシミュレートされたデバイスの実行から開始すると、これらのデバイスすべてでファームウェアを更新するジョブが実行されます。

### 前提条件 

このサンプルを実行する前に、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」の手順を完了する必要があります。つまり、シミュレートされたデバイスを実行する必要があります。事前にプロセスを完了済みの場合は、ここでシミュレートされたデバイスを再起動してください。

### サンプルの開始

サンプルを開始するには、**FirmwareUpdate.exe** プロセスを実行する必要があります。これにより、すべてのシミュレートされたデバイスで、ファームウェア更新プロセスが開始されます。次の手順に従って、サンプルを開始してください。

1.  **azure-iot-sdks** リポジトリを複製したルート フォルダーから **azure-iot-sdks\\csharp\\service\\samples\\bin** フォルダーに移動します。

2.  `FirmwareUpdate.exe <IoT Hub Connection String>` を実行します。

コマンド ライン ウィンドウの出力に、6 個のシミュレートされたデバイスに対してシミュレートされたファームウェア更新を追跡する、7 つのデバイスジョブが表示されます。

### デバイス ジョブの開始

一般に、デバイスジョブは **JobClient** インスタンスで多数の **Schedule&lt;ジョブ名&gt;Async** メソッドを使用して開始されます。ファームウェア更新のサンプルで、**ScheduleFirmwareUpdateAsync** メソッドを呼び出します。6 つのデバイス ID を渡してから 7 つのデバイス ジョブが更新中のデバイス 1 台に対して 1 つずつ開始され、親デバイス ジョブは他の 6 つのジョブの追跡に使用されます。

次のスニペットでは、**FirmwareUpdate** プロジェクトの **Program.cs** から、ファームウェア更新ジョブが開始されます。この呼び出しでは、更新するデバイスを表す **deviceId** 値の文字列配列がパラメーターとして取得されます。

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### ジョブ履歴のクエリ

サンプルでは、積極的にデバイス ジョブを実行しているデバイスの一覧が定期的に表示されます。デバイスのジョブが完了すると、関連するデバイスが一覧に表示されなくなります。次の図は、実行中の **FirmwareUpdate.exe** のスクリーン ショットです。

![][img-output1]

上記の一覧は、次のコード スニペットに示すように、**FirmwareUpdate** プロジェクトの **Program.cs** からすべてのアクティブなジョブにクエリを実行して生成されます。

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

親デバイス ジョブが完了すると、サンプルにすべてのデバイス ジョブの一覧が出力されます。これを次の図で示します。親ジョブは、関連するすべての子ジョブが完了した後にのみ完了します。次のスクリーン ショットでは、親ジョブが一覧の最後のジョブであり、これは **ParentJobId** が **''** であることから分かります。さらに、親ジョブには、クエリの集計結果を示す文字列に設定された**理由**フィールドがあります。このケースでは、6 つのデバイスが更新され、更新がすべて成功したことが示されています。

![][img-output2]

上記の一覧は、すべてのジョブにクエリを実行することで生成され、開始時刻でソートされています。下記の **JobResponse** オブジェクトのプロパティはいずれも、デバイス ジョブ履歴に対するクエリに使用できます。

![][img-properties]

デバイス ジョブ履歴に対するクエリ実行のサンプルについては、[クエリ式ライブラリ][lnk-query-samples]を参照してください。

### デバイス シミュレーターの実装の詳細

前のセクションでは、サービスの観点でのファームウェア更新プログラムの実装の詳細 (デバイス ジョブおよびその状態のクエリを開始する方法) を紹介しました。ここでは、デバイス側での対応する実装について説明します。

Azure IoT Hub デバイス管理クライアント ライブラリは、デバイスとサービス間の通信を処理するため、残る操作はデバイス固有のロジックの実装のみです。これは、次の 2 つの部分から構成されます。

- デバイス固有のファームウェア更新プロセスの実装: デバイス固有のロジックを書き込み、ファームウェア パッケージをダウンロードして、次に示す適切なコールバックで更新を適用します。シミュレーションのサンプルでは、これは[サンプル][lnk-github-firmware]に実装されます。

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- ファームウェア更新処理のサービスの通知: ファームウェア更新の状態およびファームウェア更新結果フィールドを変更します。これは、**set\_firmwareupdate\_state** と **set\_firmwareupdate\_updateresult** API を呼び出すことによって行います。シミュレーションのサンプルでは、これは[サンプル][lnk-github-firmware]に実装されます。

## 次のステップ

Azure IoT Hub デバイス管理機能の詳細については、次のチュートリアルに進んでください。

- [IoT ゲートウェイの内側で管理されたデバイスを有効にする][lnk-dm-gateway]
- [Azure IoT Hub デバイス管理クライアント ライブラリの概要][lnk-library-c]
- Azure IoT Hub DM クライアント ライブラリは、[Intel Edison デバイス][lnk-edison]を使用したエンドツーエンドのサンプルを提供します。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->