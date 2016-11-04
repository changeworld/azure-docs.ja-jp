---
title: 動的テレメトリの使用 | Microsoft Docs
description: 事前構成済みのリモート監視ソリューションで動的テレメトリを使用する方法については、このチュートリアルを参照してください。
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: dobett

---
# 事前構成済みのリモート監視ソリューションによる動的テレメトリの使用
## はじめに
事前構成済みのリモート監視ソリューションに送信されたテレメトリは、動的テレメトリによって視覚化できます。事前構成済みのソリューションと一緒にデプロイされたシミュレーション対象デバイスからは、温度と湿度のテレメトリが送信されます。このテレメトリをダッシュボードで視覚化することができます。既存のシミュレーション対象デバイスをカスタマイズする場合や、新しいシミュレーション対象デバイスを作成する場合、または事前構成済みのソリューションに物理デバイスを接続する場合は、他にも外部温度や RPM、風速などのテレメトリ値を送信することができます。そのうえでこうした追加のテレメトリをダッシュボードで視覚化できます。

このチュートリアルでは、変更を加えやすい単純な Node.js のシミュレーション対象デバイスを使用して、動的テレメトリの実験を行います。

このチュートリアルを完了するには、以下が必要になります。

* 有効な Azure サブスクリプションアカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk_free_trial]を参照してください。
* [Node.js][lnk-node] バージョン 0.12.x 以降。

このチュートリアルに使用するオペレーティング システムは、Windows や Linux など、Node.js がインストールできれば何でもかまいません。

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Node.js のシミュレーション対象デバイスの構成
1. リモート監視ダッシュボードで **[+ デバイスの追加]** をクリックし、カスタム デバイスを追加します。IoT Hub のホスト名、デバイス ID、デバイス キーをメモします。これらの情報は、後でこのチュートリアルの中で remote\_monitoring.js デバイス クライアント アプリケーションを準備するときに必要となります。
2. 開発コンピューターに Node.js のバージョン 0.12.x 以降がインストールされていることを確認します。バージョンを確認するには、コマンド プロンプトまたはシェルから「`node --version`」を実行します。パッケージ マネージャーを使用して Linux に Node.js をインストールする方法については、[パッケージ マネージャーによる Node.js のインストール][node-linux]に関するページを参照してください。
3. Node.js をインストールしたら、ご使用の開発コンピューターに最新バージョンの [azure-iot-sdks][lnk-github-repo] リポジトリを複製します。最新バージョンのライブラリとサンプルを入手するために、必ず **master** ブランチを使用してください。
4. ローカルにコピーした [azure-iot-sdks][lnk-github-repo] リポジトリの node/device/samples フォルダーから次の 2 つのファイルを開発コンピューター上の空のフォルダーにコピーします。
   
   * packages.json
   * remote\_monitoring.js
5. remote\_monitoring.js ファイルを開き、次の変数の定義を探します。
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. **[IoT Hub device connection string]** を、デバイスの接続文字列に置き換えます。手順 1. でメモした IoT Hub のホスト名、デバイス ID、デバイス キーの値を使用してください。デバイスの接続文字列は、次の形式にする必要があります。
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    IoT Hub のホスト名が **contoso**、デバイス ID が **mydevice** の場合、接続文字列は以下のようになります。
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. ファイルを保存します。これらのファイルが格納されているフォルダーから、シェルまたはコマンド プロンプトで次のコマンドを実行して必要なパッケージをインストールし、サンプル アプリケーションを実行します。
   
    ```
    npm install
    node remote_monitoring.js
    ```

## 作動中の動的テレメトリの観察
ダッシュボードには、既に実行されているシミュレーション対象デバイスから取得した温度と湿度のテレメトリが表示されます。

![The default dashboard][image1]

前のセクションで実行した Node.js のシミュレーション対象デバイスを選択した場合は、温度、湿度、外部温度のテレメトリが表示されます。

![Add external temperature to the dashboard][image2]

