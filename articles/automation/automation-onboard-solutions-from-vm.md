---
title: Azure Virtual Machine から Update Management、Change Tracking、および Inventory ソリューションの使用準備を行う方法について説明します
description: Azure Automation に含まれる Update Management、Change Tracking、および Inventory ソリューションで、Azure Virtual Machine の使用準備を行う方法について説明します
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 8713447386e116b3f1ecb2754d46cc8d88d00b39
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Azure Virtual Machine から Update Management、Change Tracking、および Inventory ソリューションの使用準備を行う

Azure Automation には、オペレーティング システムのセキュリティ更新プログラム、変更の追跡、およびご使用のコンピューターにインストールされている内容のインベントリを管理するソリューションが用意されています。 マシンの使用準備を行う方法は複数あります。仮想マシンや [Automation アカウント](automation-onboard-solutions-from-automation-account.md)から、または [Runbook](automation-onboard-solutions.md) を使用して、ソリューションの使用準備を行うことができます。 この記事では、Azure Virtual Machine からこれらのソリューションの使用準備を行う方法について説明します。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure にサインインします (https://portal.azure.com)。

## <a name="enable-the-solutions"></a>ソリューションの有効化

既存の仮想マシンに移動し、**[操作]** の下の **[更新プログラムの管理]**、**[インベントリ]**、または **[変更追跡]** を選択します。

Log Analytics ワークスペースおよび Automation アカウントを選択し、**[有効化]** をクリックして、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

![Update ソリューションの配布準備](media/automation-onboard-solutions-from-vm/onboard-solution.png)

他のソリューションに移動し、**[有効化]** をクリックします。Log analytics と Automation アカウントのドロップダウン ボックスは、前に有効にしたソリューションと同じワークスペースおよび Automation アカウントを使用するため、選択不可になっています。

![Update ソリューションの配布準備](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> **[変更追跡]** と **[インベントリ]** は、同じソリューションを使用します。一方が有効であれば、もう一方も有効です。

## <a name="scope-configuration"></a>スコープ構成

各ソリューションは、ワークスペース内のスコープ構成を使用して、ソリューションの対象となるコンピューターを決定します。 スコープ構成は、ソリューションのスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件のグループです。 スコープ構成にアクセスするには、**[関連リソース]** の Automation アカウントで、**[ワークスペース]** を選択します。次に、**[ワークスペースのデータ ソース]** のワークスペースで、**[スコープ構成]** を選択します。

既定では、**MicrosoftDefaultScopeConfig-ChangeTracking** と **MicrosoftDefaultScopeConfig-Updates** の 2 つのスコープ構成が作成されます。

いずれかの構成の省略記号 (...) をクリックし、**[編集]** を選択します。 **[スコープ構成の編集]** ページで **[コンピューター グループの選択]** を選択して、**[コンピューター グループ]** ページを開きます。 このページには、スコープ構成の作成に使用された、保存された検索条件が表示されます。

## <a name="saved-searches"></a>保存した検索条件

コンピューターが Update Management ソリューション、または Change Tracking および Inventory ソリューションに追加されると、それらはワークスペースの 2 つの保存された検索条件のいずれかに追加されます。 保存された検索条件は、これらのソリューションの対象となるコンピューターを含むクエリです。

ワークスペースに移動し、**[全般]** の **[保存された検索条件]** を選択します。 次の表は、これらのソリューションで使用される 2 つの保存された検索条件を示しています。

|Name     |カテゴリ  |エイリアス  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

いずれかの保存された検索条件を選択して、グループの設定に使用されるクエリを表示します。 次の図は、クエリとその結果を示しています。

![保存した検索条件](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>次の手順

ソリューションを使用する方法を学習するためのチュートリアルに進みます。

* [チュートリアル - VM の更新プログラムの管理](automation-tutorial-update-management.md)

* [チュートリアル - VM 上のソフトウェアの特定](automation-tutorial-installed-software.md)

* [チュートリアル - VM に対する変更のトラブルシューティング](automation-tutorial-troubleshoot-changes.md)
