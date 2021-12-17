---
title: Windows Admin Center からハイブリッド マシンを Azure に接続する
description: この記事では、Windows Admin Center から Azure Arc 対応サーバーを使用して、エージェントをインストールし、マシンを Azure に接続する方法について説明します。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: cc5e47c1f52f0a35048cc796015184d2a0d2dda7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788954"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Windows Admin Center からハイブリッド マシンを Azure に接続する

手動で一連の手順を実行することで、環境内の 1 つ以上の Windows マシンに対して Azure Arc 対応サーバーを有効にすることができます。 または、[Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) を使用すると、このツール以外の手順を実行することなく、Connected Machine エージェントをデプロイし、オンプレミスのサーバーを登録できます。

## <a name="prerequisites"></a>前提条件

* Azure Arc 対応サーバー - [前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプション、Azure アカウント、リソースが要件を満たしていることを確認します。

* Windows Admin Center - [Azure の統合をデプロイおよび構成](/windows-server/manage/windows-admin-center/azure/azure-integration)するように[環境を準備する](/windows-server/manage/windows-admin-center/deploy/prepare-environment)ための要件を確認します。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* 管理対象の Windows サーバーには、Azure にアクセスするためのインターネット接続が必要です。

### <a name="security"></a>セキュリティ

このデプロイ方法では、エージェントをインストールして構成するために、対象の Windows マシンまたはサーバーに対する管理者権限が必要です。 また、[**ゲートウェイ ユーザー**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) ロールのメンバーであることも必要です。

## <a name="deploy"></a>デプロイ

Azure Arc 対応サーバーを使用して Windows サーバーを構成するには、次の手順を実行します。

1. Windows Admin Center にサインインします。

1. **[概要]** ページの接続の一覧で、接続済み Windows サーバーの一覧からサーバーを選択して接続します。

1. 左側のペインで、 **[Azure hybrid services]\(Azure ハイブリッド サービス\)** を選択します。

1. **[Azure hybrid services]\(Azure ハイブリッド サービス\)** ページで、 **[Discover Azure services]\(Azure サービスの検出\)** を選択します。

1. **[Discover Azure services]\(Azure サービスの検出\)** ページの **[Leverage Azure policies and solutions to manage your servers with Azure Arc]\(Azure ポリシーとソリューションを利用して Azure Arc でサーバーを管理する\)** で、 **[Set up]\(設定\)** を選択します。

1. **[Settings\Azure Arc for servers]\(設定\サーバー向け Azure Arc\)** ページで、プロンプトが表示されたら Azure に対する認証を行って、 **[Get started]\(開始\)** を選択します。

1. **[Connect server to Azure]\(Azure にサーバーを接続する\)** ページに、以下を指定します。

    1. **[Azure サブスクリプション]** ドロップダウン リストで Azure サブスクリプションを選択します。
    1. **[Resource group]\(リソース グループ\)** で、 **[New]\(新規\)** を選択して新しいリソース グループを作成するか、 **[Resource group]\(リソース グループ\)** ドロップダウン リストで、マシンを登録して管理する既存のリソース グループを選択します。
    1. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。
    1. マシンまたはサーバーがインターネットに接続するためにプロキシ サーバー経由で通信している場合は、オプション **[Use proxy server]\(プロキシ サーバーを使用する\)** を選択します。 この構成を利用して、エージェントは HTTP プロトコルを使用してプロキシ サーバー経由で通信します。 プロキシ サーバーの IP アドレスか、マシンがプロキシ サーバーとの通信に使用する名前およびポート番号を指定します。

1. **[Set up]\(設定\)** を選択して、Azure Arc 対応サーバーを使用した Windows サーバーの構成を進めます。

Windows サーバーは Azure に接続し、Connected Machine エージェントをダウンロードしてインストールし、Azure Arc 対応サーバーに登録します。 進行状況を追跡するには、メニューの **[通知]** を選択します。

Connected Machine エージェントのインストールを確認するには、Windows Admin Center で、左側のペインから [ **[イベント]**](/windows-server/manage/windows-admin-center/use/manage-servers#events) を選択し、アプリケーション イベント ログで *MsiInstaller* イベントを確認します。

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 対象のマシンが [Azure portal](https://portal.azure.com) に表示されます。

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="マシンの接続に成功" border="false":::

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM 分析情報](../../azure-monitor/vm/vminsights-enable-policy.md)を使用した監視の有効化などの方法です。
