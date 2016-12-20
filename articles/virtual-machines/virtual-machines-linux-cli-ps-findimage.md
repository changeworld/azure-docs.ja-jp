---
title: "Azure CLI を使用した Linux VM イメージの選択 |Microsoft Docs"
description: "リソース マネージャー デプロイ モデルで Linux 仮想マシンを作成するときに、イメージの発行元、プラン、および SKU を決定する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 732767bcece6d892aee7a8e6976f527fc66bb48d


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Azure CLI を使用した Linux VM イメージの選択
このトピックでは、デプロイする可能性のある場所ごとに、発行元、プラン、SKU、およびバージョンを検索する方法について説明します。 たとえば、次のような Linux VM イメージが一般的に使用されます。

**一般的に使用される Linux イメージの表**

| 発行元 | プラン | SKU |
|:--- |:--- |:--- |:--- |
| RedHat |RHEL |7.2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7.1 |
| Canonical |UbuntuServer |14.04.4-LTS |
| CoreOS |CoreOS |安定版 |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


