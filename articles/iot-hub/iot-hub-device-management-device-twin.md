<properties
	pageTitle="IoT Hub デバイス管理のツイン | Microsoft Azure"
	description="Azure IoT Hub デバイス管理のチュートリアルで、デバイス ツインを使用する方法を説明します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# チュートリアル: デバイス ツインの使用方法 (プレビュー)

Azure の IoT Hub デバイス管理では、デバイス ツイン (物理デバイスのサービス側の表現) が導入されています。デバイス ツインのさまざまなコンポーネントを表示する図を次に示します。

![][img-twin]

このチュートリアルでは、デバイスのプロパティについて説明します。その他のコンポーネントの詳細については、[Azure IoT Hub デバイス管理の概要][lnk-dm-overview]を参照してください。

デバイス プロパティは、物理デバイスを表すプロパティの定義済みの辞書です。物理デバイスは、各デバイス プロパティのマスターであり、対応する各値の権限のあるストアです。このプロパティの最終的に一貫性のある表現は、クラウド内のデバイス ツインに格納されます。一貫性と更新は、後述の同期設定の影響を受けます。デバイス プロパティのいくつかの例には、ファームウェア バージョン、バッテリ レベル、および製造元の名前が含まれます。

## デバイス プロパティの同期

物理デバイスは、 デバイス プロパティの権限のあるソースです。物理デバイスで選択した値は自動的に、LWM2M で記述される*観察/通知*パターンを通じて IoT Hub でデバイス ツインに同期されます。

物理デバイスが IoT Hub に接続すると、サービスによって、選択したデバイス プロパティで*監視*が開始されます。次に、物理デバイスはデバイス プロパティの変更を IoT Hub に*通知*します。ヒステリシスを実装するため、**pmin** (最小の通知間隔) は 5 分に設定されています。つまり、変更がある場合でも、物理デバイスはプロパティごとに、5 分に 1 回より多い頻度で IoT Hub に通知することはありません。確実に更新が行われるように、**pmax** (最大の通知間隔) は 6 時間に設定されています。つまり、変更がない場合でも、物理デバイスはプロパティごとに、最低でも 6 時間に 1 回は IoT Hub に通知します。

物理デバイスが切断されると、同期が停止します。同期は、デバイスがサービスに再接続すると再開されます。常にプロパティの最終更新時刻を確認して、確実に更新が行われるようにすることができます。

自動的に監視されるデバイス プロパティの完全な一覧を次に示します。

![][img-observed]

## デバイス ツインのサンプルを実行する

次の例では、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」チュートリアル機能が拡張されます。シミュレートされた複数のデバイスを実行してから、デバイス ツインを使用して、シミュレートされたデバイスのプロパティの読み取りや変更を行います。

### 前提条件 

このサンプルを実行する前に、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」の手順を完了する必要があります。つまり、シミュレートされたデバイスを実行する必要があります。事前にプロセスを完了済みの場合は、ここでシミュレートされたデバイスを再起動してください。

### サンプルの開始

サンプルを開始するには、**DeviceTwin.exe** プロセスを実行する必要があります。デバイス ツインと物理デバイスから、デバイス プロパティが読み取られます。さらに、物理デバイスのデバイス プロパティが変更されます。次の手順に従って、サンプルを開始してください。

1.  **azure-iot-sdks** リポジトリを複製したルート フォルダーから **azure-iot-sdks\\csharp\\service\\samples\\bin** フォルダーに移動します。  

2.  `DeviceTwin.exe <IoT Hub Connection String>` を実行します。

デバイス ツインの使用を示すコマンド ライン ウィンドウに出力が表示されます。サンプル プロセスを次に示します。

1.  デバイス ツインのすべてのデバイス プロパティを印刷します。

2.  詳細読み取り: 物理デバイスからバッテリ レベルのデバイス プロパティを読み取ります (3 回)。

3.  詳細書き込み: 物理デバイスの**タイム ゾーン**デバイス プロパティを書き込みます。

4.  詳細読み取り: 物理デバイスの**タイムゾーン**デバイス プロパティを読み取り、変更されているかどうかを確認します。

