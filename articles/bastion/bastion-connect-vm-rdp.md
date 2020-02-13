---
title: Azure Bastion を使用して Windows VM に接続する | Microsoft Docs
description: この記事では、Azure Bastion を使用して、Windows を実行している Azure 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990489"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure Bastion を使用して Windows 仮想マシンに接続する

この記事では、Azure Bastion を使用して、Azure 仮想ネットワーク内の Windows VM に安全かつシームレスに RDP 接続する方法について説明します。 VM には Azure portal から直接接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 Azure Bastion の詳細については、[概要](bastion-overview.md)に関する記事をご覧ください。

## <a name="before-you-begin"></a>開始する前に

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](bastion-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。

Bastion では、Windows VM への接続には RDP を使用し、Linux VM への接続には SSH を使用していることを前提としています。 Linux VM への接続については、[VM への接続 (Linux)](bastion-connect-vm-ssh.md) に関する記事をご覧ください。

### <a name="required-roles"></a>必要なロール
接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

RDP 経由で Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。

* 受信ポート:RDP (3389)

## <a name="rdp"></a>RDP を使用して接続する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックします。 RDP 接続を使用する場合、VM は Windows 仮想マシンである必要があります。

   ![VM への接続](./media/bastion-connect-vm-rdp/connect.png)
1. [接続] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、リンクをクリックして Bastion を構成できます。 構成手順については、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![VM への接続](./media/bastion-connect-vm-rdp/bastion.png)
1. [Bastion] タブで、仮想マシンのユーザー名とパスワードを入力し、 **[接続]** をクリックします。 Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   ![VM への接続](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>次のステップ

[Bastion に関する FAQ](bastion-faq.md) を読む
