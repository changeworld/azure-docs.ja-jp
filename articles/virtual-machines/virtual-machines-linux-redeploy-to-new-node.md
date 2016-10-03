<properties 
	pageTitle="Linux 仮想マシンの再デプロイ | Microsoft Azure" 
	description="SSH 接続の問題を軽減するために、Linux 仮想マシンを再デプロイする方法について説明します。" 
	services="virtual-machines-linux" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-linux" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>

# 新しい Azure ノードへの仮想マシンの再デプロイ

Azure 仮想マシン (VM) への SSH またはアプリケーション アクセスに関するトラブルシューティングで問題が発生している場合、VM の再デプロイが有効な場合があります。VM を再デプロイするときは、Azure インフラストラクチャ内の新しいノードに VM を移動してから、電源をオンにすると、すべての構成オプションと関連するリソースが保持されます。この記事では、Azure CLI または Azure ポータルを使用して VM を再デプロイする方法について説明します。

> [AZURE.NOTE] VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられている動的 IP アドレスが更新されます。


## Azure CLI の使用

コンピューターに[最新の Azure CLI がインストール](../xplat-cli-install.md)されていること、および Resource Manager モード (`azure config mode arm`) であることを確認します。

次の Azure CLI コマンドを使用して、仮想マシンを再デプロイします。

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

再デプロイ プロセスにおいて、VM のステータスが変わっていくことがわかります。新しいホストへの再デプロイのプロセス中、VM の `PowerState` は "実行中" から "更新中"、"開始中" に変わり、最後に "実行中" に戻ります。リソース グループ内の VM のステータスを確認するには、次を使用します。

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## 次のステップ
VM への接続に関する問題が発生した場合は、[SSH 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)に関するページまたは「[SSH の詳細なトラブルシューティング手順](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)」を参照してください。VM で実行されるアプリケーションにアクセスできない場合は、[アプリケーションの問題のトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)に関するページもご覧ください。

<!---HONumber=AcomDC_0921_2016-->