このリモート監視ソリューションは、その他の種類の外部温度テレメトリを自動的に検出し、ダッシュボード上のグラフに追加します。

## テレメトリ タイプの追加
次の手順では、Node.js のシミュレーション対象デバイスによって生成されるテレメトリを、新しい値に差し替えます。

1. コマンド プロンプトまたはシェルで **Ctrl + C** キーを押し、Node.js のシミュレーション対象デバイスを停止します。
2. 既存の温度、湿度、外部温度のテレメトリに使用されるベース データ値は、remote\_monitoring.js ファイルで確認できます。次のように、**rpm** に使用するベース データ値を追加します。
   
    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```
3. Node.js のシミュレーション対象デバイスは、remote\_monitoring.js ファイルの **generateRandomIncrement** 関数を使用して、ベース データ値にランダムな増分値を加算します。既にある無作為化の処理に続けて次のコード行を追加し、**rpm** の値を無作為化します。
   
    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```
4. デバイスから IoT Hub に送信される JSON ペイロードに新しい rpm 値を追加します。
   
    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```
5. 次のコマンドを使用して、Node.js のシミュレーション対象デバイスを実行します。
   
    ```
    node remote_monitoring.js
    ```
6. ダッシュボードのグラフに表示される新しい RPM テレメトリ タイプを観察します。

![Add RPM to the dashboard][image3]

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## ダッシュボード表示のカスタマイズ
**Device-Info** メッセージには、デバイスから IoT Hub に送信できるテレメトリについてのメタデータを含めることができます。デバイスから送信されるテレメトリのタイプをこのメタデータで指定することができます。remote\_monitoring.js ファイルの **deviceMetaData** の値に変更を加えます。**Commands** の定義に続けて **Telemetry** の定義を追加してください。次のコード スニペットは、**Commands** の定義を示しています (**Commands** の定義の後、忘れずに `,` を追加してください)。

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> リモート監視ソリューションでは、メタデータの定義とテレメトリ ストリーム内のデータとを比較する際、大文字と小文字が区別されません。
> 
> 

前のコード スニペットに示すように **Telemetry** の定義を追加しても、ダッシュボードの動作は変わりません。一方、ダッシュボードの表示は、メタデータに **DisplayName** 属性を追加することによってカスタマイズすることもできます。**Telemetry** のメタデータ定義を以下のスニペットに示すように更新します。

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

次のスクリーンショットは、ダッシュボードに表示されるグラフの凡例が、この変更によってどのように変わるかを示しています。

![Customize the chart legend][image4]

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## テレメトリの種類のフィルタリング
既定では、テレメトリのストリームに含まれるすべてのデータ系列がダッシュボード上のグラフに表示されます。**Device-Info** のメタデータを使用すると、特定の種類のテレメトリがグラフに表示されないようにすることができます。

温度と湿度のテレメトリだけがグラフに表示されるようにするには、**Device-Info** の **Telemetry** メタデータから **ExternalTemperature** をコメントアウトします。

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature (屋外の温度)** がグラフに表示されなくなります。

![Filter the telemetry on the dashboard][image5]

この変更は、グラフの表示のみに影響します。**ExternalTemperature** データの値は保存されており、バックエンド処理に利用することができます。

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## エラーを処理する
データ ストリームがグラフに表示されるためには、**Device-Info** メタデータにおける対応する **Type** がテレメトリの値のデータ型と一致する必要があります。たとえば湿度データの **Type** が、メタデータで **int** と指定されているとき、テレメトリ ストリームに検出されたデータが **double** である場合、その湿度テレメトリはグラフに表示されません。それでも **Humidity** の値は保存されており、バックエンド処理に利用することができます。

## 次のステップ
動的なテレメトリを使用する方法を確認したので、構成済みのソリューションでデバイス情報を使用する方法について「[リモート監視構成済みソリューションのデバイス情報メタデータ][lnk-devinfo]」からさらに学ぶことができます。

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0831_2016-->