---
title: インストールとプロビジョニングの後に成功の検証とトラブルシューティングを行う
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979262"
---
## <a name="verify-successful-setup"></a>セットアップに成功したことを検証する

IoT Edge サービスの状態を確認します。 実行中として一覧表示されるはずです。  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

サービス ログを調べます。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

IoT Edge ランタイムのインストールが終了した直後は、**Deploy-IoTEdge** と **Initialize-IoTEdge** が実行している間、エラーの一覧が表示される可能性があります。 サービスは構成される前に開始しようとしているので、これらのエラーは予期されるものです。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

[トラブルシューティング ツール](../articles/iot-edge/troubleshoot.md#run-the-check-command)を実行して、最も一般的な構成とネットワークのエラーを確認します。

```powershell
iotedge check
```

最初のモジュールをデバイス上の IoT Edge にデプロイするまで、 **$edgeHub** システム モジュールはデバイスに展開されません。 その結果、自動チェックからは `Edge Hub can bind to ports on host` 接続チェックのエラーが返されます。 このエラーは、モジュールをデバイスに展開した後に発生した場合でなければ、無視できます。

最後に、実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

新規インストール後、実行されているモジュールは **edgeAgent** だけです。

## <a name="tips-and-troubleshooting"></a>ヒントとトラブルシューティング

リソースに制約のあるデバイスでは、[トラブルシューティング ガイド](../articles/iot-edge/troubleshoot.md)に示されているように、*OptimizeForPerformance* 環境変数を *false* に設定することを強くお勧めします。

デバイスで IoT Hub に接続できないときに、ネットワークにプロキシ サーバーがある場合は、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](../articles/iot-edge/how-to-configure-proxy-support.md)」の手順に従います。

# <a name="linux"></a>[Linux](#tab/linux)

Linux デバイスでは、`iotedge` コマンドを実行するために、昇格された特権が必要です。 ランタイムをインストールしたら、マシンからサインアウトした後サインインし直して、自動的にアクセス許可を更新します。 それまでは、`sudo` を使用して、昇格された特権でコマンドを実行します。

# <a name="windows"></a>[Windows](#tab/windows)

Windows コンテナーを実行している Windows デバイスでは、IoT Edge の一部として Moby コンテナー エンジンがインストールされました。 Moby エンジンは、Docker Desktop と並列で実行するように設計されました。 デバイス上のコンテナーを直接操作する場合は、`docker` コマンドを使用できます。 ただし、デバイスに Docker Desktop もインストールされている場合は、明確に Moby エンジンをターゲットにする必要があります。

たとえば、すべての Docker イメージを一覧表示するには、次のコマンドを使用します。

```powershell
docker images
```

すべての Moby イメージを一覧表示するには、Moby エンジンへのポインターを指定して同じコマンドを変更します。

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

エンジン URI は、インストール スクリプトの出力に示されてます。また、config.yaml ファイルのコンテナー ランタイム設定セクションでも確認できます。

![config.yaml の moby_runtime uri](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
