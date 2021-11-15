---
title: X.509 証明書を使用して Linux on Windows で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書の構成証明を使用して、デバイス プロビジョニング サービスによる Azure IoT Edge 用デバイスの大規模プロビジョニングをテストします
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 59c29f3ce91f5ee1e1e5ee65710cc34ce1572907
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846002"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>X.509 証明書を使用して IoT Edge for Linux on Windows デバイスを大規模に作成してプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、X.509 証明書を使用して 1 つ以上の [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を確認してから先に進んでください。

タスクは次のとおりです。

1. 証明書とキーを生成する。
1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムがインストールされた Linux 仮想マシンをデプロイし、それを IoT Hub に接続する。

X.509 証明書を構成証明メカニズムとして使用することは、実稼働環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 通常、X.509 証明書は信頼する証明書チェーンに配置されます。 自己署名証明書または信頼されたルート証明書から開始して、チェーン内の各証明書によって、それよりも 1 つ下位にある証明書が署名されます。 このパターンにより、ルート証明書から各中間証明書を経由して、デバイスにインストールされた最終的な "リーフ" 証明書に至る、信頼する委任チェーンが作成されます。

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

## <a name="generate-device-identity-certificates"></a>デバイス ID 証明書を生成する

デバイス ID 証明書は、信頼する証明書チェーンを介して最上位の X.509 証明機関 (CA) 証明書につながるリーフ証明書です。 デバイス ID 証明書の共通名 (CN) は、IoT ハブ内のデバイスに付けるデバイス ID に設定されている必要があります。

デバイス ID 証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 証明書に署名するためのものではありません (IoT Edge デバイスから確認のためにモジュールまたはリーフ デバイスに提示される CA 証明書とは異なります)。 詳細については、[Azure IoT Edge 証明書の使用方法に関する詳細](iot-edge-certs.md)のページを参照してください。

デバイス ID 証明書を作成すると、2 つのファイルができます。証明書の公開部分を含む .cer または pem ファイルと、証明書の秘密キーを含む .cer または pem ファイルです。 DPS でグループ登録を使用する予定がある場合は、同じ信頼する証明書チェーン内にある中間証明書またはルート CA 証明書の公開部分も必要です。

X.509 を使用して自動プロビジョニングを設定するには、次のファイルが必要です。

* デバイス ID 証明書とその秘密キー証明書。 個別登録を作成すると、デバイス ID 証明書が DPS にアップロードされます。 秘密キーは IoT Edge ランタイムに渡されます。
* 完全なチェーン証明書。少なくともデバイス ID と中間証明書が含まれている必要があります。 完全なチェーン証明書は IoT Edge ランタイムに渡されます。
* 信頼する証明書チェーンからの中間証明書またはルート CA 証明書。 グループ登録を作成すると、この証明書は DPS にアップロードされます。

> [!NOTE]
> 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

### <a name="use-test-certificates-optional"></a>テスト証明書を使用する (省略可能)

新しい ID 証明書を作成するために使用できる証明機関がない場合、このシナリオを試すには、Azure IoT Edge の Git リポジトリにあるスクリプトを使用してテスト証明書を生成できます。 それらの証明書は開発テスト専用に設計されているため、運用環境では使用できません。

テスト証明書を作成するには、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](how-to-create-test-certificates.md)」の手順に従います。 証明書生成スクリプトを設定してルート CA 証明書を作成するために必要とされる、2 つのセクションを完了します。 次に、手順に従ってデバイス ID 証明書を作成します。 完了すると、次の証明書チェーンとキーの組が作成されます。

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

これらの証明書の両方が IoT Edge デバイス上に必要です。 DPS で個別登録を使用する場合は、.cert.pem ファイルをアップロードします。 DPS でグループ登録を使用する場合は、同じ信頼する証明書チェーン内に、アップロードする中間証明書またはルート CA 証明書も必要です。 デモ用の証明書を使用している場合は、グループ登録用に `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 証明書を使用します。

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するための情報を使用してデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値。 Azure portal で、使用している DPS インスタンスの概要ページから、この値を取得できます。
* デバイス上のデバイス ID 証明書チェーン ファイル。
* デバイス上のデバイス ID キー ファイル。

PowerShell または Windows Admin Center を使用して、IoT Edge デバイスをプロビジョニングできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell の場合は、プレースホルダーの値をご自身の値で更新して、次のコマンドを実行します。

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center の場合は、次の手順に従います。

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[X509 Certificate (DPS)]\(X509 証明書 (DPS)\)** を選択します。

1. DPS スコープ ID、デバイス登録 ID、および登録の主キーまたは派生キーを Windows Admin Center の各フィールドに入力します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![対称キーのプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

デバイス プロビジョニング サービスで作成した個々の登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

デバイス プロビジョニング サービスで作成したグループ登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. PowerShell セッションで次のコマンドを使用して、お使いの IoT Edge for Linux on Windows 仮想マシンにログインします。

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >仮想マシンへの SSH 接続が許可されるアカウントは、それを作成したユーザーだけです。

1. ログインすると、次の Linux コマンドを使用して、実行中の IoT Edge モジュールの一覧を確認できます。

   ```bash
   sudo iotedge list
   ```

1. IoT Edge サービスのトラブルシューティングを行う必要がある場合は、次の Linux コマンドを使用します。

    1. サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。

       ```bash
       sudo journalctl -u iotedge
       ```

    2. `check` ツールを使用して、デバイスの構成と接続の状態を確認します。

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Windows Admin Center で接続されているデバイスの一覧から IoT Edge デバイスを選択して、それに接続します。

1. デバイスの概要ページに、デバイスに関するいくつかの情報が表示されます。

   * **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** セクションに、デバイスで実行中のモジュールが表示されます。 IoT Edge サービスが初めて開始されたときは、**edgeAgent** モジュールだけが実行中であると表示されるはずです。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。

   * **[IoT Edge Status]\(IoT Edge の状態\)** セクションにはサービスの状態が表示され、 **[active (running)]\(アクティブ (実行中)\)** と報告されるはずです。

---

新しい IoT Edge デバイスを作成すると、Azure portal で状態コード `417 -- The device's deployment configuration is not set` が表示されます。 これは正常な状態であり、デバイスにモジュールをデプロイできる状態であることを表します。

## <a name="next-steps"></a>次のステップ

デバイス プロビジョニング サービスの登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。

次のこともできます。

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
