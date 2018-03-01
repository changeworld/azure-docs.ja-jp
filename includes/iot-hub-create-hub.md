1. [Azure Portal][lnk-portal] にサインインします。
1. **[リソースの作成]** > **[モノのインターネット]** > **[IoT Hub]** を選択します。
   
    ![Azure Portal での IoT Hub へのナビゲーションのスクリーン ショット][1]

1. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   * **[名前]**: IoT ハブの名前を作成します。 入力した名前が有効である場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **[価格とスケールティア]**: このチュートリアルでは、**[F1 - Free]** レベルを選択します。 詳細については、[価格とスケールティア][lnk-pricing]に関するページを参照してください。

   * **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。

   * **[場所]**: 最も近い場所を選択します。

   * **[ダッシュボードにピン留めする]**: ダッシュボードから IoT ハブに簡単にアクセスできるようにするには、このオプションをオンにします。

    ![[IoT Hub] ウィンドウ][2]

1. **Create** をクリックしてください。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を監視できます。
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md