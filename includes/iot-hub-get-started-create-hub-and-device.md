## <a name="create-an-iot-hub"></a>IoT Hub の作成

1. [Azure Portal](https://portal.azure.com/) で **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順にクリックします。

   ![Azure Portal で IoT Hub を作成する](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   **[名前]**: IoT Hub の名前です。 入力した名前が有効である場合は、緑色のチェック マークが表示されます。

   **[Pricing and scale tier (価格とスケール レベル)]**: 無料の F1 レベルを選択します。 このデモでは、このオプションで十分です。 [[pricing and scale tier] (価格とスケール レベル)](https://azure.microsoft.com/pricing/details/iot-hub/) を参照してください。

   **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 [リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-portal.md)に関する記事をご覧ください。

   **[場所]**: IoT Hub が作成される場所に最も近い場所を選択します。

   **[ダッシュボードにピン留めする]**: ダッシュボードから IoT Hub に簡単にアクセスするためには、このオプションにチェックを入れます。

   ![Azure IoT Hub を作成するためにフィールドに入力する](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. **[作成]**をクリックします。 IoT Hub の作成には数分かかります。 **[通知]** ウィンドウで進行状況を確認できます。

   ![IoT Hub の作成の進行状況についての通知を見る](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. IoT Hub 作成後は、ダッシュボードで IoT Hub をクリックします。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。

   ![IoT Hub のホスト名を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. **[共有アクセス ポリシー]** ウィンドウで、**[iothubowner]** ポリシーをクリックし、IoT Hub の **[接続文字列]** をコピーしてメモしておきます。 詳細については、「[IoT Hub へのアクセスの制御](../articles/iot-hub/iot-hub-devguide-security.md)」を参照してください。

   ![IoT Hub の接続文字列を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-the-your-device"></a>デバイスの IoT hub にデバイスを登録する

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。
1. **デバイス エクスプローラー**をクリックします。
1. デバイス エクスプローラー ウィンドウで **[追加]** をクリックして、デバイスを IoT Hub に追加します。

   **[デバイス ID]**: 新しいデバイスの ID。

   **[認証の種類]**: **[対称キー]** を選択します。

   **[Auto Generate Keys (自動生成キー)]**: このフィールドをオンにします。

   **[Connect device to IoT Hub (デバイスを IoT Hub に接続する)]**: **[有効]** をクリックします。

   ![IoT Hub のデバイス エクスプローラーでデバイスを追加する](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. **[Save]**をクリックします。
1. デバイスが作成された後、**デバイス エクスプローラー** ウィンドウでデバイスを開きます。
1. 接続文字列の主キーをメモします。

   ![デバイスの接続文字列を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)