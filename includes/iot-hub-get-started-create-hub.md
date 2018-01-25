## <a name="create-an-iot-hub"></a>IoT Hub の作成
シミュレーション対象デバイス アプリの接続先となる IoT Hub を作成します。 次の手順では、この作業を Azure ポータルで行う方法を示しています。

1. [Azure Portal][lnk-portal] にサインインします。

1. **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順に選択します。
   
    ![Azure portal Jumpbar][1]

1. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   * **[名前]**: IoT ハブの名前を作成します。 入力した名前が有効である場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **[価格とスケールティア]**: このチュートリアルでは、**[F1 - Free]** レベルを選択します。 詳細については、[価格とスケールティア][lnk-pricing]に関するページを参照してください。

   * **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。

   * **[場所]**: 最も近い場所を選択します。

   * **[ダッシュボードにピン留めする]**: ダッシュボードから IoT ハブに簡単にアクセスできるようにするには、このオプションをオンにします。

    ![[IoT Hub] ウィンドウ][2]

1. **Create** をクリックしてください。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を監視できます。

1. IoT ハブが正常に作成されたら、Azure Portal で IoT ハブの新しいタイルをクリックし、新しい IoT ハブのプロパティ ウィンドウを開きます。 IoT Hub を作成したら、その IoT Hub にデバイスとアプリケーションを接続するために必要な重要な情報を把握します。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。
   
    ![新しい [IoT Hub] ウィンドウ][4]

1. **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT Hub 接続文字列をコピーしてメモします。 詳細については、IoT Hub 開発者ガイドの[アクセス制御][lnk-access-control]に関するページを参照してください。
   
    ![共有アクセス ポリシー][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
