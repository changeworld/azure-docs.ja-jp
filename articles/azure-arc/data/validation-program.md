---
title: Azure Arc 対応データ サービスの検証
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: Kubernetes ディストリビューション向けの、Azure Arc 対応データ サービスの検証プログラムについて説明します。
keywords: Kubernetes, Arc, Azure, K8s, 検証, データ サービス, SQL Managed Instance
ms.openlocfilehash: 45c6967a536bc4e32e2382acd795f508a4bea0a4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615184"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>Azure Arc 対応データ サービス Kubernetes 検証

Azure Arc 対応データ サービス チームでは、業界のパートナーと協力して、Azure Arc 対応データ サービスをホストするための特定のディストリビューションおよびソリューションを検証してきました。 この検証により、データ サービスを対象とした [Azure Arc 対応の Kubernetes 検証](../kubernetes/validation-program.md)が拡張されます。 この記事では、データ サービスのサポートに関しての検証が完了しているパートナー ソリューション、バージョン、Kubernetes のバージョン、SQL エンジンのバージョン、PostgreSQL Hyperscale のバージョンを示します。 

すべての Azure Arc 対応コンポーネントの検証のしくみについては、[検証プログラムの概要](../validation-program/overview.md)に関する記事を参照してください。

> [!NOTE]
> 現時点で、Azure Arc 対応の SQL マネージド インスタンスは、一部のリージョンで一般提供されています。
>
> Azure Arc 対応の PostgreSQL Hyperscale は、一部のリージョンでプレビューが提供されています。

## <a name="partners"></a>パートナー

### <a name="cisco"></a>Cisco

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
|VMware 上の Cisco Hyperflex <br/> Cisco IKS ESXi 6.7 U3 |v1.19.5|v1.0.0_2021-07-30|15.0.2148.140| 検証なし |

### <a name="dell"></a>Dell

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 検証なし |
| PowerFlex バージョン 3.6 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 検証なし |
| PowerFlex CSI バージョン 1.4 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 検証なし |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1) |
| Powerstore T|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS: 5.19.1.5<br/>AHV:20201105.1021<br/>PC: バージョン pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="platform-9"></a>Platform 9

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| Platform9 Managed Kubernetes v5.3.0 | 1.20.5 | v1.0.0_2021-07-30| 15.0.2148.140 | 検証なし |

### <a name="purestorage"></a>PureStorage

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | 15.0.2148.140 | 検証なし |

### <a name="red-hat"></a>Red Hat

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | 15.0.2148.140 | postgres 12.3 (Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|ソリューションとバージョン | Kubernetes バージョン | Azure Arc 対応データ サービスのバージョン | SQL エンジンのバージョン | PostgreSQL Hyperscale のバージョン
|-----|-----|-----|-----|-----|
| TKGm v1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3 (Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>データ サービスの検証プロセス

Sonobuoy Azure Arc 対応データ サービス プラグインは、Kubernetes クラスターでの Azure Arc 対応のデータ サービスのプロビジョニングとテストを自動化します。

### <a name="prerequisites"></a>前提条件

ツールをインストールします。 

- [Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio - Insider ビルド](https://github.com/microsoft/azuredatastudio)

ターゲットの Kubernetes クラスターにアクセスするように構成された Kubernetes 構成ファイルを作成し、現在のコンテキストとして設定します。 このファイルを生成してローカルでコンピューターに導入する方法は、プラットフォームによって異なります。 [Kubernetes.io](https://kubernetes.io/docs/home/) を参照してください。

### <a name="process"></a>Process

適合テストは、Azure Arc 対応データ サービスの検証の一部として実行されます。 これらのテストを実行するための前提条件は、使用中の Kubernetes ディストリビューション向けの Azure Arc 対応 Kubernetes のテストに合格することです。

これらのテストでは、データ サービスの実行と運用の要件に製品が準拠していることを検証します。 このプロセスは、製品がエンタープライズ環境へのデプロイに対応しているかどうかの評価に役立ちます。

データ サービスのテストでは、間接接続モードで以下を実行します。

1. 間接モードでデータ コントローラーをデプロイする
2. [Azure Arc 対応 SQL マネージド インスタンス](create-sql-managed-instance.md)をデプロイする
3. [Azure Arc 対応 PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md)をデプロイする
4. [PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md) をスケールアウトする

Azure Arc 対応データ サービスの今後のリリースで、テストの追加が予定されています。

## <a name="additional-information"></a>関連情報

- [検証プログラムの概要](../validation-program/overview.md)
- [Azure Arc 対応 Kubernetes 検証](../kubernetes/validation-program.md)
- [Azure Arc 検証プログラム - GitHub プロジェクト](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>次のステップ

[データ コントローラーの作成](create-data-controller.md)
