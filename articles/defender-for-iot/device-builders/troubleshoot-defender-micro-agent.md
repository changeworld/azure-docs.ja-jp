---
title: Defender for IoT マイクロ エージェントのトラブルシューティング (プレビュー)
description: 予期しないまたは原因不明のエラーを処理する方法について説明します。
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 23722195f4a7f7a19f10962ce648bc1a6e2a4012
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018749"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender for IoT マイクロ エージェントのトラブルシューティング (プレビュー)

予期しないエラーが発生した場合は、これらのトラブルシューティング方法を使用して問題の解決を試みることができます。 必要に応じて、Azure Defender for IoT 製品チームにもお問い合わせいただけます。   

## <a name="service-status"></a>サービスの状態 

サービスの状態を表示するには、次のようにします。 

1. 次のコマンドを実行します。

    ```bash
    systemctl status defender-iot-micro-agent.service 
    ```

1. サービスが安定していることを確認するには、`active`であること、およびプロセスの稼働時間が適切であることを確認します。

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="アクティブであることと、稼働時間が適切であることを確認して、サービスが安定していることを確認します。":::

サービスが `inactive` として表示されている場合は、次のコマンドを使用してサービスを開始します。

```bash
systemctl start defender-iot-micro-agent.service 
```

プロセスの稼働時間が 2 分未満の場合、サービスがクラッシュしていることがわかります。 この問題を解決するには、[ログを確認する](#review-the-logs)必要があります。

## <a name="validate-micro-agent-root-privileges"></a>マイクロ エージェントの root 権限を検証する

次のコマンドを使用して、Defender IoT マイクロ エージェント サービスが root 権限で実行されていることを確認します。

```bash
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Defender IoT マイクロ エージェント サービスが root 権限で実行されていることを確認します。":::
## <a name="review-the-logs"></a>ログを確認する 

ログを確認するには、次のコマンドを使用します。  

```bash
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>クイック ログの確認

マイクロ エージェントの実行中に問題が発生した場合は、マイクロ エージェントを一時的な状態で実行できます。これにより、次のコマンドを使用してログを表示できます。

```bash
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>サービスを再起動します。

サービスを再起動するには、次のコマンドを使用します。 

```bash
sudo systemctl restart defender-iot-micro-agent 
```

## <a name="next-steps"></a>次のステップ

「[機能のサポートと廃止](edge-security-module-deprecation.md)」を確認してください。
