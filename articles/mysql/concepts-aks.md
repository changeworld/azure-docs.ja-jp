---
title: Azure Kubernetes Service (AKS) と Azure Database for MySQL を接続する
description: Azure Kubernetes Service と Azure Database for MySQL を接続する方法について説明します
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/28/2018
ms.openlocfilehash: baba85aeb800dd8effe3be295b2149179604b41d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164842"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Azure Kubernetes Service と Azure Database for MySQL を接続する

Azure Kubernetes Service (AKS) からは、Azure で使用できるマネージド Kubernetes クラスターが提供されます。 AKS と Azure Database for MySQL を併用してアプリケーションを作成するときに考慮する選択肢を下にいくつか示します。


## <a name="accelerated-networking"></a>Accelerated Networking
AKS クラスターで高速ネットワーク対応の基礎 VM を使用します。 VM で高速ネットワークが有効になると、待ち時間が少なくなり、ジッターが減り、VM の CPU 使用率が下がります。 高速ネットワークのしくみ、サポートされている OS バージョン、[Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 向けにサポートされている VM インスタンスについて説明します。

2018 年 11 月から、AKS は、これらのサポートされている VM インスタンスでの高速ネットワークに対応します。 これらの VM を使用する新しい AKS クラスターでは、高速ネットワークが既定で有効になっています。

AKS クラスターで高速ネットワークが有効になっているかどうかは次の方法で確認できます。
1. Azure portal に進み、AKS クラスターを選択します。
2. [プロパティ] タブを選択します。
3. **[インフラストラクチャ リソース グループ]** の名前をコピーします。
4. ポータル検索バーを使用し、インフラストラクチャ リソース グループを見つけて開きます。
5. そのリソース グループ内の VM を選択します。
6. VM の **[ネットワーク]** タブに移動します。
7. **[高速ネットワーク]** が "有効" になっていることを確認します。


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) を使用すると、Kubernetes または Cloud Foundry から Azure サービスを直接プロビジョニングできます。 これは Azure 用の [Open Service Broker API](https://www.openservicebrokerapi.org/) 実装です。

OSBA を使用すると、Azure Database for MySQL サーバーを作成し、Kubernetes のネイティブ言語で AKS クラスターにそれをバインドできます。 OSBA と Azure Database for MySQL を併用する方法については、[OSBA GitHub ページ](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md)で確認できます。 



## <a name="next-steps"></a>次の手順
- [Azure Kubernetes Service クラスターを作成する](../aks/kubernetes-walkthrough.md)
- [OSBA と Azure Database for MySQL を使用して Helm チャートから WordPress をインストールする](../aks/integrate-azure.md)方法を学習する
