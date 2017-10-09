---
title: "Azure Stack で Azure CLI を使用して Linux 仮想マシンを作成する | Microsoft Docs"
description: "Azure Stack で CLI を使用して Linux 仮想マシンを作成します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: d868ce59fcb09bae8c111da3892af33317003474
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Azure Stack で Azure CLI を使用して Linux 仮想マシンを作成する

*適用対象: Azure Stack 統合システム*

Azure CLI は、コマンドラインで Azure Stack リソースを作成および管理するために使用します。 このクイックスタートでは、Azure CLI を使用して、Azure Stack に Linux 仮想マシンを作成する方法について説明します。  VM が作成されると、Web サーバーがインストールされ、Web トラフィックを許可するためにポート 80 が開きます。

始める前に、Azure Stack オペレーターが Azure Stack Marketplace に "Ubuntu Server 16.04 LTS" のイメージを追加していることを確認してください。  

リソースを作成して管理するため、Azure Stack には Azure CLI の特定のバージョンが必要です。 Azure Stack 用に Azure CLI を構成していない場合は、[Azure CLI のインストールと構成](azure-stack-connect-cli.md)の手順に従います。

最後に、id_rsa.pub という名前の公開 SSH キーを Windows ユーザー プロファイルの .ssh ディレクトリに作成しておく必要があります。 SSH キーの作成の詳細については、[Windows での SSH キーの作成](../../virtual-machines/linux/ssh-from-windows.md)に関するページを参照してください。 


## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure Stack リソースのデプロイ先となって管理される論理コンテナーです。 [az group create](/cli/azure/group#create) コマンドを使用して、リソース グループを作成します。 このドキュメントではすべての変数に値を割り当てていますが、そのまま使用することも、異なる値を割り当てることもできます。 次の例では、myResourceGroup という名前のリソース グループをローカルの場所に作成します。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) コマンドを使用して VM を作成します。 次の例では、myVM という名前の VM を作成します。 この例では、管理ユーザーの名前に Demouser、パスワードに Demouser@123 を使用します。 これらの値を、環境に適した内容に更新します。 これらの値は、仮想マシンに接続する際に必要です。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

完了すると、コマンドによって、仮想マシンのパラメーターが出力されます。  *PublicIPAddress* を書き留めておきます。これを使用して、仮想マシンに接続して管理するためです。

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く

Azure にデプロイされている Linux 仮想マシンに対しては、既定で SSH 接続のみが許可されます。 この VM を Web サーバーとして使用する場合は、インターネットからポート 80 を開く必要があります。 [az vm open-port](/cli/azure/vm#open-port) コマンドを使用して、目的のポートを開きます。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>VM への SSH 接続

SSH がインストールされているシステムから、次のコマンドを使用して仮想マシンに接続します。 Windows で作業している場合は、[Putty](http://www.putty.org/) を使用して接続を作成できます。 仮想マシンの正しいパブリック IP アドレスに置き換えます。 上記の例では、IP アドレスは 192.168.102.36 です。

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>NGINX のインストール

次の bash スクリプトを使用して、パッケージのソースを更新し、最新の NGINX パッケージをインストールします。 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>NGINX のようこそページの表示

NGINX をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して NGINX の既定のようこそページを表示することができます。 上の手順で指定した *publicIpAddress* を使用して既定のページにアクセスします。 

![NGINX の既定のサイト](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[Key Vault に格納されているパスワードを使用して仮想マシンを作成する](azure-stack-kv-deploy-vm-with-secret.md)

[Azure Stack の Storage について学ぶ](azure-stack-storage-overview.md)


