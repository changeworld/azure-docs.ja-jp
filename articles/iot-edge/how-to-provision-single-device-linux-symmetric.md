---
title: 対称キーを使用して Linux で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーを使用して手動でプロビジョニングするために IoT Hub に 1 つの IoT Edge デバイスを作成およびプロビジョニングする
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: v-tcassi
ms.openlocfilehash: e2b45db2072bc779442d5f900de5c5e0321cdee0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269714"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-symmetric-keys"></a>対称キーを使用して Linux で IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、Linux IoT Edge デバイスを登録およびプロビジョニングするエンドツーエンドの手順 (IoT Edge のインストールを含む) について説明します。

IoT ハブに接続するすべてのデバイスには、cloud-to-device または device-to-cloud の通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

この記事の手順では、手動プロビジョニングと呼ばれるプロセスについて説明します。ここでは、1 つのデバイスを IoT ハブに接続します。 手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**: IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**: 2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスでは IoT Hub に対して認証を行うときに、1 つの証明書が提示され、その証明書がその拇印と一致することが IoT によって検証されます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、認証方法として対称キーを使用する方法について説明します。 X.509 証明書を使用する場合は、「[x.509 証明書を使用して Linux で IoT Edge デバイスを作成およびプロビジョニングする](how-to-provision-single-device-linux-x509.md)」を参照してください。

> [!NOTE]
> 設定するデバイスが多数あり、それぞれを手動でプロビジョニングしたくない場合は、次の記事のいずれかを使用して、IoT Edge が IoT Hub Device Provisioning Service でどのように動作するかを確認してください。
>
> * [X.509 証明書を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-linux-x509.md)
> * [TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-auto-provision-simulated-device-linux.md)
> * [対称キーを使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>[前提条件]

この記事では、IoT Edge デバイスを登録し、そこに IoT Edge をインストールする方法について説明します。 これらのタスクには、実行するために使用されるさまざまな前提条件とユーティリティがあります。 次に進む前に、すべての前提条件が満たされていることを確認してください。

### <a name="device-registration"></a>デバイス登録

デバイスを登録する手順には、**Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。 ユーティリティごとに個別の前提条件があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。

---

### <a name="iot-edge-installation"></a>IoT Edge のインストール

X64、ARM32、または ARM64 の Linux デバイス。

Microsoft からは、Ubuntu Server 18.04 および Raspberry Pi OS Stretch の各オペレーティング システム用のインストール パッケージが提供されています。

運用シナリオ向けに現在サポートされているオペレーティング システムに関する最新の情報については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください

>[!NOTE]
>ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

## <a name="register-your-device"></a>デバイスを登録する

デバイスを登録するには、好みに応じて **Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-provision-single-device-linux-symmetric/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、後で使用するのでメモしておいてください。
   * 認証の種類として **[対称キー]** を選択します。
   * 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を機能させるには、Azure アカウントにサインインし、ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-provision-single-device-linux-symmetric/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code を使用して新しいデバイスを登録する

1. Visual Studio Code エクスプローラーで、 **[Azure IoT Hub]** セクションを展開します。
1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/iot/hub/device-identity) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* `--device-id` または `-d`: IoT ハブ内で一意のわかりやすい名前を指定します。
* `--hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`:デバイスが IoT Edge デバイスであることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-provision-single-device-linux-symmetric/create-edge-device-cli.png)

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する情報を取得します。

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>登録済みデバイスを表示し、プロビジョニング情報を取得する

対称キー認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために接続文字列が必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-linux-symmetric/portal-view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

対称キーを使用して認証を行うデバイスでは、接続文字列をポータルでコピーできます。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-linux-symmetric/view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>`connection-string show` コマンドは、Azure IoT 拡張機能のバージョン 0.9.8 で導入され、これによって、非推奨の `show-connection-string` コマンドが置き換えられました。 このコマンドの実行中にエラーが発生した場合は、拡張機能のバージョンが 0.9.8 以降に更新されていることを確認してください。 詳細情報と最新の更新プログラムについては、[Azure CLI 用の Microsoft Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)に関するページを参照してください。

