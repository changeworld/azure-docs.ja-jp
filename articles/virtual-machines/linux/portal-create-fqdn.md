---
title: Azure portal で VM の FQDN を作成する
description: Azure portal で Linux 仮想マシン用に完全修飾ドメイン名 (FQDN) を作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331473"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM 用の Azure Portal での完全修飾ドメイン名の作成

[Azure ポータル](https://portal.azure.com)で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルでは[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) は作成されませんが、VM の作成後に追加できます。 この記事では、DNS 名または FQDN を作成する手順を示します。

## <a name="create-a-fqdn"></a>FQDN の作成
この記事では、既に VM が作成されていることを前提としています。 必要であれば、[ポータルで VM を作成する](quick-create-portal.md)か、[Azure CLI を使用して VM を作成する](quick-create-cli.md)ことができます。 VM が起動したら、次の手順を実行します。

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

`ssh azureuser@mydns.westus.cloudapp.azure.com`など、この DNS 名を使用して、VM にリモートで接続できるようになります。

## <a name="next-steps"></a>次のステップ
これで、VM がパブリック IP と DNS 名を持つようになったため、nginx、MongoDB、Docker などの、共通のアプリケーション フレームワークやサービスをデプロイできるようになりました。

Azure デプロイの構築に関するヒントについては、[Resource Manager の使用](../../azure-resource-manager/management/overview.md)に関する記事から確認することもできます。

