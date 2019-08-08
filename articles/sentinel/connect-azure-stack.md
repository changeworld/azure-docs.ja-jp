---
title: Azure Stack 仮想マシンを Azure Sentinel にオンボードする | Microsoft Docs
description: この記事では、Azure Monitor, Update, and Configuration Management 仮想マシン拡張機能を Azure Stack 仮想マシンにプロビジョニングし、Sentinel での監視を開始する方法を示します。
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600502"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure Stack 仮想マシンを Azure Sentinel に接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


Azure Sentinel では、Azure で稼動する VM と Azure Stack で稼動する VM を 1 か所で監視できます。 Azure Stack マシンを Azure Sentinel にオンボードするにはまず、既存の Azure Stack 仮想マシンに仮想マシン拡張機能を追加する必要があります。 

Azure Stack マシンを接続したら、データに基づく分析情報を可視化したダッシュボードをギャラリーから選択します。 これらのダッシュボードは、お客様のニーズに合わせて簡単にカスタマイズすることができます。



## <a name="add-the-virtual-machine-extension"></a>仮想マシン拡張機能を追加する 

Azure Stack で動作している仮想マシンに **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加します。 

1. 新しいブラウザー タブで、[Azure Stack ポータル](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)にログインします。
2. **[仮想マシン]** ページに移動し、Azure Sentinel で保護したい仮想マシンを選択します。 Azure Stack で仮想マシンを作成する方法については、「[Azure Stack ポータルを使用して Windows サーバー VM を作成する](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)」または「[Azure Stack ポータルを使用して Linux サーバー VM を作成する](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)」を参照してください。
3. **[拡張機能]** を選択します。 この仮想マシンにインストールされている仮想マシン拡張機能の一覧が表示されます。
4. **[Add (追加)]** タブをクリックします。 **[新しいリソース]** メニュー ブレードが開かれ、使用可能な仮想マシン拡張機能の一覧が表示されます。 
5. **Azure Monitor, Update, and Configuration Management** 拡張機能を選択し、 **[作成]** をクリックします。 **[拡張機能のインストール]** 構成ウィンドウが開きます。

   ![Azure Monitor, Update, and Configuration Management の設定](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > **Azure Monitor, Update and Configuration Management** 拡張機能が Marketplace に一覧表示されていない場合は、該当の Azure Stack オペレーターに連絡して使用可能にしてください。

6. Azure Sentinel メニューで、 **[ワークスペースの設定]** 、 **[Advanced]\(詳細\)** の順に選択し、 **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** をコピーします。 
1. Azure Stack の **[拡張機能のインストール]** ウィンドウで、指定されたフィールドにそれらを貼り付け、 **[OK]** をクリックします。
1. 拡張機能のインストールが完了すると、その状態が **[プロビジョニング成功]** と表示されます。 Azure Sentinel ポータルに仮想マシンが表示されるまでに、最大で 1 時間かかる場合があります。

Windows 用エージェントのインストールと構成の詳細については、[Windows コンピューターの接続](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)に関するページを参照してください。

エージェントの問題に対する Linux のトラブルシューティングについては、[Azure Log Analytics Linux エージェントのトラブルシューティング](../azure-monitor/platform/agent-linux-troubleshoot.md)に関するページを参照してください。

Azure の Azure Sentinel ポータルの **[Virtual Machines]** に、すべての VM とコンピューターの概要がその状態と共に表示されます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、拡張機能は Azure Stack ポータルを使用して仮想マシンから削除できます。

拡張機能を削除するには:

1. **Azure Stack ポータル**を開きます。
2. **[仮想マシン]** ページに移動し、拡張機能を削除したい仮想マシンを選択します。
3. **[拡張機能]** を選択し、拡張機能 **Microsoft.EnterpriseCloud.Monitoring** を選択します。
4. **[アンインストール]** をクリックし、選択内容を確認します。

## <a name="next-steps"></a>次の手順

Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
- [一般的なエラー形式のアプライアンス](connect-common-event-format.md)から Azure Sentinel にデータをストリーミングする。
