---
title: "顧客の仮想ネットワークにデプロイした Azure RemoteApp 向けにホワイトリスト登録する必要があるポートと URL の一覧 | Microsoft Docs"
description: "Azure RemoteApp を介した通信用に構成する必要があるポートと URL について説明します。"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9390af174262e0dd2bb5beb30ae08b3063c1a5e6
ms.lasthandoff: 03/31/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>顧客の Virtual Network にデプロイした Azure RemoteApp にアクセスを許可する必要があるポートと URL の一覧
> [!IMPORTANT]
> Azure RemoteApp の提供は、2017 年 8 月 31 日で終了します。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp クラウドまたはハイブリッド コレクションを仮想ネットワーク (VNET) にデプロイする場合は、次のポート情報を確認してください。 仮想ネットワークの詳細については、「[Virtual Network の概要](../virtual-network/virtual-networks-overview.md)」を参照してください。 コレクション内の仮想ネットワーク リソースへのトラフィックを制限するネットワーク セキュリティ グループ (NSG) を作成した場合は、仮想ネットワークのセキュリティ ポリシーで次のポートへのアクセスが許可されていることを確認してください。 ネットワーク セキュリティ グループの詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp サブネットがアクセスする必要があるエンドポイントと URL:
* *.servicebus.windows.net
* *. servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* 送信: TCP: TCP: 443、9351、9352、10101 ～ 10175 
* オプション – UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp クライアントがアクセスする必要があるエンドポイントと URL:
クライアントとは、Azure RemoteApp コレクションにデプロイされたアプリに接続する際に使用するデスクトップやデバイスなどのことです。

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (オプションの UDP ポートはこのアドレスで使用) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* 送信: TCP: 443  
* オプション - UDP: 3391 


