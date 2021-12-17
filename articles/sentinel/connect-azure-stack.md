---
title: Azure Stack Hub 仮想マシンを Microsoft Sentinel にオンボードする | Microsoft Docs
description: この記事では、Azure Monitor, Update, and Configuration Management 仮想マシン拡張機能を Azure Stack Hub 仮想マシン上にプロビジョニングし、Microsoft Sentinel を使用してそれらの監視を開始する方法を示します。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 570f297cfdd16aaac18f36d11d989c1dfa732e6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518909"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-microsoft-sentinel"></a>Azure Stack Hub 仮想マシンを Microsoft Sentinel に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel では、Azure 上で稼動する VM と Azure Stack Hub で稼動する VM を 1 か所で監視できます。 Azure Stack マシンを Microsoft Sentinel にオンボードするにはまず、既存の Azure Stack Hub 仮想マシンに仮想マシン拡張機能を追加する必要があります。 

Azure Stack Hub マシンを接続したら、データに基づく分析情報を可視化したダッシュボードをギャラリーから選択します。 これらのダッシュボードは、お客様のニーズに合わせて簡単にカスタマイズすることができます。

## <a name="add-the-virtual-machine-extension"></a>仮想マシン拡張機能を追加する 

Azure Stack Hub で動作している仮想マシンに **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加します。 

1. 新しいブラウザー タブで、[Azure Stack Hub ポータル](/azure-stack/user/azure-stack-use-portal#access-the-portal)にログインします。

1. **[仮想マシン]** ページに移動し、Microsoft Sentinel を使用して保護したい仮想マシンを選択します。 Azure Stack Hub で仮想マシンを作成する方法については、「[Azure Stack Hub ポータルを使用して Windows サーバー VM を作成する](/azure-stack/user/azure-stack-quick-windows-portal)」または「[Azure Stack Hub ポータルを使用して Linux サーバー VM を作成する](/azure-stack/user/azure-stack-quick-linux-portal)」を参照してください。

1. **[拡張機能]** を選択します。 この仮想マシンにインストールされている仮想マシン拡張機能の一覧が表示されます。

1. **[追加]** タブを選択します。 **[新しいリソース]** メニュー ブレードが開かれ、使用可能な仮想マシン拡張機能の一覧が表示されます。 

1. **Azure Monitor, Update, and Configuration Management** 拡張機能を選択し、 **[作成]** を選択します。 **[拡張機能のインストール]** 構成ウィンドウが開きます。

   ![Azure Monitor, Update, and Configuration Management の設定](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > **Azure Monitor, Update and Configuration Management** 拡張機能が Marketplace に一覧表示されていない場合は、担当の Azure Stack Hub オペレーターに連絡して使用可能にしてください。

1. Microsoft Sentinel メニューで、 **[ワークスペースの設定]** 、 **[Advanced]\(詳細\)** の順に選択し、 **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** をコピーします。 

1. Azure Stack Hub の **[拡張機能のインストール]** ウィンドウで、指定されたフィールドにそれらを貼り付け、 **[OK]** を選択します。

1. 拡張機能のインストールが完了すると、その状態が **[プロビジョニング成功]** と表示されます。 Microsoft Sentinel ポータルに仮想マシンが表示されるまでに、最大で 1 時間かかる場合があります。

Windows 用エージェントのインストールと構成の詳細については、[Windows コンピューターの接続](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)に関するページを参照してください。

エージェントの問題に対する Linux のトラブルシューティングについては、[Azure Log Analytics Linux エージェントのトラブルシューティング](../azure-monitor/agents/agent-linux-troubleshoot.md)に関するページを参照してください。

Azure の Microsoft Sentinel ポータルの **[Virtual Machines]** に、すべての VM とコンピューターの概要がその状態と共に表示されます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった拡張機能は、Azure Stack Hub ポータルを使用して仮想マシンから削除できます。

拡張機能を削除するには:

1. **Azure Stack Hub ポータル** を開きます。

1. **[仮想マシン]** ページに移動し、拡張機能を削除したい仮想マシンを選択します。

1. **[拡張機能]** を選択し、拡張機能 **Microsoft.EnterpriseCloud.Monitoring** を選択します。

1. **[アンインストール]** を選択し、選択内容を確認します。

## <a name="next-steps"></a>次のステップ

Microsoft Sentinel の詳細については、次の記事を参照してください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [一般的なイベント形式のアプライアンス](connect-common-event-format.md)から Microsoft Sentinel へのデータのストリーム配信。
