---
title: Service Fabric マネージド クラスターに関するよくある質問
description: Service Fabric マネージド クラスターについてよく寄せられる質問 (機能、ユース ケース、一般的なシナリオなど)。
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 4dc41d2c13c834657534971041440bb744cfca38
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319821"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric マネージド クラスターに関してよく寄せられる質問

Service Fabric マネージド クラスター (プレビュー) で実行できる内容とその使用方法に関してよく寄せられる多数の質問があります。 このドキュメントでは、これらのよく寄せられる質問とその回答を示します。

## <a name="general"></a>全般

### <a name="what-are-service-fabric-managed-clusters"></a>Service Fabric マネージド クラスターとはどのようなものですか?

Service Fabric マネージド クラスターは、クラスターのデプロイと管理を容易にするために設計された Service Fabric クラスター リソース モデルの進化版です。 Service Fabric マネージド クラスターで使用されている Azure Resource Manager カプセル化モデルにより、ユーザーはクラスター リソースを 1 つ定義してデプロイするだけでよく、現在のように多数の独立したリソース (仮想マシン スケール セット、Load Balancer、IP など) をデプロイする必要はありません。

### <a name="what-regions-are-supported-in-the-preview"></a>プレビューでサポートされているリージョンはどこですか?

Service Fabric マネージド クラスター プレビューは、`centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus`、`eastus2` などのリージョンでサポートされています。

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>既存の Service Fabric クラスターをマネージド クラスター リソースにインプレース移行することはできますか?

現時点では、新しい Service Fabric マネージド クラスター リソースの種類を使用するには、新しい Service Fabric クラスター リソースを作成する必要があります。

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric マネージド クラスターを使用すると追加料金が発生しますか?

いいえ。クラスターに必要な基礎となるコンピューティング、ストレージ、ネットワーク リソースのコスト以外に、Service Fabric マネージド クラスターに関連する追加コストは発生しません。

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric マネージド クラスター リソースによって導入される新しい SLA はありますか?

SLA は、現在の Service Fabric リソース モデルから変更されません。

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Basic SKU と Standard SKU のクラスターでは何が違いますか?

Basic SKU クラスターを使用すると、ほとんどの構成が Service Fabric リソース プロバイダーによって提供されます。 Basic SKU クラスターは、テスト環境および運用前環境での使用が意図されています。 Standard SKU クラスターを使用すると、ユーザーがニーズを満たすようにクラスターを構成できます。 詳細については、「[Service Fabric マネージド クラスターの SKU](./overview-managed-cluster.md#service-fabric-managed-cluster-skus)」を参照してください。

## <a name="cluster-deployment-and-management"></a>クラスターのデプロイと管理

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>仮想マシン スケール セットでカスタム スクリプト拡張機能を実行しています。Service Fabric のマネージド リソースでも引き続き使用できますか?

はい。ノードの種類で VM 拡張機能を指定できます。 詳細については、ノードの種類の拡張機能サンプルを参照してください。

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>内部専用のロード バランサーを使用することはできますか?

現在、内部専用のロード バランサーを使用することはできません。 ネットワーク セキュリティ グループの規則をロックダウンし、不要な受信および送信トラフィックをブロックすることをお勧めします。

### <a name="can-i-autoscale-my-cluster"></a>クラスターを自動スケーリングできますか? 
現在、プレビューでは自動スケーリングは利用できません。

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>可用性ゾーンをまたいでクラスターをデプロイできますか? 
クロス可用性ゾーン クラスターは、現在プレビューでは使用できません。

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>クラスター ランタイムで自動アップグレードと手動アップグレードのどちらかを選択できますか? 
プレビューでは、すべてのランタイム アップグレードは自動的に行われます。

## <a name="applications"></a>アプリケーション

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Service Fabric マネージド クラスター用のローカル開発エクスペリエンスはありますか?

ローカル開発エクスペリエンスは、既存の Service Fabric クラスターから変更されていません。 ローカル開発エクスペリエンスの詳細については、[.NET アプリケーションの作成](./service-fabric-quickstart-dotnet.md)に関する記事を参照してください。

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>アプリケーションを Azure Resource Manager リソースとしてデプロイできますか?

プレビューでは、アプリケーションを Azure Resource Manager リソースとしてデプロイすることはできません。 アプリケーションをデプロイするには、PowerShell または CLI を使用してクラスターに直接接続する必要があります。 この機能は、Service Fabric マネージド クラスターが一般提供になる前に追加されます。