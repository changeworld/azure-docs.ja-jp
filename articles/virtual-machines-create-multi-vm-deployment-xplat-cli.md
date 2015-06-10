<properties
   pageTitle="Azure CLI を使用して複数の VM のデプロイメントを作成する | Azure"
   description="Azure CLI を使用して複数の VM のデプロイメントを作成する方法について説明します。"
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# Azure CLI を使用して複数の VM のデプロイメントを作成する

次のスクリプトは、Azure コマンドライン インターフェイス (Azure CLI) を使用して VNET で複数 VM の複数クラウド サービスのデプロイメントを構成する方法を示しています。

次の図は、スクリプトが完了した後でデプロイメントがどのようになるかを示しています。

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

このスクリプトによって、クラウド サービス **servercs** で 1 つの VM (**servervm**) が、クラウド サービス **workercs** で 2 つの VM (**clientvm1、clientvm2**) が作成されます。どちらのクラウド サービスも VNET **samplevnet** にあります。**servercs** クラウド サービスは、外部接続用に構成されたエンドポイントも保持しています。

## CLI スクリプトでの構成
比較的簡単なコードで構成できます。具体的には次の通りです。

>[AZURE.NOTE]ほとんどの場合、servercs と workercs の両クラウド サービスの名前は固有の名前に変更する必要があります。

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

コードはそのまま次のように分けられます。

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*–q オプションはオブジェクト削除の相互確認を排除し、-b オプションは VM に関連付けられたディスクや BLOB をクリーンアップします。*

## コマンドで使用する汎用的な形式

どの Azure CLI コマンドでも -help オプションを使用すれば詳細を確認できますが、上記で使用された各コマンドの汎用的な形式は次の通りです。

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## 次のステップ


* [Azure での Linux とオープン ソース コンピューティング](virtual-machines-linux-opensource.md)
* [Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)

<!---HONumber=58-->