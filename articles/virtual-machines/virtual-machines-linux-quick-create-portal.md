---
title: "Azure Portal を使用した Linux VM の作成 | Microsoft Docs"
description: "Azure ポータルを使用して Linux VM を作成します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 1/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: beff4fb41ed46b016088734054e7a7897fed1a30
ms.openlocfilehash: 7287b87b1e50e28de06a5363a1f35bd7ac34d51c
ms.lasthandoff: 02/15/2017


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>ポータルを使用して Azure に Linux VM を作成する
この記事では、[Azure Portal](https://portal.azure.com/) を使用して Linux 仮想マシンを作成する方法について説明します。

要件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>[サインイン]
Azure アカウント ID で Azure Portal にサインインします。 左上隅にある **[+ 新規]** をクリックします。

![新しい Azure リソースの作成](./media/virtual-machines-linux-quick-create-portal/create_new_resource.png)

## <a name="choose-vm"></a>VM の選択
**[Marketplace]** で **[Compute]** をクリックし、**[おすすめアプリ]** イメージ リストから **[Ubuntu Server 16.04 LTS]** を選択します。  下部でデプロイメント モデルとして `Resource Manager` が選択されていることを確認してから、 **[作成]**をクリックします。

![Azure Marketplace から仮想マシン イメージを選択](./media/virtual-machines-linux-quick-create-portal/create_new_vm.png)

## <a name="enter-vm-options"></a>VM オプションの入力
**[基本]** ページで、以下の項目を入力します。

* VM の名前
* VM ディスクの種類 (既定値 [SSD] または [HDD])
* 管理者ユーザーのユーザー名
* **[認証の種類]** は **[SSH 公開鍵]** に設定します
* 文字列で表された SSH 公開鍵 (`~/.ssh/` ディレクトリから取得)
* リソース グループ名 (または、既存のリソース グループを選択)

**[OK]** をクリックして続行します。 ブレードは次のスクリーンショットのようになります。

![基本的な Azure VM オプションの入力](./media/virtual-machines-linux-quick-create-portal/enter_basic_vm_details.png)

## <a name="choose-vm-size"></a>VM のサイズの選択
VM のサイズを選択します。 次の例では、Premium SSD に Ubuntu をインストールする **[DS1_V2 Standard]** を選択します。 VM のサイズの **S** は、SSD のサポートを表します。 **[選択]** をクリックして設定を構成します。

![Azure VM サイズの選択](./media/virtual-machines-linux-quick-create-portal/select_vm_size.png)

## <a name="storage-and-network"></a>ストレージとネットワーク
**[設定]** ブレードで、VM に Azure Managed Disks を使用するよう選択できます。 現在の既定の設定では、非管理対象ディスクを使用します。 Azure Managed Disks は Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 Azure Managed Disks の詳細については、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」をご覧ください。 非管理対象ディスクの場合は、仮想ハード ディスク用のストレージ アカウントを作成または選択する必要があります。

![非管理対象ディスク用のストレージ アカウントを選択](./media/virtual-machines-linux-quick-create-portal/configure_non_managed_disks.png)

Azure Managed Disks の使用を選択した場合は、次の例に示すように、構成する追加のストレージ オプションはありません。

![ポータルでの Azure Managed Disks オプションの選択](./media/virtual-machines-linux-quick-create-portal/select_managed_disks.png)

ネットワーク設定の残りの部分は既定値のままにします。

## <a name="confirm-vm-settings-and-launch"></a>VM 設定の確認と起動
新しい Ubuntu VM の設定を確認し、 **[OK]**をクリックします。

![Azure VM 設定の確認と VM の作成](./media/virtual-machines-linux-quick-create-portal/review_final_vm_settings.png)

## <a name="select-the-vm-resource"></a>VM のリソースの選択
ポータルのホーム ページを開き、左上隅のメニューから **[リソース グループ]** を選択します。 必要な場合は、メニューの一番上にある&3; 本のバーをクリックすると、次に示すように一覧が展開されます。

![リソース グループの一覧を開く](./media/virtual-machines-linux-quick-create-portal/select_resource_group.png)

リソース グループを選択し、新しい VM をクリックします。

![Azure VM の NIC 設定を見つける](./media/virtual-machines-linux-quick-create-portal/select_vm_resource.png)

## <a name="find-the-public-ip"></a>パブリック IP の検出
VM に割り当てられた**パブリック IP アドレス**を確認します。

![Azure VM のパブリック IP アドレスを入手](./media/virtual-machines-linux-quick-create-portal/view_public_ip_address.png)

## <a name="ssh-to-the-vm"></a>VM への SSH 接続
SSH 公開キーを使用して、パブリック IP に SSH 接続します。  Mac または Linux のワークステーションでは、端末から直接 SSH 接続できます。 Windows ワークステーションの場合は、Linux に SSH 接続するために、PuTTY、MobaXTerm、または Cygwin を使用する必要があります。  Windows ワークステーションで Linux に SSH 接続するための準備については、以下のドキュメントを参照してください。

[Azure 上の Windows における SSH の使用方法](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ops@40.112.255.214
```

## <a name="next-steps"></a>次のステップ
テストまたはデモンストレーション用の Linux VM を迅速に作成しました。 インフラストラクチャに合わせてカスタマイズした Linux VM を作成する方法については、次の記事を参照してください。

* [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して、SSH で保護された Linux VM を Azure で作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