`device-id` パラメーターの値は、大文字と小文字が区別されます。

デバイスで使用する接続文字列をコピーする場合は、接続文字列を囲んでいる引用符を含めないでください。

---

## <a name="install-iot-edge"></a>IoT Edge をインストールする

このセクションでは、Linux 仮想マシンまたは IoT Edge の物理デバイスを準備します。 次に、IoT Edge をインストールします。

IoT Edge ランタイムをインストールするように準備するには、デバイスで完了する必要がある 2 つの手順があります。 デバイスは Microsoft インストール パッケージにアクセスする必要があります。また、コンテナー エンジンがインストールされている必要があります。

### <a name="prepare-your-device-to-access-the-microsoft-installation-packages"></a>Microsoft インストール パッケージにアクセスするようにデバイスを準備します

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。

   * **Ubuntu Server 18.04**:

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
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

### <a name="install-a-container-engine-on-your-device"></a>デバイスにコンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby エンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

Moby コンテナー エンジンをインストールするときにエラーが発生する場合は、Linux カーネルに Moby との互換性があることを確認します。 埋め込みデバイスの一部の製造元からは、コンテナー エンジンとの互換性のために必要な機能を備えていないカスタム Linux カーネルを含むデバイス イメージが提供されています。 Moby から提供されている [check-config スクリプト](https://github.com/moby/moby/blob/master/contrib/check-config.sh)を使用する次のコマンドを実行して、カーネルの構成を確認します。

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

スクリプトの出力で、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。 足りない機能がある場合は、カーネルをソースからリビルドし、適切なカーネルの .config に含める関連モジュールを選択することで、それらを有効にします。同様に、`defconfig` や `menuconfig` などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、ご自分のカーネルを適宜再構築します。 新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、必要なすべての機能が正常に有効になっていることを確認します。

### <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation-optional)」の手順に従ってください。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

IoT Edge バージョン 1.1* と **libiothsm-std** パッケージをインストールします。

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge バージョン 1.1 は、IoT Edge の長期サポート ブランチです。 古いバージョンを実行している場合は、サポートされなくなるため、最新のパッチのインストールまたは更新をお勧めします。

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge サービスによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 サービスは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

IoT ID サービスは、IoT Edge のバージョン 1.2 と共に導入されました。 このサービスにより、IoT Edge と、IoT Hub と通信する必要があるその他のデバイス コンポーネントの ID のプロビジョニングと管理が処理されます。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation-optional)」の手順に従ってください。

>[!NOTE]
>このセクションの手順では、IoT Edge バージョン 1.2 をインストールする方法について説明します。
>
>以前のバージョンを実行している IoT Edge デバイスが既にあり、1.2 にアップグレードする場合は、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」の手順を使用します。 バージョン 1.2 は、以前のバージョンの IoT Edge とは異なる点が多いため、アップグレードするには特定の手順が必要です。

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

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、クラウド ID と認証情報を使用してデバイスを設定できます。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

ファイルのプロビジョニング構成を見つけ、"**Manual provisioning configuration using a connection string**" セクションがまだコメント解除されていない場合はコメント解除します。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

次のコマンドで、対称キー認証を使用する IoT Edge デバイスを簡単に構成できます。

   ```bash
   sudo iotedge config mp --connection-string 'PASTE_CONNECTION_STRING_HERE'
   ```

`iotedge config mp` コマンドを実行して、デバイスに構成ファイルを作成し、接続文字列を提供し、構成の変更を適用します。

構成ファイルを表示する場合は、次の方法で開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

構成ファイルに変更を加えた場合は、`iotedge config apply` コマンドを使用して変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge デバイスにランタイムが正常にインストールされ、構成されていることを確認します。

