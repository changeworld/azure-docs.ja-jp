---
title: VM の自動開始設定を構成する
description: ラボで VM の自動開始設定を構成する方法について説明します。 この設定により、ラボ内の VM はスケジュールに基づいて自動的に開始されます。
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: ea4220af8a3d7ade997b9e64b7164757edfc165a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286718"
---
# <a name="start-up-lab-virtual-machines-automatically"></a>ラボ仮想マシンを自動的に起動する

自動開始を使用すると、毎日スケジュールされた時刻にラボ内の仮想マシン (VM) を自動的に起動できます。 まず、自動開始ポリシーを作成する必要があります。 次に、ポリシーに従う VM を選ぶ必要があります。 自動的に開始する VM をわざわざ明示的に選ぶのは、必要のない VM が誤って開始されてコストが増えるのを防ぐためです。

この記事では、ラボの自動開始ポリシーの構成方法について説明します。 自動シャットダウンの設定の構成については、[Azure DevTest Labs でのラボの自動シャットダウン ポリシーの管理](devtest-lab-auto-shutdown.md)に関する記事をご覧ください。 

## <a name="configure-auto-start-settings-for-a-lab"></a>ラボの自動開始設定を構成する 

このポリシーでは、ラボの VM に自動開始が自動的に適用されることはありません。 ポリシーを構成した後、「[ラボの VM で自動開始を有効にする](#enable-auto start-for-a-vm-in-the-lab)」の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **DevTest Labs** のラボに移動します。

1. **[設定]** で **[構成とポリシー]** を選びます。 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="DevTest ラボの [構成とポリシー] メニューを示すスクリーンショット。":::

1. **[構成とポリシー]** ページの **[スケジュール]** で、 **[自動開始]** を選びます。

1. **[Allow auto-start]\(自動開始を許可する\)** で、 **[はい]** を選びます。 スケジュール情報が表示されます。

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="[スケジュール] の [自動開始] オプションのスクリーンショット。":::
 
1. 次のスケジュール情報を指定します。

    |プロパティ | 説明 |
    |---|---|
    |開始予定| 開始時刻を入力します。|
    |タイム ゾーン| ドロップダウン リストからタイム ゾーンを選びます。|
    |曜日| スケジュールを適用する各曜日の横にあるボックスをオンにします。|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="自動開始のスケジュール設定のスクリーンショット。":::

1. **[保存]** を選択します。 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>ラボの VM で自動開始を有効にする

以下の手順は、前のセクションから続けて行います。 自動開始ポリシーが作成されたので、ポリシーを適用する VM を選びます。

1. **[構成とポリシー]** ページを閉じて、 **[DevTest Labs]** ページに戻ります。

1. **[自分の仮想マシン]** で VM を選びます。

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="[自分の仮想マシン] の一覧からの VM の選択のスクリーンショット。":::

1. **仮想マシン** のページで、 **[操作]** の **[自動開始]** を選びます。 

1. **[自動開始]** ページで **[はい]** を選び、 **[保存]** を選びます。

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="自動開始選択メニューのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [Azure DevTest Labs でラボの自動シャットダウン ポリシーを管理する](devtest-lab-auto-shutdown.md)
