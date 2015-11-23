<properties
   pageTitle="構成済みソリューションへのデバイスの接続 | Microsoft Azure"
   description="温度と湿度のデータに関する例を使用して、Azure IoT Suite の事前構成済みリモート監視ソリューションにデバイスを接続する方法を説明します。"
   services=""
   documentationCenter="na"
   authors="hegate"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="hegate"/>


# Azure IoT Suite リモート監視ソリューションにデバイスを接続する


## シナリオの概要

この例では、シミュレートされた 3 つのデータ ソースとして、外部温度、内部温度、湿度を使用します。ここでは、わかりやすくするために実際のセンサーは使用していません (データはクライアント コードで自動生成されたものです)。ただし、次のステップとして、普段使用しているセンサーを接続して実際のデータを送信することをお勧めします。詳細については、「ソリューションの拡張」セクションでリンクされているリソースを参照してください。

## 前提条件

### IoT Suite をプロビジョニングする

リモート監視の事前構成済みソリューションをまだプロビジョニングしていない場合は、[こちら](http://www.microsoft.com/ja-JP/server-cloud/internet-of-things/getting-started.aspx)でプロビジョニングできます。


### リモート監視ソリューションでデバイスをプロビジョニングする
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
デバイスを事前構成済みソリューションに接続するには、ダッシュボードからデバイスの資格情報を取得する必要があります。資格情報は、デバイスを識別できるようにクライアント アプリケーションで使用されます。次の手順に従います。

1.  ダッシュボードの左下隅にある [デバイスの追加] をクリックします。

    ![][1]

2.  [カスタム デバイス] で [新規追加] ボタンをクリックします。

    ![][2]

3.  realdevice1 などの名前を入力して独自のデバイス ID を選択し、[ID の確認] をクリックしてその名前がまだ使用されていないことを確認します。

    ![][3]

5. 表示された資格情報 (デバイス ID、IoT Hub ホスト名、デバイス キー) をコピーしてください。これらの資格情報は、デバイスをソリューションに接続するために後でクライアント アプリケーションで必要になります。

    ![][4]

6. お使いのデバイスが [デバイス] セクションに正しく表示されていることを確認します。状態は "保留中" になります。デバイスからクラウドへの接続が確立されるまではこの状態が続くことが予想されます。

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

ここで、サンプルを続行するために使用する言語を選択します。このチュートリアルでは、C と node.js 用のサンプル コードを作成しましたが、このサンプル コードは C# と Java で実装することもできます。

## C を使用してデバイス データをリモート監視ソリューションに送信する


### Linux 上でデバイスを実行する

1. 環境を設定します。これまでデバイス SDK を使用したことがない場合は、[こちら](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux)で Linux 上に環境を設定する方法を確認してください。

1. テキスト エディターで **c/serializer/samples/serializer/remote\_monitoring.c** ファイルを開きます。

2. ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
    
3. "[Device Id]" と "[Device Key]" を自身のデバイス データに置き換えます。

4. デバイス データである IoT Hub ホスト名を使用して、IoTHub 名と IoTHub サフィックスを設定します。これを設定するには、IoT Hub ホスト名を IoTHub 名と IoTHub サフィックスに分割する必要があります。たとえば、IoT Hub ホスト名が "Contoso.azure-devices.net" である場合は、"Contoso" が IoTHub 名、残りの部分がサフィックスになります。次のようになります。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. 変更を保存し、サンプルをビルドします。サンプルをビルドするには、**c/build\_all/linux** ディレクトリにある build.sh スクリプトを実行します。

6. **c/serializer/samples/remote\_monitoring/linux/remote\_monitoring** サンプル アプリケーションを実行します。

#### 登録済みデバイスとデータを視覚化する

7. リモート監視ソリューションのダッシュボードに戻ります。デバイスの一覧で、デバイスの状態が "実行中" に変更されていることがわかります。

    ![][18]

8. ダッシュボードをクリックすると、データが受信されていることがわかります。サンプルは、内部温度の場合は 50 ユニット、外部温度の場合は 55 ユニット、湿度の場合は 50 ユニットを送信するように構成されています。ダッシュボードに既定で表示されるのは温度と湿度のみであることに注意してください。

8. ここで、[コマンドと制御](#command)に関するセクションに進み、リモート監視ソリューションからデバイスの温度を変更する方法を確認してください。


### Windows 上でデバイスを実行する


1. 環境を設定します。これまでデバイス SDK を使用したことがない場合は、[こちら](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows)で Windows 上に環境を設定する方法を確認します。

1. Visual Studio 2015 の新しいインスタンスを開始します。リポジトリのローカル コピーで **c\\serializer\\build\\windows** フォルダー内の **remote\_monitoring.sln** ソリューションを開きます。

2. Visual Studio の**ソリューション エクスプローラー**で、samples フォルダーに移動します。**remote\_monitoring** プロジェクトの **remote\_monitoring.c** ファイルを開きます。

2. ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. "[Device Id]" および "[Device Key]" を自身のデバイス データに置き換えます。

4. デバイス データである IoT Hub ホスト名を使用して、IoTHub 名と IoTHub サフィックスを設定します。これを設定するには、IoT Hub ホスト名を次のように分割する必要があります。

    IoT Hub ホスト名が Contoso.azure-devices.net である場合は、Contoso が IoTHub 名、残りの部分がサフィックスになります。次のようになります。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. **ソリューション エクスプローラー**で、**remote\_monitoring** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックして、サンプルをビルドして実行します。アプリケーションから IoT Hub に D2C メッセージが送信されると、コンソールにメッセージが表示されます。

#### 登録済みデバイスとデータを視覚化する

7. リモート監視ソリューションのダッシュボードに戻ります。デバイスの一覧で、デバイスの状態が "実行中" に変更されていることがわかります。

    ![][18]

8. ダッシュボードをクリックすると、データが受信されていることがわかります。サンプルは、内部温度の場合は 50 ユニット、外部温度の場合は 55 ユニット、湿度の場合は 50 ユニットを送信するように構成されています。ダッシュボードに既定で表示されるのは温度と湿度のみであることに注意してください。

8. ここで、[コマンドと制御](#command)に関するセクションに進み、リモート監視ソリューションからデバイスの温度を変更する方法を確認してください。

### mbed 上でデバイスを実行する

次に、[mbed 対応の Freescale FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/) デバイスを Azure IoT Hub に接続する手順を説明します。


#### 必要条件

- 必要なハードウェア: [mbed 対応の Freescale K64F](https://developer.mbed.org/platforms/FRDM-K64F/) または類似のハードウェア。

#### デバイスを接続する

- イーサネット ケーブルを使用してボードをネットワークに接続します。このサンプルではインターネット アクセスを使用するため、この手順は必須です。

- マイクロ USB ケーブルを使用してデバイスをコンピューターに接続します。[こちら](https://developer.mbed.org/platforms/frdm-k64f/)の「Getting started」セクションで図示されているように、ケーブルは必ずデバイスの正しい USB ポートに取り付けてください。

- 開発用コンピューターからデバイスとのシリアル接続を設定するには、[mbed に関するハンドブックの手順](https://developer.mbed.org/handbook/SerialPC)に従ってください。Windows を使用している場合は、[こちら](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port)にある Windows シリアル ポート ドライバーをインストールしてください。

#### mbed プロジェクトを作成してサンプル コードをインポートする

- Web ブラウザーで、mbed.org の[開発者向けサイト](https://developer.mbed.org/)に移動します。サインアップしていない場合は、新しいアカウントを作成するオプションが表示されます (アカウントの作成は無料です)。既にサインアップしている場合は、アカウントの資格情報を使用してログインします。次に、ページの右上隅の **[Compiler]** をクリックします。これにより、ワークスペース管理インターフェイスが表示されます。

- 使用しているハードウェア プラットフォームがウィンドウの右上隅に表示されていることを確認するか、右上隅にあるアイコンをクリックしてハードウェア プラットフォームを選択します。

- メイン メニューの **[インポート]** をクリックします。次に、mbed の地球ロゴの横にある **[ここをクリック]** をクリックし、URL リンクからインポートします。

    ![][6]

- ポップアップ ウィンドウで、サンプル コード用のリンク https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ を入力します。

    ![][7]

- mbed コンパイラでは、このプロジェクトをインポートしたことでさまざまなライブラリがインポートされたことを確認できます。ライブラリには、Azure IoT チームが提供および管理するライブラリ ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/)、[proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)) もあれば、mbed ライブラリ カタログで入手可能なサード パーティのライブラリもあります。

    ![][8]

- remote\_monitoring\\remote\_monitoring.c を開き、ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. [Device Id] と [Device Key] を自身のデバイス データに置き換えます。

4. デバイス データである IoT Hub ホスト名を使用して、IoTHub 名と IoTHub サフィックスを設定します。これを設定するには、IoT Hub ホスト名を次のように分割する必要があります。

    IoT Hub ホスト名が Contoso.azure-devices.net である場合は、Contoso が IoTHub 名、残りの部分がサフィックスになります。次のようになります。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

#### プログラムをビルドして実行する

- **[コンパイル]** をクリックしてプログラムをビルドします。警告は無視してかまいません。ただし、ビルドでエラーが発生する場合は、続行する前にそのエラーを修正してください。

- ビルドが成功すると、プロジェクト名の付いた .bin ファイルが生成されます。.bin ファイルをデバイスにコピーします。.bin ファイルをデバイスに保存すると、デバイスに対する現在のターミナル セッションがリセットされます。再接続したら、もう一度ターミナルを手動でリセットするか、新しいターミナルを開始します。これにより、mbed デバイスはプログラムの実行をリセットして開始することができます。

- PuTTY などの SSH クライアント アプリケーションを使用して、デバイスに接続します。Windows デバイス マネージャーを確認すると、デバイスで使用されているシリアル ポートを特定できます。


- PuTTY で、接続タイプとして **[シリアル]** をクリックします。ほとんどの場合、デバイスの接続速度は 115200 であるため、この値を **[スピード]** ボックスに入力します。その後、**[開く]** をクリックします。

    ![][11]

プログラムの実行が開始されます。接続時にプログラムが自動的に開始されない場合は、ボードのリセットが必要になることがあります (Ctrl キーを押しながら Break キーを押すか、ボードのリセット ボタンを押します)。

#### 登録済みデバイスとデータを視覚化する

7. リモート監視ソリューションのダッシュボードに戻ります。デバイスの一覧で、デバイスの状態が "実行中" に変更されていることがわかります。![][18]

8. ダッシュボードをクリックすると、データが受信されていることがわかります。サンプルは、内部温度の場合は 50 ユニット、外部温度の場合は 55 ユニット、湿度の場合は 50 ユニットを送信するように構成されています。ダッシュボードに既定で表示されるのは温度と湿度のみであることに注意してください。

8. ここで、[コマンドと制御](#command)に関するセクションに進み、リモート監視ソリューションからデバイスの温度を変更する方法を確認してください。



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

コマンドを送信して制御する方法については、このチュートリアルに後で出てくるそのセクションをご覧ください。

## node.js を使用してリモート監視ソリューションにデバイス データを送信する



-   azure-iot-sdks リポジトリで、packages.json ファイル (/node/common 内) と remote\_monitoring.js ファイル (/ node/device/samples/ 内) を見つけます。これらのファイルをデバイスにコピーして同じフォルダー内に配置します。

- remote-monitoring.js ファイルを開き、次の変数を探します。


   ```
   var deviceID = "[DeviceID]";
   var deviceKey = "[Device Key]";
   var hubName = "[IoT Hub Name]";
   var hubSuffix = "[IoT Hub Suffix i.e azure-devices.net]";
   ```

-  "[Device Id]" および "[Device Key]" を自身のデバイス データに置き換えます。

-  デバイス データである IoT Hub ホスト名を使用して、IoTHub 名と IoTHub サフィックスを設定します。これを設定するには、IoT Hub ホスト名を次のように分割する必要があります。

   IoT Hub ホスト名が Contoso.azure-devices.net である場合は、Contoso が IoTHub 名、残りの部分がサフィックスになります。次のようになります。


   ```
   var deviceID = "mydevice";
   var deviceKey = "mykey";
   var hubName = "Contoso";
   var hubSuffix = "azure-devices.net";
   ```


- ファイルを保存します。保存先フォルダーで次のコマンドを実行します。

```
npm install
node .
```

3.  各変数を、前の手順で収集した情報に置き換えます。変更を保存します。


4. シェル (Linux) または Node.js コマンド プロンプト (Windows) を開き、**node\\samples** フォルダーに移動します。その後、次のコマンドを使用してサンプル アプリケーションを実行します。

    ```
    node simple_sample_remotemonitoring.js
    ```

#### デバイスと受信データを視覚化する

6. 事前構成済みソリューションのポータルで、[デバイス] セクションをクリックし、デバイスの状態が "実行中" に変更されていることとすべての製造元データが表示されていることを確認します。

7. ダッシュボードをクリックし、[表示するデバイス] でデバイスを選択します。これで、テレメトリ データがリモート監視ソリューションで監視されていることがわかります。


## <a name="command"></a>ダッシュボードからデバイスにコマンドを送信して制御する

デバイスが接続され、自動生成された温度データがデバイスから送信されたので、IoT Hub からリモートでデバイスにコマンドを送信して制御することができます。ビジネス アプリケーションに適した複数の種類のコマンドを実装できます。この場合は、ソリューションから温度変化を制御する必要があったかのように、温度変化を実装しました。コマンドを送信するには、次の手順を実行してください。

-  デバイスの一覧の [デバイス ID] をクリックします (デバイス セクションは左側のメニューにあります)。

    ![][13]

- デバイスの詳細が表示される右側のメニューで、[コマンドの送信] をクリックします。


- 実行するコマンドを選択します。ここでは、デバイスの設定温度を変更するため、"温度の設定" を選択します。コマンドと温度の値を選択します。[コマンドの送信] をクリックすると、新しい温度がデバイスにプッシュされます。注: コマンドの履歴で、コマンドの結果が "保留中" になっていることがわかります。これは、簡略化のために、このサンプルでは、IoT Hub に応答するためのロジックをデバイスに実装していないためです。ソリューションを拡張してこのロジックを実装できます。

    ![][14]

- ダッシュボードに戻り、更新されたデータが届いていることを確認します。更新された温度の統計と新しいデータがテレメトリ履歴に表示されていることがわかります。




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## 次のステップ

このサンプルの機能を拡張するには、実際のセンサーをデバイスに接続して実際のデータを送信する、コマンドと制御機能を実装するなど、複数の方法があります。詳細を確認するには、リモート監視ソリューションの拡張方法に関する[ガイド](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md)を使用してください。

<!---HONumber=Nov15_HO3-->