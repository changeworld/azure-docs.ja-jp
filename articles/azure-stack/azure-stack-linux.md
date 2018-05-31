---
title: Azure Stack への Linux イメージの追加
description: Azure Stack へ Linux イメージを追加する方法について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935718"
---
# <a name="add-linux-images-to-azure-stack"></a>Azure Stack への Linux イメージの追加

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack Marketplace に Linux ベースのイメージを追加することによって、Azure Stack に Linux 仮想マシン (VM) をデプロイできます。 最も容易に Linux イメージを Azure Stack に追加する方法は、Marketplace Management からです。 これらのイメージは、Azure Stack との互換性を確保できるよう、あらかじめ準備され、テストされています。

## <a name="marketplace-management"></a>Marketplace Management

Azure Marketplace の Linux のイメージをダウンロードするには、次の記事の手順に従ってください。 Azure Stack で、ユーザーに提供する Linux のイメージを選択します。 

[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)

これらのイメージは頻繁に更新されるので、Marketplace Management をこまめに確認して最新の状態に保つようにしてください。

## <a name="prepare-your-own-image"></a>独自のイメージを準備する

 可能な限り、Azure Stack 用に準備されてテストされている Marketplace Management から使用可能なイメージをダウンロードします。 
 
 Azure Linux エージェント (一般に `WALinuxAgent` または `walinuxagent` と呼ばれます) が必要であり、エージェントのバージョンによっては Azure Stack で動作しないものがあります。 独自のイメージを作成する場合は、バージョン 2.2.18 以降を使う必要があります。 現在、[cloud-init](https://cloud-init.io/) は Azure Stack でサポートされていないことに注意してください。

 次の手順を使って、独自の Linux イメージを準備できます。

   * [CentOS ベースのディストリビューション](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES と openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Marketplace にイメージを追加する
 
[Marketplace へのイメージの追加](azure-stack-add-vm-image.md)に関するページに従ってください。 `OSType` パラメーターが `Linux` に設定されていることを確認してください。

Marketplace にイメージを追加すると、Marketplace アイテムが作成され、ユーザーが Linux 仮想マシンをデプロイできます。
