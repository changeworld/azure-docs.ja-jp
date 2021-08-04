---
title: Azure との Elastic 統合を管理する - Azure パートナー ソリューション
description: この記事では、Azure portal での Elastic の管理について説明します。 診断設定を構成し、リソースを削除する方法。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9feb4c5eb143ced9079bfe9e63b451aecd437212
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952586"
---
# <a name="manage-the-elastic-integration-with-azure"></a>Azure との Elastic 統合を管理する

この記事では、Elastic と Azure の統合を管理する方法について説明します。 診断設定を構成し、Elastic リソースを削除する方法について説明します。

## <a name="reconfigure-rules-for-metrics-and-logs"></a>メトリックとログのルールを再構成する

Elastic リソースを作成したときに、Elastic に送信するログを構成しました。 これらの設定を変更する必要がある場合は、左側のウィンドウで **[メトリックとログ]** を選択します。 ログの Elastic への送信方法に必要な変更を加えます。

2 種類のログの詳細については、「[クイックスタート: Elastic の使用を開始する](create.md)」を参照してください。

:::image type="content" source="media/manage/reconfigure-logs.png" alt-text="ログ設定を変更する":::

## <a name="view-monitored-resources"></a>監視対象のリソースを表示する

Elastic にログを送信するリソースの一覧を表示するには、左側のウィンドウで **[監視対象リソース]** を選択します。

:::image type="content" source="media/manage/monitored-resources.png" alt-text="監視対象のリソースを表示する":::

一覧は、リソースの種類、リソース グループ名、場所、およびリソースがログを送信しているかどうかによってフィルター処理できます。

**[Logs to Elastic]\(Elastic にログ\)** 列には、リソースから Elastic にログが送信されているかどうかが示されます。 リソースからログが送信されていない場合、このフィールドでログが送信されていない理由を指定します。 次のような理由が考えられます。

* リソースでログの送信がサポートされていません。 Elastic にログを送信するように構成できるのは、ここで定義されているすべてのリソースの種類とログ カテゴリの Azure リソース ログだけです。
* 診断設定が上限の 5 個に達しました。 各 Azure リソースに設定できる[診断設定](../../azure-monitor/essentials/diagnostic-settings.md)の数は、最大 5 個です。
* エラーにより、ログを Elastic に送信できません。
* リソースに対してログが構成されていません。 適切なリソース タグを持つリソースのみが Elastic に送信されます。 ログ構成でタグ規則を指定しました。 
* リージョンがサポートされていません。 Azure リソースは、Elastic へのログの送信が現在サポートされていないリージョンに存在しています。 

## <a name="monitor-virtual-machines-using-elastic-agent"></a>Elastic エージェントを使用して仮想マシンを監視する

仮想マシンに拡張機能として Elastic エージェントをインストールできます。 サブスクリプションで使用可能な仮想マシンを表示するには、Elastic リソースの左側のウィンドウから **[仮想マシン]** を選択します。

:::image type="content" source="media/manage/vm-agents.png" alt-text="仮想マシンを表示する":::

仮想マシンごとに、次のデータが表示されます。

* [Resource Name]\(リソース名\) — 仮想マシンの名前。
* [Resource Status]\(リソースの状態\) – 仮想マシンが停止されているか、実行中か。 Elastic エージェントは、実行中の仮想マシンにのみインストールできます。 仮想マシンが停止されている場合、Elastic エージェントのインストールは無効になります。
* [Agent version]\(エージェントのバージョン\) – Elastic エージェントのバージョン番号。
* [Agent status]\(エージェントの状態\) – Elastic エージェントが仮想マシンで実行されているかどうか。
* [Integrations enabled]\(有効になっている統合\) – Elastic エージェントによって収集されている主要なメトリック。
* [Sending logs]\(ログの送信\) – Elastic エージェントが Elastic にログを送信しているかどうか。

Elastic エージェントをインストールするには、仮想マシンを選択し、 **[拡張機能のインストール]** を選択します。

既定の認証を使用してエージェントをインストールするかどうかを確認するメッセージがポータルに表示されます。 **[OK]** を選択して、インストールを開始します。 エージェントのインストールとプロビジョニングが済むまで、 **[インストール中]** という状態がポータルに表示されます。

Elastic エージェントがインストールされると、状態は **[インストール済み]** に変わります。

Elastic エージェントがインストールされたことを確認するには、仮想マシンを選択して、 **[拡張機能]** に移動します。

仮想マシンで Elastic エージェントをアンインストールするには、仮想マシン、 **[拡張機能のアンインストール]** の順に選択します。

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

リソースの診断設定を構成するには、そのリソースを選択します。 左側のウィンドウで、 **[診断設定]** を選択します。 

宛先の詳細セクションで、パートナー ソリューションに送信するオプションをオンにし、宛先ターゲットとして Elastic を選択します。 このオプションは、Elastic リソースが作成された後にのみ使用できます。

:::image type="content" source="media/manage/diagnostic-settings.png" alt-text="診断設定を構成する":::

## <a name="delete-elastic-resource"></a>Elastic リソースを削除する

Elastic リソースが不要になった場合は、Azure portal でそのリソースを削除します。

Azure でリソースを削除するには、該当する Elastic リソースを選択します。 **[概要]** で **[削除]** を選択します。 Elastic リソースを削除することを確認します。

:::image type="content" source="media/manage/delete-elastic.png" alt-text="Elastic リソースを削除する":::

Elastic リソースが削除されると、ログは Elastic に送信されなくなります。 Azure Marketplace を通じた Elastic への課金は、すべて停止します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングの方法については、「[Azure との Elastic の統合のトラブルシューティング](troubleshoot.md)」を参照してください。