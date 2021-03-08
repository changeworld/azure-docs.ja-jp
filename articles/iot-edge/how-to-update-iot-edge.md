---
title: デバイス上の IoT Edge バージョンの更新 - Azure IoT Edge | Microsoft Docs
description: セキュリティ デーモンと IoT Edge ランタイムの最新バージョンを実行するように IoT Edge デバイスを更新する方法
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0adcbf49ff2128fdbe623121838058c5ed89dce2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378028"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge セキュリティ デーモンおよびランタイムの更新

IoT Edge サービスの新しいバージョンがリリースされたら、最新の機能およびセキュリティの強化のために、IoT Edge デバイスを更新する必要があります。 この記事では、新しいバージョンが使用可能になったときに、IoT Edge デバイスを更新する方法について説明します。

新しいバージョンに移行する場合、IoT Edge デバイスの 2 つのコンポーネントを更新する必要があります。 1 つ目は、デバイスで実行され、デバイスの起動時にランタイム モジュールを起動するセキュリティ デーモンです。 現在、セキュリティ デーモンは、デバイス自体からのみ更新できます。 2 つ目のコンポーネントはランタイムで、IoT Edge ハブと IoT Edge エージェント モジュールから構成されます。 デプロイの構成方法に応じて、ランタイムはデバイスから、またはリモートで更新できます。

Azure IoT Edge の最新バージョンを見つけるには、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

## <a name="update-the-security-daemon"></a>セキュリティ デーモンの更新

IoT Edge セキュリティ デーモンは、IoT Edge デバイス上のパッケージ マネージャーを使用して更新する必要があるネイティブ コンポーネントです。

デバイスで実行されているセキュリティ デーモンのバージョンを確認するには、コマンド `iotedge version` を使用します。

# <a name="linux"></a>[Linux](#tab/linux)

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

使用できる IoT Edge のバージョンを確認します。

   ```bash
   apt list -a iotedge
   ```

最新バージョンのセキュリティ デーモンに更新するには、次のコマンドを使用します。これにより **libiothsm-std** も最新バージョンに更新されます。

   ```bash
   sudo apt-get install iotedge
   ```

特定のバージョンのセキュリティデーモンに更新する場合は、apt リスト出力からバージョンを指定します。 **iotedge** が更新されるたびに、**libiothsm std** パッケージの最新バージョンへの更新が自動的に試行されます。これにより、依存関係の競合が発生する可能性があります。 最新バージョンに移行しない場合は、必ず同じバージョンの両方のパッケージをターゲットにしてください。 たとえば、次のコマンドでは、1.0.9 リリースの特定のバージョンがインストールされます。

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

インストールするバージョンが apt によって使用できない場合は、curl を使用することで、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases) リポジトリからの任意のバージョンをターゲットにすることができます。 インストールするバージョンに応じて、ご利用のデバイスに適した **libiothsm-std** および **iotedge** ファイルを見つけます。 ファイルごとに、ファイル リンクを右クリックして、リンクのアドレスをコピーします。 リンクのアドレスを使用して、それらのコンポーネントの特定のバージョンをインストールします。

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge for Linux on Windows では、IoT Edge は Windows デバイスでホストされている Linux 仮想マシンで実行されます。 この仮想マシンは IoT Edge と共にプレインストールされ、コンポーネントを最新の状態に保つために Microsoft Update で管理されます。 現在、利用できる更新プログラムはありません。

::: moniker-end

Windows の IoT Edge では、IoT Edge は Windows デバイスで直接実行されます。 PowerShell スクリプトを使用した更新手順については、「[Azure IoT Edge for Windows をインストールおよび管理する](how-to-install-iot-edge-windows-on-windows.md)」を参照してください。

---

## <a name="update-the-runtime-containers"></a>ランタイム コンテナーの更新

IoT Edge エージェントおよび IoT Edge ハブ コンテナーを更新する方法は、デプロイにローリング タグ (1.0 など) を使用しているか、または特定のタグ (1.0.7 など) を使用しているかによって異なります。

