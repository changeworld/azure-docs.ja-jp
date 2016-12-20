---
title: "Azure Portal を使用した Linux VM の作成 | Microsoft Docs"
description: "Azure ポータルを使用して Linux VM を作成します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3ad64861bc4c3b0a938c75990fc516ef634943ef


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>ポータルを使用して Azure に Linux VM を作成する
この記事では、[Azure Portal](https://portal.azure.com/) を使用して Linux 仮想マシンを作成する方法について説明します。

要件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>[サインイン]
Azure アカウント ID で Azure ポータルにサインインしたうえで、左上隅にある **[+ 新規]** をクリックします。

![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>VM の選択
**[Marketplace]** で **[Virtual Machines]** をクリックし、**[おすすめアプリ]** イメージ リストから **[Ubuntu Server 14.04 LTS]** をクリックします。  下部でデプロイメント モデルとして `Resource Manager` が選択されていることを確認してから、 **[作成]**をクリックします。

![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>VM オプションの入力
**[基本]** ページで、以下の項目を入力します。

* VM の名前
* 管理者ユーザーのユーザー名
* 認証の種類 ( **[SSH 公開キー]**
* 文字列で表された SSH 公開キー ( `~/.ssh/` ディレクトリから取得)
* リソース グループ名 (または既存のグループを選択)

**[OK]** をクリックして続行し、VM サイズを選択すると、以下のように表示されます。

![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>VM のサイズの選択
**DS1** サイズを選び (Premium SSD に Ubuntu をインストールする場合)、**[選択]** をクリックして設定を構成します。

![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>ストレージとネットワーク
**[設定]** の [ストレージ] と [ネットワーク] の値は既定値のままにし、**[OK]** をクリックして概要を表示します。  DS1 を選択するとディスクの種類が Premium SSD に設定されることに注意してください。**S** は SSD を示します。

![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>VM 設定の確認と起動
新しい Ubuntu VM の設定を確認し、 **[OK]**をクリックします。

![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>VM の NIC の検出
ポータルのダッシュボードを開き、 **[ネットワーク インターフェイス]** で NIC を選択します。

![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>パブリック IP の検出
NIC 設定でパブリック IP アドレスのメニューを開きます。

![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>VM への SSH 接続
SSH 公開キーを使用して、パブリック IP に SSH 接続します。  Mac または Linux のワークステーションでは、端末から直接 SSH 接続できます。 Windows ワークステーションの場合は、Linux に SSH 接続するために、PuTTY、MobaXTerm、または Cygwin を使用する必要があります。  Windows ワークステーションで Linux に SSH 接続するための準備については、以下のドキュメントを参照してください。

[Azure 上の Windows における SSH の使用方法](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>次のステップ
テストまたはデモンストレーション用の Linux VM を迅速に作成しました。 インフラストラクチャに合わせてカスタマイズした Linux VM を作成する方法については、次の記事を参照してください。

* [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して、SSH で保護された Linux VM を Azure で作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO2-->


