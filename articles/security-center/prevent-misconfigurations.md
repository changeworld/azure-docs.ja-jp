---
title: Azure Security Center で構成ミスを防ぐ方法
description: 推奨事項の詳細ページで Security Center の "適用" オプションおよび "拒否" オプションを使用する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906396"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>適用/拒否の推奨事項を使用した構成ミスの防止

セキュリティ構成ミスは、セキュリティ インシデントの大きな原因です。 Security Center には、特定の推奨事項に関する新しいリソースの構成ミスを "*防止*" する際に役立つ機能が追加されました。 

この機能は、ワークロードの安全性を確保し、ご自身のセキュリティ スコアを安定させるうえで役立ちます。

特定の推奨事項に基づいたセキュリティで保護された構成は、次の 2 つのモードで提供されます。

- Azure Policy の **Deny** 効果を使用して、異常なリソースが作成されるのを防ぐことができます
- **適用**オプションを使用すると、Azure Policy の **DeployIfNotExist** 効果を利用して、作成時に非対応リソースを自動的に修復できます

これは、選択したセキュリティの推奨事項のリソースの詳細ページの上部にあります ([拒否/適用オプションの推奨事項](#recommendations-with-denyenforce-options)に関するページをご覧ください)。

## <a name="prevent-resource-creation"></a>リソースの作成を防止する

1. 新しいリソースが満たす必要のある推奨事項を開き、ページ上部にある **[拒否]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ":::

    構成ウィンドウが開き、スコープ オプションの一覧が表示されます。 

1. 関連するサブスクリプションまたは管理グループを選択して、スコープを設定します。

    > [!TIP]
    > 行の末尾にある 3 つのドットを使用して 1 つのサブスクリプションを変更するか、チェックボックスを使用して複数のサブスクリプションまたはグループを選択し、 **[Change to Deny]\(拒否に変更\)** を選択することができます。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ":::


## <a name="enforce-a-secure-configuration"></a>セキュリティで保護された構成を適用する

1. テンプレート デプロイをデプロイする推奨事項を開き (新しいリソースがそれを満たさない場合)、ページ上部にある **[適用]** ボタンを選択します。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ":::

    構成ウィンドウが開き、すべてのポリシー構成オプションが表示されます。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="[拒否] ボタンが強調表示されている [推奨事項] ページ":::

1. スコープ、割り当て名、およびその他の関連オプションを設定します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

## <a name="recommendations-with-denyenforce-options"></a>拒否/適用オプションを使用した推奨事項

これらの推奨事項は **deny** オプションと共に使用できます。

- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- イベント ハブの名前空間から RootManageSharedAccessKey 以外のすべての承認規則を削除する必要がある
- Service Bus の名前空間から RootManageSharedAccessKey 以外のすべての承認規則を削除する必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- Automation アカウント変数は、暗号化する必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査


次の推奨事項は、**適用**オプションで使用できます。

- Logic Apps で診断ログを有効にする必要がある
- Data Lake Analytics の診断ログを有効にする必要がある
- IoT Hub の診断ログを有効にする必要がある
- Batch アカウントで診断ログを有効にする必要がある
- Azure Stream Analytics で診断ログを有効にする必要がある
- Service Bus で診断ログを有効にする必要がある
- Search サービスにおける診断ログを有効にする必要がある
- イベント ハブの診断ログを有効にする必要がある
- 仮想マシン スケール セットの診断ログを有効にする必要がある
- Key Vault で診断ログを有効にする必要がある
- SQL Server の監査を有効にする必要があります
- Advanced Data Security を、SQL サーバー上で有効にする必要がある



