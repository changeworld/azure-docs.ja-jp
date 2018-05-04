---
title: Azure CLI を使用して Azure Stack に Windows 仮想マシンを作成する | Microsoft Docs
description: Azure CLI を使用して Azure Stack に Windows VM を作成する方法を説明します
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>クイック スタート: Azure CLI を使用して Azure Stack に Windows 仮想マシンを作成する

Azure CLI は、コマンドラインで Azure Stack リソースを作成および管理するために使用します。 この記事では、Azure CLI を使用して、Azure Stack に Windows Server 2016 仮想マシンを作成してアクセスする方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

* Azure Stack オペレーターが Azure Stack Marketplace に "Windows Server 2016" のイメージを追加していることを確認します。

* リソースを作成して管理するため、Azure Stack には Azure CLI の特定のバージョンが必要です。 Azure Stack 用に Azure CLI を構成していない場合は、[Azure CLI のインストールと構成](azure-stack-version-profiles-azurecli2.md)の手順に従います。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure Stack リソースのデプロイ先となって管理される論理コンテナーです。 開発キットまたは Azure Stack 統合システムから、[az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成します。 このドキュメントではすべての変数に値を割り当てていますが、これらの値をそのまま使用することも、新しい値を割り当てることもできます。 次の例では、myResourceGroup という名前のリソース グループをローカルの場所に作成します。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用して VM を作成します。 次の例では、myVM という名前の VM を作成します。 この例では、管理ユーザーの名前に Demouser、パスワードに Demouser@123 を使用します。 これらの値を、環境に適した内容に更新します。 これらの値は、仮想マシンに接続する際に必要です。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

VM が作成されると、*PublicIPAddress* パラメーターが出力されます。 このアドレスをメモしておきます。このアドレスは、後で VM にアクセスする際に必要になります。

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く

Azure Stack にデプロイされている Windows 仮想マシンに対しては、既定で RDP 接続のみが許可されます。 この VM を Web サーバーとして使用する場合は、インターネットからポート 80 を開く必要があります。 [az vm open-port](/cli/azure/vm#open-port) コマンドを使用して、目的のポートを開きます。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンのパブリック IP アドレスに置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell を使用した IIS のインストール

Azure VM にログインした後、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS のようこそページの表示

任意の Web ブラウザーを使用して、IIS の既定のウェルカム ページを確認します。 前のセクションで指定したパブリック IP アドレスを使用して、既定のページにアクセスします。

![IIS の既定のサイト](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な Windows 仮想マシンをデプロイしました。 Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)」に進んでください。
