---
title: Azure Virtual Desktop の自動スケーリング機能の診断を設定する
description: Azure Virtual Desktop デプロイのスケーリング サービスに関する診断レポートを設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b5621d829050c6749795df1191ea53d835e16487
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181539"
---
# <a name="set-up-diagnostics-for-the-autoscale-feature-preview"></a>自動スケーリング機能の診断を設定する (プレビュー)

> [!IMPORTANT]
> 自動スケール機能は、現在プレビューの段階にあります。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

自動スケーリング機能 (プレビュー) の潜在的な問題は、診断を通じて監視し、スケーリング プランに支障をきたす前の時点で修正することができます。

現在、自動スケーリング機能の診断ログは、Azure ストレージ アカウントに送信するか、イベント ハブに取り込むことができます。 Azure ストレージ アカウントをご使用の場合、スケーリング プランと同じリージョンにストレージ アカウントが存在することを確認してください。 診断設定の詳細については、[診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。 リソース ログのデータ インジェスト時間について詳しくは、「[Azure Monitor でのログ データ インジェスト時間](../azure-monitor/logs/data-ingestion-time.md)」を参照してください。

## <a name="enable-diagnostics-for-scaling-plans"></a>スケーリング プランの診断を有効にする

スケーリング プランの診断を有効にするには、次の手順に従います。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

2. **[スケーリング プラン]** を選択し、レポートで追跡したいスケーリング プランを選択します。

3. **[診断設定]** に移動し、**[診断設定を追加する]** を選択します。

4. 診断設定の名前を入力します。

5. 次に、**[自動スケーリング]** を選択し、希望するレポート送信先に応じて、**ストレージ アカウント** または **イベント ハブ** を選択します。

6. **[保存]** を選択します。

## <a name="set-log-location-in-azure-storage"></a>Azure Storage におけるログの場所を設定する

診断設定の構成後は、次の手順に従ってログを見つけることができます。

1. Azure portal で、診断ログの送信先のストレージ グループに移動します。

2. **[コンテナー]** を選択します。 **insight-logs-autoscaling** というフォルダーが開きます。

3. **insight-logs-autoscaling フォルダー** を選択し、確認したいログを開きます。 そのフォルダーのサブフォルダーをたどって目的の JSON ファイルを表示し、そのフォルダーのすべてのアイテムを選択して右クリックし、それらをローカル コンピューターにダウンロードします。

4. 最後に、任意のテキスト エディターで JSON ファイルを開きます。

## <a name="view-diagnostic-logs"></a>診断ログを表示する

JSON ファイルを開いたら、レポートの各要素の意味をざっと確認しましょう。

- **CorrelationID** は、サポート ケースの作成時に示す必要のある ID です。

- **OperationName** は、問題の発生時に実行されていた操作の種類です。

- **ResultType** は操作の結果です。 なんらかの不備が見つかった場合、この要素を見て問題の発生箇所を確認できます。

- **Message** はエラー メッセージで、不完全な操作に関する情報が得られます。 このメッセージには重要なトラブルシューティング ドキュメントへのリンクが含まれていることがあるため、よく確認してください。

次の JSON ファイルは、レポートを開いたときに表示される内容の例です。

```json
{
    "host_Ring": "R0",
    "Level": 4,
    "ActivityId": "c1111111-1111-1111-b111-11111cd1ba1b1",
    "time": "2021-08-31T16:00:46.5246835Z",
    "resourceId": "/SUBSCRIPTIONS/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.DESKTOPVIRTUALIZATION/SCALINGPLANS/TESTPLAN",
    "operationName": "HostPoolLoadBalancerTypeUpdated",
    "category": "Autoscale",
    "resultType": "Succeeded",
    "level": "Informational",
    "correlationId": "35ec619b-b5d8-5b5f-9242-824aa4d2b878",
    "properties": {
        "Message": "Host pool's load balancing algorithm updated",
        "HostPoolArmPath": "/subscriptions/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/resourcegroups/test/providers/microsoft.desktopvirtualization/hostpools/testHostPool ",
        "PreviousLoadBalancerType": "BreadthFirst",
        "NewLoadBalancerType": "DepthFirst"
    }
}. L
```

## <a name="next-steps"></a>次のステップ

- スケーリング プランの作成方法については、[Azure Virtual Desktop セッション ホストの自動スケーリング](autoscale-scaling-plan.md)に関する記事を参照してください。
- [新しいホスト プールまたは既存のホスト プールへのスケーリング プランの割り当て](autoscale-new-existing-host-pool.md)に関するページを参照してください。
