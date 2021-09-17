---
title: Azure Security Center で構成ミスを防ぐ方法
description: 推奨事項の詳細ページで Security Center の "適用" オプションおよび "拒否" オプションを使用する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/17/2021
ms.author: memildin
ms.openlocfilehash: 4316a53675a8a7e51359b041143e8625e1db4f1f
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606141"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>適用/拒否の推奨事項を使用した構成ミスの防止

セキュリティ構成ミスは、セキュリティ インシデントの大きな原因です。 Azure Security Center は、特定の推奨事項に関する新しいリソースの構成ミスを "*防止*" する際に役立ちます。 

この機能は、ワークロードの安全性を確保し、ご自身のセキュリティ スコアを安定させるうえで役立ちます。

特定の推奨事項に基づいたセキュリティで保護された構成は、次の 2 つのモードで提供されます。

- Azure Policy の **Deny** 効果を使用して、異常なリソースが作成されるのを防ぐことができます
- **適用** オプションを使用すると、Azure Policy の **DeployIfNotExist** 効果を利用して、作成時に非対応リソースを自動的に修復できます

これは、選択したセキュリティの推奨事項のリソースの詳細ページの上部にあります ([拒否/適用オプションの推奨事項](#recommendations-with-denyenforce-options)に関するページをご覧ください)。

## <a name="prevent-resource-creation"></a>リソースの作成を防止する

1. 新しいリソースが満たす必要のある推奨事項を開き、ページ上部にある **[拒否]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ。":::

    構成ウィンドウが開き、スコープ オプションの一覧が表示されます。 

1. 関連するサブスクリプションまたは管理グループを選択して、スコープを設定します。

    > [!TIP]
    > 行の末尾にある 3 つのドットを使用して 1 つのサブスクリプションを変更するか、チェックボックスを使用して複数のサブスクリプションまたはグループを選択し、 **[Change to Deny]\(拒否に変更\)** を選択することができます。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy 拒否のスコープの設定。":::


## <a name="enforce-a-secure-configuration"></a>セキュリティで保護された構成を適用する

1. テンプレート デプロイをデプロイする推奨事項を開き (新しいリソースがそれを満たさない場合)、ページ上部にある **[適用]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="[適用] ボタンが強調表示されている [推奨事項] ページ。":::

    構成ウィンドウが開き、すべてのポリシー構成オプションが表示されます。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="構成オプションの適用。":::

1. スコープ、割り当て名、およびその他の関連オプションを設定します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

## <a name="recommendations-with-denyenforce-options"></a>拒否/適用オプションを使用した推奨事項

これらの推奨事項は **deny** オプションと共に使用できます。

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

次の推奨事項は、**適用** オプションで使用できます。

- SQL Server の監査を有効にする必要があります
- Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある
- 仮想マシンに対して Azure Backup を有効にする必要がある
- Azure Defender for App Service を有効にする必要がある
- Azure Defender for container registries を有効にする必要がある
- Azure Defender for DNS を有効にする必要がある
- Azure Defender for Key Vault を有効にする必要がある
- Azure Defender for Kubernetes を有効にする必要がある
- Azure Defender for Resource Manager を有効にする必要がある
- Azure Defender for servers を有効にする必要がある
- Azure Defender for Azure SQL Database servers を有効にする必要がある
- Azure Defender for SQL servers on machines を有効にする必要がある
- 保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある
- Azure Defender for Storage を有効にする必要がある
- Kubernetes 用の Azure Policy アドオンをクラスターにインストールして有効にする必要がある
- Azure Stream Analytics で診断ログを有効にする必要がある
- Batch アカウントで診断ログを有効にする必要がある
- Data Lake Analytics の診断ログを有効にする必要がある
- イベント ハブの診断ログを有効にする必要がある
- Key Vault で診断ログを有効にする必要がある
- Logic Apps で診断ログを有効にする必要がある
- Search サービスにおける診断ログを有効にする必要がある
- Service Bus で診断ログを有効にする必要がある