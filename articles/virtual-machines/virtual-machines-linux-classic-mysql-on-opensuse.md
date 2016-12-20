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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf51a9835448339b4fb4d35bd0c97097bf767ec1


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール
[MySQL][MySQL] は広く普及しているオープン ソースの SQL Database です。 このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成してから、MySQL をインストールする方法を説明します。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux を実行する仮想マシンの作成
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>仮想マシンへの MySQL のインストールと実行
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>次のステップ
MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Nov16_HO3-->


