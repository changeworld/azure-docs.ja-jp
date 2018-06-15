---
title: Azure IoT Edge のトラブルシューティング | Microsoft Docs
description: Azure IoT Edge での一般的な問題を解決し、トラブルシューティング スキルについて説明します
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad22b0cd1457c1d4146a75047ff18e916c0c7ccd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633538"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge での一般的な問題と解決

お使いの環境で Azure IoT Edge の実行中に問題が発生した場合は、この記事を参考にしてトラブルシューティングと解決を行ってください。 

## <a name="standard-diagnostic-steps"></a>標準的な診断手順 

問題が発生したときは、コンテナーのログと、デバイスとの間でやり取りされたメッセージを調べて、お使いの IoT Edge デバイスの状態を詳しく把握します。 情報を収集するには、このセクションのコマンドとツールを使います。 

* Docker コンテナーのログを調べて、問題を検出します。 最初に展開済みのコンテナーを調べた後、IoT Edge ランタイムを構成するコンテナー (Edge エージェントと Edge ハブ) を調べます。 通常、Edge エージェントのログでは、各コンテナーのライフサイクルについての情報が提供されます。 Edge ハブのログでは、メッセージングとルーティングについての情報が提供されます。 

   ```cmd
   docker logs <container name>
   ```

* Edge ハブを通過したメッセージを確認し、ランタイム コンテナーからの詳細なログでデバイスのプロパティの更新についての洞察を収集します。

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* iotedgectl コマンドからの詳細ログを確認します。

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* 接続の問題が発生している場合は、デバイスの接続文字列など、Edge デバイスの環境変数を調べます。

   ```cmd
   docker exec edgeAgent printenv
   ```

IoT Hub デバイスと IoT Edge デバイスの間で送信されたメッセージを確認することもできます。 Visual Studio Code 用の [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 拡張機能を使ってメッセージを表示します。 詳しくは、「[Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)」(Azure IoT で開発するときの便利なツール) をご覧ください。

ログとメッセージの情報を調べた後は、Azure IoT Edge ランタイムの再起動を試みることもできます。

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>約 1 分後に Edge エージェントが停止する

Edge エージェントが起動し、約 1 分間正常に実行した後、停止します。 ログでは、Edge エージェントが AMQP 経由で IoT Hub に接続を試み、約 30 秒後に WebSocket 経由の AMQP を使って接続しようとしたことが示されています。 それが失敗すると、Edge エージェントは終了します。 

Edge エージェント ログの例:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>根本原因
ホスト ネットワークでのネットワーク構成のために、Edge エージェントはネットワークに到達できません。 エージェントは、最初に AMQP (ポート 5671) で接続を試みます。 それが失敗した場合は、WebSocket (ポート 443) を試します。

IoT Edge ランタイムは、各モジュールが通信するネットワークをセットアップします。 Linux では、このネットワークはブリッジ ネットワークです。 Windows では、NAT を使います。 この問題は、NAT ネットワークを使う Windows コンテナーを使っている Windows デバイスで、より多く見られます。 

### <a name="resolution"></a>解決策
このブリッジ/NAT ネットワークに割り当てられている IP アドレスにインターネットへのルートが存在することを確認します。 ホストでの VPN 構成が IoT Edge ネットワークをオーバーライドしている場合があります。 

## <a name="edge-hub-fails-to-start"></a>Edge ハブの起動が失敗する

Edge ハブの起動が失敗し、次のメッセージがログに出力されます。 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>根本原因
ホスト コンピューター上の他のプロセスが、ポート 443 にバインドしています。 Edge ハブは、ゲートウェイ シナリオで使うためにポート 5671 と 443 をマップします。 別のプロセスがこのポートを既にバインドしている場合、このポート マッピングは失敗します。 

### <a name="resolution"></a>解決策
ポート 443 を使っているプロセスを探して停止します。 通常、このプロセスは Web サーバーです。

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge エージェントがモジュールのイメージにアクセスできない (403)
コンテナーの実行が失敗し、Edge エージェントのログで 403 エラーが表示されます。 

### <a name="root-cause"></a>根本原因
Edge エージェントに、モジュールのイメージにアクセスするためのアクセス許可がありません。 

### <a name="resolution"></a>解決策
`iotedgectl login` コマンドをもう一度実行してみます。

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl で Docker が検出されない

コマンド `iotedgectl setup` または `iotedgectl start` が失敗して、ログに次のメッセージが出力されます。
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>根本原因
前提条件である Docker を iotedgectl が検出できません。

### <a name="resolution"></a>解決策
Docker をインストールし、実行されていることを確認してから再試行してください。

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>無効な hostname のために iotedgectl セットアップが失敗する

コマンド `iotedgectl setup` が失敗して、次のメッセージが出力されます。 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>根本原因
IoT Edge ランタイムは、64 文字未満のホスト名のみをサポートできます。 これは通常、物理マシンの場合は問題になりませんが、仮想マシン上にランタイムを設定するときに発生する場合があります。 特に、Azure でホストされる Windows 仮想マシンのために自動生成されるホスト名は長くなる傾向があります。 

### <a name="resolution"></a>解決策
このエラーが発生したときは、仮想マシンの DNS 名を構成し、setup コマンドでその DNS 名をホスト名として設定することで、エラーを解決できます。

1. Azure Portal で、目的の仮想マシンの概要ページに移動します。 
2. DNS 名の下の **[構成]** を選択します。 仮想マシンに既に構成済みの DNS 名がある場合は、新しいものを構成する必要はありません。 

   ![DNS 名を構成する](./media/troubleshoot/configure-dns.png)

3. **[DNS 名ラベル]** に値を指定し、**[保存]** を選択します。
4. 新しい DNS 名をコピーします。名前は **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** の形式である必要があります。
5. 仮想マシン内で、次のコマンドを使用して、実際の DNS 名によって IoT Edge ランタイムを設定します。

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>次の手順
IoT Edge プラットフォームのバグを発見したと思われる場合は、 改善を続けられるように[問題を報告](https://github.com/Azure/iot-edge/issues)してください。 