デバイス上の IoT Edge エージェントおよび IoT Edge ハブ モジュールの現在のバージョンを確認するには、コマンド `iotedge logs edgeAgent` または `iotedge logs edgeHub` を使用します。

  ![ログ内でコンテナー バージョンを検索する](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge のタグについて

IoT Edge エージェントおよび IoT Edge ハブ イメージには、関連付けられている IoT Edge のバージョンでタグ付けされます。 ランタイム イメージでタグを使用する方法は 2 つあります。

* **ローリング タグ** - バージョン番号の先頭の 2 つの値のみを使用して、これらの数字に一致する最新のイメージを取得します。 たとえば、最新の 1.0.x バージョンを指す新しいリリースが存在するたびに、1.0 が更新されます。 IoT Edge デバイス上のコンテナー ランタイムによって、再度イメージが取得されると、ランタイム モジュールが最新バージョンに更新されます。 開発目的では、このアプローチが推奨されます。 Azure portal からのデプロイでは、既定でローリング タグに設定されます。

* **特定のタグ** - バージョン番号の 3 つすべての値を使用して、イメージのバージョンを明示的に設定します。 たとえば、1.0.7 はその最初のリリース後に変更されることはありません。 更新する準備ができたら、配置マニフェストに新しいバージョン番号を宣言できます。 運用環境目的では、このアプローチが推奨されます。

### <a name="update-a-rolling-tag-image"></a>ローリング タグ イメージの更新

デプロイでローリング タグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.0** など) 場合、デバイス上のコンテナー ランタイムに強制的にイメージの最新バージョンを取得させる必要があります。

IoT Edge デバイスからイメージのローカル バージョンを削除します。 Windows マシンでは、セキュリティ デーモンをアンインストールするとランタイム イメージも削除されるため、この手順をもう一度実行する必要はありません。

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

強制の `-f` フラグを使用して、イメージを削除する必要がある場合があります。

IoT Edge サービスによって、ランタイム イメージの最新バージョンが取得され、自動的にデバイス上でそれらが再度起動されます。

### <a name="update-a-specific-tag-image"></a>特定のタグ イメージの更新

デプロイで特定のタグを使用している (mcr.microsoft.com/azureiotedge-hub:**1.0.8** など) 場合、行う必要がある作業は、配置マニフェスト内のタグを更新し、デバイスに変更を適用することだけです。

1. Azure portal の IoT Hub で、IoT Edge デバイスを選択し、 **[Set Modules]\(モジュールの設定\)** を選択します。

1. **[IoT Edge モジュール]** セクションで、 **[Runtime Settings]\(ランタイムの設定\)** を選択します。

   ![ランタイム設定の構成](./media/how-to-update-iot-edge/configure-runtime.png)

1. **[Runtime Settings]\(ランタイムの設定\)** で、 **[Edge ハブ]** の **[イメージ]** の値を適切なバージョンに更新します。 **[保存]** はまだ選択しないでください。

   ![Edge ハブのイメージのバージョンの更新](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. **[Edge ハブ]** の設定を折りたたむか、下にスクロールするかして、 **[Edge エージェント]** の **[イメージ]** の値を同じ適切なバージョンに更新します。

   ![Edge ハブのエージェントのバージョンの更新](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **[保存]** を選択します。

1. **[Review + create]\(確認と作成\)** を選択し、デプロイを確認して、 **[作成]** を選択します。

## <a name="update-to-a-release-candidate-version"></a>リリース候補バージョンに更新する

Azure IoT Edge では、定期的に新しいバージョンの IoT Edge サービスをリリースしています。 個々の安定版リリースの前に、1 つ以上のリリース候補 (RC) バージョンがあります。 RC バージョンには、そのリリースで予定されているすべての機能が含まれていますが、テストおよび検証プロセスはまだ進行中です。 早い段階で新しい機能をテストする場合は、GitHub を介して RC バージョンをインストールし、フィードバックを提供できます。

リリース候補バージョンは、リリースの同じ番号付け規則に従いますが、 **-rc** と末尾に増分の番号が追加されます。 [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)の一覧には、安定版と同じリリース候補があります。 たとえば、**1.0.9** より前に存在するリリース候補の 2 つ (**1.0.9-rc5** と **1.0.9-rc6**) を見つけます。 また、RC バージョンには **プレリリース** のラベルが付いていることもわかります。

IoT Edge エージェント モジュールおよびハブ モジュールには、同じ規則でタグ付けされた RC バージョンがあります。 たとえば、**mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6** があります。

プレビューの場合、リリース候補バージョンは、通常のインストーラーがターゲットとする最新版として含まれていません。 代わりに、テストする RC バージョンの資産を手動でターゲットにする必要があります。 ほとんどの場合、RC バージョンのインストールや RC バージョンへの更新は、IoT Edge の他の特定のバージョンをターゲットとする場合と同じです。

IoT Edge デバイスを特定のバージョンのセキュリティ デーモンまたはランタイム モジュールに更新する方法については、この記事の各セクションを参照してください。

既存のインストールをアップグレードせず、IoT Edge をインストールする場合、「[オフラインまたは特定のバージョンのインストール](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)」を参照してください。

## <a name="next-steps"></a>次のステップ

最新の [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を確認する

[モノのインターネットのブログ](https://azure.microsoft.com/blog/topics/internet-of-things/)の最新の更新とお知らせによって最新情報を得る