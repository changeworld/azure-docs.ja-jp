---
title: Azure Security Center で構成ミスを防ぐ方法
description: 推奨事項の詳細ページで Security Center の "適用" オプションおよび "拒否" オプションを使用する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558180"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>適用/拒否の推奨事項を使用した構成ミスの防止

セキュリティ構成ミスは、セキュリティ インシデントの大きな原因です。 Security Center には、特定の推奨事項に関する新しいリソースの構成ミスを "*防止*" する際に役立つ機能が追加されました。 

この機能は、ワークロードの安全性を確保し、ご自身のセキュリティ スコアを安定させるうえで役立ちます。

特定の推奨事項に基づいたセキュリティで保護された構成は、次の 2 つのモードで提供されます。

- Azure Policy の **Deny** 効果を使用して、異常なリソースが作成されるのを防ぐことができます
- **適用** オプションを使用すると、Azure Policy の **DeployIfNotExist** 効果を利用して、作成時に非対応リソースを自動的に修復できます

これは、選択したセキュリティの推奨事項のリソースの詳細ページの上部にあります ([拒否/適用オプションの推奨事項](#recommendations-with-denyenforce-options)に関するページをご覧ください)。

## <a name="prevent-resource-creation"></a>リソースの作成を防止する

1. 新しいリソースが満たす必要のある推奨事項を開き、ページ上部にある **[拒否]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ":::

    構成ウィンドウが開き、スコープ オプションの一覧が表示されます。 

1. 関連するサブスクリプションまたは管理グループを選択して、スコープを設定します。

    > [!TIP]
    > 行の末尾にある 3 つのドットを使用して 1 つのサブスクリプションを変更するか、チェックボックスを使用して複数のサブスクリプションまたはグループを選択し、 **[Change to Deny]\(拒否に変更\)** を選択することができます。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy 拒否のスコープの設定":::


## <a name="enforce-a-secure-configuration"></a>セキュリティで保護された構成を適用する

1. テンプレート デプロイをデプロイする推奨事項を開き (新しいリソースがそれを満たさない場合)、ページ上部にある **[適用]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="[適用] ボタンが強調表示されている [推奨事項] ページ":::

    構成ウィンドウが開き、すべてのポリシー構成オプションが表示されます。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="構成オプションの適用":::

1. スコープ、割り当て名、およびその他の関連オプションを設定します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

## <a name="recommendations-with-denyenforce-options"></a>拒否/適用オプションを使用した推奨事項

これらの推奨事項は **deny** オプションと共に使用できます。

- ファイアウォールと仮想ネットワークの構成があるストレージ アカウントへのアクセスを制限する必要がある
- Azure Cache for Redis は仮想ネットワーク内に存在しなければならない
- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Azure Spring Cloud でネットワークの挿入を使用する必要がある
- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある
- コンテナーの CPU とメモリの制限を強制する必要がある
- コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある
- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- 特権エスカレーションを含むコンテナーは避ける必要がある
- 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある
- コンテナーは許可されたポートでのみリッスンする必要がある
- コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある
- Key Vault キーには有効期限が必要である
- Key Vault シークレットには有効期限が必要である
- キー コンテナーで消去保護が有効になっている必要がある
- キー コンテナーで論理的な削除が有効になっている必要がある
- コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある
- 特権コンテナーの使用を避ける
- コンテナーをルート ユーザーとして実行しない
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- サービスは許可されたポートでのみリッスンする必要がある
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- ストレージ アカウントは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある
- ホスト ネットワークとポートの使用を制限する必要がある
- ポッドの HostPath ボリューム マウントの使用を既知のリストに制限して、侵害されたコンテナーからのノード アクセスを制限する必要がある
- Azure Key Vault に保存されている証明書の有効期間は 12 か月以内にする必要がある
- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある
- Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある
- Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある

次の推奨事項は、**適用** オプションで使用できます。

- SQL Server の監査を有効にする必要があります
- 仮想マシンに対して Azure Backup を有効にする必要がある
- お使いの SQL サーバーで Azure Defender for SQL を有効にする必要があります
- Azure Stream Analytics で診断ログを有効にする必要がある
- Batch アカウントで診断ログを有効にする必要がある
- Data Lake Analytics の診断ログを有効にする必要がある
- イベント ハブの診断ログを有効にする必要がある
- Key Vault で診断ログを有効にする必要がある
- Logic Apps で診断ログを有効にする必要がある
- Search サービスにおける診断ログを有効にする必要がある
- Service Bus で診断ログを有効にする必要がある