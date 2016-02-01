
<properties
    pageTitle="顧客の Virtual Network にデプロイした Azure RemoteApp 向けにホワイトリスト登録する必要があるポートと URL の一覧 | Microsoft Azure"
    description="Azure RemoteApp を介した通信用に構成する必要があるポートと URL について説明します。"
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/19/2016"
    ms.author="elizapo" />



# 顧客の Virtual Network にデプロイした Azure RemoteApp にアクセスを許可する必要があるポートと URL の一覧 

以下の要件は、Azure RemoteApp のクラウドまたはハイブリッド コレクションを仮想ネットワーク (VNET) にデプロイしている場合に適用されます。仮想ネットワークの詳細については、「[Virtual Network の概要](virtual-networks-overview.md)」を参照してください。Azure RemoteApp 用に選択した仮想ネットワーク リソースへのトラフィックを制限するネットワーク セキュリティ グループ (NSG) を作成した場合は、仮想ネットワークのセキュリティ ポリシーで次のエンドポイントと URL へのアクセスが許可されていることを確認してください。ネットワーク セキュリティ グループの詳細については、「[ネットワーク セキュリティ グループ (NSG) について](virtual-networks-nsg.md)」を参照してください。

##  Azure RemoteApp サブネットがアクセスする必要があるエンドポイントと URL: 
*	**.servicebus.windows.net
*	 **. servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 送信: TCP: 443、TCP: 10101-10175 
*	 オプション – UDP: 10201-10275 
 
## Azure RemoteApp クライアントがアクセスする必要があるエンドポイントと URL: 

クライアントとは、Azure RemoteApp コレクションにデプロイされたアプリに接続する際に使用するデスクトップやデバイスなどのことです。

-  https://telemetry.remoteapp.windowsazure.com  
-  https://**.remoteapp.windowsazure.com (オプションの UDP ポートはこのアドレスで使用) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://**.remoteapp.windowsazure.com  
-  https://*.core.windows.net  
-  送信: TCP: 443  
-  オプション - UDP: 10201-10275

<!---HONumber=AcomDC_0121_2016-->