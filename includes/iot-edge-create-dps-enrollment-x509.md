---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16338971721edfb68c93821ddf08c5cc36caf0b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846001"
---
## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

生成された証明書とキーを使用して、DPS で 1 つまたは複数の IoT Edge デバイスの登録を作成します。

単一の IoT Edge デバイスをプロビジョニングする方法を求めている場合は、**個別の登録** を作成します。 複数のデバイスをプロビジョニングする必要がある場合は、DPS **グループ登録** を作成する手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](../articles/iot-edge/how-to-deploy-at-scale.md)を作成するために使用されます。

デバイス プロビジョニング サービスでの登録の詳細については、[デバイス登録の管理方法](../articles/iot-dps/how-to-manage-enrollments.md)に関するページをご覧ください。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

個別登録では、デバイスの ID 証明書の公開部分が渡され、デバイス上の証明書と照合されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   * **メカニズム**: **[X.509]** を選択します。

   * **[プライマリ証明書の .pem ファイルまたは .cer ファイル]** : デバイス ID 証明書からパブリック ファイルをアップロードします。 スクリプトを使用してテスト証明書を生成した場合は、次のファイルを選択します。

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

   * **[IoT Hub のデバイス ID]** : 必要に応じて、デバイス ID を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しない場合は、X.509 証明書内の共通名 (CN) が使用されます。

   * **[IoT Edge デバイス]** : **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **[保存]** を選択します。

**[登録を管理します]** で、作成した登録の **登録 ID** を確認できます。 デバイスをプロビジョニングするときに使用できるので、メモします。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

グループ登録では、個々のデバイス ID 証明書の生成に使用された信頼する証明書チェーンから、中間証明書またはルート CA 証明書を使用します。

#### <a name="verify-your-root-certificate"></a>ルート証明書を確認する

登録グループを作成するときに、確認済みの証明書を使用することもできます。 ルート証明書の所有権があることを示すことによって、DPS で証明書を確認できます。 詳細については、[X.509 CA 証明書の所有証明を行う方法](../articles/iot-dps/how-to-verify-certificates.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. 左側のメニューから **[証明書]** を選択します。

1. **[追加]** を選択して新しい証明書を追加します。

1. 証明書のフレンドリ名を入力し、X.509 証明書の公開部分を表す .cer または .pem ファイルを参照します。

   デモ用の証明書を使用している場合は、`<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` 証明書をアップロードします。

1. **[保存]** を選択します。

1. これで、その証明書が **[証明書]** ページに表示されます。 それを選択して証明書の詳細を開きます。

1. **[確認コードを生成します]** を選択した後、生成されたコードをコピーします。

1. 独自の CA 証明書を入手したか、デモ用の証明書を使用しているかにかかわらず、IoT Edge リポジトリに用意されている検証ツールを使用して、所有証明を確認することができます。 検証ツールでは、CA 証明書を使用して、指定された確認コードをサブジェクト名として持つ新しい証明書に署名します。

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Azure portal の同じ証明書詳細ページで、新しく生成された検証証明書をアップロードします。

1. **[認証]** を選択します。

#### <a name="create-enrollment-group"></a>登録グループを作成する

デバイス プロビジョニング サービスでの登録の詳細については、[デバイス登録の管理方法](../articles/iot-dps/how-to-manage-enrollments.md)に関するページをご覧ください。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用してグループ登録を作成することもできます。 詳細については、[az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[登録グループの追加]** を選択し、登録を構成する以下の手順を完了します。

   * **[グループ名]** : このグループ登録の覚えやすい名前を入力します。

   * **[構成証明の種類]** : **[Certificate]** を選択します。

   * **[IoT Edge デバイス]** : **[True]** を選択します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   * **[証明書の種類]** : **[CA 証明書]** を選択します。

   * **[プライマリ証明書]** : ドロップダウン リストからご利用の証明書を選択します。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **[保存]** を選択します。

**[登録を管理します]** で、作成した登録の **登録 ID** を確認できます。 デバイスをプロビジョニングするときに使用できるので、メモします。

これで、これらのデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

---
