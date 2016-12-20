---
title: "CLI を使用したクラシック Linux VM の作成 | Microsoft Docs"
description: "クラシック デプロイ モデルを使用して Azure CLI で Linux 仮想マシンを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: cfb86b803e2c74dfff567b0a9920735ddb9e69f4


---
# <a name="how-to-create-a-linux-vm-with-the-azure-cli"></a>Azure CLI を使用して Linux VM を作成する方法
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager バージョンについては、[こちら](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

このトピックでは、クラシック デプロイ モデルを使用して Azure CLI で Linux 仮想マシン (VM) を作成する方法について説明します。 ここでは、Azure 上の使用可能な **イメージ** にある Linux イメージを使用します。 Azure CLI コマンドでは、次のような構成を選択できます。

* 仮想ネットワークと VM との接続
* 既存のクラウド サービスへの VM の追加
* 既存のストレージ アカウントへの VM の追加
* 可用性セットまたは場所への VM の追加

> [!IMPORTANT]
> VM が仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、VM の作成時に必ず仮想ネットワークを指定する必要があります。 VM が仮想ネットワークに接続するように設定できるのは、VM の作成時に限られています。 仮想ネットワークの詳細については、「 [Virtual Network の概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して Linux VM を作成する方法
[!INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]




<!--HONumber=Nov16_HO3-->


