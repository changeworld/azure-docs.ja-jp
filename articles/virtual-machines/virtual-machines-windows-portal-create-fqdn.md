<properties
   pageTitle="Azure ポータルでの VM の FQDN の作成 | Microsoft Azure"
   description="Azure ポータルで仮想マシンに基づいて、リソース マネージャーの完全修飾ドメイン名 (FQDN) を作成する方法を説明します。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/24/2016"
   ms.author="iainfou"/>

# Azure ポータルでの完全修飾ドメイン名の作成
Resource Manager デプロイメント モデルを使用して [Azure ポータル](https://portal.azure.com)で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。この IP アドレスを使用して、VM にリモートでアクセスします。ポータルの既定では[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) が作成されませんが、VM の作成後に作成できます。この記事では、DNS 名または FQDN を作成する手順を示します。

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

リモート デスクトップ プロトコル (RDP) 用など、この DNS 名を使用して、VM にリモートで接続できるようになります。

## 次のステップ
これで、VM にパブリック IP と DNS 名が設定されたため、IIS、SQL、SharePoint などの一般的なアプリケーション フレームワークやサービスをデプロイできるようになりました。

Azure デプロイメントの構築に関するヒントについては、[Resource Manager の使用](../resource-group-overview.md)に関する記事から確認することもできます。

<!---HONumber=AcomDC_0831_2016-->