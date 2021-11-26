---
title: Microsoft Sentinel でカスタマー マネージド キーを設定する | Microsoft Docs
description: Microsoft Sentinel でカスタマー マネージド キー (CMK) を設定する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 535b0295bac0aa8b8ccdfddbc8735dd8e7b162e8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721520"
---
# <a name="set-up-microsoft-sentinel-customer-managed-key"></a>Microsoft Sentinel のカスタマー マネージド キーの設定

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel 用の[カスタマー マネージド キー (CMK)](../azure-monitor/logs/customer-managed-keys.md) を構成するための背景情報と手順について説明します。 CMK では、Microsoft Sentinel に格納されているすべてのデータ (関連するすべてのストレージ リソースで Microsoft によって既に暗号化されている) をさらに保護することができます。このために使用する暗号化キーは、自ら作成して所有し、[Azure Key Vault](../key-vault/general/overview.md) に格納しておきます。

## <a name="prerequisites"></a>前提条件

- CMK 機能には、少なくとも 500 GB/日のコミットメント レベルを持つ Log Analytics 専用クラスターが必要です。 複数のワークスペースを同じ専用クラスターにリンクすることができ、それらが同じカスタマー マネージド キーを共有します。

- このガイドの手順を完了した後、ワークスペースを使用する前に、オンボードの確認について [Microsoft Sentinel 製品グループ](mailto:azuresentinelCMK@microsoft.com)にお問い合わせください。

- [Log Analytics 専用クラスターの価格](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model)について学習します。

## <a name="considerations"></a>考慮事項

- CMK ワークスペースを Sentinel にオンボードすることは、REST API 経由でのみサポートされており、Azure portal ではサポートされていません。 現在、Azure Resource Manager テンプレート (ARM テンプレート) は CMK オンボードではサポートされていません。

- Microsoft Sentinel CMK 機能が提供されるのは、"*Microsoft Sentinel にまだオンボードされていない* *Log Analytics 専用クラスター内のワークスペース*" のみです。

- 次の CMK 関連の変更は、無効になるため "*サポートされません*" (Microsoft Sentinel データは、CMK ではなく、Microsoft が管理するキーによってのみ暗号化されます)。

  - Microsoft Sentinel に "*既にオンボードされた*" ワークスペースでの CMK の有効化。
  - Sentinel にオンボードされたワークスペースを含むクラスター上での CMK の有効化。
  - Sentinel にオンボードされた非 CMK ワークスペースの CMK 対応クラスターへのリンク。

- 次の CMK 関連の変更は、不明な動作や問題がある動作を引き起こす可能性があるため、"*サポートされません*"。

  - Microsoft Sentinel に既にオンボードされたワークスペースでの CMK の無効化。
  - Sentinel にオンボードされた CMK 対応ワークスペースを、CMK 対応の専用クラスターとのリンクを解除することで、非 CMK ワークスペースとして設定すること。
  - CMK 対応 Log Analytics 専用クラスター上での CMK の無効化。

- Microsoft Sentinel では、CMK 構成でシステム割り当て ID がサポートされています。 そのため、専用 Log Analytics クラスターの ID の種類は **[システム割り当て済]** である必要があります。 Log Analytics クラスターの作成時に自動的に割り当てられる ID を使用することをお勧めします。

