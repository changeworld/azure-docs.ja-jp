---
title: Azure IoT Hub Device Provisioning Service でマルチテナント用にデバイスをプロビジョニングする方法
description: デバイス プロビジョニング サービス (DPS) インスタンスでマルチテナント用にデバイスをプロビジョニングする方法
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670313"
---
# <a name="how-to-provision-for-multitenancy"></a>マルチテナント用にプロビジョニングする方法 

このハウツー記事では、[割り当てポリシー](concepts-service.md#allocation-policy)を利用し、IoT Hub のグループに複数のシミュレートされた対称キーのデバイスを安全にプロビジョニングする方法を示します。 プロビジョニング サービスによって定義されている割り当てポリシーでは、さまざまな割り当てシナリオがサポートされています。 よく使用されるシナリオは次の 2 つです。

* **位置情報/geo 待機時間**: デバイスが異なる場所の間を移動するときは、各場所に最も近い IoT ハブに対してデバイスをプロビジョニングすることにより、ネットワーク待機時間が改善されます。 このシナリオでは、異なるリージョンに広がる IoT ハブのグループを、登録対象に選択します。 これらの登録に対して、**最短待機時間** 割り当てポリシーを選択します。 このポリシーを指定すると、Device Provisioning Service はデバイスの待機時間を評価して、IoT ハブのグループから最も近い IoT ハブを決定します。

* **マルチテナント**: IoT ソリューション内で使用されるデバイスは、特定の IoT ハブまたは IoT ハブのグループに割り当てることが必要な場合があります。 ソリューションでは、特定のテナントのすべてのデバイスが、IoT ハブの特定のグループと通信することが必要な場合があります。 場合によっては、テナントが IoT ハブを所有しており、デバイスを IoT ハブに割り当てることが必要なことがあります。

これら 2 つのシナリオを結合するのは一般的なことです。 たとえば、マルチテナントの IoT ソリューションでは、複数のリージョンに分散している IoT ハブのグループを使用して、テナントのデバイスを割り当てることがよくあります。 これらのテナント デバイスは、地理的な場所に基づいて、そのグループ内で待機時間が最短の IoT ハブに割り当てることができます。

この記事では、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のシミュレートされたデバイスのサンプルを使用して、複数リージョンのマルチテナント シナリオでデバイスをプロビジョニングする方法を示します。 この記事では、以下の手順を実施します。

> [!div class="checklist"]
> * Azure CLI を使用して、2 つのリージョン (**米国西部 2** と **米国東部**) に IoT ハブを作成します
> * マルチテナントの登録を作成します
> * Azure CLI を使用して、同じ 2 つのリージョン (**米国西部 2** と **米国東部**) にデバイスとして機能する Linux VM を作成します
> * 両方の Linux VM 上に Azure IoT C SDK 用の開発環境をセットアップします
> * デバイスをシミュレートして、最も近いリージョンの同じテナントに対してプロビジョニングされることを確認します

>[!IMPORTANT]
> 一部のリージョンでは、Virtual Machines の作成に制限が適用されている場合があります。 このガイドの執筆時点では、*westus2* および *eastus* リージョンで VM の作成が許可されています。 これらのリージョンのいずれかで作成できない場合は、別のリージョンを試してみてください。 VM を作成するときの Azure の地理的なリージョンの選択については、「[Azure の仮想マシンのリージョン](../virtual-machines/regions.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を完了します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>2 つのリージョンに IoT ハブを作成する

このセクションでは、1 つの Azure リソース グループと、1 つのテナント用に 2 つの新しいリージョン IoT ハブ リソースを作成します。 1 つの IoT ハブは **米国西部 2** リージョン用、もう 1 つは **米国東部** リージョン用です。

>[!IMPORTANT]
>この記事で作成するすべてのリソースには、この同じリソース グループを使用することをお勧めします。 そうすれば、終わった後のクリーンアップが簡単になります。

1. Azure Cloud Shell で、次の [az group create](/cli/azure/group#az_group_create) コマンドを使用してリソース グループを作成します。

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. *eastus* の場所に IoT ハブを作成し、次の [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) コマンドを使用して作成したリソース グループに追加します (`{unique-hub-name}` は独自の一意の名前に置き換えます)。

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    このコマンドが完了するまでに数分かかる場合があります。

3. 次は、*westus2* の場所に IoT ハブを作成し、次の [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) コマンドを使用して作成したリソース グループに追加します (`{unique-hub-name}` は独自の一意の名前に置き換えます)。

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    このコマンドが完了するまでに数分かかる場合があります。

## <a name="create-the-multitenant-enrollment"></a>マルチテナントの登録を作成する

このセクションでは、テナント デバイス用の新しい登録グループを作成します。  

わかりやすくするため、この記事では[対称キーの構成証明](concepts-symmetric-key-attestation.md)を登録で使用します。 ソリューションをさらに安全にするには、信頼チェーンで [X.509 証明書構成証明](concepts-x509-attestation.md)を使用することを検討してください。

1. Azure portal で Device Provisioning Service を選択します。

2. **[設定]** メニューで **[登録の管理]** を選択します。

3. **[+ 登録グループの追加]** を選択します。

4. **[登録グループの追加]** ページで、次の情報を入力します。

    **[グループ名]**: 「*contoso-us-devices*」と入力します。

    **[Attestation Type]\(構成証明の種類\)**: *[対称キー]* を選択します。

    **[キーの自動生成]**: このチェック ボックスは既にオンになっているはずです。

    **[デバイスをハブに割り当てる方法を選択してください]**: *[最短待機時間]* を選択します。

5. **[新しい IoT ハブのリンク]** を選択します

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="対称キー構成証明にマルチテナントの登録グループを追加します。":::

6. **[IoT Hub へのリンクを追加します]** ページで、次の情報を入力します。

    **[サブスクリプション]**: 複数のサブスクリプションがある場合は、リージョンの IoT ハブを作成したサブスクリプションを選択します。

    **IoT ハブ**: *eastus* の場所用に作成した IoT ハブを選択します。

    **アクセス ポリシー**: *[iothubowner]* を選択します。

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="リージョンの IoT ハブをプロビジョニング サービスとリンクします。":::

7. **[保存]** を選択します。

8. *westgus* の場所に作成した 2 つ目の IoT ハブについて、手順 5 から 7 を繰り返します。

9. **[このグループを割り当てることができる IoT ハブを選択する]** ドロップダウンで、作成した 2 つの IoT ハブを選択します。

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="リンクされた IoT ハブを選択します。":::

10. **[保存]** を選びます。

11. 登録グループの一覧で *[contoso-us-devices]* を選択します。

12. *[主キー]* をコピーします。 このキーは、後で両方のシミュレートされたデバイスに対する一意のデバイス キーを生成するために使用されます。

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="主キーをコピーします。":::

## <a name="create-regional-linux-vms"></a>リージョンの Linux VM を作成する

このセクションでは、2 つのリージョン Linux 仮想マシン (VM) を作成します。 これらの VM は、各リージョンからデバイス シミュレーション サンプルを実行して、両方のリージョンからのテナント デバイスのデバイス プロビジョニングをデモンストレーションします。

クリーンアップを容易にするため、これらの VM は、作成された IoT ハブを格納しているのと同じリソース グループ *contoso-us-resource-group* に追加されます。 ただし、VM は別のリージョン (**米国西部 2** と **米国東部**) で実行されます。

1. Azure Cloud Shell で次のコマンドを実行して、**米国東部** リージョンの VM を作成します。実行前に、コマンドの次のパラメーターを変更します。

    **--name**: 自分の **米国東部** リージョンのデバイスの VM に対する一意名を入力します。

    **--admin-username**: 自分の管理者ユーザー名を使用します。

    **--admin-password**: 自分の管理者パスワードを使用します。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    このコマンドが完了するまでに数分かかります。 

2. コマンドが完了したら、米国東部リージョンの VM の **publicIpAddress** の値をコピーします。

3. Azure Cloud Shell で次のコマンドを実行して、**米国西部 2** リージョンの VM を作成します。実行前に、コマンドの次のパラメーターを変更します。

    **--name**: 自分の **米国西部 2** リージョンのデバイスの VM に対する一意名を入力します。 

    **--admin-username**: 自分の管理者ユーザー名を使用します。

    **--admin-password**: 自分の管理者パスワードを使用します。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    このコマンドが完了するまでに数分かかります。

4. コマンドが完了したら、米国西部 2 リージョンの VM の **publicIpAddress** の値をコピーします。

5. コマンド ライン シェルを 2 つ開きます。

6. SSH を使用して各シェルでリージョン VM の 1 つに接続します。

    管理者のユーザー名と、コピーしたパブリック IP アドレスを、SSH へのパラメーターとして渡します。 メッセージが表示されたら、管理者のパスワードを入力します。

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

このセクションでは、各 VM で Azure IoT C SDK を複製します。 SDK には、各リージョンからのテナントのデバイスのプロビジョニングをシミュレートするサンプルが含まれています。

VM ごとに、次の手順を実行します。

1. 次のコマンドを使用して **CMake**、**g++** 、**gcc**、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) をインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. SDK の[最新リリース](https://github.com/Azure/azure-iot-sdk-c/releases/latest)のタグ名を見つけてコピーします。

3. 両方の VM で、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を複製します。  `-b` パラメーターの値として、前の手順で見つけたタグを使用します。

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作は、完了するまでに数分かかります。

4. リポジトリの内部に新しい **cmake** フォルダーを作成し、そのフォルダーに移動します。

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>一意のデバイス キーを派生させる

グループ登録で対称キーの構成証明を使用する場合、登録グループのキーを直接は使用しません。 その代わりに、各デバイスの登録グループ キーから固有のキーを派生させます。 詳細については、[対称キーを使用したグループの登録](concepts-symmetric-key-attestation.md#group-enrollments)に関するセクションを参照してください。

チュートリアルのこの部分では、グループ マスター キーからデバイス キーを生成し、デバイス用の一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算します。 結果は Base64 形式に変換されます。

>[!IMPORTANT]
>デバイス コードにはグループのマスター キーを含めないでください。

*eastus* と *westus 2* の **両方** のデバイスの場合:

1. **openssl** を使用して、一意のキーを生成します。 次の Bash シェル スクリプトを使用します (`{primary-key}` を先ほどコピーした登録グループの **主キー** に置き換え、`{contoso-simdevice-east}` を各デバイスに独自の一意の登録 ID に置き換えます。 両方の ID を定義するには、小文字の英字、数字、ダッシュ ('-') 文字を使用します)。

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. このスクリプトにより、次のようなキーが出力されます。

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. これで、各テナント デバイスには、プロビジョニング プロセス中に登録グループで対称キーの構成証明を実行するための独自の派生デバイス キーと一意の登録 ID が割り当てられました。

## <a name="simulate-the-devices-from-each-region"></a>各リージョンからデバイスをシミュレートする

このセクションでは、両方のリージョン VM について、Azure IoT C SDK のプロビジョニング サンプルを更新します。 

サンプル コードでは、Device Provisioning Services のインスタンスにプロビジョニング要求を送信するデバイスのブート シーケンスがシミュレートされます。 ブート シーケンスにより、デバイスが認識され、待機時間に基づいて最も近い IoT ハブに割り当てられます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択し、 **[_ID スコープ_]** の値を書き留めます。

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="ポータル ブレードから Device Provisioning Service のエンドポイント情報を抽出します。":::

2. 両方の VM で **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** を開いて編集します。

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. 両方の VM で定数 `id_scope` を探し、先ほどコピーした **ID スコープ** の値で置き換えます。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. 両方の VM で、同じファイル内の `main()` 関数の定義を探します。 次に示すように、登録グループの構成証明の方法に合わせて `hsm_type` 変数が `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` に設定されていることを確認します。 

    両方の VM でファイルに変更を保存します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. **prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを両方の VM で探します。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、各デバイスについて、プレースホルダーの値 (山かっこを含む) を、前のセクションで派生させた一意の登録 ID とデバイス派生キーに置き換えます。 以下に示したキーはあくまでも例です。 先ほど生成したキーを使用してください。

    米国東部: 
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    米国西部: 
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. 両方の VM 上のファイルを保存します。

7. 両方の VM で、次に示すサンプル フォルダーに移動し、サンプルをビルドします。

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. ビルドが成功したら、両方の VM で **prov\_dev\_client\_sample.exe** を実行して、各リージョンからテナント デバイスをシミュレートします。 シミュレートされたデバイスのリージョンに最も近いテナント IoT ハブに、各デバイスが割り当てられることに注意してください。

    シミュレーションを実行する: 
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    米国東部の VM からの出力例:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    米国西部の VM からの出力例:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースを引き続き使用する場合は、そのままにしてかまいません。 それ以外の場合は、次の手順で、この記事で作成したすべてのリソースを削除し、不要な課金を回避します。

以下の手順では、この記事の説明に従って **contoso-us-resource-group** という名前の同じリソース グループにすべてのリソースが作成されていることを前提にしています。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

名前でリソース グループを削除するには

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[リソース グループ]** を選択します。

3. **[名前でフィルター]** テキスト ボックスに、リソースが含まれているリソース グループの名前 **contoso-us-resource-group** を入力します。

4. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。

5. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにもう一度リソース グループの名前を入力し、 **[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

* 再プロビジョニングの詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [IoT Hub デバイスの再プロビジョニングの概念](concepts-device-reprovision.md)

* プロビジョニング解除の詳細については、次の記事を参照してください。
> [!div class="nextstepaction"]
> [自動プロビジョニングされた以前のデバイスのプロビジョニングを解除する方法](how-to-unprovision-devices.md)