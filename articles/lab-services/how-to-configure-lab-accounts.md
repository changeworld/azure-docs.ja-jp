---
title: Azure Lab Services での VM の自動シャットダウンを構成する
description: この記事では、ラボ アカウントでの VM の自動シャットダウンを構成する方法について説明します。
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: 40b4f3971d2fd9fc84337bb38d49d04212b3f5ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228527"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>ラボ アカウント用に VM の自動シャットダウンを構成する

自動シャットダウンによる複数のコスト制御機能を有効にして、仮想マシンがアクティブに使用されていないときの追加コストを事前に防ぐことができます。 次の 3 つの自動シャットダウンおよび切断機能を組み合わせると、ユーザーが誤って仮想マシンを実行状態のまま放置するケースのほとんどをキャッチできます。
 
- OS によってアイドル状態と見なされた仮想マシンから、ユーザーを自動的に切断する。
- ユーザーが切断したときに、仮想マシンを自動的にシャットダウンする。
- 起動したがユーザーが接続しない仮想マシンを自動的にシャットダウンする。

自動シャットダウン機能の詳細については、[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションを参照してください。

> [!IMPORTANT]
> Linux ラボでは、ユーザーが切断したときと、VM が起動したがユーザーが接続していないときにのみ、自動シャットダウン機能をサポートします。  またサポートは、[Linux の特定のディストリビューションとバージョン](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)によっても異なります。  [Data Science Virtual Machine - Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) イメージでは、シャットダウン設定はサポートされていません。 

## <a name="enable-automatic-shutdown"></a>自動シャットダウン処理を有効にする

1. [Azure portal](https://portal.azure.com/) で、 **[ラボ アカウント]** ページに移動します。
1. 左側のメニューで **[ラボ設定]** を選択します。
1. シナリオに適した自動シャットダウン設定を選択します。  

    > [!div class="mx-imgBorder"]
    > ![ラボ アカウントの自動シャットダウン設定](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    ここでの設定は、ラボ アカウントで作成されたすべてのラボに適用されます。 ラボ作成者 (教師) は、ラボ レベルでこの設定をオーバーライドできます。 ラボ アカウントでこの設定を変更した場合、変更後に作成されたラボにのみ変更が適用されます。

    設定を無効にするには、このページのチェック ボックスをオフにします。

## <a name="next-steps"></a>次のステップ

ラボ所有者がこの設定をラボ レベルで構成またはオーバーライドする方法については、[ラボの VM の自動シャットダウンの構成](how-to-enable-shutdown-disconnect.md)に関するページを参照してください