### 簡易読み取り

*簡易*読み取りと*詳細*読み取り/書き込みには違いがあります。簡易読み取りでは、Azure IoT Hub に格納されているデバイス ツインから要求されたプロパティの値を返します。これは、前回の通知操作からの値です。物理デバイスは、 デバイス プロパティの権限のあるソースであるため、簡易読み取りを行うことができません。簡易読み取りは、デバイス ツインからのプロパティを読み取ります。

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].Value.ToString();
```

最終更新時刻を確認して、これらの値の更新の間隔を特定することができます。

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].LastUpdatedTime.ToString();
```

同様に、デバイス ツインにのみ格納されているサービスのプロパティを読み取ることができます。これらのプロパティはデバイスに同期されません。

### 詳細な読み取り

詳細な読み取りでは、物理デバイスから要求されたプロパティの値を読み取るデバイス ジョブを開始します。デバイス ジョブは「[Overview of Azure IoT device management (Azure IoT デバイス管理の概要)][lnk-dm-overview]」で導入され、「[Tutorial: How to use device jobs to update device firmware (チュートリアル: デバイスのジョブを使用して、デバイスのファームウェアを更新する方法)][lnk-dm-jobs]」で詳しく説明されています。更新が通知間隔によって制限されないため、詳細な読み取りにより、デバイス プロパティの最新の値を取得できます。ジョブは、物理デバイスにメッセージを送信し、指定したプロパティのみに対して最新の値が含まれるデバイス ツインを更新します。すべてのデバイス ツインが更新されるわけではありません。

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyReadAsync(Guid.NewGuid().ToString(), deviceId, propertyToRead);
```

サービス プロパティやタグはデバイスに同期されていないため、これらの詳細な読み取りを行うことはできません。

### 詳細な書き込み

書き込み可能なデバイス プロパティを変更する場合は、物理デバイスの値を書き込むデバイス ジョブを開始する、詳細な書き込みによって行うことができます。書き込み可能ではないデバイス プロパティもあります。完全な一覧は、「[Introducing the Azure IoT Hub device management client library (Azure IoT Hub デバイス管理クライアント ライブラリの概要)][lnk-dm-library]」を参照してください。

このジョブによって、指定されたを更新するように物理デバイスにメッセージが送信されます。ジョブの完了後、デバイス ツインはすぐには更新されません。次の通知間隔まで待たなければなりません。同期が発生すると、簡易読み取りでデバイス ツインの変更を確認できます。

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyWriteAsync(Guid.NewGuid().ToString(), deviceId, propertyToSet, setValue);
```

### デバイス シミュレーターの実装の詳細

確認/通知パターンと詳細読み取り/書き込みを実装するために、デバイス側で何を行う必要があるかを見てみましょう。

デバイス プロパティの同期は Azure IoT Hub DM クライアント ライブラリを介して完全に処理されるため、必要な操作は一定の間隔でデバイス プロパティ (この例ではバッテリ レベル) を設定する API の呼び出しのみです。サービスが詳細な読み取りを行う場合、最後に設定した値が返されます。サービスが詳細な書き込みを行う場合、この set メソッドが呼び出されます。 **Iotdm\_simple\_sample.c** で、この例を確認できます。

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

set メソッドを使用する代わりに、コールバックを実装することもできます。このオプションの詳細については、「[Introducing the Azure IoT Hub device management library (Azure IoT Hub デバイス管理クライアント ライブラリの概要)][lnk-dm-library]」を参照してください。

## 次のステップ

Azure IoT Hub デバイス管理機能の詳細については、次のチュートリアルに進んでください。

- [クエリを使用したデバイス ツインの検索方法][lnk-tutorial-queries]

- [デバイス ジョブを使用して、デバイスのファームウェアを更新する方法][lnk-dm-jobs]

- デバイス管理クライアント ライブラリは、 [Intel Edison デバイス][lnk-edison]を使用したエンドツーエンドのサンプルを提供します。

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample

<!---HONumber=AcomDC_0504_2016-->