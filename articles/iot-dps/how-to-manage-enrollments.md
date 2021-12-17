---
title: Azure portal で Azure IoT Hub Device Provisioning Service に対するデバイスの登録を管理する
description: Azure portal 上でデバイス プロビジョニング サービス (DPS) 用にデバイス登録を管理する方法
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/25/2021
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 7f0fdcf8a3e51405861082b3c2ad8a6385f5a6e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843389"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure portal でデバイス登録を管理する方法

"*デバイス登録*" では、1 つのデバイスまたはデバイス グループのレコードを作成して、ある時点で Azure IoT Hub Device Provisioning Service (DPS) に登録できるようにします。 登録レコードには、デバイスの初期構成がその登録の一部として含まれます。 構成には、デバイスが割り当てられる IoT ハブ、またはハブのセットからハブを構成する割り当てポリシーが含まれます。 この記事では、プロビジョニング サービス用にデバイス登録を管理する方法を説明します。

Azure IoT Device Provisioning Service では、次の 2 種類の登録がサポートされています。

* [登録グループ](concepts-service.md#enrollment-group)：複数の関連するデバイスを登録するために使用します。
* [個別登録](concepts-service.md#individual-enrollment): 単一デバイスを登録するために使用します。

## <a name="create-an-enrollment-group"></a>登録グループを作成する

 登録グループは、共通の構成証明メカニズムを共有するデバイスのグループに対するエントリです。 初期構成を共有するデバイスが多数ある場合や、デバイスが同じテナントに属する予定の場合は、登録グループを使用することをお勧めします。 [対称キー](concepts-symmetric-key-attestation.md)または [X.509 証明書](concepts-x509-attestation.md)の構成証明を使用するデバイスがサポートされます。

### <a name="create-a-symmetric-key-enrollment-group"></a>対称キーの登録グループを作成する

対称キーで登録グループを作成して使用する場合は、[対称キーによるデバイスのプロビジョニング](how-to-legacy-device-symm-key.md)に関するチュートリアルを参照してください。

対称キーの登録グループを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで **[登録の管理]** を選択します。

5. ページの上部で、 **[+ 登録グループの追加]** を選択します。

6. **[登録グループの追加]** ページで、次の情報を入力します。

   | フィールド | 説明 |
    | :--- | :--- |
    | **グループ名** | デバイスのグループの名前。|
    | **構成証明の種類** |**[対称キー]** を選択します。|
    | **キーの自動生成** |このボックスをオンにします。|
    | **デバイスをハブに割り当てる方法を選択してください** |特定のハブに割り当てることができるように、 *[静的構成]* を選択します。|
    | **このグループを割り当てることができる IoT ハブを選択してください** |お使いのハブのいずれかを選択します。|

    それ以外のフィールドは、既定の値をそのまま使用します。

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-symm-key.png" alt-text="対称キー構成証明に登録グループを追加します。":::

7. **[保存]** を選択します。

### <a name="create-a-x509-certificate-enrollment-group"></a>X.509 証明書の登録グループを作成する

X.509 証明書の登録グループを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで **[登録の管理]** を選択します。

5. ページの上部で、 **[+ 登録グループの追加]** を選択します。

6. **[登録グループの追加]** ページで、次の情報を入力します。

    | フィールド | 説明 |
    | :--- | :--- |
    | **グループ名** | デバイスのグループの名前。|
    | **構成証明の種類** |**[Certificate]** を選択します。|
    | **証明書の種類** | デバイス証明書への署名に使用された証明書に基づいて、 **[CA 証明書]** または **[中間証明書]** を選択します。|
    | **プライマリ証明書**| CA 証明書を使用してデバイス証明書に署名している場合、ルート CA 証明書は[所有証明](how-to-verify-certificates.md)が完了している必要があります。 中間証明書を使用してデバイス証明書に署名している場合は、アップロード ボタンが使用できるようになり、中間証明書をアップロードできます。 中間証明書に署名した証明書についても、[所有証明](how-to-verify-certificates.md)が完了している必要があります。

    それ以外のフィールドは、既定の値をそのまま使用します。

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-cert.png" alt-text="CA 証明書の構成証明の登録グループを追加します。":::

7. **[保存]** を選択します。

## <a name="create-an-individual-enrollment"></a>個別登録を作成する

個別の登録は、IoT ハブに割り当てることができる単一のデバイス用のエントリです。 [対称キー](concepts-symmetric-key-attestation.md)、[X.509 証明書](concepts-x509-attestation.md)、[TPM 構成証明](concepts-tpm-attestation.md)を使用しているデバイスがサポートされます。

### <a name="create-a-symmetric-key-individual-enrollment"></a>対称キーの個別登録を作成する

>[!TIP]
>対称キーを使用して個別の登録を作成して使用する方法の詳細については、「[クイックスタート: シミュレートされた対称キー デバイスをプロビジョニングする](quick-create-simulated-device-symm-key.md#create-a-device-enrollment)」を参照してください。

対称キーの個別登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで、 **[登録の管理]** を選択します。

5. ページの上部にある **[+ 個別登録の追加]** を選択します。

6. **[登録の追加]** ページで、次の情報を入力します。

    | フィールド | 説明 |
    | :--- | :--- |
    | **メカニズム** | *[対称キー]* を選択します |
    | **キーの自動生成** |このボックスをオンにします。 |
    | **登録 ID** | 一意の登録 ID を入力します。|
    | **IoT Hub のデバイス ID** |  この ID はデバイスを表します。 デバイス ID の規則に従う必要があります。 詳細については、「デバイス ID のプロパティ」(../iot-hub/iot-hub-devguide-identity-registry) を参照してください。 デバイス ID が指定されていない場合は、登録 ID が使用されます。|
    | **デバイスをハブに割り当てる方法を選択してください** |特定のハブに割り当てることができるように、 *[静的構成]* を選択します。|
    | **このグループを割り当てることができる IoT ハブを選択してください** |お使いのハブのいずれかを選択します。|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-symm-key.png" alt-text="対称キーの構成証明に対する個別の登録を追加します。":::

7. **[保存]** を選択します。

### <a name="create-a-x509-certificate-individual-enrollment"></a>X.509 証明書の個別登録を作成する

>[!TIP]
>X.509 証明書を使用して個別登録を作成して使用する方法の詳細については、[クイックスタート: X.509 証明書デバイスをプロビジョニングする](quick-create-simulated-device-x509.md#create-a-device-enrollment)に関するページを参照してください。

X.509 証明書の個別登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで、 **[登録の管理]** を選択します。

5. ページの上部にある **[+ 個別登録の追加]** を選択します。

6. **[登録の追加]** ページで、次の情報を入力します。

    | フィールド | 説明 |
    | :--- | :--- |
    | **メカニズム** | *X.509* を選択します。 |
    | **プライマリ証明書の .pem または .cer ファイル** | リーフ証明書を生成できる証明書をアップロードします。 .cer ファイルを選択した場合は、base-64 でエンコードされた証明書のみ受け入れられます。 |
    | **IoT Hub のデバイス ID** |  この ID はデバイスを表します。 デバイス ID の規則に従う必要があります。 詳細については、「デバイス ID のプロパティ」(../iot-hub/iot-hub-devguide-identity-registry) を参照してください。 デバイス ID は、登録用にアップロードするデバイス証明書のサブジェクト名である必要があります。 そのサブジェクト名は、デバイス ID の規則に準拠している必要があります。|
    | **デバイスをハブに割り当てる方法を選択してください** |特定のハブに割り当てることができるように、 *[静的構成]* を選択します。|
    | **このグループを割り当てることができる IoT ハブを選択してください** |お使いのハブのいずれかを選択します。|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-cert.png" alt-text="X.509 証明書の構成証明の個別登録を追加します。":::

7. **[保存]** を選択します。

### <a name="create-a-tpm-individual-enrollment"></a>TPM の個別登録を作成する

>[!TIP]
>TPM 構成証明を使用して個別登録を作成して使用する方法の詳細な手順については、[シミュレートされた TPM デバイスのプロビジョニング](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry)に関するサンプルのいずれかを参照してください。

TPM の個別登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで、 **[登録の管理]** を選択します。

5. ページの上部にある **[+ 個別登録の追加]** を選択します。

6. **[登録の追加]** ページで、次の情報を入力します。

    | フィールド | 説明 |
    | :--- | :--- |
    | **メカニズム** | *[TPM]* を選択します。 |
    | **保証キー** | TPM デバイスの一意の保証キー。 |
    | **登録 ID** | 一意の登録 ID を入力します。|
    | **IoT Hub のデバイス ID** |  この ID はデバイスを表します。 デバイス ID の規則に従う必要があります。 詳細については、「デバイス ID のプロパティ」(../iot-hub/iot-hub-devguide-identity-registry) を参照してください。 デバイス ID が指定されていない場合は、登録 ID が使用されます。|
    | **デバイスをハブに割り当てる方法を選択してください** |特定のハブに割り当てることができるように、 *[静的構成]* を選択します。|
    | **このグループを割り当てることができる IoT ハブを選択してください** |お使いのハブのいずれかを選択します。|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-tpm.png" alt-text="TPM 構成証明の個別登録を追加します。":::

7. **[保存]** を選択します。

## <a name="update-an-enrollment-entry"></a>登録エントリを更新する

既存の登録エントリを更新するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで **[登録の管理]** を選択します。

5. 変更する登録エントリを選択します。

6. 登録エントリの詳細ページで、セキュリティの種類と資格情報を除く、すべての項目を更新できます。

7. 完了したら、 **[保存]** を選択します。

## <a name="remove-a-device-enrollment"></a>デバイス登録を削除する

登録エントリを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスを登録する Device Provisioning Service を選択します。

4. **[設定]** メニューで **[登録の管理]** を選択します。

5. 削除する登録エントリを選択します。 

6. ページの上部で、**[削除]** を選択します。

7. 確認メッセージが表示されたら、 **[はい]** を選択します。

8. アクションが完了すると、デバイス登録の一覧からエントリが削除されたことがわかります。
