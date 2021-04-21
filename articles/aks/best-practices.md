---
title: Azure Kubernetes Service (AKS) に関するベスト プラクティス
description: Azure Kubernetes Service (AKS) でのアプリケーションの構築および管理のためのクラスター オペレーターと開発者のベスト プラクティス集
services: container-service
ms.topic: article
ms.date: 03/09/2021
ms.openlocfilehash: f4bd109ce6827de26b1649380b6f6a2ba7c10e17
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105903"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションの構築および管理のためのクラスター オペレーターと開発者のベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを正常にビルドして実行するには、次のような重要な考慮事項を理解して実装する必要があります。
* マルチテナントとスケジューラの機能。
* クラスターおよびポッドのセキュリティ。
* 事業継続とディザスター リカバリー。 


AKS 製品グループ、エンジニアリング チーム、およびフィールド チーム (Global Black Belt (GBB) を含む) は、次のベスト プラクティスと概念に関する記事の執筆、グループ化に貢献しました。 その目的は、クラスターのオペレーターと開発者が上記の考慮事項を理解し、適切な機能を実装できるようにすることです。


## <a name="cluster-operator-best-practices"></a>クラスター オペレーターのベスト プラクティス

クラスター オペレーターは、アプリケーション所有者および開発者連携してそのニーズを理解します。 必要に応じて、次のベスト プラクティスを使用して AKS クラスターを構成できます。

**マルチテナント**

* [クラスター分離に関するベスト プラクティス](operator-best-practices-cluster-isolation.md)
    * マルチ テナント機能のコア コンポーネントと名前空間を使用した論理的な分離が含まれます。
* [スケジューラの基本的な機能に関するベスト プラクティス](operator-best-practices-scheduler.md)
    * リソース クォータとポッド中断バジェットの使用が含まれます。
* [スケジューラの高度な機能に関するベスト プラクティス](operator-best-practices-advanced-scheduler.md)
    * テイントと容認、ノード セレクターとノード アフィニティ、ポッド間アフィニティと非アフィニティの使用が含まれます。
* [認証と認可に関するベスト プラクティス](operator-best-practices-identity.md)
    * Azure Active Directory との統合、Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) の使用、Azure RBAC の使用、およびポッド ID が含まれます。

**Security**

* [クラスターのセキュリティとアップグレードに関するベスト プラクティス](operator-best-practices-cluster-security.md)
    * API サーバーへのアクセスのセキュリティ保護、コンテナーへのアクセスの制限、アップグレードとノードの再起動の管理が含まれます。
* [コンテナー イメージの管理とセキュリティに関するベスト プラクティス](operator-best-practices-container-image-management.md)
    * イメージとランタイムのセキュリティ保護と基本イメージ更新プログラムの自動ビルドが含まれます。
* [ポッドのセキュリティに関するベスト プラクティス](developer-best-practices-pod-security.md)
    * リソースへのアクセスのセキュリティ保護、資格情報の公開の制限、ポッド ID とデジタルのキー コンテナーの使用が含まれます。

**ネットワークとストレージ**

* [ネットワーク接続に関するベスト プラクティス](operator-best-practices-network.md)
    * さまざまなネットワーク モデル、イングレスと Web アプリケーション ファイアウォール (WAF) の使用、ノードの SSH アクセスのセキュリティ保護が含まれます。
* [ストレージとバックアップに関するベスト プラクティス](operator-best-practices-storage.md)
    * 適切なストレージの種類とノード サイズの選択、ボリュームの動的プロビジョニング、およびデータのバックアップが含まれます。

**エンタープライズ対応ワークロードの実行**

* [事業継続とディザスター リカバリーに関するベスト プラクティス](operator-best-practices-multi-region.md)
    * リージョンのペア、Azure Traffic Manager を使用した複数のクラスター、およびコンテナー イメージの geo レプリケーションの使用が含まれます。

## <a name="developer-best-practices"></a>開発者のベスト プラクティス

開発者またはアプリケーションの所有者は、開発エクスペリエンスを簡略化し、必要なアプリケーション パフォーマンスのニーズを定義することができます。

* [リソースを管理するアプリケーション開発者に関するベスト プラクティス](developer-best-practices-resource-management.md)
    * ポッドのリソースの要求と制限の定義、開発ツールの構成、およびアプリケーションの問題に関するチェックが含まれます。
* [ポッドのセキュリティに関するベスト プラクティス](developer-best-practices-pod-security.md)
    * リソースへのアクセスのセキュリティ保護、資格情報の公開の制限、ポッド ID とデジタルのキー コンテナーの使用が含まれます。

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS の概念

これらのベスト プラクティスの機能とコンポーネントのいくつかを理解するには、Azure Kubernetes Service (AKS) クラスターの概念に関する次の記事も参照してください。

* [Kubernetes の中心概念](concepts-clusters-workloads.md)
* [アクセスと ID](concepts-identity.md)
* [セキュリティの概念](concepts-security.md)
* [ネットワークの概念](concepts-network.md)
* [ストレージ オプション](concepts-storage.md)
* [スケーリング オプション](concepts-scale.md)

## <a name="next-steps"></a>次のステップ

AKS を使用する必要がある場合は、いずれかのクイック スタートに従って、[Azure CLI](kubernetes-walkthrough.md) または [Azure portal](kubernetes-walkthrough-portal.md) を使用して Azure Kubernetes Service (AKS) クラスターをデプロイしてください。
