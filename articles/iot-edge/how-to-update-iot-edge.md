---
title: デバイス上の IoT Edge バージョンの更新 - Azure IoT Edge | Microsoft Docs
description: セキュリティ デーモンと IoT Edge ランタイムの最新バージョンを実行するように IoT Edge デバイスを更新する方法
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 90a03b86e54c214fb5dd17f11ea01247b7b77e9b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057215"
---
# <a name="update-iot-edge"></a>IoT Edge を更新する

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge サービスの新しいバージョンがリリースされたら、最新の機能およびセキュリティの強化のために、IoT Edge デバイスを更新する必要があります。 この記事では、新しいバージョンが使用可能になったときに、IoT Edge デバイスを更新する方法について説明します。

新しいバージョンに移行する場合、IoT Edge デバイスの 2 つのコンポーネントを更新する必要があります。 1 つ目は、デバイスで実行され、デバイスの起動時にランタイム モジュールを起動するセキュリティ デーモンです。 現在、セキュリティ デーモンは、デバイス自体からのみ更新できます。 2 つ目のコンポーネントはランタイムで、IoT Edge ハブと IoT Edge エージェント モジュールから構成されます。 デプロイの構成方法に応じて、ランタイムはデバイスから、またはリモートで更新できます。

Azure IoT Edge の最新バージョンを見つけるには、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

## <a name="update-the-security-daemon"></a>セキュリティ デーモンの更新

IoT Edge セキュリティ デーモンは、IoT Edge デバイス上のパッケージ マネージャーを使用して更新する必要があるネイティブ コンポーネントです。

デバイスで実行されているセキュリティ デーモンのバージョンを確認するには、コマンド `iotedge version` を使用します。 IoT Edge for Linux on Windows を使用している場合は、Linux 仮想マシンに SSH で接続してバージョンを確認する必要があります。

# <a name="linux"></a>[Linux](#tab/linux)

>[!IMPORTANT]
>デバイスをバージョン 1.0 または 1.1 からバージョン 1.2 に更新する場合、インストールと構成のプロセスに違いがあるため、追加の手順が必要です。 詳細については、この記事で後述する手順「[特殊なケース: 1.0 または 1.1 から 1.2 に更新する](#special-case-update-from-10-or-11-to-12)」を参照してください。

Linux x64 デバイスでは、apt-get または適切なパッケージ マネージャーを使用して、セキュリティ デーモンを最新バージョンに更新します。

Microsoft から最新のリポジトリ構成を取得します。

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

生成された一覧をコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

apt を更新します。

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

使用できる IoT Edge のバージョンを確認します。

   ```bash
   apt list -a iotedge
   ```

最新バージョンのセキュリティ デーモンに更新するには、次のコマンドを使用します。これにより **libiothsm-std** も最新バージョンに更新されます。

   ```bash
   sudo apt-get install iotedge
   ```

特定のバージョンのセキュリティデーモンに更新する場合は、apt リスト出力からバージョンを指定します。 **iotedge** が更新されるたびに、**libiothsm std** パッケージの最新バージョンへの更新が自動的に試行されます。これにより、依存関係の競合が発生する可能性があります。 最新バージョンに移行しない場合は、必ず同じバージョンの両方のパッケージをターゲットにしてください。 たとえば、次のコマンドでは、1.1 リリースの特定のバージョンがインストールされます。

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

インストールするバージョンが apt によって使用できない場合は、curl を使用することで、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases) リポジトリからの任意のバージョンをターゲットにすることができます。 インストールするバージョンに応じて、ご利用のデバイスに適した **libiothsm-std** および **iotedge** ファイルを見つけます。 ファイルごとに、ファイル リンクを右クリックして、リンクのアドレスをコピーします。 リンクのアドレスを使用して、それらのコンポーネントの特定のバージョンをインストールします。

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

使用できる IoT Edge のバージョンを確認します。

   ```bash
   apt list -a aziot-edge
   ```

