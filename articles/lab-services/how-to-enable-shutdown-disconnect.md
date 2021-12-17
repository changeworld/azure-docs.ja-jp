---
title: Azure Lab Services のラボで VM の自動シャットダウンを構成する
description: リモート デスクトップ接続が切断されたときの VM の自動シャットダウンを有効または無効にする方法について説明します。
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: ab0a78faff158b939e3c1dc540def67ae149f84c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241898"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>ラボで VM の自動シャットダウンを構成する

この記事では、ラボで VM の自動シャットダウンを構成する方法について説明します。

自動シャットダウンによる複数のコスト制御機能を有効にして、仮想マシンがアクティブに使用されていないときの追加コストを事前に防ぐことができます。 次の 3 つの自動シャットダウンおよび切断機能を組み合わせると、ユーザーが誤って仮想マシンを実行状態のまま放置するケースのほとんどを捕捉できます。
 
* OS によってアイドル状態と見なされた仮想マシンから、ユーザーを自動的に切断する。
* ユーザーが切断したときに、仮想マシンを自動的にシャットダウンする。
* 起動したがユーザーが接続しない仮想マシンを自動的にシャットダウンする。

自動シャットダウン機能について詳しくは、[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションをご覧ください。

ラボ アカウント管理者は、ラボを作成するラボ アカウントにこの設定を構成できます。 詳細については、[ラボ アカウントで VM の自動シャットダウンを構成する](how-to-configure-lab-accounts.md)方法に関するページを参照してください。 ラボ所有者は、ラボの作成時またはラボの作成後に、設定をオーバーライドできます。

> [!IMPORTANT]
> Linux ラボでは、ユーザーが切断したときと、VM が起動したがユーザーが接続していないときにのみ、自動シャットダウン機能をサポートします。  またサポートは、[Linux の特定のディストリビューションとバージョン](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)によっても異なります。  [Data Science Virtual Machine - Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) イメージでは、シャットダウン設定はサポートされていません。

## <a name="configure-for-the-lab-level"></a>ラボ レベル用に構成する

自動シャットダウンの設定は、[Azure Lab Services](https://labs.azure.com/) で構成できます。

* ラボの作成時 (**ラボ ポリシー** から)、または
* ラボの作成後 ( **[設定]** で)

> [!div class="mx-imgBorder"]
> ![ラボの作成時に構成する](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションで、自動シャットダウンの詳細を確認してください。

> [!WARNING]
> VM に対する RDP セッションを切断する前に VM で Linux または Windows オペレーティング システム (OS) をシャットダウンすると、自動シャットダウン機能が正常に機能しなくなります。  
## <a name="next-steps"></a>次のステップ

[ラボのダッシュボード](use-dashboard.md)
