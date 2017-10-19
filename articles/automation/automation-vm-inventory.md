---
title: "インベントリ収集による Azure 仮想マシンの管理 | Microsoft Docs"
description: "インベントリ収集による仮想マシンの管理"
services: automation
keywords: "インベントリ, オートメーション, 変更, 追跡"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>インベントリ収集による Azure 仮想マシンの管理

Azure 仮想マシンに対するインベントリの追跡は、その仮想マシンのリソース ページから有効にすることができます。 この方法では、ブラウザーベースのユーザー インターフェイスで、インベントリ収集を設定および構成できます。

## <a name="before-you-begin"></a>開始する前に
Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
Azure 仮想マシンをお持ちでない場合は、[仮想マシンを作成](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。
[Azure ポータル](https://portal.azure.com/)にサインインします。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>仮想マシン リソース ページからインベントリ収集を有効にする

1. Azure Portal の左ウィンドウで **[仮想マシン]** を選択します。
2. 仮想マシンの一覧で仮想マシンを選択します。
3. **[リソース]** メニューの **[操作]** で **[インベントリ (プレビュー)]** を選択します。  
    ウィンドウの上部に、このソリューションが有効でないことを通知するバナーが表示されます。 
4. ソリューションを有効にするには、バナーを選択します。
5. データ ログを格納する Log Analytics ワークスペースを選択します。  
    そのリージョンで利用可能なワークスペースがない場合は、既定のワークスペースと Automation アカウントを作成するよう求められます。 
6. コンピューターのオンボードを開始するために、**[Enable]\(有効化\)** を選択します。

   ![オンボード オプションの表示](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    ステータス バーで、ソリューションが有効にされていることが通知されます。 このプロセスには最大 15 分かかる可能性があります。 この間、ウィンドウは閉じても開いたままでもかまいません。開いたままにした場合は、ソリューションが有効になると、そのことを伝えるメッセージが表示されます。 デプロイの状態は通知ペインから監視することができます。

   ![オンボードの直後にインベントリ ソリューションを表示する](./media/automation-vm-inventory/inventory-onboarded.png)

デプロイが完了すると、ステータス バーは表示されなくなります。 システムがまだインベントリ データを収集しているため、データが表示されない可能性があります。 データの完全な収集には 24 時間かかることがあります。

## <a name="configure-your-inventory-settings"></a>インベントリ設定を構成する

既定では、ソフトウェア、Windows サービス、Linux デーモンが収集対象として構成されています。 Windows レジストリとファイル インベントリを収集するには、インベントリ収集設定を構成します。

1. **[インベントリ (プレビュー)]** ビューで、ウィンドウ上部にある **[設定の編集]** ボタンをクリックします。
2. 新しい収集設定を追加するには、**[Windows レジストリ]**、**[Windows ファイル]**、**[Linux ファイル]** の各タブを選択して、追加する設定カテゴリに移動します。 
3. ウィンドウ上部の **[追加]** を選択します。
4. 各設定プロパティの詳細と説明については、[インベントリのドキュメント ページ](https://aka.ms/configinventorydocs)を参照してください。

## <a name="disconnect-your-virtual-machine-from-management"></a>仮想マシンを管理から切断する

インベントリ管理から仮想マシンを除外するには:

1. Azure Portal の左側にある **[Log Analytics]** をクリックし、仮想マシンのオンボード時に使用したワークスペースを選択します。
2. **[Log Analytics]** ウィンドウの **[リソース]** メニューの **[ワークスペースのデータ ソース]** カテゴリで、**[仮想マシン]** を選択します。 
3. 切断する仮想マシンを一覧で選択します。 仮想マシンの **[OMS 接続]** 列の **[このワークスペース]** の横に、緑色のチェック マークが表示されます。 
4. 次のページの上部にある **[切断]** を選択します。
5. 確認ウィンドウで **[はい]** を選択します。  
    この操作によって、マシンが管理から切断されます。

## <a name="next-steps"></a>次のステップ

* 仮想マシン上のファイルとレジストリ設定内の変更を管理する方法については、「[変更の追跡ソリューションを使用してユーザーの環境内のソフトウェアの変更を追跡する](../log-analytics/log-analytics-change-tracking.md)」を参照してください。
* 仮想マシン上の Windows とパッケージの更新を管理する方法については、「[OMS の更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)」を参照してください。
