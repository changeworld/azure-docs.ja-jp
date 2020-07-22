---
title: Oracle WebLogic Server Azure アプリケーション | Microsoft Docs
description: Microsoft Azure で Oracle WebLogic Server を実行する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664050"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure アプリケーション

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server は、スケーラブルなエンタープライズ対応の Java EE アプリケーション サーバーです。

Oracle WebLogic Server は、多層分散型エンタープライズ アプリケーションを開発してデプロイするための、世界初のクラウド ネイティブのエンタープライズ Java プラットフォーム アプリケーション サーバーです。 Azure WebLogic Server オファーは、クラウド コンピューティングを最小限の労力で導入して最大限の効果を得られるように、Java EE アプリケーションから Azure Cloud へのリフトアンドシフトを含め、WebLogic 移行の際の選択肢を増やし、柔軟性を高めます。 これらのオファーは、仮想ネットワーク、ストレージ、Linux リソースのプロビジョニング、WebLogic Server のインストール、ネットワーク セキュリティ グループを使用したセキュリティの設定、Azure App Gateway による負荷分散、Azure Active Directory による認証、データベース接続の簡易化を自動的に行うため、ビジネス アプリケーションを迅速に開始できます。

さまざまなシナリオに対応できるように 4 つのオファーが用意されています (管理サーバーがある単一ノード、管理サーバーがない単一ノード、クラスター、動的クラスター)。  これらのオファーは無料で提供されており、自由にお試しいただけます。

_これらのオファーはライセンス持ち込み型です_。 Oracle ですでに適切なライセンスを調達していること、Microsoft Azure でオファーを実行するための適切なライセンスをお持ちであることを前提としています。

_オファーを開発しているエンジニアリング チームと密接に連携しながら移行シナリオに取り組みたい場合は、_ [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) で [[連絡する]](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) ボタンをクリックしてください。 プログラム マネージャー、アーキテクト、エンジニアからすぐに連絡があり、コラボレーションが開始されます。

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server Single Node

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに Oracle WebLogic Server がインストールされます。 ドメインの作成や管理サーバーの起動は行われません。 これは、高度にカスタマイズしたドメイン構成を使用するシナリオで役立ちます。

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server with Admin Server

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに Oracle WebLogic Server がインストールされます。 さらに、ドメインが作成され、管理サーバーが起動されて、ドメインの管理が可能になります。

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

このオファーでは、WebLogic Server 仮想マシンの高可用性クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されて、ドメインの管理が可能になります。

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server Dynamic Cluster

このオファーでは、可用性が高くスケーラブルな、WebLogic Server 仮想マシンの動的クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されて、ドメインの管理が可能になります。

## <a name="next-steps"></a>次のステップ

Azure Marketplace で各オファーについて調べます。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Single Node](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server with Admin Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Dynamic Cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
