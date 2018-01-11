---
title: "Azure 仮想マシン内の変更を追跡する | Microsoft Docs"
description: "変更の追跡を使用して、仮想マシン上のファイルとレジストリの変更を追跡します。"
services: automation
documentationcenter: automation
author: georgewallace
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: 3a661fada2e768c2206183c125593d019b557c1d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Azure 仮想マシン内の変更を追跡する

変更の追跡を有効にすることで、仮想マシン上のファイルと Windows レジストリ キーの変更を追跡できます。 構成の変更を識別することで、運用上の問題を特定できるようになります。

変更の追跡は、Azure 仮想マシンから直接有効にすることができます。

Azure 仮想マシンがない場合は、[Windows クイック スタート](../virtual-machines/windows/quick-create-portal.md)または [Linux のクイック スタート](../virtual-machines/linux/quick-create-portal.md)の記事の手順に従って作成できます。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。
[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Azure 仮想マシンに対する変更の追跡を有効にする

1. Azure Portal の左ウィンドウで **[仮想マシン]** を選択します。
2. 一覧から仮想マシンを選択します。
3. 仮想マシンのウィンドウで、**[操作]** の **[変更の追跡]** を選択します。 

   ![仮想マシンでの変更の追跡の開始](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    **[更新管理の有効化]** ウィンドウが開きます。

    この仮想マシンで変更の追跡が有効になっているかどうかを確認する検証が行われます。 変更の追跡が有効になっていない場合は、ソリューションを有効にするオプションを示すバナーが表示されます。

   ![変更の追跡の開始構成バナー](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. ソリューションを有効にするには、バナーを選択します。 以下の項目がない場合は、自動的に追加されます。

   * [Log Analytics](../log-analytics/log-analytics-overview.md) ワークスペース
   * [Automation アカウント](../automation/automation-offering-get-started.md)

5. 変更の追跡からのデータ ログを格納する Log Analytics ワークスペースと、変更を追跡するための Automation アカウントを選択し、**[有効化]** を選択します。  
    ステータス バーに、ソリューションが有効になっていることが通知されます。 このプロセスには最大 15 分かかる可能性があります。

## <a name="configure-change-tracking"></a>変更の追跡を構成する

変更の追跡が有効になると、**[変更の追跡]** ウィンドウが表示されます。 

追跡するファイルとレジストリ キーを選択するには、**[設定の編集]** を選択します。

   ![変更の追跡の設定の編集](./media/automation-vm-change-tracking/change-edit-settings.png)

   **[ワークスペースの構成]** ウィンドウが開きます。 

**[ワークスペースの構成]** ウィンドウでは、次の 3 つのセクションで説明するように、追跡する Windows レジストリ キー、Windows ファイル、または Linux ファイルを追加します。

### <a name="add-a-windows-registry-key"></a>Windows レジストリ キーを追加する

1. **[Windows レジストリ]** タブで **[追加]** を選択します。  
    **[変更履歴用の Windows レジストリを追加する]** ウィンドウが開きます。

   ![変更の追跡へのレジストリの追加](./media/automation-vm-change-tracking/change-add-registry.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[Windows レジストリ キー]** ボックスに、追跡するレジストリ キーの名前を入力します。
6. **[保存]** を選択します。

### <a name="add-a-windows-file"></a>Windows ファイルを追加する

1. **[Windows ファイル]** タブで **[追加]** を選択します。  
    **[変更履歴用の Windows ファイルを追加する]** ウィンドウが開きます。

   ![変更の追跡への Windows ファイルの追加](./media/automation-vm-change-tracking/change-add-win-file.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[パスの入力]** ボックスに、追跡するファイルの完全なパスとファイル名を入力します。
6. **[保存]** を選択します。

### <a name="add-a-linux-file"></a>Linux ファイルを追加する

1. **[Linux ファイル]** タブで **[追加]** を選択します。  
    **[変更履歴用の Linux ファイルを追加する]** ウィンドウが開きます。

   ![変更の追跡への Linux ファイルの追加](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[パスの入力]** ボックスに、追跡するファイルの完全なパスとファイル名を入力します。
6. **[パスの種類]** ボックスで、**[ファイル]** または **[ディレクトリ]** を選択します。
7. **[再帰]** で、指定したパスとその下にあるすべてのファイルとパスの変更を追跡するには、**[オン]** を選択します。 選択したパスまたはファイルのみを追跡するには、**[オフ]** を選択します。
8. **[Sudo の使用]** で、`sudo` コマンドを使用してアクセスする必要があるファイルを追跡するには、で、**[オン]** を選択します。 それ以外の場合は、**[オフ]** を選択します。
9. **[保存]** を選択します。

## <a name="view-changes"></a>変更を表示する

**[変更の追跡]** ウィンドウで、仮想マシンで一定期間中に発生したさまざまなカテゴリの変更を確認できます。

   ![変更の追跡での変更の表示](./media/automation-vm-change-tracking/change-view-changes.png)

表示する変更のカテゴリと時間の範囲を選択できます。 ウィンドウ上部には、一定期間の変更がグラフィカル表示されます。 ウィンドウ下部に、最近の変更の一覧が表示されます。

## <a name="view-change-tracking-log-data"></a>変更の追跡のログ データを表示する

変更の追跡は、Log Analytics に送信されるログ データを生成します。 クエリを実行してログを検索するには、**[変更の追跡]** ウィンドウの上部にある **[Log Analytics]** ウィンドウを選択します。

   ![変更の追跡の Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

Log Analytics でのログ ファイルの実行と検索については、[Log Analytics ](../log-analytics/log-analytics-overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

* 変更の追跡の詳細については、「[変更の追跡ソリューションを使用してユーザーの環境内のソフトウェアの変更を追跡する](../log-analytics/log-analytics-change-tracking.md)」を参照してください。
* 仮想マシンの更新管理の詳細については、「[OMS の更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)」を参照してください。
