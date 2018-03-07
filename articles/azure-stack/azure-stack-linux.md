---
title: "Azure Stack への Linux イメージの追加"
description: "Azure Stack へ Linux イメージを追加する方法について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Azure Stack への Linux イメージの追加

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack Marketplace に Linux ベースのイメージを追加することによって、Azure Stack に Linux 仮想マシン (VM) をデプロイできます。 最も容易に Linux イメージを Azure Stack に追加する方法は、Marketplace Management からです。 これらのイメージは、Azure Stack との互換性を確保できるよう、あらかじめ準備され、テストされています。

## <a name="marketplace-management"></a>Marketplace Management

Azure Marketplace の Linux のイメージをダウンロードするには、次の記事の手順に従ってください。 Azure Stack で、ユーザーに提供する Linux のイメージを選択します。

[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)

## <a name="prepare-your-own-image"></a>独自のイメージを準備する

次のいずれかの手順を使用すると、独自の Linux イメージを準備できます。

   * [CentOS ベースのディストリビューション](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES と openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. [Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/)をダウンロードしてインストールします。

    Azure Stack に Linux VM をプロビジョニングするためには、Azure Linux エージェント バージョン 2.2.2 以上が必要です。なお、一部のバージョン (2.2.12 や 2.2.13 など) は動作しません。 前に説明したディストリビューションの多くには、エージェントのバージョンが既に含まれています (通常 `WALinuxAgent` または `walinuxagent` と呼ばれる)。 カスタム イメージを作成する場合は、エージェントを手動でインストールする必要があります。 インストールされているバージョンは、パッケージの名前から、または VM で `/usr/sbin/waagent -version` を実行して判断できます。

    Azure Linux エージェントを手動でインストールするには、以下の手順に従います。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 まず、[GitHub](https://github.com/Azure/WALinuxAgent/releases) から、最新の Azure Linux エージェントをダウンロードします。例:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Azure エージェントを展開します。

            # tar -vzxf v2.2.21.tar.gz
   c. python-setuptools をインストールします。

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Azure エージェントをインストールします。

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     Python 2.x および Python 3.x がサイド バイ サイドでインストールされたシステムでは、次のコマンドを実行する必要がある場合があります。

         sudo python3 setup.py install --register-service
     詳細については、Azure Linux エージェント [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) をご覧ください。
2. [Marketplace にイメージを追加](azure-stack-add-vm-image.md)します。 `OSType` パラメーターが `Linux` に設定されていることを確認してください。
3. Marketplace にイメージを追加すると、Marketplace アイテムが作成され、ユーザーが Linux 仮想マシンをデプロイできます。

## <a name="next-steps"></a>次の手順
[Azure Stack でのサービスの提供の概要](azure-stack-offer-services-overview.md)
