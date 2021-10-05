---
title: RDP を使用して Windows VM に接続する
titleSuffix: Azure Bastion
description: RDP を使用して Windows VM に接続するために Azure Bastion を使用する方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699300"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>Azure Bastion を使用して Windows VM への RDP 接続を作成する

この記事では、Azure 仮想ネットワーク内にある Windows VM への RDP 接続を、Azure portal 経由で直接安全かつシームレスに作成する方法について説明します。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 SSH を使用して Windows VM に接続することもできます。 詳細については、[Windows VM への SSH 接続の作成](bastion-connect-vm-ssh-windows.md)に関する記事を参照してください。

Azure Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。 詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしていることを確認します。

* Bastion ホストが既にインストールされている VNet。

  * VM が置かれている仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 
  * Azure Bastion ホストを設定するには、「[Bastion ホストの作成](tutorial-create-host-portal.md#createhost)」を参照してください。 カスタム ポートの値を構成する予定がある場合は、Bastion の構成時に必ず Standard SKU を選択してください。

* 仮想ネットワーク内の Windows 仮想マシン。

### <a name="required-roles"></a>必要なロール

* 仮想マシンに対する閲覧者ロール。
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
* Azure Bastion リソースに対する閲覧者ロール。

### <a name="ports"></a>Port

Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。

*   受信ポート: RDP (3389) "***または***"
*   受信ポート: カスタム値 (Azure Bastion 経由で VM に接続するときに、このカスタム ポートを指定する必要がある)

> [!NOTE]
> カスタム ポート値を指定する場合、Standard SKU を使用して Azure Bastion を構成する必要があります。 Basic SKU では、カスタム ポートを指定できません。 Standard SKU は現在プレビュー段階です。
>

## <a name="connect"></a><a name="rdp"></a>接続

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
