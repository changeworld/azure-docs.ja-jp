---
title: Azure VM から Azure Automation の Update Management を有効にする
description: この記事では、Azure VM から Update Management を有効にする方法について説明します。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a1fe48b8bc776faf98f71eb882720e8180a2c573
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186029"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Azure VM から Update Management を有効にする

この記事では、Azure VM を使用して、他のマシンで [Update Management](automation-update-management.md) 機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の VM を有効にする必要があります。 

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](./index.yml)。
* [仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-the-feature-for-deployment"></a>デプロイために機能を有効にする

1. [[Azure portal]](https://portal.azure.com) で **[仮想マシン]** を選択するか、[ホーム] ページから **[仮想マシン]** を検索して選択します。

2. Update Management を有効にする VM を選択します。 VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。 あなたが

3. VM のページで、 **[操作]** の下の **[Update Management]** を選択します。

4. ワークスペースに対して VM が有効かどうかを判断するには、`Microsoft.OperationalInsights/workspaces/read` アクセス許可が必要です。 必要な追加のアクセス許可については、[マシンを有効にするために必要なアクセス許可](automation-role-based-access-control.md#feature-setup-permissions)に関する記事をご覧ください。 一度に複数のマシンを有効にする方法については、「[Automation アカウントから Update Management を有効にする](automation-onboard-solutions-from-automation-account.md)」をご覧ください。

5. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、Update Management を有効にします。 セットアップが完了するまでに最大 15 分かかります。 

    ![Update Management の有効化](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](troubleshoot/update-management.md)に関する記事を参照してください。
* Windows Update エージェントに関する問題のトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題のトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。
