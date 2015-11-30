> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## シナリオの概要

このシナリオでは、次のテレメトリをリモート監視[構成済みソリューション][lnk-what-are-preconfig-solutions]に送信するデバイスを作成します。

- 外部温度
- 内部温度
- 湿度

わかりやすくするために、デバイス上のコードではサンプル値を生成しますが、デバイスに実際のセンサーを接続し、実際のテレメトリを送信して、サンプルを拡張することをお勧めします。

## 開始する前に

デバイス用のコードを作成する前に、リモート監視構成済みソリューションをプロビジョニングし、そのソリューションにデバイスをプロビジョニングする必要があります。

### リモート監視構成済みソリューションをプロビジョニングする

作成するデバイスは、[リモート監視][lnk-remote-monitoring]構成済みソリューションのインスタンスにデータを送信します。[Azure IoT Suite の概要][lnk-getstarted]に関するページにアクセスして Azure アカウントを作成し、IoT Suite をプロビジョニングします。新しいソリューションを作成するときは、**[リモート監視]** を選択します。

リモート監視ソリューションを既にプロビジョニングしている場合は、**[起動]** をクリックしてソリューション ダッシュボードを開きます。

![][img-dashboard]

### リモート監視ソリューションでデバイスをプロビジョニングする

> [AZURE.NOTE]ソリューションにデバイスを既にプロビジョニングしている場合は、この手順を省略して構いません。クライアント アプリケーションを作成するときに、デバイスの資格情報が必要になります。

デバイスが構成済みソリューションに接続するには、デバイスが有効な資格情報を使用してそれ自体を識別できる必要があります。ソリューション ダッシュボードからデバイスの資格情報を取得し、クライアント アプリケーションに含めることができます。

新しいデバイスをリモート監視ソリューションに追加するには、ソリューション ダッシュボードで次の手順を実行します。

1.  ダッシュボードの左下隅にある **[デバイスの追加]** をクリックします。

    ![][1]

2.  **[カスタム デバイス]** パネルで、**[新規追加]** をクリックします。

    ![][2]

3.  **[自分で自分のデバイス ID を定義する]** を選択し、**mydevice** などのデバイス ID を入力します。**[ID の確認]** をクリックして、その名前が使用中でないことを確認し、**[作成]** をクリックしてデバイスをプロビジョニングします。

    ![][3]

5. デバイスの資格情報 (デバイス ID、IoT Hub ホスト名、デバイス キー) をメモしておきます。クライアント アプリケーションがデバイスをリモート監視ソリューションに接続する際に、この資格情報が必要になります。次に、**[Done]** をクリックします。

    ![][4]

6. デバイス セクションにデバイスが正しく表示されていることを確認します。デバイスがリモート監視ソリューションへの接続を確立するまで、状態が **[保留中]** になります。

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: http://www.microsoft.com/server-cloud/internet-of-things/getting-started.aspx
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=Nov15_HO4-->