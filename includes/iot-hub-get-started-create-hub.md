## <a name="create-an-iot-hub"></a>IoT Hub の作成
シミュレーション対象デバイス アプリの接続先となる IoT Hub を作成します。 次の手順では、この作業を Azure ポータルで行う方法を示しています。

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

IoT Hub を作成したら、その IoT Hub にデバイスとアプリケーションを接続するために必要な重要な情報を把握します。 

1. IoT ハブが正常に作成されたら、Azure Portal で IoT ハブの新しいタイルをクリックし、新しい IoT ハブのプロパティ ウィンドウを開きます。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。
   
    ![新しい [IoT Hub] ウィンドウ][4]
1. **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT Hub 接続文字列をコピーしてメモします。 詳細については、IoT Hub 開発者ガイドの[アクセス制御][lnk-access-control]に関するページを参照してください。
   
    ![共有アクセス ポリシー][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
