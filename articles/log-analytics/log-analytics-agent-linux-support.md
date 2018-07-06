---
title: Azure Log Analytics Linux エージェントのトラブルシューティング | Microsoft Docs
description: Log Analytics Linux エージェントで最も一般的な問題の現象、原因、解決方法を説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 49a53b68fd394772f38b6040b80ec80c93d9c46c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131771"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Log Analytics 用 Linux エージェントに関する問題のトラブルシューティング方法

この記事では、Log Analytics 用 Linux エージェントで発生することがあるエラーのトラブルシューティングのヘルプを提供し、それらのエラーの考えられる解決策を提案します。

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>問題: プロキシ経由で Log Analytics に接続できない

### <a name="probable-causes"></a>考えられる原因
* オンボード中に指定されたプロキシが正しくありません
* Log Analytics と Azure Automation サービスのエンドポイントが、データセンターの許可リストに載っていません 

### <a name="resolutions"></a>解決策
1. オプション `-v` を有効にして以下のコマンドを使用して、OMS エージェント for Linux で Log Analytics サービスに再オンボードします。 そうすると、プロキシ経由で OMS サービスに接続しているエージェントで詳細出力ができるようになります。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 「[プロキシ設定を更新する](log-analytics-agent-manage.md#update-proxy-settings)」セクションを参照して、プロキシ サーバー経由で通信するようにエージェントを正しく構成したことを確認します。    
* 以下の Log Analytics サービス エンドポイントが許可リストに載っていることを再確認します。

    |エージェントのリソース| ポート | 方向 |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | ポート 443| 受信および送信 |  
    |*.oms.opinsights.azure.com | ポート 443| 受信および送信 |  
    |*.blob.core.windows.net | ポート 443| 受信および送信 |  
    |*.azure-automation.net | ポート 443| 受信および送信 | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>問題: オンボードしようとすると 403 エラーが発生する

### <a name="probable-causes"></a>考えられる原因
* Linux サーバーの日付と時刻が正しくありません 
* 使用されているワークスペース ID とワークスペース キーが正しくありません

### <a name="resolution"></a>解決策

1. date コマンドを使用して、Linux サーバーの時刻を確認します。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。 これを修正するには、Linux サーバーの日付やタイムゾーンを更新します。 
2. 最新バージョンの OMS Agent for Linux をインストールしたことを確認します。  最新バージョンでは、時間の差によってオンボードが失敗する場合に通知するようになりました。
3. このトピックの前述のインストール手順に従って、正しいワークスペース ID とワークスペース キーを使用して再オンボードします。

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>問題: オンボードの直後にログ ファイルに 500 および 404 エラーが表示される
これは、Linux データを Log Analytics ワークスペースに最初にアップロードするときに発生する既知の問題です。 送信されているデータやサービス エクスペリエンスには影響しません。

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>問題: Azure Portal にデータが表示されない

### <a name="probable-causes"></a>考えられる原因

- Log Analytics サービスへのオンボードが失敗しました
- Log Analytics サービスへの接続がブロックされています
- OMS Agent for Linux のデータがバックアップされています

### <a name="resolutions"></a>解決策
1. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` というファイルが存在するかどうかを確認し、Log Analytics サービスのオンボードに成功したかどうかを確認します。
2. `omsadmin.sh` コマンドライン命令を使用して再オンボードします。
3. プロキシを使用する場合は、上記のプロキシの解決手順を参照してください。
4. 場合によっては、OMS エージェント for Linux がサービスと通信できないときに、エージェントのデータが最大バッファー サイズ (50 MB) までキューイングされます。 `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]` コマンドを実行して、OMS Agent for Linux を再起動する必要があります。 

    >[!NOTE]
    >この問題は、エージェント バージョン 1.1.0-28 以降で修正されます。

