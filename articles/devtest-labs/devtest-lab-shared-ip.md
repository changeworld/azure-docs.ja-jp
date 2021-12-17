---
title: 共有 IP アドレスについて
description: Azure DevTest Labs で共有 IP アドレスを使用して、ラボの VM へのアクセスに必要なパブリック IP アドレスを最小限に抑える方法について説明します。
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 06aac18fb7016a7eb5bee938a4d9988719f86a2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056954"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs の共有 IP アドレスについて

Azure DevTest Labs の仮想マシン (VM) は、パブリック IP アドレスを共有して、ラボの VM へのアクセスに必要なパブリック IP の数を最小限に抑えることができます。  この記事では、共有 IP のしくみと、共有 IP アドレスを構成する方法について説明します。

## <a name="shared-ip-settings"></a>共有 IP 設定

1 つまたは複数のサブネットを持つことができる仮想ネットワークで DevTest Labs ラボを作成します。 既定のサブネットでは **[共有パブリック IP を有効にする]** が **[はい]** に設定されています。  この構成では、サブネット全体に対して 1 つのパブリック IP アドレスが作成されます。 このサブネット内のすべての VM では、既定で共有 IP を使用します。

仮想ネットワークとサブネットの構成について詳しくは、「[Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md)」をご覧ください。

![[ラボ サブネット] ページの [共有 IP] 設定を示すスクリーンショット。](media/devtest-lab-shared-ip/lab-subnet.png)

既存のラボの場合、このオプションを確認または設定するには、ラボの左側のナビゲーションで **[構成とポリシー]** を選択し、 **[外部リソース]** の下の **[仮想ネットワーク]** を選択します。 一覧から仮想ネットワークを選択すると、そのサブネットの共有 IP 設定が表示されます。

設定を変更するには、リストからサブネットを選択し、 **[共有パブリック IP を有効にする]** を **[はい]** または **[いいえ]** に変更します。

VM を作成するときに、 **[IP アドレス]** の横にある **[詳細設定]** ページでこの設定にアクセスできます。

![新しい VM を作成する場合の [詳細設定] の [共有 IP] 設定を示すスクリーンショット。](media/devtest-lab-shared-ip/new-vm.png)

- **共有:** **[共有]** として作成された VM はすべて、1 つのリソース グループに配置されます。 リソース グループには、リソース グループ内のすべての VM で使用される IP アドレスが割り当てられています。
- **パブリック:** すべてのパブリック VM には、独自の IP アドレスとリソース グループがあります。
- **プライベート:** 作成したすべての VM ではプライベート IP アドレスが使用されます。 リモート デスクトップ プロトコル (RDP) を使用して、インターネットからこれらの VM に接続することはできません。

共有 IP を持つ VM をサブネットに追加すると、DevTest Labs により、VM がロード バランサーに自動的に追加され、VM にパブリック IP アドレスの TCP ポート番号が割り当てられます。 ポート番号は、VM の Secure Shell (SSH) ポートに転送されます。

## <a name="use-a-shared-ip"></a>共有 IP を使用する

- **Windows ユーザー:** VM の **[概要]** ページで **[接続]** ボタンを選択して、構成済み RDP ファイルをダウンロードして VM にアクセスします。

- **Linux ユーザー:** Secure Shell (SSH) から VM に接続するには、IP アドレスまたは完全修飾ドメイン名、コロン、ポート番号の順に指定します。 たとえば、次のスクリーンショットは、`contosolab21000000000000.westus3.cloudapp.azure.com:65013` の SSH 接続アドレスを示しています。

  ![VM の [概要] ページの [RDP および SSH] 接続オプションを示すスクリーンショット。](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>次の手順

- [Azure DevTest Labs でラボのポリシーを定義する](devtest-lab-set-lab-policy.md)
- [Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md)
