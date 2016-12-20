---
title: "Azure ポータルでの VM の FQDN の作成 | Microsoft Docs"
description: "Azure ポータルで仮想マシンに基づいて、リソース マネージャーの完全修飾ドメイン名 (FQDN) を作成する方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 9bd1032c8a831ab22bbebad8881a0f6ea434e360


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Azure ポータルでの完全修飾ドメイン名の作成
Resource Manager デプロイメント モデルを使用して [Azure ポータル](https://portal.azure.com) で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルの既定では [完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN) が作成されませんが、VM の作成後に追加できます。 この記事では、DNS 名または FQDN を作成する手順を示します。

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

`ssh ops@mydns.westus.cloudapp.azure.com`など、この DNS 名を使用して、VM にリモートで接続できるようになります。

## <a name="next-steps"></a>次のステップ
これで、VM がパブリック IP と DNS 名を持つようになったため、nginx、MongoDB、Docker などの、共通のアプリケーション フレームワークやサービスをデプロイできるようになりました。

Azure デプロイの構築に関するヒントについては、[Resource Manager の使用](../azure-resource-manager/resource-group-overview.md)に関する記事から確認することもできます。




<!--HONumber=Nov16_HO3-->