- 現在、別の URI を使用して、カスタマー マネージド キーを別のキーに変更することは "*サポートされていません*"。 キーを変更するには[ローテーション](../azure-monitor/logs/customer-managed-keys.md#key-rotation)を行う必要があります。

- 運用ワークスペースまたは Log Analytics クラスターに CMK の変更を加える前に、[Microsoft Sentinel 製品グループ](mailto:azuresentinelCMK@microsoft.com)にお問い合わせください。

## <a name="how-cmk-works"></a>CMK のしくみ 

Microsoft Sentinel ソリューションでは、ログの収集と機能に複数のストレージ リソースが使用されます (Log Analytics 専用クラスターを含む)。 Microsoft Sentinel CMK 構成の一部として、関連する Log Analytics 専用クラスター上でも CMK 設定を構成する必要があります。 Microsoft Sentinel によって Log Analytics 以外のストレージ リソースに保存されたデータも、専用 Log Analytics クラスターに構成されたカスタマー マネージド キーを使用して暗号化されます。

次のその他の関連ドキュメントを参照してください。
- [Azure Monitor のカスタマー マネージド キー (CMK)](../azure-monitor/logs/customer-managed-keys.md)。
- [Azure Key Vault](../key-vault/general/overview.md)。
- [Log Analytics 専用クラスター](../azure-monitor/logs/logs-dedicated-clusters.md)。

> [!NOTE]
> Microsoft Sentinel で CMK を有効にすると、CMK をサポートしていないパブリック プレビュー機能は有効になりません。

## <a name="enable-cmk"></a>CMK を有効にする 

CMK をプロビジョニングするには、次の手順を実行します。 

1.  Azure Key Vault を作成し、キーを生成するかインポートします。

2.  Log Analytics ワークスペースで CMK を有効にします。

3.  Cosmos DB リソース プロバイダーに登録します。

4.  Azure Key Vault インスタンスにアクセス ポリシーを追加します。

5.  [オンボード API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) を使用してワークスペースを Microsoft Sentinel にオンボードします。

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>手順 1: Azure Key Vault を作成し、キーを生成またはインポートする

1. [Azure Key Vault リソースを作成](/azure-stack/user/azure-stack-key-vault-manage-portal)してから、データの暗号化に使用するキーを生成またはインポートします。

    > [!NOTE]
    >  キーとアクセスを保護するため、Azure Key Vault を回復可能として構成する必要があります。

1.  [回復オプションを有効にする:](../key-vault/general/key-vault-recovery.md)

    -   [[論理的な削除]](../key-vault/general/soft-delete-overview.md) が有効になっていることを確認します。

    -   論理的な削除の後でもシークレットまたはコンテナーの強制削除を防ぐには、[消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)を有効にする必要があります

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>手順 2: Log Analytics ワークスペースで CMK を有効にする

以降の手順で Microsoft Sentinel ワークスペースとして使用する CMK ワークスペースを作成するには、[Azure Monitor のカスタマー マネージド キーの構成](../azure-monitor/logs/customer-managed-keys.md)に関するページの手順に従います。

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>手順 3: Cosmos DB リソース プロバイダーに登録する

Microsoft Sentinel では、追加のストレージ リソースとして Cosmos DB が使用されます。 必ず Cosmos DB リソース プロバイダーに登録します。

Cosmos DB の指示に従って、ご利用の Azure サブスクリプションの [Azure Cosmos DB リソース プロバイダーに登録](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)します。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>手順 4:Azure Key Vault インスタンスにアクセス ポリシーを追加する

Cosmos DB からご使用の Azure Key Vault インスタンスに確実にアクセス権を追加します。 Cosmos DB の指示に従って、Azure Cosmos DB プリンシパルで[ご使用の Azure Key Vault インスタンスにアクセス ポリシーを追加](../cosmos-db/how-to-setup-cmk.md#add-access-policy)します。

### <a name="step-5-onboard-the-workspace-to-microsoft-sentinel-via-the-onboarding-api"></a>手順 5: オンボード API を使用してワークスペースを Microsoft Sentinel にオンボードする

[オンボード API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) を使用してワークスペースを Microsoft Sentinel にオンボードします。

## <a name="key-encryption-key-revocation-or-deletion"></a>キー暗号化キーの失効または削除

ユーザーがキー暗号化キー (CMK) を削除するか、専用クラスターと Cosmos DB リソース プロバイダーのアクセス権を削除して、キー暗号化キーを取り消した場合、Microsoft Sentinel は、1 時間以内に変更を受け入れ、データが使用できなくなった場合と同様に動作します。 この時点で、データ インジェスト、永続的な構成の変更、インシデントの作成など、永続ストレージ リソースを使用する操作はすべて行えなくなります。 以前に格納されたデータは削除されませんが、アクセスできなくなります。 アクセスできないデータは、データ保持ポリシーによって管理され、そのポリシーに従って消去されます。

暗号化キーが失効または削除されてから実行可能な操作は、アカウントの削除だけです。

失効後にアクセスが復元されると、Microsoft Sentinel によって 1 時間以内にデータへのアクセスが復元されます。

データへのアクセス権を取り消すには、専用 Log Analytics クラスターと Cosmos DB の両方について、キー コンテナーのカスタマー マネージド キーを無効にするか、キーのアクセス ポリシーを削除します。 アクセス権を取り消すために、専用 Log Analytics クラスターからキーを削除したり、専用 Log Analytics クラスターに関連付けられた ID を削除したりすることは、サポートされていません。

Azure Monitor でのこの動作の詳細については、[Azure Monitor の CMK の失効](../azure-monitor/logs/customer-managed-keys.md#key-revocation)に関するセクションを参照してください。

## <a name="customer-managed-key-rotation"></a>カスタマー マネージド キーのローテーション

Microsoft Sentinel と Log Analytics では、キーのローテーションがサポートされています。 ユーザーが Key Vault でキーのローテーションを実行すると、Microsoft Sentinel では 1 時間以内に新しいキーがサポートされます。

Key Vault では、キーの新しいバージョンを作成することによってキーのローテーションを実行できます。

![キーのローテーション](./media/customer-managed-keys/key-rotation.png)

キーの以前のバージョンは、24 時間後に無効にするか、または Azure Key Vault の監査ログに以前のバージョンを使用するアクティビティが表示されないようになってから無効にすることができます。

キーをローテーションした後、Log Analytics 内の専用 Log Analytics クラスター リソースを、Azure Key Vault の新しいキー バージョンで明示的に更新する必要があります。 詳細については、[Azure Monitor の CMK のローテーション](../azure-monitor/logs/customer-managed-keys.md#key-rotation)に関するセクションを参照してください。

## <a name="replacing-a-customer-managed-key"></a>カスタマー マネージド キーの置換

Microsoft Sentinel では、カスタマー マネージド キーの置換はサポートされません。 代わりに[キー ローテーション機能](#customer-managed-key-rotation)を使用する必要があります。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Sentinel でカスタマー マネージド キーを設定する方法について説明しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
