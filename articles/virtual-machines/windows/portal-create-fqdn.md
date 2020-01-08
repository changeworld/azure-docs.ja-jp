---
title: Azure Portal で Windows VM の FQDN を作成する
description: Azure Portal で仮想マシンに基づいて、Resource Manager の完全修飾ドメイン名 (FQDN) を作成する方法を説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa4e4bd7561a7a745e6eb0b70016144b9fb57998
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371277"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Windows VM 用の Azure Portal での完全修飾ドメイン名の作成

[Azure ポータル](https://portal.azure.com)で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルでは[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) は作成されませんが、VM の作成後に作成できます。 この記事では、DNS 名または FQDN を作成する手順を示します。

## <a name="create-a-fqdn"></a>FQDN の作成
この記事では、既に VM が作成されていることを前提としています。 必要であれば、[ポータルで VM を作成する](quick-create-portal.md)か、[ Azure PowerShell を使用して VM を作成する](quick-create-powershell.md)ことができます。 VM が起動したら、次の手順を実行します。

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

リモート デスクトップ プロトコル (RDP) 用など、この DNS 名を使用して、VM にリモートで接続できるようになります。

## <a name="next-steps"></a>次のステップ
これで、VM にパブリック IP と DNS 名が設定されたため、IIS、SQL、SharePoint などの一般的なアプリケーション フレームワークやサービスをデプロイできるようになりました。

Azure デプロイの構築に関するヒントについては、[Resource Manager の使用](../../azure-resource-manager/management/overview.md)に関する記事から確認することもできます。

