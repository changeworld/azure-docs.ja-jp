<properties
   pageTitle="AAD の職場または学校の ID を作成する |Microsoft Azure"
   description="Linux 仮想マシンで使用する Azure Active Directory の職場または学校の ID を作成する方法について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Linux VM と共に使用する Azure Active Directory での職場または学校の ID を作成する

MSDN Azure クレジットを活用するために、個人の Azure アカウントを作成した場合や、個人の MSDN サブスクリプションがあり、Azure アカウントを作成した場合、*Microsoft アカウント* ID を使用して作成しています。Azure の優れた機能の中でも[リソース グループ テンプレート](../resource-group-overview.md)はその一例ですが、作業にあたって職場または学校のアカウント (Azure Active Directory で管理される ID) が必要です。下の指示に従って、新しい職場または学校のアカウントを作成できます。個人の Azure アカウントを持つことで得られるメリットの 1 つは、既定の Azure Active Directory ドメインが付属することです。それを使用して職場または学校のアカウントを新規に作成し、アカウントが必要な Azure 機能で利用できます。

ただし、最近の変更によって、[ここ](../xplat-cli-connect.md)で説明されている `azure login` 対話型ログイン方法を使用するすべての種類の Azure アカウントでサブスクリプションを管理できるようになっています。そのメカニズムを使用することも、次の指示に従うこともできます。[Windows VM と共に使用する Azure Active Directory での職場または学校の ID を作成する](virtual-machines-windows-create-aad-work-id.md)こともできます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<!---HONumber=AcomDC_0330_2016-->