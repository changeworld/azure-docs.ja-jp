---
title: RDP を使用して Linux VM に接続する
titleSuffix: Azure Bastion
description: RDP を使用して Linux VM に接続するために Azure Bastion を使用する方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8704ade5def1938e789ad4e092cca804cb9af62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033515"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure"></a>Azure を使用して Linux VM への RDP 接続を作成する 

この記事では、Azure portal から直接、Azure 仮想ネットワークに置かれている Linux VM への RDP 接続を安全かつシームレスに作成する方法について説明します。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 SSH を使用して Linux VM に接続することもできます。 詳細については、[Linux VM への SSH 接続の作成](bastion-connect-vm-ssh-linux.md)に関する記事を参照してください。

Azure Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。 詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

> [!NOTE]
> RDP を使用して Linux 仮想マシンに接続するには、Azure Bastion Standard SKU が必要です。
>

Azure Bastion と RDP を使用して Linux 仮想マシンに接続するときは、認証にユーザー名とパスワードを使用する必要があります。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしていることを確認します。

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](tutorial-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。

また、Linux 仮想マシンに RDP で接続するには、ご使用の Linux 仮想マシンに xrdp をインストールし、構成を済ませておく必要があります。 これを行う方法については、[Linux での xrdp の使用](../virtual-machines/linux/use-remote-desktop.md)に関する記事を参照してください。

### <a name="required-roles"></a>必要なロール

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

RDP 経由で Linux VM に接続するには、お使いの VM で次のポートを開いておく必要があります。

* 受信ポート: RDP (3389) "*または*"
* 受信ポート: カスタム値 (Azure Bastion 経由で VM に接続するときに、このカスタム ポートを指定する必要がある)

### <a name="supported-configurations"></a>サポートされている構成

現時点では、Azure Bastion は、**xrdp** を使用した RDP 経由の Linux VM への接続のみをサポートしています。

## <a name="connect"></a><a name="rdp"></a>接続

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
