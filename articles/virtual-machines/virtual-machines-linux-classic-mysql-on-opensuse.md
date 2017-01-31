---
title: "OpenSUSE VM への MySQL のインストール | Microsoft Docs"
description: "Azure 上の OpenSUSE Linux 仮想マシンに MySQL をインストールする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ff9e23e9324cc47ae1a5d7cb52f13920000b8557


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール
[MySQL][MySQL] は広く普及しているオープン ソースの SQL Database です。 このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成してから、MySQL をインストールする方法を説明します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux を実行する仮想マシンの作成
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>仮想マシンへの MySQL のインストールと実行
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>次のステップ
MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Dec16_HO1-->


