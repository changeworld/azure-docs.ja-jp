---
title: Azure Virtual Machines で Oracle WebLogic Server を実行するためのソリューションとは
description: Microsoft Azure Virtual Machines で Oracle WebLogic Server を実行する方法について説明します。
author: rezar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: c19d6d2513d1bf266d10e90dfb1d6fe79c9410d1
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993462"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Azure Virtual Machines で Oracle WebLogic Server を実行するためのソリューションとは

このページでは、Azure Virtual Machines で Oracle WebLogic Server (WLS) を実行するためのソリューションについて説明します。 これらのソリューションは、Oracle と Microsoft が共同で開発しています。

WLS は、世界中の最もミッション クリティカルなエンタープライズ Java アプリケーションを複数実行している最先端の Java アプリケーション サーバーです。 WLS は、Oracle ソフトウェア スイートのミドルウェア基盤を形成しています。 Oracle と Microsoft は、最先端のクラウド プラットフォームとして Azure 上でワークロードを実行するための選択肢と柔軟性を WLS の顧客に与えることに取り組んでいます。

Azure WLS ソリューションは、ほとんどの定型操作を自動化することで、Java EE アプリケーションをできるだけ簡単に Azure 仮想マシンにリフト アンド シフトで移行できるようにすることを目的としています。 ソリューションでは、仮想ネットワーク、ストレージ、Java、および Linux のリソースが自動的にプロビジョニングされます。 最低限の手間で、WebLogic Server がインストールされます。 このソリューションでは、ネットワーク セキュリティ グループを使用してセキュリティを設定し、Azure App Gateway によって負荷分散を行い、Azure Active Directory で認証を行います。 また、Oracle クラウドまたは Azure 上の Azure PostgreSQL、Azure SQL、Oracle DB などの既存のデータベースに自動的に接続することもできます。 ソリューションのロード マップには、Oracle Coherence を介した分散ログと分散キャッシュを有効にする機能が含まれています。 Microsoft と Oracle パートナーとなって、WebLogic と Azure Kubernetes Service (AKS) で同様の機能を有効にしています。

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Azure portal を使用して、Azure に WebLogic Server をデプロイできる":::

さまざまなシナリオに対応できるよう、管理サーバーがある単一ノード、管理サーバーがない単一ノード、クラスター、動的クラスターの 4 つのオファーが用意されています。 これらのオファーは無料で使用できます。 これらのオファーの説明とリンクは以下のとおりです。

_これらのオファーはライセンス持ち込み型です_。 ここでは、Oracle で既に適切なライセンスを取得していること、および Azure でオファーを実行するための適切なライセンスをお持ちであることを前提としています。

このオファーでは、基本イメージ (WebLogic Server 14 や JDK 11 on Oracle Linux 7.6 など) を使用して、さまざまなオペレーティング システム、Java、および WLS バージョンをサポートしています。 これらの基本イメージは、Azure で単独で使用することもできます。 基本イメージは、カスタマイズされた複雑な Azure デプロイを必要とする顧客に適しています。 現在の基本イメージ セットは、[こちら](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)から入手できます。

_これらのオファーを開発しているエンジニアリング チームと密接に連携しながら移行シナリオに取り組むことに関心がある場合は、_ [マーケットプレース オファーの概要ページ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)の [[CONTACT ME]\(お問い合わせ\)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) ボタンを選択してください。 プログラム マネージャー、アーキテクト、エンジニアからすぐに連絡があり、密接なコラボレーションが開始されます。 移行シナリオで共同作業を行う機会は、オファーの開発が現在進められている場合でも無料です。

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server Single Node

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに WLS がインストールされます。 ドメインの作成や管理サーバーの起動は行われません。 単一ノード オファーは、高度にカスタマイズしたドメイン構成を使用するシナリオで役立ちます。

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server with Admin Server

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに WLS がインストールされます。 ドメインが作成され、管理サーバーが起動されます。 ドメインを管理し、すぐにアプリケーションのデプロイを開始できます。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

このオファーでは、WLS 仮想マシンの高可用性クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されます。 クラスターを管理し、可用性の高いアプリケーションでの作業をすぐに開始できます。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server Dynamic Cluster

このオファーでは、可用性が高くてスケーラブルな、WLS 仮想マシンの動的クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されます。

ソリューションでは、広範な運用環境に対応したデプロイ アーキテクチャが比較的簡単に有効になります。 ビジネス アプリケーションの開発に専念できるようにすることによって、可能な限り最も生産的な方法でほとんどの移行ケースを満たすことができます。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Azure portal を使用して、Azure に WebLogic Server をデプロイできる":::

顧客は、ソリューションによって自動的にプロビジョニングされるもの以外に、さらにデプロイをカスタマイズできる完全な柔軟性を得ています。 おそらく顧客は、アプリケーションのデプロイに加え、Azure リソースをそれらのデプロイと統合します。 ソリューションのさらなる改善に関するフィードバックを提供することをお勧めします。

## <a name="next-steps"></a>次の手順

Azure のオファーをご覧ください。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Single Node](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server with Admin Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Dynamic Cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
