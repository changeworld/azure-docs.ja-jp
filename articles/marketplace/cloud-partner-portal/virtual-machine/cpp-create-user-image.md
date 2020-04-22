---
title: Azure Marketplace 向けのユーザー VM イメージを作成する
description: ユーザー VM イメージを作成するために必要な手順と参照情報の一覧を示します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273955"
---
# <a name="create-a-user-vm-image"></a>ユーザー VM イメージの作成

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure 仮想マシン オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure 仮想マシンのテクニカル アセットを作成する](https://aka.ms/AzureVMTechAsset)」の手順に従って、移行されたオファーを管理します。

この記事では、汎用化された VHD からアンマネージド イメージを作成するために必要な 2 つの一般的なステップについて説明します。  各ステップ (イメージのキャプチャ、イメージの汎用化) について説明されている参照情報を提供します。


## <a name="capture-the-vm-image"></a>VM イメージのキャプチャ

VM のキャプチャに関する次の記事から、アクセス方法に対応する手順を使用してください。

-  PowerShell:[Azure VM から非管理対象 VM イメージを作成する方法](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI:[仮想マシンまたは VHD のイメージを作成する方法](../../../virtual-machines/linux/capture-image.md)
-  API:[Virtual Machines - キャプチャ](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>VM イメージの汎用化

以前に汎用化した VHD からユーザー イメージを生成しているため、これも汎用化する必要があります。  ここでも、アクセス メカニズムに対応する記事を次から選択します。  (ディスクをキャプチャしたときに、既に汎用化している可能性があります。)

-  PowerShell:[VM の汎用化](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI:[手順 2:VM イメージを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API:[Virtual Machines - 汎用化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>次のステップ

次に、[証明書を作成](cpp-create-key-vault-cert.md)し、新しい Azure キー コンテナーに格納します。  この証明書は、VM 上でセキュリティ保護された WinRM 接続を確立するために必要です。
