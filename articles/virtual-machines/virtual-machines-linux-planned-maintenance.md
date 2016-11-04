---
title: Linux VM に対する計画的なメンテナンス | Microsoft Docs
description: Azure の計画的なメンテナンスの概要と、それが Azure で実行されている Linux 仮想マシンに及ぼす影響について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: drewm
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: drewm

---
# Azure での Linux 仮想マシンに対する計画的なメンテナンス
Azure の計画的なメンテナンスの概要と、それが Linux 仮想マシンの可用性に及ぼす影響について説明します。この記事は、[Windows 仮想マシン](virtual-machines-windows-planned-maintenance.md)にも利用できます。

この記事では、Azure の計画的なメンテナンス プロセスに関する基本情報を提供します。VM が再起動した原因についてトラブルシューティングを行う場合は、[VM の再起動ログの表示について詳述しているこのブログ記事をご覧ください](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Azure が計画的なメンテナンスを実行する理由
Microsoft Azure は、世界各地で定期的に更新を行い、仮想マシンの基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。これらの更新の多くは、仮想マシンや Cloud Services に影響を及ぼさずに実行されます (たとえば、メモリ保護更新など)。

ただし、一部の更新では、インフラストラクチャに必須の更新を適用するときに仮想マシンの再起動が必要になります。その場合、インフラストラクチャにパッチを適用する間、仮想マシンはシャットダウンされ、その後、仮想マシンの再起動が行われます。

仮想マシンの可用性に影響を及ぼす可能性があるメンテナンスには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。このページでは、Microsoft Azure による計画的なメンテナンスの実行について説明します。計画外のメンテナンスの詳細については、「[Understand planned versus unplanned maintenance (計画的なメンテナンスと計画外のメンテナンスの概要)](virtual-machines-linux-manage-availability.md)」を参照してください。

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0525_2016-->