---
title: Datadog リソースを管理する - Azure パートナー ソリューション
description: この記事では、Azure portal での Datadog リソースの管理について説明します。 シングル サインオンをセットアップし、Confluent 組織を削除し、サポートを受ける方法。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046353"
---
# <a name="manage-the-datadog-resource"></a>Datadog リソースを管理する

この記事では、Azure と Datadog の統合に関する設定を管理する方法について説明します。

## <a name="resource-overview"></a>ソースの概要

Datadog リソースの詳細を見るには、左側のペインの **[概要]** を選択してください。

:::image type="content" source="media/manage/resource-overview.png" alt-text="Datadog リソースの概要" border="true" lightbox="media/manage/resource-overview.png":::

詳細には次のものが含まれます。

- リソース グループ名
- 場所またはリージョン
- サブスクリプション
- Tags
- Datadog 組織へのシングル サインオン リンク
- Datadog のオファーまたはプラン
- 請求期間

また、Datadog のダッシュボード、ログ、ホスト マップへのリンクも用意されています。

概要画面には、Datadog にログとメトリックを送信しているリソースの概要が表示されます。

- リソースの種類 – Azure リソースの種類。
- リソースの合計 – リソースの種類に該当するすべてのリソースの数。
- ログを送信しているリソース – 統合を介して Datadog にログを送信しているリソースの数。
- メトリックを送信しているリソース – 統合を介して Datadog にメトリックを送信しているリソースの数。

## <a name="reconfigure-rules-for-metrics-and-logs"></a>メトリックとログのルールを再構成する

メトリックとログの構成ルールを変更するには、左側のペインで **[Metrics and Logs]\(メトリックとログ\)** を選択します。

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Datadog リソースのログとメトリックの構成を変更します。" border="true":::

