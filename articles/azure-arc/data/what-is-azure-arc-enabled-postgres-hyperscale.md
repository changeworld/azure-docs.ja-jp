---
title: Azure Arc 対応 PostgreSQL Hyperscale とは
description: Azure Arc 対応 PostgreSQL Hyperscale とは
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e8746d9b29b0b4af30ba799628328acc3fd8bc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931279"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale とは

Azure Arc 対応 PostgreSQL Hyperscale は、Azure Arc 対応データ サービスの一部として使用できるデータベース サービスの 1 つです。 Azure Arc により、Kubernetes と任意のインフラストラクチャを使用して、Azure データ サービスをオンプレミス、エッジ、パブリック クラウドで実行できるようになります。 Azure Arc 対応データ サービスの価値提案は次の内容を示します。
- 常に最新
- エラスティック スケール
- セルフサービス プロビジョニング
- 統合された管理
- 切断されたシナリオのサポート

詳細については、次を参照してください。
- [Azure Arc 対応データ サービスとは](overview.md)
- [接続モードと要件](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>ソリューションを比較する

このセクションでは、Azure Arc 対応 PostgreSQL Hyperscale が Azure Database for PostgreSQL Hyperscale (Citus) とはどのように異なるかについて説明します。

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL Hyperscale (Citus)":::

これは、Azure でのサービスとしてのデータベース (PaaS) として使用できる Postgres データベース エンジンのハイパースケール フォーム ファクターです。 ハイパースケール エクスペリエンスを可能にする Citus 拡張機能を備えています。 このフォーム ファクターでは、サービスは Microsoft データセンターで実行され、Microsoft によって運用されます。

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure SQL Database for PostgreSQL Hyperscale (Citus)":::

これは、Azure Arc 対応データ サービスで使用できる Postgres データベース エンジンのハイパースケール フォーム ファクターです。 これもまた、ハイパースケール エクスペリエンスを可能にする Citus 拡張機能を備えています。 このフォーム ファクターでは、システムをホストするインフラストラクチャを顧客が提供し、それらのシステムを運用します。

## <a name="next-steps"></a>次のステップ
- **作成**
   > **試してみたい場合**  
   > Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) をすぐに開始できます。

   - **作成:**
      - [クライアント ツールをインストールする](install-client-tools.md)
      - [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールしておく必要があります)
      - [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成しておく必要があります)
- [**Azure Arc 対応データ サービスについて確認する**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**Azure Arc について確認する**](https://aka.ms/azurearc)