>[!TIP]
>`iotedge` コマンドの実行には、昇格された特権が必要です。 IoT Edge ランタイムのインストール後に初めてマシンにサインインし直すと、アクセス許可は自動的に更新されます。 それまでは、コマンドの前に `sudo` を使用します。

IoT Edge システム サービスが実行されていることを確認します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

正常な状態の応答は `Ok` です。

::: moniker-end

サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

`check` ツールを使用して、デバイスの構成と接続の状態を確認します。

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>アクセス許可が更新された後でも、必ず `sudo` を使用してチェック ツールを実行してください。 このツールには、構成状態を確認するために、config ファイルにアクセスするための昇格された特権が必要です。

IoT Edge デバイス上で実行されているすべてのモジュールを表示します。 初めてサービスが開始されると、**edgeAgent** モジュールが実行されていることだけを確認できます。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。

   ```bash
   sudo iotedge list
   ```

新しい IoT Edge デバイスを作成すると、Azure portal で状態コード `417 -- The device's deployment configuration is not set` が表示されます。 これは正常な状態であり、デバイスにモジュールをデプロイできる状態であることを表します。

## <a name="offline-or-specific-version-installation-optional"></a>オフラインまたは特定のバージョンのインストール (省略可能)

このセクションの手順は、標準のインストール手順では説明されていないシナリオを対象としています。 次のような場合が含まれます。

* オフラインの間に IoT Edge をインストールする
* リリース候補バージョンをインストールする

`apt-get install` を介して使用できない特定のバージョンの Azure IoT Edge ランタイムをインストールする場合は、このセクションの手順を使用します。 Microsoft パッケージのリストには、最近のバージョンとそのサブバージョンの限られたセットしか含まれていないので、これらの手順は、古いバージョンまたはリリース候補バージョンをインストールするユーザーが対象となります。

curl コマンドを使用すると、IoT Edge GitHub リポジトリから直接、コンポーネント ファイルをターゲットにすることができます。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. 各リリースには、IoT Edge セキュリティ デーモンと hsmlib の新しいファイルが必要です。 オフライン デバイスに IoT Edge をインストールする場合は、事前にこれらのファイルをダウンロードします。 それ以外の場合は、以下のコマンドを使用して、これらのコンポーネントを更新します。

   1. IoT Edge デバイスのアーキテクチャに対応する **libiothsm-std** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの hsmlib をインストールします。

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **iotedge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge セキュリティ デーモンをインストールします。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>デバイスで現在 IoT Edge バージョン 1.1 以前が実行されている場合は、このセクションの手順を行う前に、**IoT Edge** と **libiothsm-std** パッケージをアンインストールします。 詳細については、[1.0 または 1.1 から 1.2 への更新](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)に関するセクションを参照してください。

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. 各リリースには、IoT Edge と ID サービスの新しいファイルがあります。 オフライン デバイスに IoT Edge をインストールする場合は、事前にこれらのファイルをダウンロードします。 それ以外の場合は、以下のコマンドを使用して、これらのコンポーネントを更新します。

   1. IoT Edge デバイスのアーキテクチャに一致する **aziot-identity-service** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの ID サービスをインストールします。

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **aziot-edge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge をインストールします。

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、[クラウド ID を使用してデバイスをプロビジョニング](#provision-the-device-with-its-cloud-identity)できます。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

IoT Edge ランタイムを削除します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

構成ファイルを含め、IoT Edge に関連付けられているすべてのファイルを削除する場合は、`--purge` フラグを使用します。 IoT Edge を再インストールし、今後同じ構成情報を使用する場合は、このフラグを使用しないでください。

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示して、どのコンテナーが残っているかを確認します。

```bash
sudo docker ps -a
```

2 つのランタイム コンテナーを含め、デバイスからコンテナーを削除します。

```bash
sudo docker rm -f <container name>
```

最後に、デバイスからコンテナー ランタイムを削除します。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
