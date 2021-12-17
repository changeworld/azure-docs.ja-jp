---
title: Azure Arc の概要
description: Azure Arc とは何か、そしてお客様が Azure の他のサービスや機能を使用してハイブリッド リソースの管理とガバナンスを実現するうえでどのように役立つかについて説明します。
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 62395b53fea3f86e42726f1bb2d35b12d8a0bdca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345740"
---
# <a name="azure-arc-overview"></a>Azure Arc の概要

現在、企業はますます複雑化する環境の制御と管理に苦労しています。 こうした環境は、複数のデータ センターや複数のクラウド、エッジをまたいで広がっています。 環境とクラウドごとにまとまりのない管理ツールの独自のセットを所有しており、それらを習得して運用しなければなりません。

同時に、DevOps と ITOps の新しい運用モデルの導入も簡単ではありません。既存のツールでは、新しいクラウド ネイティブのパターンに対応できないためです。

Azure Arc は、マルチクラウドとオンプレミスの管理プラットフォームに一貫性をもたらすことでガバナンスと管理を簡素化します。 Azure Arc では、次のことが実行できます。
* Azure 以外、オンプレミス、または他のクラウドでお使いの既存のリソースを Azure Resource Manager に投影することで、お使いの環境全体を単一のウィンドウで管理できるようにします。 
* あたかも Azure 内で実行されているかのように、仮想マシン、Kubernetes クラスター、データベースを管理します。 
* 使い慣れた Azure のサービスや管理機能を、それらがどこにあっても使用することが可能です。 
* 従来の ITOps を引き続き使用しながら、DevOps プラクティスを導入し、自分の環境で新しいクラウド ネイティブ パターンをサポートすることができます。
* カスタムの場所を、Azure Arc 対応 Kubernetes クラスター、クラスター接続、クラスター拡張機能の上の抽象化レイヤーとして構成します。  

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc の管理コントロール プレーンの図" border="false":::

現在、Azure の外でホストされた次の種類のリソースを Azure Arc で管理することができます。

* サーバー - Windows または Linux を実行するマシン (物理マシンと仮想マシンの両方)。
* Kubernetes クラスター - 複数の Kubernetes ディストリビューションをサポート。
* Azure データ サービス - Azure SQL Managed Instance および PostgreSQL Hyperscale サービス。
* SQL Server - 任意の場所からインスタンスを [Azure Arc 対応サーバー上の SQL Server](/sql/sql-server/azure-arc/overview) に登録します。

## <a name="what-does-azure-arc-deliver"></a>Azure Arc で実現できること

Azure Arc の主な機能は次のとおりです。

* 自分のサーバー用に、環境全体で一貫したインベントリ、管理、ガバナンス、セキュリティを導入する。

* Azure 管理サービスを使用してサーバーの監視、保護、更新を行うように [Azure VM の拡張機能](./servers/manage-vm-extensions.md)を構成する。

* Kubernetes クラスターを大規模に管理、統制する。

* GitOps を使用して、Git リポジトリから 1 つ以上のクラスターに構成をデプロイする。

*  Azure Policy を使用して、Kubernetes クラスターのコンプライアンスと構成をゼロ タッチで実行する。

* あたかも Azure で実行しているかのように、Kubernetes 環境で [Azure データ サービス](../azure-arc/kubernetes/custom-locations.md) (具体的には Azure SQL Managed Instance と Azure Database for PostgreSQL Hyperscale) を実行し、アップグレードと更新、セキュリティ、監視などのメリットを活かす。 エラスティック スケールを使用し、Azure への継続的な接続が得られなくても、アプリケーションのダウンタイムを発生させずに更新プログラムを適用する。

* [Azure Arc 対応 Kubernetes](./kubernetes/overview.md) クラスターの上に[カスタムの場所](./kubernetes/custom-locations.md)を作成し、Azure サービス インスタンスをデプロイするためのターゲットの場所として使用する。 [Azure Arc 対応 Data Services](./data/create-data-controller-direct-azure-portal.md)、[Azure Arc 上の App Services](../app-service/overview-arc-integration.md) (Web、関数、ロジック アプリを含む) および [Kubernetes 上の Event Grid](../event-grid/kubernetes/overview.md) の Azure サービス クラスター拡張機能をデプロイする。

* Azure portal、Azure CLI、Azure PowerShell、Azure REST API のどれを使用していても、統合されたエクスペリエンスで Azure Arc 対応リソースを表示する。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc のコスト

以降、Azure Arc で現在利用できる機能の価格について詳しく取り上げます。

### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー

次の Azure Arc コントロール プレーンの機能は、追加コストなしで提供されます。

* Azure 管理グループと Azure タグを使用したリソース組織。

* Azure Resource Graph を使用した検索とインデックス作成。

* Azure RBAC とサブスクリプションを使用したアクセスとセキュリティ。

* テンプレートと拡張機能を使用した環境と自動化。

* 更新の管理。

Azure Arc 対応サーバーで使用されるすべての Azure サービス (Microsoft Defender for Cloud、Azure Monitor など) は、そのサービスの価格で請求されます。 詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/)をご覧ください。

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes

Azure Arc 対応 Kubernetes で使用されるすべての Azure サービス (Microsoft Defender for Cloud、Azure Monitor など) は、そのサービスの価格で請求されます。 Azure Arc 対応 Kubernetes 上の構成に対する料金の詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/)をご覧ください。

### <a name="azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービス

詳細については、[Azure の価格のページ](https://azure.microsoft.com/pricing/)を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Arc 対応サーバーの詳細については、次の[概要](./servers/overview.md)を参照してください

* Azure Arc 対応 Kubernetes の詳細については、次の[概要](./kubernetes/overview.md)を参照してください

* Azure Arc 対応データ サービスの詳細については、次の[概要](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)を参照してください

* Azure Arc 対応サーバーでの SQL Server の詳細については、次の[概要](/sql/sql-server/azure-arc/overview)を参照してください

* [概念実証のジャンプスタート](https://azurearcjumpstart.io/azure_arc_jumpstart/)に関するページから Azure Arc 対応サービスを体験します
