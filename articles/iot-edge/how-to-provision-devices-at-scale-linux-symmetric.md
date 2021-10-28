---
title: Linux で対称キーを使用して IoT Edge デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーの構成証明を使用して、Azure IoT Edge と Device Provisioning Service に対して大規模に Linux デバイスのプロビジョニングをテストする
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b19990be57332df6de4ccbb45ae92c74b30041b3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226055"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>対称キーを使用して Linux で大規模に IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、対称キーを使用して 1 つ以上の Linux IoT Edge デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

タスクは次のとおりです。

1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムをインストールし、IoT Hub に接続する。

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>簡素化されたエクスペリエンスのために、[Azure IoT Edge 構成ツール](https://github.com/azure/iot-edge-config)をお試しください。 現在パブリック プレビュー中のこのコマンド ライン ツールは、デバイスに IoT Edge をインストールし、DPS と対称キーの構成証明を使用してプロビジョニングします。
:::moniker-end

対称キーの構成証明は、Device Provisioning Service インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

## <a name="prerequisites"></a>前提条件

* アクティブな IoT Hub。
* IoT Edge デバイスとなる物理デバイスまたは仮想 Linux デバイス。
  * 各デバイスを識別するために、一意の **登録 ID** を定義する必要があります。 デバイスの MAC アドレス、シリアル番号、または何らかの固有の情報を使用できます。 この例では、MAC アドレスとシリアル番号の組み合わせを使用して、次のような登録 ID の文字列を形成します: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。 有効な文字は、小文字の英字、数字、ダッシュ (`-`) です。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスをお持ちでない場合は、IoT Hub Device Provisioning Service クイックスタートの[新しい IoT Hub Device Provisioning Service の作成](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)および [IoT Hub と Device Provisioning Service のリンク](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)に関するセクションの手順に従ってください。
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

DPS を介して 1 つ以上のデバイスをプロビジョニングする登録を作成します。

単一の IoT Edge デバイスをプロビジョニングする方法を求めている場合は、**個別の登録** を作成します。 複数のデバイスをプロビジョニングする必要がある場合は、DPS **グループ登録** を作成する手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[対称キー]** を選択します。

   1. デバイスの一意の **登録 ID** を指定します。

   1. 必要に応じて、デバイスの **IoT Hub デバイス ID** を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

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

1. IoT Edge ランタイムのインストール時に使用する個別登録の **主キー** の値をコピーします。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用してグループ登録を作成することもできます。 詳細については、[az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[グループ名]** を指定します。

   1. 構成証明の種類として **[対称キー]** を選択します。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

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

1. グループ登録で使用するデバイス キーを作成するときに使用する、登録グループの **主キー** の値をコピーします。

これで、登録グループが存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

#### <a name="derive-a-device-key"></a>デバイス キーを派生させる

グループ登録の一部としてプロビジョニングされる各デバイスには、プロビジョニング中に登録で対称キー構成証明を実行するために、派生デバイス キーが必要です。

デバイス キーを生成するには、DPS 登録グループからコピーしたキーを使用して、デバイスに対する一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。

> [!IMPORTANT]
> デバイス コードには、登録の主キーやセカンダリ キーを含めないでください。

Linux では、次の例に示すように、openssl を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値をデバイスの登録 ID に置き換えます。

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

派生デバイス キーの出力例を次に示します。

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

このセクションでは、Linux 仮想マシンまたは IoT Edge の物理デバイスを準備します。 次に、IoT Edge をインストールします。

IoT Edge ランタイムをインストールするように準備するには、デバイスで完了する必要がある 2 つの手順があります。 デバイスは Microsoft インストール パッケージにアクセスする必要があります。また、コンテナー エンジンがインストールされている必要があります。

### <a name="access-the-microsoft-installation-packages"></a>Microsoft インストール パッケージにアクセスする

お使いのデバイスは、Microsoft インストール パッケージにアクセスできる必要があります。

1. デバイスのオペレーティング システムに一致するリポジトリ構成をインストールします。

   * **Ubuntu Sever 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

### <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby エンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby コンテナー エンジンをインストールするときにエラーが発生する場合は、Linux カーネルに Moby との互換性があることを確認します。 埋め込みデバイスの一部の製造元からは、コンテナー エンジンとの互換性のために必要な機能を備えていないカスタム Linux カーネルを含むデバイス イメージが提供されています。 Moby から提供されている [check-config スクリプト](https://github.com/moby/moby/blob/master/contrib/check-config.sh)を使用する次のコマンドを実行して、カーネルの構成を確認します。
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > スクリプトの出力で、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。 足りない機能がある場合は、カーネルをソースからリビルドし、適切なカーネルの .config に含める関連モジュールを選択することで、それらを有効にします。同様に、`defconfig` や `menuconfig` などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、ご自分のカーネルを適宜再構築します。 新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、必要なすべての機能が正常に有効になっていることを確認します。

### <a name="install-iot-edge"></a>IoT Edge をインストールする

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、「[オフラインまたは特定のバージョンのインストール](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional)」の手順に従ってください。

1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. IoT Edge バージョン 1.1* と **libiothsm-std** パッケージをインストールします。

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge バージョン 1.1 は、IoT Edge の長期サポート ブランチです。 古いバージョンを実行している場合は、サポートされなくなるため、最新のパッチのインストールまたは更新をお勧めします。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge サービスによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 サービスは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

IoT ID サービスは、IoT Edge のバージョン 1.2 と共に導入されました。 このサービスにより、IoT Edge と、IoT Hub と通信する必要があるその他のデバイス コンポーネントの ID のプロビジョニングと管理が処理されます。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、「[オフラインまたは特定のバージョンのインストール](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional)」の手順に従ってください。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

IoT Edge がどのバージョンであるかと、IoT ID サービスが使用できることを確認します。

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

最新バージョンの IoT Edge と IoT ID サービス パッケージをインストールするには、次のコマンドを使用します。

   ```bash
   sudo apt-get install aziot-edge
   ```

また、最新バージョンではないバージョンの IoT Edge をインストールする場合は、`aziot-edge` サービスと `aziot-identity-service` サービスの両方に必ず同じバージョンをインストールしてください。

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* 個別登録の **主キー**、またはグループ登録を使用するデバイスの [派生キー](#derive-a-device-key)のいずれか。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 DPS 対称キーのプロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

1. 必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に DPS を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的に再プロビジョニングするようにデバイスが設定されている場合、再プロビジョニング イベントが検出されると、IoT Edge が再起動し、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge のインストールの一部として提供されるテンプレート ファイルに基づいて、デバイスの構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. ファイルの **プロビジョニング** セクションを見つけます。 対称キーによる DPS のプロビジョニング行をコメント解除し、他にもプロビジョニング行があれば、それらもコメント アウトするようにします。

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. `id_scope`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

   対称キー パラメーターは、インライン キー、ファイル URI、または PKCS#11 URI の値を受け入れることができます。 使用している形式に基づいて、1 つの対称キー行だけをコメント解除します。

   任意の PKCS#11 URI を使用する場合は、構成ファイルで **PKCS#11** セクションを見つけ、PKCS#11 構成に関する情報を提供します。

1. config.toml ファイルを保存して閉じます。

1. 加えた構成の変更を IoT Edge に適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

Device Provisioning Service で作成したグループ登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

IoT Edge が正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge サービスの状態を確認します。

```cmd/sh
systemctl status iotedge
```

サービス ログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge サービスの状態を確認します。

```cmd/sh
sudo iotedge system status
```

サービス ログを調べます。

```cmd/sh
sudo iotedge system logs
```

実行中のモジュールを一覧表示します。

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
