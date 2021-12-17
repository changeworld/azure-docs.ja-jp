---
title: Azure と Logz.io の統合を管理する - Azure パートナー ソリューション
description: Azure と Logz.io の統合を管理する方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 223dba26ab32659b710edde8f5bbbca7a67ba1af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076587"
---
# <a name="manage-the-logzio-integration-in-azure"></a>Azure で Logz.io の統合を管理する

この記事では、Azure と Logz.io の統合に関する設定を管理する方法について説明します。

## <a name="resource-overview"></a>ソースの概要

Logz.io の **[Overview]\(概要\)** 画面には、次の項目を含むリソースの詳細が表示されます。

- **リソース グループ**
- **場所**
- **サブスクリプション**
- **タグ**
- **Logz.io SSO**
- **請求期間**

シングル サインオン (SSO) のオプトインまたはオプトアウトの状況に応じて、**Logz.io SSO** のリンクは異なります。

- SSO にオプトインした場合:

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="シングル サインオンをオプトイン。":::

- SSO からオプトアウトした場合:

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="シングル サインオンをオプトアウト。":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>ログとメトリックのルールを再構成する

構成ルールを変更するには、 **[Logz 構成]** で **[Logs and metrics]\(ログとメトリック\)** を選択します。 詳細については、「[ログを構成する](./create.md#configure-logs)」を参照してください。

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="ログとメトリックの構成。":::

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

診断設定を構成するには、リソースを選択します。 左側のウィンドウで、 **[診断設定]** を選択します。

**[宛先の詳細]** 列で、 **[Send to partner solution]\(パートナー ソリューションに送信する\)** オプションをオンにして、宛先ターゲットとして Logz.io を選択します。 このオプションは、Logz リソースが作成された後でのみ使用できます。

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="診断設定を構成します。":::

## <a name="view-monitored-resources"></a>監視対象のリソースを表示する

Logz.io に (構成されたビジネス ルールを通して) ログを送信しているリソースの一覧を表示するには、 **[Logz 構成]** に移動し、 **[監視対象リソース]** を選択します。

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="監視対象リソースの構成。":::

一覧は、リソースの種類、リソース グループ名、場所、およびリソースがログを送信しているかどうかによってフィルター処理できます。

次に示すのは、 **[監視対象リソース]** 画面に表示される列の説明です。

- **[リソース名]** : Azure リソースの名前。
- **[リソースの種類]** : Azure リソースの種類。
- **[リソース グループ]** : Azure リソースのリソース グループ名が表示されます。
- **[リージョン]** : Azure リソースのリージョンまたは場所が表示されます。
- **[Logs to Logz]\(Logz へのログ\)** : リソースから Logz.io にログが送信されているかどうかが示されます。 リソースからログが送信されていない場合、このフィールドには、Logz.io にログが送信されていない適切な理由が示されます。 次のような理由が考えられます。
  - リソースでログの送信がサポートされていない: Logz.io にログを送信するように構成できるのは、すべてのリソースの種類と[こちらで定義されているログ カテゴリ](../../azure-monitor/essentials/resource-logs-categories.md)の Azure リソース ログだけです。
  - 診断設定が上限の 5 個に達した: 各 Azure リソースに設定できる診断設定の数は、最大 5 個です。 詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。
  - エラー: エラーにより、ログを Logz.io に送信できません。
  - ログが構成されていない: Logz.io にログを送信できるのは、適切なリソース タグが設定されているリソースのみです。 「[ログを構成する](./create.md#configure-logs)」を参照してください。
  - リージョンがサポートされていない: Logz.io へのログの送信が現在サポートされていないリージョンに、Azure リソースが存在しています。

Azure リソースのタグの一覧を編集するには、 **[タグの編集]** を選択します。 新しいタグを追加したり、既存のタグを削除したりすることができます。

## <a name="monitor-vm-using-logzio-agent"></a>Logz.io エージェントを使用して VM を監視する

仮想マシン (VM) に拡張機能として Logz.io エージェントをインストールできます。 **[Logz 構成]** に移動し、 **[Virtual machine agent]\(仮想マシン エージェント\)** を選択します。 この画面には、サブスクリプション内のすべての VM の一覧が表示されます。

VM ごとに、次のデータが表示されます。

- **[リソース名]** : 仮想マシンの名前。
- **[リソースの種類]** : Azure リソースの種類。
- **[リソース グループ]** : Azure リソースのリソース グループ名が表示されます。
- **[リージョン]** : Azure リソースのリージョンまたは場所が表示されます。
- **[エージェントのバージョン]** : Logz.io エージェントのバージョン番号。
- **[Extension status]\(拡張機能の状態\)** : Logz.io エージェントが VM に **インストールされている** か、**インストールされていない** かが示されます。
- **[Logs to Logz]\(Logz へのログ\)** : Logz.io エージェントから Logz.io にログが送信されているかどうかが示されます。

:::image type="content" source="./media/manage/vm-agent.png" alt-text="仮想マシン エージェント。":::

### <a name="install-vm-agent"></a>VM エージェントをインストールする

VM エージェントをインストールするには、次の手順のようにします。

1. Logz.io エージェントをインストールする VM を選択し、 **[エージェントのインストール]** を選択します。

   既定の認証を使用してエージェントをインストールするかどうかを確認するメッセージがポータルに表示されます。

1. **[OK]** を選択して、インストールを開始します。

   エージェントのインストールとプロビジョニングが済むまで、 **[インストール中]** という状態が Azure に表示されます。

   Logz.io エージェントがインストールされると、状態が **[インストール済み]** に変わります。

1. Logz.io エージェントがインストールされたことを確認するには、VM を選択して、 **[拡張機能]** ウィンドウに移動します。

> [!NOTE]
> VM エージェントがインストールされた後で、VM が停止されると、その VM の **[Logs to Logz]\(Logz へのログ\)** 列に **[Not Sending]\(非送信中\)** と表示されるようになります。

### <a name="uninstall-vm-agent"></a>VM エージェントをアンインストールする

**[Virtual machine agent]\(仮想マシン エージェント\)** から、VM 上の Logz.io エージェントをアンインストールできます。 VM を選択し、 **[Uninstall agent]\(エージェントのアンインストール\)** を選択します。

## <a name="create-logzio-sub-account"></a>Logz.io のサブ アカウントを作成する

サブ アカウントを作成できるのは、Logz.io アカウントの管理者のみです。 既定では、Logz.io の作成者が管理者として設定されます。 他のユーザーに管理者アクセス許可を付与するには、Logz.io アカウントにサインインします。

詳細については、Logz.io の Web サイトで [Logz.io のサブ アカウント](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/)に関する記事を参照してください。

**[概要]**  >  **[Add Sub Account]\(サブ アカウントの追加\)** ボタンを使用して、Logz.io のサブ アカウントを作成できます。

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="[概要] から Logz.io のサブ アカウントを作成します。":::

もう 1 つの方法は、 **[Logz 構成]** に移動して、 **[Add sub account]\(サブ アカウントの追加\)** を選択します。 画面の指示に従って、サブ アカウントを設定し、サブ アカウントを使用したログの配布を有効にします。

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="[Logz 構成] から Logz.io のサブ アカウントを作成します。":::

## <a name="delete-logzio-sub-account"></a>Logz.io のサブ アカウントを削除する

1. Logz.io リソースで **[Logz 構成]** を選択して、 **[Logz sub accounts]\(Logz サブ アカウント\)** を選択します。
1. 削除するサブ アカウントを選択します。
1. **[削除]** を選択します。

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="[Logz 構成] から Logz.io のサブ アカウントを削除します。":::

1. 「_yes_」と入力して、Logz.io リソースの削除を確認します。
1. **[削除]** を選択します。

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="Logz.io サブ アカウントの削除を確認します。":::

Logz.io にログが送信されなくなり、Logz.io に関するすべての課金が停止します。

## <a name="delete-logzio-account"></a>Logz.io アカウントを削除する

1. Logz.io リソースで **[概要]** を選択して、 **[削除]** を選択します。
1. Logz.io リソースを削除することを確認します。
1. **[削除]** を選択します。

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="[概要] から Logz.io リソースを削除します。":::

1. 「_yes_」と入力して、Logz.io リソースの削除を確認します。
1. **[削除]** を選択します。

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="Logz.io リソースの削除を確認します。":::

> [!NOTE]
> メイン アカウントの **[削除]** オプションは、メイン アカウントにマップされているすべてのサブ アカウントが既に削除されている場合にのみアクティブになります。 サブ アカウントを削除する方法の詳細については、「[Logz.io のサブ アカウントを削除する](#delete-logzio-sub-account)」参照してください。

リソースが削除されると、Logz.io にログが送信されなくなり、Azure Marketplace での Logz.io に関するすべての課金が停止します。

## <a name="get-support"></a>サポートを受ける

Azure と Logz.io の統合に関してサポートに問い合わせるには、 **[サポートとトラブルシューティング]** で **[新規サポート リクエスト]** を選択します。 このオプションを選択すると、[Logz.io ポータル](https://app.logz.io/)に移動します。 チャット内サポートを使用するか、[help@logz.io](mailto:help@logz.io) にメールを送信します。

## <a name="next-steps"></a>次の手順

- 統合に関する問題を解決するには、[トラブルシューティング](troubleshoot.md)を参照してください。
