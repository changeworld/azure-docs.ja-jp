---
title: "Azure CLI を使用したカスタム VM イメージの作成 | Microsoft Docs"
description: "チュートリアル - Azure CLI を使用してカスタム VM イメージを作成する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>CLI を使用した Azure VM のカスタム イメージの作成

このチュートリアルでは、Azure 仮想マシンの独自のカスタム イメージを作成する方法を説明します。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 カスタム イメージを作成すると、VM と、接続されているすべてのディスクが、イメージ内に含まれます。 

このチュートリアルの手順は、最新バージョンの [Azure CLI 2.0](/cli/azure/install-azure-cli) を使用して行うことができます。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループと VM の名前を適宜置き換えてください。

## <a name="create-an-image"></a>イメージを作成する

仮想マシンのイメージを作成するには、ソース VM のプロビジョニングと割り当てを解除し、汎用とマークすることで VM を準備する必要があります。

### <a name="deprovision-the-vm"></a>VM のプロビジョニングを解除する 

プロビジョニングを解除すると、マシン固有の情報が削除されるため、VM が汎用化されます。 この汎用化によって、1 つのイメージから多数の VM をデプロイできるようになります。 プロビジョニングの解除の際に、ホスト名は `localhost.localdomain` にリセットされます。 SSH ホスト キー、ネームサーバー構成、ルート パスワード、およびキャッシュされた DHCP リースも削除されます。

VM のプロビジョニングを解除するには、Azure VM エージェント (waagent) を使用します。 Azure VM エージェントは VM にインストールして、プロビジョニングと Azure ファブリック コントローラーとのやり取りの管理に使用します。 詳細については、「[Azure Linux エージェント ユーザー ガイド](agent-user-guide.md)」をご覧ください。

SSH を使用して VM に接続し、VM のプロビジョニングを解除するコマンドを実行します。 `+user` 引数を使用して、前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除します。 サンプルの IP アドレスは、使用している VM のパブリック IP アドレスで置き換えてください。

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
```
SSH セッションを閉じます。

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>VM の割り当てを解除して VM を汎用としてマークする

イメージを作成するには、VM の割り当てを解除する必要があります。 [az vm deallocate](/cli//azure/vm#deallocate) を使用して VM の割り当てを解除します。 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

最後に、[az vm generalize](/cli//azure/vm#generalize) を使用して VM の状態を汎用に設定します。
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>イメージのキャプチャ

これで、[az image create](/cli//azure/image#create) を使用して VM のイメージを作成できるようになりました。 次の例では、`myVM` という名前の VM から `myImage` という名前のイメージを作成します。
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>イメージから VM を作成する

[az vm create](/cli/azure/vm#create) とイメージを使用して VM を作成できます。 次の例では、`myImage` という名前のイメージから `myVMfromImage` という名前の VM を作成します。

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カスタム VM カスタム イメージを作成する方法を説明しました。 次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

[高可用性 VM の作成](tutorial-availability-sets.md)


