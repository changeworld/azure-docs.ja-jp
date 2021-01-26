---
title: アラート ワークフローの高速化
description: アラートとインシデント ワークフローを改善します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2020
ms.locfileid: "97836539"
---
# <a name="accelerate-alert-workflows"></a>アラート ワークフローの高速化

この記事では、Azure Defender for IoT でアラートのコメント、アラート グループ、カスタム アラート ルールを使用してアラート ワークフローを高速化する方法について説明します。  これらのツールは、次の場合に役立ちます。

- ネットワークで検出された大量のアラート イベントを分析して管理します。

- 特定のネットワーク アクティビティを特定して処理します。

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>アラートのコメントを使用してインシデント ワークフローを高速化する

アラートのコメントを使用して、アラート イベントの調査中に個人とチーム間のコミュニケーションを改善します。

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="悪意のあるアクティビティを示すスクリーンショット。":::

アラートのコメントを使用して次の点を改善します。

- **ワークフローのステップ**:アラートの軽減ステップを提供します。

- **ワークフローのフォローアップ**:ステップが実行されたことを通知します。

- **ワークフローのガイダンス**:イベントに関する推奨事項、分析情報、または警告を提供します。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="アラートのコメントを示すスクリーンショット。":::

各アラートには、使用可能なオプションの一覧が表示されます。 ユーザーは 1 つまたは複数のメッセージを選択できます。

アラートのコメントを追加するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[アラートのコメント]** を選択します。

3. **[コメントの追加]** ボックスに、コメントのテキストを入力します。 最大で 50 文字です。 カンマは許可されません。

4. **[追加]** を選択します。

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>アラート グループを使用してインシデント ワークフローを高速化する

アラート グループを使用すると、SOC チームは SIEM ソリューションのアラートを表示してフィルター処理し、エンタープライズ セキュリティ ポリシーとビジネスの優先順位に基づいてこれらのアラートを管理できます。 たとえば、新しい検出に関するアラートは、検出グループに分類されます。 このグループには、新しいデバイス、新しい VLAN、新しいユーザー アカウント、新しい MAC アドレスなどの検出に対処するアラートが含まれます。

アラート グループは、次のパートナー ソリューションの転送ルールを作成するときに適用されます。

  - Syslog サーバー

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="転送ルールの作成のスクリーンショット。":::

関連するアラート グループが、パートナー出力ソリューションに表示されます。 

### <a name="requirements"></a>必要条件

アラート グループは、次のプレフィックスを持つ、サポートされているパートナー ソリューションに表示されます。

  - **cat**: QRadar、ArcSight、Syslog CEF、Syslog LEEF の場合

  - **Alert Group**: Syslog テキスト メッセージの場合

  - **alert_group**: Syslog オブジェクトの場合

これらのフィールドは、パートナー ソリューションで、アラート グループ名を表示するように構成する必要があります。 アラート グループに関連付けられたアラートがない場合、パートナー ソリューションのフィールドには「**NA**」と表示されます。

### <a name="default-alert-groups"></a>既定のアラート グループ

次のアラート グループが自動的に定義されます。
|  |  |  |
|--|--|--|
| 異常な通信動作 | カスタム アラート | リモート アクセス |
| 異常な HTTP 通信動作 | 検出 | 再起動コマンドと停止コマンド |
| 認証 | ファームウェアの変更 | スキャン |
| 未認可の通信動作 | 無効なコマンド | センサー トラフィック |
| 帯域幅の異常 | インターネットへのアクセス | マルウェアの疑い |
| バッファー オーバーフロー | 操作の失敗 | 悪意のあるアクティビティの疑い |
| コマンドの失敗 | 操作上の問題 |  |
| 構成の変更 | プログラミング |  |

アラート グループは事前に定義されています。 アラート グループに関連付けられているアラートの詳細、およびカスタム アラート グループの作成については、[Microsoft サポート](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)にお問い合わせください。

## <a name="customize-alert-rules"></a>アラート ルールをカスタマイズする

個々のセンサーによって検出された情報に基づいて、カスタム アラート ルールを追加できます。 たとえば、ソース IP、宛先 IP、または (プロトコル内の) コマンドに基づいてアラートをトリガーするように、センサーに指示するルールを定義します。 ルールで定義されているトラフィックがセンサーで検出されると、アラートまたはイベントが生成されます。

アラート メッセージでは、ユーザー定義のルールによってアラートがトリガーされたことが示されます。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="カスタマイズされたアラートを示すスクリーンショット。":::

カスタム アラート ルールを作成するには:

1. センサーのサイド メニューから **[カスタムアラート]** を選択します。
1. プラス記号 ( **+** ) を選択して、ルールを作成します。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="ユーザー定義のルールを示すスクリーンショット。":::

1. ルール名を定義します。
1. **[カテゴリ]** ペインで、カテゴリまたはプロトコルを選択します。
1. 特定のソースおよび宛先 IP または MAC アドレスを定義するか、任意のアドレスを選択します。
1. 条件を追加します。 条件とそのプロパティの一覧は、カテゴリごとに一意です。 アラートごとに複数の条件を選択できます。
1. ルールで **アラーム** と **イベント** のどちらをトリガーするか指定します。
1. アラートに重大度レベルを割り当てます。
1. アラートに PCAP ファイルを含めるかどうかを示します。
1. **[保存]** を選択します。

ルールは **[カスタマイズされたアラート ルール]** 一覧に追加されます。ここでは、基本的なルール パラメーター、ルールが最後にトリガーされた時刻などを確認できます。 また、一覧からルールを有効または無効にすることもできます。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="ユーザーが追加したカスタマイズされたルールのスクリーンショット。":::

### <a name="see-also"></a>関連項目

[アラートで提供される情報の表示](how-to-view-information-provided-in-alerts.md)

[アラート イベントの管理](how-to-manage-the-alert-event.md)