詳細については、「[メトリックとログを構成する](create.md#configure-metrics-and-logs)」を参照してください。

## <a name="view-monitored-resources"></a>監視対象のリソースを表示する

Datadog にログを送信しているリソースの一覧を表示するには、左側のペインで **[Monitored Resources]\(監視対象リソース\)** を選択します。

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Datadog によって監視されているリソースを表示します" border="true":::

リソースの種類、リソース グループ名、場所、およびリソースがログとメトリックを送信しているかどうかによって、リソースの一覧をフィルター処理できます。

**[logs To Datadog]\(Datadog へのログ\)** 列には、リソースから Datadog にログが送信されているかどうかが示されます。 リソースからログが送信されていない場合、このフィールドには、Datadog にログが送信されていない理由が示されます。 次のような理由が考えられます。

- リソースでログの送信がサポートされていません。 Datadog にログを送信するように構成できるのは、監視ログ カテゴリを持つリソースの種類のみです。
- 診断設定が上限の 5 個に達しました。 各 Azure リソースに設定できる診断設定の数は、最大 5 個です。 詳細については、[診断の設定](../../azure-monitor/essentials/diagnostic-settings.md)に関する記事を参照してください。
- エラー。 リソースは Datadog にログを送信するように構成されていますが、エラーによってブロックされています。
- ログが構成されていません。 Datadog にログを送信するように構成されるのは、適切なリソース タグが設定されている Azure リソースのみです。
- サポートされていないリージョン。 Azure リソースは、Datadog へのログの送信が現在サポートされていないリージョンに存在します。
- Datadog エージェントが構成されていません。 Datadog エージェントがインストールされていない仮想マシンからは、Datadog へのログは送信されません。

## <a name="api-keys"></a>API キー

Datadog リソースの API キーの一覧を表示するには、左側のペインで **[キー]** を選択します。 キーに関する情報が表示されます。

:::image type="content" source="media/manage/keys.png" alt-text="Datadog 組織の API キー。" border="true":::

Azure portal には、API キーの読み取り専用ビューが用意されています。 キーを管理するには、Datadog ポータルのリンクを選択します。 Datadog ポータルで変更を行った後、Azure portal のビューを更新します。

Azure と Datadog の統合により、仮想マシンまたは App Service に Datadog エージェントをインストールできるようになります。 既定のキーが選択されていない場合、Datadog エージェントのインストールは失敗します。

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Datadog エージェントを使用して仮想マシンを監視する

仮想マシンに拡張機能として Datadog エージェントをインストールできます。 左側のペインで、[Datadog org configurations]\(Datadog 組織の構成\) の下にある **[Virtual machine agent]\(仮想マシン エージェント\)** に移動します。 この画面には、サブスクリプション内のすべての仮想マシンの一覧が表示されます。

仮想マシンごとに、次のデータが表示されます。

- [Resource Name]\(リソース名\) — 仮想マシンの名前
- [Resource Status]\(リソースの状態\) – 仮想マシンが停止されているか、実行中か。 Datadog エージェントは、実行中の仮想マシンにのみインストールできます。 仮想マシンが停止されている場合、Datadog エージェントのインストールは無効になります。
- [Agent version]\(エージェントのバージョン\) – Datadog エージェントのバージョン番号。
- [Agent status]\(エージェントの状態\) – Datadog エージェントが仮想マシンで実行されているかどうか。
- [Integrations enabled]\(有効になっている統合\) – Datadog エージェントによって収集されている主要なメトリック。
- [Install Method]\(インストール方法\) – Datadog エージェントのインストールに使用された特定のツール。 たとえば、Chef や Script などです。
- [Sending logs]\(ログの送信\) – Datadog エージェントが Datadog にログを送信しているかどうか。

Datadog エージェントをインストールする仮想マシンを選択します。 **[エージェントのインストール]** を選択します。

既定のキーを使用してエージェントをインストールするかどうかを確認するメッセージがポータルに表示されます。 **[OK]** を選択して、インストールを開始します。 エージェントのインストールとプロビジョニングが済むまで、 **[インストール中]** という状態が Azure に表示されます。

Datadog エージェントがインストールされると、状態は [インストール済み] に変わります。

Datadog エージェントがインストールされたことを確認するには、仮想マシンを選択して、[拡張機能] ウィンドウに移動します。

**[Virtual machine agent]\(仮想マシン エージェント\)** に移動することにより、仮想マシン上の Datadog エージェントをアンインストールできます。 仮想マシンを選択し、 **[Uninstall agent]\(エージェントをアンインストール\)** を選択します。

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>拡張機能として Datadog エージェントを使用して App Services を監視する

App Services に拡張機能とし Datadog エージェントをインストールできます。 左側のペインで **[App Service extension]\(App Service 拡張機能\)** に移動します。 この画面には、サブスクリプション内のすべての App Services の一覧が表示されます。

App Service ごとに、次のデータ要素が表示されます。

- [Resource Name]\(リソース名\) — 仮想マシンの名前。
- [Resource Status]\(リソースの状態\) – App Service が停止されているか、実行中か。 Datadog エージェントは、実行中の App Service にのみインストールできます。 App Service が停止されている場合、Datadog エージェントのインストールは無効になります。
- [App service plan]\(App Service プラン\) – App Service 用に構成された特定のプラン。
- [Agent version]\(エージェントのバージョン\) – Datadog エージェントのバージョン番号。

Datadog エージェントをインストールするには、App Service を選択して **[拡張機能のインストール]** を選択します。 最新の Datadog エージェントが、拡張機能として App Service にインストールされます。

Datadog エージェントをインストールすることがポータルによって確認されます。 また、特定の App Service のアプリケーション設定が既定のキーで更新されます。 Datadog エージェントのインストールが完了すると、App Service が再起動されます。

**[OK]** を選択して、Datadog エージェントのインストール プロセスを開始します。 エージェントのインストールが済むまで、ポータルには **[インストール中]** という状態が表示されます。 Datadog エージェントがインストールされると、状態は [インストール済み] に変わります。

App Service の Datadog エージェントをアンインストールするには、 **[App Service extension]\(App Service 拡張機能\)** に移動します。 App Service を選択して、 **[拡張機能のアンインストール]** を選択します

## <a name="reconfigure-single-sign-on"></a>シングル サインオンを再構成する

シングル サインオンを再構成する場合は、左側のペインで **[シングル サインオン]** を選択します。

Azure Active Directory でシングル サインオンを確立するには、 **[Azure Active Directory を使用したシングル サインオンを有効にする]** をオンにします。

ポータルによって、Azure Active Directory から適切な Datadog アプリケーションが取得されます。 アプリは、統合のセットアップ時に選択したエンタープライズ アプリ名から取得されます。 次に示すように、Datadog アプリ名を選択します。
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="シングル サインオン アプリケーションを再構成します。" border="true":::
 
## <a name="disable-or-enable-integration"></a>統合を無効または有効にする

Azure から Datadog へのログとメトリックの送信を停止できます。 メトリックとログの監視に関係のない他の Datadog サービスについては、引き続き課金されます。

Azure と Datadog の統合を無効にするには、 **[概要]** に移動します。 **[無効]** を選択して、 **[OK]** を選択します。
 
:::image type="content" source="media/manage/disable.png" alt-text="Datadog リソースを無効にします。" border="true":::

Azure と Datadog の統合を有効にするには、 **[概要]** に移動します。 **[有効]** を選択して、 **[OK]** を選択します。 **[有効]** を選択すると、メトリックとログに関する以前の構成がすべて取得されます。 構成により、Datadog にメトリックとログを送信する Azure リソースが決定されます。 手順を完了すると、メトリックとログが Datadog に送信されます。
 
:::image type="content" source="media/manage/enable.png" alt-text="Datadog リソースを有効にします。" border="true":::

## <a name="delete-datadog-resource"></a>Datadog リソースを削除する

左側のペインで **[概要]** に移動し、 **[削除]** を選択します。 Datadog リソースを削除することを確認します。 **[削除]** を選択します。

:::image type="content" source="media/manage/delete.png" alt-text="Datadog リソースを削除します" border="true":::

Datadog 組織にマップされている Datadog リソースが 1 つだけの場合、ログとメトリックは Datadog に送信されなくなります。 Azure Marketplace を通じた Datadog への課金は、すべて停止します。

複数の Datadog リソースが Datadog 組織にマップされている場合、Datadog リソースを削除することによって停止されるのは、その Datadog リソースについてのログとメトリックの送信だけです。 Datadog 組織は他の Azure リソースにリンクされているため、Azure Marketplace を通じた課金は継続されます。

## <a name="getting-support"></a>サポートの利用

Azure と Datadog の統合に関してサポートに問い合わせるには、左側のペインで **[新しいサポート リクエスト]** を選択します。 Datadog ポータルへのリンクを選択します。

:::image type="content" source="media/manage/support-request.png" alt-text="新しい Datadog サポート リクエストを作成します" border="true":::

## <a name="next-steps"></a>次のステップ

トラブルシューティングに関しては、[Datadog ソリューションのトラブルシューティング](troubleshoot.md)に関する記事を参照してください。