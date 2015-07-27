
<properties
    pageTitle="Azure RemoteApp で使用する Azure VNET の検証"
    description="Azure VNET を Azure RemoteApp で使用する準備が整っていることを確認する方法を説明します。"
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/02/2015"
    ms.author="elizapo" />



# Azure RemoteApp で使用する Azure VNET の検証

Azure RemoteApp で Azure VNET を使用する前に、VNET を検証できます。これは、Azure RemoteApp の接続の問題を防ぐのに役立ちます。

Azure VNET を検証するには、次の手順を行います。

1. Azure RemoteApp で使用する Azure VNET のサブネット内に Azure 仮想マシンを作成します。

2. 管理ポータルで **[接続]** オプションを使用して、作成した VM に接続します。
3. 仮想マシンを Azure RemoteApp を使用するのと同じドメインに参加させます。

これに成功した場合は、Azure VNET は RemoteApp で使用する準備が整っています。

エンド ツー エンドのハイブリッド コレクションのワークフローの詳細については、次の記事を参照してください。

- [RemoteApp のハイブリッド デプロイメントの作成方法](remoteapp-create-hybrid-deployment.md)
- [Azure RemoteApp コレクションの Azure Virtual Network へのデプロイ方法 (ExpressRoute のサポートを利用)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
 

<!---HONumber=July15_HO2-->