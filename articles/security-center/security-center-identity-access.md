---
title: Azure Security Center での ID とアクセスの監視 | Microsoft Docs
description: Azure Security Center の ID とアクセス機能を使用して、ユーザーのアクセス アクティビティと ID 関連の問題を監視する方法を説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: b36f52fd97a9f7ff294f14ec394ff9a9f607b5b0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187155"
---
# <a name="monitor-identity-and-access"></a>ID とアクセスを監視する

セキュリティ境界は、ネットワーク境界から ID 境界へと発展してきました。 このデプロイでは、ネットワーク保護のセキュリティよりも、アプリ、データ、およびユーザーのセキュリティを管理することに重点を置いています。

ID に関連するアクティビティと構成を監視することで、インシデントが発生する前に事前対応型のアクションを実行するか、攻撃が試みられた場合にそれを阻止する事後対応型のアクションを実行することができます。

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Security Center によって提供される ID とアクセスの保護 

Azure Security Center には、組織の ID とセキュリティの要件を確実に満たすための 2 つの専用のセキュリティ コントロールがあります。 

 - **アクセスとアクセス許可の管理** - [最小特権アクセス モデル](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)を採用し、ユーザーが各自の仕事を遂行するために必要なアクセス許可のみを付与することをお勧めします。 このコントロールには、リソースへのアクセスを制御するための [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を実装するための推奨事項も含まれます。
 
 - **MFA の有効化** - [MFA](https://www.microsoft.com/security/business/identity/mfa) を有効にすると、アカウントのセキュリティが強化され、しかもユーザーはそれまでと同様、シングル サインオンでほとんどすべてのアプリケーションに対して認証を行うことができます。

### <a name="example-recommendations-for-identity-and-access"></a>ID とアクセスに対する推奨事項の例

これらの 2 つのコントロールに関して、Security Center の **[推奨事項]** ページに表示される可能性がある推奨事項の例:

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 非推奨のアカウントをサブスクリプションから削除する必要があります (非推奨のアカウントとは不要になったアカウントであり、Azure Active Directory によってサインインがブロックされます)

> [!TIP]
> これらの推奨事項とこれらのコントロールに関して表示されるその他の推奨事項については、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identity)」を参照してください。

### <a name="limitations"></a>制限事項

Security Center の ID とアクセスの保護にはいくつかの制限があります。

- 600 を超えるアカウントを持つサブスクリプションでは、ID に関する推奨事項は利用できません。 このような場合、これらの推奨事項は [利用できない評価] の下に表示されます。
- クラウド ソリューション プロバイダー (CSP) パートナーの管理エージェントでは、ID に関する推奨事項は利用できません。
- ID に関する推奨事項では、特権 ID 管理 (PIM) システムによって管理されているアカウントは識別されません。 PIM ツールを使用している場合、**アクセスとアクセス許可の管理** コントロールに不正確な結果が表示されることがあります。

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>多要素認証 (MFA) と Azure Active Directory 

MFA を有効にするには、[Azure Active Directory (AD) テナントのアクセス許可](../active-directory/roles/permissions-reference.md)が必要です。

- AD の Premium Edition を使用している場合は、[条件付きアクセス](../active-directory/conditional-access/concept-conditional-access-policy-common.md)を使用して MFA を有効にします。
- AD の無料エディションを使用している場合は、[Azure Active Directory のドキュメント](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)の説明に従って、**セキュリティの既定値** を有効にします。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>多要素認証 (MFA) が有効になっていないアカウントを識別する

MFA が有効になっていないアカウントを確認するには、次の Azure Resource Graph クエリを使用します。 このクエリによって、"サブスクリプションの所有者アクセス許可を持つアカウントで MFA を有効にする必要があります" という推奨事項があるすべての異常なリソース (アカウント) が返されます。 

1. **Azure Resource Graph エクスプローラー** を開きます。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. 次のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData` プロパティによって、MFA が適用されていないアカウントのアカウント オブジェクト ID の一覧が表示されます。 

    > [!NOTE]
    > アカウントは、アカウント所有者のプライバシーを保護するために、アカウント名ではなくオブジェクト ID として表示されます。

> [!TIP]
> 別の方法として、Security Center の REST API メソッド [Assessments - Get](/rest/api/securitycenter/assessments/get) を使用できます。


## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次の記事をご覧ください。

- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)