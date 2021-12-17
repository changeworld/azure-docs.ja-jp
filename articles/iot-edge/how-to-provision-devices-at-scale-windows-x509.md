---
title: X.509 証明書を使用して Windows 上で IoT Edge デバイスを大規模に作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書を使用して、デバイス プロビジョニング サービスによる Azure IoT Edge 用デバイスの大規模プロビジョニングをテストします
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 639feb61fad0c1e274b6b74950835d67fc1af937
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851973"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>X.509 証明書を使用して、Windows 上で IoT Edge デバイスを大規模に作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、X.509 証明書を使用して 1 つまたは複数の Windows IoT Edge デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を確認してから先に進んでください。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。
>
>Azure IoT Edge for Linux on Windows を使用する場合は、[同等のハウツーガイド](how-to-provision-devices-at-scale-linux-on-windows-x509.md)に記載されている手順に従います。

タスクは次のとおりです。

1. 証明書とキーを生成する。
1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムをインストールし、デバイスを IoT Hub に登録する。

X.509 証明書を構成証明メカニズムとして使用することは、実稼働環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 通常、X.509 証明書は信頼する証明書チェーンに配置されます。 自己署名証明書または信頼されたルート証明書から開始して、チェーン内の各証明書によって、それよりも 1 つ下位にある証明書が署名されます。 このパターンにより、ルート証明書から各中間証明書を経由して、デバイスにインストールされた最終的な "リーフ" 証明書に至る、信頼する委任チェーンが作成されます。

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

IoT Edge デバイスとなる物理デバイスまたは仮想 Windows デバイス。

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

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するための情報を使用してデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値。 Azure portal で、使用している DPS インスタンスの概要ページから、この値を取得できます。
* デバイス上のデバイス ID 証明書チェーン ファイル。
* デバイス上のデバイス ID キー ファイル。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。そのため、`-DpsX509` フラグを使用し、X.509 証明書認証と共に自動プロビジョニングを使用します。

   `scope_id`、`identity cert chain path`、`identity key path` のプレースホルダー値を、使用している DPS インスタンスの適切な値およびデバイス上のファイル パスに置き換えます。

   デバイス ID を ID 証明書の CN 名以外の何かとして設定する場合、`-RegistrationId paste_registration_id_here` パラメーターを追加します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId paste_scope_id_here -X509IdentityCertificate paste_identity_cert_chain_path_here -X509IdentityPrivateKey paste_identity_key_path_here
   ```

   >[!TIP]
   >構成ファイルには、証明書とキー情報がファイル URI として格納されます。 ただし、Initialize-IoTEdge コマンドを使用すると、この書式設定の手順が処理されるため、デバイス上の証明書ファイルとキー ファイルの絶対パスを指定できます。

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

デバイス プロビジョニング サービスで作成した個々の登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

デバイス プロビジョニング サービスで作成したグループ登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

IoT Edge が正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

サービス ログを調べます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次のステップ

デバイス プロビジョニング サービスの登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
