<properties
   pageTitle="Linux VM イメージへの移動と選択 | Microsoft Azure"
   description="リソース マネージャー デプロイ モデルで Linux 仮想マシンを作成するときに、イメージの発行元、プラン、および SKU を決定する方法について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="03/14/2016"
   ms.author="rasquill"/>

# CLI または Powershell を使用した Azure での Linux 仮想マシン イメージへの移動と選択

このトピックでは、デプロイする可能性のある場所ごとに、発行元、プラン、SKU、およびバージョンを検索する方法について説明します。たとえば、次のような Linux VM イメージが一般的に使用されます。

**一般的に使用される Linux イメージの表**


| 発行元 | プラン | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->