IoT Edge の最新バージョンに更新する場合は、次のコマンドを使用すると、ID サービスも最新バージョンに更新されます。

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="linux-on-windows"></a>[Linux on Windows](#tab/linuxonwindows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>現在、Linux for Windows 仮想マシン上で実行されている IoT Edge バージョン 1.2 はサポートされていません。
>
>Windows 上の Linux 用の IoT Edgeを更新する手順については、「IoT Edge [1.1 」を参照してください](?view=iotedge-2018-06&preserve-view=true&tabs=linuxonwindows)。

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

>[!IMPORTANT]
>IoT Edge for Linux on Windows のパブリック プレビュー バージョンから一般提供バージョンにデバイスを更新する場合は、Azure IoT Edge をアンインストールしてから再インストールする必要があります。
>
>現在パブリック プレビュー バージョンを使用しているかどうかを確認するには、お使いの Windows デバイスで **[設定]**  >  **[アプリ]** に移動します。 アプリと機能の一覧で **Azure IoT Edge** を見つけます。 表示されているバージョンが 1.0.x の場合は、パブリック プレビュー バージョンが実行されています。 アプリをアンインストールし、もう一度 [IoT Edge for Linux on Windows をインストールしてプロビジョニングします](how-to-provision-single-device-linux-on-windows-symmetric.md)。 表示されているバージョンが 1.1.x の場合は、一般提供バージョンが実行されていて、Microsoft Update を介して更新プログラムを受け取ることができます。

>[!IMPORTANT]
>IoT Edge for Linux on Windows の 1.1.2110.03111 バージョンより前の Windows Server SKU デバイスを最新の利用可能なバージョンに更新する場合は、手動で移行する必要があります。
>
>更新プログラム [1.1.2110.0311](https://github.com/Azure/iotedge-eflow/releases/tag/1.1.2110.03111) では、EFLOW Windows Server のデプロイに使用される VM テクノロジ (HCS から VMMS) に変更が加えられました。 次の手順に従って、VM の移行を実行できます。
> 1. Microsoft Update を使用して、1.1.2110.03111 更新プログラム をダウンロードしてインストールします (他の EFLOW 更新プログラムと同じであり、EFLOW の更新プログラムが有効になっている限り、手動の手順は不要です)。
> 2. EFLOW の更新が完了したら、管理者特権の PowerShell セッションを開きます。
> 3. 移行スクリプトを実行します。
>  ```powershell
>   Migrate-EflowVmFromHcsToVmms
>   ```
>
> 注: Windows Server SKU に新しい EFLOW 1.1.2110.0311 msi をインストールすると、VMMS テクノロジを使用して EFLOW デプロイが行われるため、移行は必要ありません。

IoT Edge for Linux on Windows では、IoT Edge は Windows デバイスでホストされている Linux 仮想マシンで実行されます。 この仮想マシンには IoT Edge がプレインストールされており、IoT Edge コンポーネントを手動で更新または変更することはできません。 代わりに、この仮想マシンは、コンポーネントを自動的に最新の状態に保つために Microsoft Update で管理されます。

Azure IoT Edge for Linux on Windows の最新バージョンを調べるには、[EFLOW リリース](https://aka.ms/AzEFLOW-Releases)に関するページを参照してください。


IoT Edge for Linux on Windows の更新プログラムを受信するには、他の Microsoft 製品の更新プログラムを受信するように Windows ホストを構成する必要があります。 このオプションは、次の手順で有効にすることができます。

1. Windows ホストで **[設定]** を開きます。

1. **[更新とセキュリティ]** を選択します。

1. **[詳細オプション]** を選択します。

1. *[Windows の更新時に他の Microsoft 製品の更新プログラムを受け取る]* ボタンを **オン** に切り替えます。

:::moniker-end
<!-- end 1.1 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>現在、Windows デバイス上で実行されている IoT Edge バージョン 1.2 はサポートされていません。
>
>Windows 上の Linux 用の IoT Edgeを更新する手順については、「IoT Edge [1.1 」を参照してください](?view=iotedge-2018-06&preserve-view=true&tabs=windows)。

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Windows の IoT Edge では、IoT Edge は Windows デバイスで直接実行されます。

`Update-IoTEdge` コマンドを使用して、セキュリティ デーモンを更新します。 スクリプトにより、セキュリティ デーモンの最新バージョンが自動的にプルされます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Update-IoTEdge コマンドを実行すると、デバイスから 2 つのランタイム コンテナー イメージと共にセキュリティ デーモンが削除および更新されます。 config.yaml ファイルは、Moby コンテナー エンジンのデータと同様に、デバイス上にも保存されています。 構成情報の保持は、更新プロセス中に、接続文字列または Device Provisioning Service 情報をデバイスに再び提供する必要がないことを意味します。

特定のバージョンのセキュリティ デーモンに更新する場合は、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から、対象となる 1.1 リリース チャネルからのバージョンを見つけます。 そのバージョンで、**Microsoft-Azure-IoTEdge.cab** ファイルをダウンロードします。 次に、`-OfflineInstallationPath` パラメーターを使用してローカル ファイルの場所を指定します。 次に例を示します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` パラメーターでは、指定されたディレクトリで **Microsoft-Azure-IoTEdge.cab** という名前のファイルを検索します。 ファイルの名前を変更し、アーキテクチャのサフィックスがある場合は削除します。

デバイスをオフラインで更新する場合は、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から、対象のバージョンを見つけます。 そのバージョンで、*IoTEdgeSecurityDaemon.ps1* と *Microsoft-Azure-IoTEdge.cab* の各ファイルをダウンロードします。 各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

オフライン コンポーネントを使用して更新するには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で使用します。 次に、`Update-IoTEdge` コマンドの一部として `-OfflineInstallationPath` パラメーターを使用し、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

更新オプションの詳細については、コマンド `Get-Help Update-IoTEdge -full` を使用するか、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

:::moniker-end
<!-- end 1.1 -->

---

## <a name="update-the-runtime-containers"></a>ランタイム コンテナーの更新

IoT Edge エージェントおよび IoT Edge ハブ コンテナーを更新する方法は、デプロイにローリング タグ (1.1 など) を使用しているか、または特定のタグ (1.1.1 など) を使用しているかによって異なります。

デバイス上の IoT Edge エージェントおよび IoT Edge ハブ モジュールの現在のバージョンを確認するには、コマンド `iotedge logs edgeAgent` または `iotedge logs edgeHub` を使用します。 IoT Edge for Linux on Windows を使用している場合は、Linux 仮想マシンに SSH で接続してランタイム モジュールのバージョンを確認する必要があります。

  ![ログ内でコンテナー バージョンを検索する](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge のタグについて

IoT Edge エージェントおよび IoT Edge ハブ イメージには、関連付けられている IoT Edge のバージョンでタグ付けされます。 ランタイム イメージでタグを使用する方法は 2 つあります。

* **ローリング タグ** - バージョン番号の先頭の 2 つの値のみを使用して、これらの数字に一致する最新のイメージを取得します。 たとえば、最新の 1.1.x バージョンを指す新しいリリースが存在するたびに、1.1 が更新されます。 IoT Edge デバイス上のコンテナー ランタイムによって、再度イメージが取得されると、ランタイム モジュールが最新バージョンに更新されます。 Azure portal からのデプロイでは、既定でローリング タグに設定されます。 *開発目的では、このアプローチが推奨されます。*

* **特定のタグ** - バージョン番号の 3 つすべての値を使用して、イメージのバージョンを明示的に設定します。 たとえば、1.1.0 はその最初のリリース後に変更されることはありません。 更新する準備ができたら、配置マニフェストに新しいバージョン番号を宣言できます。 *運用環境目的では、このアプローチが推奨されます。*

### <a name="update-a-rolling-tag-image"></a>ローリング タグ イメージの更新

デプロイでローリング タグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.1** など) 場合、デバイス上のコンテナー ランタイムに強制的にイメージの最新バージョンを取得させる必要があります。

IoT Edge デバイスからイメージのローカル バージョンを削除します。 Windows マシンでは、セキュリティ デーモンをアンインストールするとランタイム イメージも削除されるため、この手順をもう一度実行する必要はありません。

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

強制の `-f` フラグを使用して、イメージを削除する必要がある場合があります。

IoT Edge サービスによって、ランタイム イメージの最新バージョンが取得され、自動的にデバイス上でそれらが再度起動されます。

### <a name="update-a-specific-tag-image"></a>特定のタグ イメージの更新

デプロイで特定のタグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.1.1** など) 場合、行う必要がある作業は、配置マニフェスト内のタグを更新し、デバイスに変更を適用することだけです。

1. Azure portal の IoT Hub で、IoT Edge デバイスを選択し、 **[Set Modules]\(モジュールの設定\)** を選択します。

1. **[IoT Edge モジュール]** セクションで、 **[Runtime Settings]\(ランタイムの設定\)** を選択します。

   ![ランタイム設定の構成](./media/how-to-update-iot-edge/configure-runtime.png)

1. **[Runtime Settings]\(ランタイムの設定\)** で、 **[Edge ハブ]** の **[イメージ]** の値を適切なバージョンに更新します。 **[保存]** はまだ選択しないでください。

   ![Edge ハブのイメージのバージョンの更新](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. **[Edge ハブ]** の設定を折りたたむか、下にスクロールするかして、 **[Edge エージェント]** の **[イメージ]** の値を同じ適切なバージョンに更新します。

   ![Edge ハブのエージェントのバージョンの更新](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **[保存]** を選択します。

1. **[Review + create]\(確認と作成\)** を選択し、デプロイを確認して、 **[作成]** を選択します。

## <a name="special-case-update-from-10-or-11-to-12"></a>特殊なケース: 1.0 または 1.1 から 1.2 に更新する

>[!NOTE]
>Windows コンテナーまたは IoT Edge for Linux on Windows を使用している場合は、この特殊なケースのセクションは適用されません。

バージョン 1.2 以降、IoT Edge サービスには新しいパッケージ名が使用されるようになりました。また、インストールと構成のプロセスにいくつかの違いがあります。 バージョン 1.0 または 1.1 を実行している IoT Edge デバイスがある場合、1.2 に更新する方法については、以下の手順を参照してください。

>[!NOTE]
>現在、Windows デバイス上で実行されている IoT Edge バージョン 1.2 はサポートされていません。

1\.2 とそれより前のバージョンの主な違いは次のとおりです。

* パッケージ名は **iotedge** から **aziot-edge** に変更されました。
* **libiothsm-std** パッケージは使用されなくなりました。 IoT Edge リリースの一部として提供されている標準パッケージを使用した場合は、構成を新しいバージョンに移行できます。 libiothsm-std の別の実装を使用していた場合は、デバイス ID 証明書、デバイス CA、信頼バンドルなどのユーザー指定の証明書を再構成する必要があります。
* 新しい ID サービス **aziot-identity-service** は、1.2 リリースの一部として導入されました。 このサービスにより、IoT Edge と、[IoT Hub 用デバイスの更新](../iot-hub-device-update/understand-device-update.md)などの IoT Hub と通信する必要があるその他のデバイス コンポーネントの ID のプロビジョニングと管理が処理されます。
* 既定の構成ファイルは、名前と場所が新しくなっています。 以前は `/etc/iotedge/config.yaml` でしたが、デバイス構成情報は既定で `/etc/aziot/config.toml` 内にあることが想定されています。 `iotedge config import` コマンドを使用すると、構成情報を以前の場所と構文から新しいものへと移行できます。
  * Import コマンドでは、デバイスのトラステッド プラットフォーム モジュール (TPM) に対するアクセス規則を検出または変更することはできません。 デバイスで TPM 構成証明を使用している場合は、/etc/udev/rules.d/tpmaccess.rules ファイルを手動で更新して、aziottpm サービスにアクセスできるようにする必要があります。 詳細については、「[IoT Edge に TPM へのアクセス権を付与する](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm)」を参照してください。
* バージョン 1.2 のワークロード API は、暗号化されたシークレットを新しい形式で保存します。 以前のバージョンからバージョン 1.2 にアップグレードすると、既存のマスター暗号化キーがインポートされます。 ワークロード API は、インポートした暗号化キーを使用して、以前の形式で保存されたシークレットを読み取ることができます。 ただし、ワークロード API では、暗号化されたシークレットを以前の形式で書き込むことはできません。 モジュールによって再暗号化されると、シークレットは新しい形式で保存されます。 バージョン 1.2 で暗号化されたシークレットは、バージョン 1.1 の同じモジュールでは読み取ることができません。 暗号化されたデータをホストによってマウントされたフォルダーまたはボリュームに保持する場合は、必要に応じてダウングレードする機能を保持するために、アップグレードする *前に* 必ずデータのバックアップ コピーを作成してください。

更新プロセスを自動化する前に、テスト マシンで機能することを確認してください。

準備ができたら、次の手順に従ってデバイスの IoT Edge を更新します。

1. Microsoft から最新のリポジトリ構成を取得します。

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 生成された一覧をコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. apt を更新します。

   ```bash
   sudo apt-get update
   ```

5. 構成ファイルをそのままにして、以前のバージョンの IoT Edge をアンインストールします。

   ```bash
   sudo apt-get remove iotedge
   ```

6. IoT ID サービスと共に最新バージョンの IoT Edge をインストールします。

   ```bash
   sudo apt-get install aziot-edge
   ```

7. 以前の config.yaml ファイルを新しい形式にインポートし、構成情報を適用します。

   ```bash
   sudo iotedge config import
   ```

これで、デバイス上で実行されている IoT Edge サービスが更新されました。次は、この記事の手順に従って、[ランタイム コンテナーを更新](#update-the-runtime-containers)してください。

## <a name="special-case-update-to-a-release-candidate-version"></a>特殊なケース: リリース候補バージョンに更新する

>[!NOTE]
>Windows コンテナーまたは IoT Edge for Linux on Windows を使用している場合は、この特殊なケースのセクションは適用されません。

Azure IoT Edge では、定期的に新しいバージョンの IoT Edge サービスをリリースしています。 個々の安定版リリースの前に、1 つ以上のリリース候補 (RC) バージョンがあります。 RC バージョンには、そのリリースで予定されているすべての機能が含まれていますが、テストおよび検証プロセスはまだ進行中です。 早い段階で新しい機能をテストする場合は、GitHub を介して RC バージョンをインストールし、フィードバックを提供できます。

リリース候補バージョンは、リリースの同じ番号付け規則に従いますが、 **-rc** と末尾に増分の番号が追加されます。 [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)の一覧には、安定版と同じリリース候補があります。 たとえば、 **1.2.0** の前にリリースされたリリース候補の 1 つである **1.2.0-rc4** を見つけます。 また、RC バージョンには **プレリリース** のラベルが付いていることもわかります。

IoT Edge エージェント モジュールおよびハブ モジュールには、同じ規則でタグ付けされた RC バージョンがあります。 たとえば、**mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4** があります。

プレビューの場合、リリース候補バージョンは、通常のインストーラーがターゲットとする最新版として含まれていません。 代わりに、テストする RC バージョンの資産を手動でターゲットにする必要があります。 ほとんどの場合、RC バージョンのインストールや RC バージョンへの更新は、IoT Edge の他の特定のバージョンをターゲットとする場合と同じです。

IoT Edge デバイスを特定のバージョンのセキュリティ デーモンまたはランタイム モジュールに更新する方法については、この記事の各セクションを参照してください。

既存のインストールをアップグレードせず、IoT Edge をインストールする場合、「[オフラインまたは特定のバージョンのインストール](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional)」を参照してください。

## <a name="next-steps"></a>次のステップ

最新の [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を確認する

[モノのインターネットのブログ](https://azure.microsoft.com/blog/topics/internet-of-things/)の最新の更新とお知らせによって最新情報を得る
