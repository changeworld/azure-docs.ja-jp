---
title: Azure Arc 対応データ サービスとは
description: Azure Arc 対応データ サービスの概要
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 642448cab1fa6511d9d12eb4a3de894f3e5c1251
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056760"
---
# <a name="what-are-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスとは

Azure Arc を使用すると、Kubernetes と選択したインフラストラクチャを使用して、オンプレミス、エッジ、パブリック クラウドで Azure データ サービスを実行できます。

現在、次の Azure Arc 対応データ サービスを利用できます。 

- SQL Managed Instance
- PostgreSQL Hyperscale (プレビュー)

Azure Arc 対応データ サービスでハイブリッド作業環境がどのようにサポートされているかの概要については、こちらの入門ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows//Inside-Azure-for-IT/Choose-the-right-data-solution-for-your-hybrid-environment/player?format=ny]

## <a name="always-current"></a>常に最新

Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale などの Azure Arc 対応データ サービスでは、Azure でのエクスペリエンスと同様に、サービス パッチや新機能などの更新が頻繁に行われます。 Microsoft Container Registry からの更新プログラムが提供され、デプロイ頻度がポリシーに従って設定されます。 これにより、オンプレミスのデータベースを最新の状態に保ちながら、制御を維持することができます。 Azure Arc 対応データ サービスはサブスクリプション サービスであるため、データベースのサポート終了に直面する状況はなくなります。

## <a name="elastic-scale"></a>エラスティック スケール

クラウドに似た柔軟性をオンプレミスで利用することで、インフラストラクチャの利用可能容量に基づいて、Azure 内とほぼ同じ方法でデータベースを動的にスケールアップまたはスケールダウンできます。 この機能により、リアルタイム、任意のスケール、1 秒未満の応答時間でのデータの取り込みやクエリを必要とするシナリオなど、揮発性のニーズを持つバースト シナリオに対応することができます。 さらに、Azure Database for PostgreSQL Hyperscale の固有のハイパー スケール デプロイ オプションを使用して、データベース インスタンスをスケールアウトすることもできます。 この機能により、データ ワークロードの容量の最適化が向上します。これには、一意のスケール "*アウト*" 読み取りと書き込みが使用されます。

## <a name="self-service-provisioning"></a>セルフサービス プロビジョニング

Azure Arc には、高速デプロイや大規模な自動化など、クラウドの利点が他にもあります。 Kubernetes ベースのオーケストレーションにより、GUI または CLI のいずれかのツールを使用して、データベースを数秒でデプロイできます。

## <a name="unified-management"></a>統合された管理

Azure portal、Azure Data Studio、`arcdata` 拡張子が付いた Azure CLI (`az`) などの使い慣れたツールを使用して、Azure Arc によってデプロイされたすべてのデータ資産の統合ビューを取得できるようになりました。お使いの環境と Azure でさまざまなリレーショナル データベースを表示および管理できるだけでなく、Kubernetes API からログとテレメトリを取得して、基盤となるインフラストラクチャの容量と正常性を分析することもできます。 ローカライズされたログ分析とパフォーマンスの監視に加えて、Azure Monitor を利用して、資産全体にわたる総合的なオペレーション インサイトを実現できるようになりました。

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="disconnected-scenario-support"></a>切断されたシナリオのサポート

セルフサービス プロビジョニング、自動バックアップ/復元、監視などのサービスの多くは、Azure への直接接続を使用して、または使用せずに、インフラストラクチャ内でローカルに実行できます。 Azure に直接接続すると、Azure Monitor などの他の Azure サービスとの統合に関する追加のオプションを利用でき、世界中のどこからでも Azure portal と Azure Resource Manager API を使用して Azure Arc 対応データ サービスを管理することができます。

## <a name="supported-regions"></a>サポートされているリージョン

Azure Arc 対応データ サービスで現在サポートされているシナリオを次の表に示します。

|Azure リージョン  |直接接続モード  |間接接続モード  |
|---------|---------|---------|
|米国東部|利用可能|利用可能
|米国東部 2|利用可能|利用可能
|米国西部|利用可能|利用可能
|米国西部 2|利用可能|利用可能
|米国西部 3|利用可能|利用可能
|米国中北部 | 利用可能 | 利用可能
|米国中部|使用不可|利用可能
|米国中南部|利用可能|利用可能
|英国南部|利用可能|利用可能
|フランス中部|利用可能|利用可能
|西ヨーロッパ |利用可能 |利用可能
|北ヨーロッパ|利用可能|利用可能
|東日本|使用不可|利用可能
|韓国中部|使用不可|利用可能
|東アジア|使用不可|利用可能
|東南アジア|利用可能|利用可能
|オーストラリア東部|利用可能|利用可能

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。
>
>さらに、Azure Arc のすべてのもののための簡単にデプロイできるサンドボックスである [Jumpstart ArcBox](https://azurearcjumpstart.io/azure_jumpstart_arcbox/) をデプロイします。ArcBox は、単一の Azure サブスクリプションとリソース グループ内で完全に自己完結するように設計されており、使用可能な Azure サブスクリプションだけで使用可能なすべての Azure Arc 対応テクノロジを簡単に使用できます。

[クライアント ツールをインストールする](install-client-tools.md)

[Azure Arc データ サービスのデプロイを計画する](plan-azure-arc-data-services.md) (最初にクライアント ツールをインストールする必要があります)

[Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)

[Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
