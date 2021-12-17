---
title: NIST SP 800-53 Rev. 4 の規制コンプライアンスの詳細
description: NIST SP 800-53 Rev. 4 規制コンプライアンスの組み込みイニシアチブの詳細。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 09/17/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 27fbe443a5e93181005d15a58c58f42ccba9cec4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620618"
---
# <a name="details-of-the-nist-sp-800-53-rev-4-regulatory-compliance-built-in-initiative"></a>NIST SP 800-53 Rev. 4 規制コンプライアンスの組み込みイニシアチブの詳細

この記事では、Azure Policy 規制コンプライアンスの組み込みイニシアチブの定義が、NIST SP 800-53 Rev. 4 の **コンプライアンス ドメイン** と **コントロール** にどのように対応するのかについて詳しく説明します。
このコンプライアンス標準の詳細については、[NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53) に関するページを参照してください。 "_所有権_" については、[Azure Policy のポリシー定義](../concepts/definition-structure.md#type)に関するページと、「[クラウドにおける共同責任](../../../security/fundamentals/shared-responsibility.md)」を参照してください。

以下のマッピングは、**NIST SP 800-53 Rev. 4** コントロールに対するものです。 右側のナビゲーションを使用すると、特定の **コンプライアンス ドメイン** に直接移動します。 コントロールの多くは、[Azure Policy](../overview.md) のイニシアチブ定義で実装されています。 完全なイニシアチブ定義を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。
次に、 **[NIST SP 800-53 Rev. 4]** 規制コンプライアンスの組み込みイニシアチブ定義を見つけて選択します。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../overview.md) 定義に関連します。
> これらのポリシーは、コントロールに対する[コンプライアンスの評価](../how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** では、ポリシー定義自体のみが示されています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス標準に対するコンプライアンス ドメイン、コントロール、Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)に関するページを参照してください。

## <a name="access-control"></a>アクセス制御

### <a name="access-control-policy-and-procedures"></a>アクセスの制御ポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 AC-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1000 - アクセスの制御のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft マネージド コントロール 1001 - アクセスの制御のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>アカウント管理

**ID**: NIST SP 800-53 Rev. 4 AC-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[非推奨のアカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |非推奨のアカウントは、サブスクリプションから削除する必要があります。  非推奨のアカウントは、サインインがブロックされているアカウントです。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |所有者としてのアクセス許可を持つ非推奨のアカウントは、サブスクリプションから削除する必要があります。  非推奨のアカウントは、サインインがブロックされているアカウントです。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |監視されていないアクセスを防止するために、所有者アクセス許可を持つ外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |監視されていないアクセスを防止するために、読み取り権限がある外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |監視されていないアクセスを防止するために、書き込み権限がある外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[API アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[関数アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft マネージド コントロール 1002 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft マネージド コントロール 1003 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft マネージド コントロール 1004 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft マネージド コントロール 1005 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft マネージド コントロール 1006 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft マネージド コントロール 1007 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft マネージド コントロール 1008 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft マネージド コントロール 1009 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft マネージド コントロール 1010 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft マネージド コントロール 1011 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft マネージド コントロール 1012 - アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="automated-system-account-management"></a>システム アカウント管理の自動化

**ID**: NIST SP 800-53 Rev. 4 AC-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft マネージド コントロール 1013 - アカウント管理 \| 自動システム アカウント管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="removal-of-temporary--emergency-accounts"></a>仮のアカウント/緊急アカウントの削除

**ID**: NIST SP 800-53 Rev. 4 AC-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1014 - アカウント管理 \| 一時的/緊急アカウントの削除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="disable-inactive-accounts"></a>アクティブでないアカウントの無効化

**ID**: NIST SP 800-53 Rev. 4 AC-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1015 - アカウント管理 \| 非アクティブなアカウントの無効化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="automated-audit-actions"></a>監査処理の自動化

**ID**: NIST SP 800-53 Rev. 4 AC-2 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1016 - アカウント管理 \| 自動監査アクション](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="inactivity-logout"></a>非アクティブな状態のログアウト

**ID**: NIST SP 800-53 Rev. 4 AC-2 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1017 - アカウント管理 \| 非アクティブ ログアウト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="role-based-schemes"></a>ロールベースのスキーム

**ID**: NIST SP 800-53 Rev. 4 AC-2 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft マネージド コントロール 1018 - アカウント管理 \| ロールベースのスキーム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft マネージド コントロール 1019 - アカウント管理 \| ロールベースのスキーム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft マネージド コントロール 1020 - アカウント管理 \| ロールベースのスキーム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[サブスクリプションを保護するために、管理証明書ではなくサービス プリンシパルを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |管理証明書を使用すると、それを使用して認証するすべてのユーザーが、証明書に関連付けられているサブスクリプションを管理できます。 サブスクリプションをより安全に管理するには、証明書の侵害の影響を限定するため、Resource Manager でサービス プリンシパルを使用することをお勧めします。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="restrictions-on-use-of-shared--group-accounts"></a>共有/グループ アカウントの使用に関する制限

**ID**: NIST SP 800-53 Rev. 4 AC-2 (9)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1021 - アカウント管理 \| 共有/グループ アカウントの使用に対する制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="shared--group-account-credential-termination"></a>共有/グループ アカウントの資格情報の終了

**ID**: NIST SP 800-53 Rev. 4 AC-2 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1022 - アカウント管理 \| 共有/グループ アカウントの資格情報の終了](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="usage-conditions"></a>使用条件

**ID**: NIST SP 800-53 Rev. 4 AC-2 (11)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1023 - アカウント管理 \| 使用条件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-monitoring--atypical-usage"></a>アカウントの監視/一般的でない使用法

**ID**: NIST SP 800-53 Rev. 4 AC-2 (12)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない SQL Managed Instance に対して、Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft マネージド コントロール 1024 - アカウント管理 \| アカウントの監視/特殊な使用方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft マネージド コントロール 1025 - アカウント管理 \| アカウントの監視/特殊な使用方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="disable-accounts-for-high-risk-individuals"></a>危険性の高い個人のアカウントの無効化

**ID**: NIST SP 800-53 Rev. 4 AC-2 (13)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1026 - アカウント管理 \| 危険度の高い個人のアカウントの無効化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>アクセスの適用

**ID**: NIST SP 800-53 Rev. 4 AC-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされているものの、マネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[ユーザー割り当て ID を持つ VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされており、少なくとも 1 つのユーザー割り当て ID を持っているものの、システム割り当てマネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[パスワードなしのアカウントが存在する Linux マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードなしのアカウントがある Linux マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Linux マシンに対する認証では SSH キーを要求する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |SSH 自体は暗号化された接続を提供しますが、SSH でパスワードを使用すると、VM はブルートフォース攻撃に対して脆弱になります。 Azure Linux 仮想マシンに対して SSH による認証を行うための最も安全な方法は、公開キー/秘密キー ペア (SSH キーとも呼ばれます) を使用することです。 詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Linux VM でゲスト構成の割り当てを有効にするために Linux ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Linux 仮想マシンに Linux ゲスト構成拡張機能がデプロイされます。 Linux ゲスト構成拡張機能は、すべての Linux ゲスト構成割り当ての前提条件であるため、Linux ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[API アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[関数アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft マネージド コントロール 1027 - アクセスの適用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |新しい Azure Resource Manager をお使いのストレージ アカウントに使用して、セキュリティの拡張機能を提供します。たとえば、アクセス制御の強化 (RBAC)、監査の改善、Azure Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレット取得のための Key Vault へのアクセス、Azure AD ベースの認証、セキュリティ管理を容易にするタグとリソース グループのサポートがあります |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
|[仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |ご使用の仮想マシンに新しい Azure Resource Manager を使用して、セキュリティを強化します。これには、アクセス制御の強化 (RBAC)、監査の改善、Azure Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレット取得のためのキー コンテナーへのアクセス、Azure AD に基づく認証、セキュリティ管理を容易にするタグとリソース グループのサポートなどがあります |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

**ID**: NIST SP 800-53 Rev. 4 AC-3 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services でロールベースのアクセス制御 (RBAC) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |ユーザーが実行できるアクションに関して詳細なフィルター処理を行うために、ロールベースのアクセス制御 (RBAC) を使用して、Kubernetes Service クラスター内のアクセス許可を管理し、関連する承認ポリシーを構成します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="information-flow-enforcement"></a>情報フローの適用

**ID**: NIST SP 800-53 Rev. 4 AC-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |一部のサブネットが次世代のファイアウォールで保護されていないことを Azure Security Center で確認しました。 Azure Firewall またはサポートされている次世代のファイアウォールを使用してアクセスを制限することにより、潜在的な脅威からサブネットを保護してください |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center により、お使いのネットワーク セキュリティ グループの一部の受信規則について制限が少なすぎると判断されました。 受信規則では、"Any" または "Internet" の範囲からのアクセスを許可しないでください。 これにより、攻撃者がお使いのリソースをターゲットにできる可能性があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management サービスには仮想ネットワークが使用されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network のデプロイにより、セキュリティが強化され、分離が行われ、インターネット ルーティングできないネットワークに API Management サービスを配置して、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用してオンプレミス ネットワークに接続できます。これにより、ネットワークやオンプレミス内のバックエンド サービスにアクセスできるようになります。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[Kubernetes Services では、許可する IP の範囲を定義する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |Kubernetes Service Management API へのアクセスは、特定の範囲の IP にのみ API アクセスに許可して制限する必要があります。 許可されたネットワークのアプリケーションのみがクラスターにアクセスできるよう、許可する IP 範囲にアクセスを制限することをお勧めします。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスではパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Azure Cognitive Search サービスが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成すると、Search サービスの露出を制限できます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search サービスはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Cognitive Search にマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB のアカウントにはファイアウォール規則を含める必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |承認されていないソースからのトラフィックを防ぐために、Azure Cosmos DB アカウントに対してファイアウォール規則を定義する必要があります。 仮想ネットワーク フィルターを有効にして定義されている IP 規則が少なくとも 1 つあるアカウントは、準拠していると見なされます。 パブリック アクセスを無効にしているアカウントも、準拠していると見なされます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory にはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Data Factory にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid ドメインではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid ドメインにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid トピックではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid トピックにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault should disable public network access (Azure Key Vault で公衆ネットワーク アクセスを無効にする必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |キー コンテナーの公衆ネットワーク アクセスを無効にして、パブリック インターネット経由でアクセスできないようにします。 これにより、データ漏えいのリスクを軽減することができます。 詳細については、[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md) を参照してください。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Machine Learning ワークスペースにマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Service Bus 名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Azure SignalR Service リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが減ります。 プライベート リンクの詳細については、[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md) を参照してください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Synapse ワークスペースにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub サービスではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Web PubSub サービスにマッピングすると、データ漏えいのリスクを軽減できます。 プライベート リンクの詳細については、[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services アカウントでパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Cognitive Services アカウントが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成することで、Cognitive Services アカウントの露出を制限できます。 詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。  |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services アカウントでネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみが Cognitive Services アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Cognitive Services にマッピングすることにより、データ漏えいの可能性を減らすことができます。 プライベート リンクの詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 潜在的な脅威からレジストリを保護するには、特定のパブリック IP アドレスまたはアドレス範囲のみからのアクセスを許可します。 レジストリに IP またはファイアウォール規則、あるいは構成済みの仮想ネットワークがない場合は、異常なリソースとして表示されます。 Container Registry ネットワーク規則の詳細については、[https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) と [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |クロス オリジン リソース共有 (CORS) で、Web アプリケーションへのアクセスをすべてのドメインには許可しないでください。 Web アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[CosmosDB アカウントでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 CosmosDB アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[ディスク アクセス リソースにはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをディスク アクセスにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[イベント ハブ名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをイベント ハブ名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub Device Provisioning Service インスタンスでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを IoT Hub Device Provisioning Service にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[仮想マシン上の IP 転送を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |仮想マシンの NIC で IP 転送を有効にすると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft マネージド コントロール 1028 - 情報フローの適用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |
|[インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、インターネットに接続されていない仮想マシンを潜在的な脅威から保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |プライベート エンドポイント接続は、Azure SQL Database へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault 用にプライベート エンドポイントを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |プライベート リンクを使用すると、パブリック インターネット経由でトラフィックを送信せずに、Key Vault を Azure リソースに接続できます。 プライベート リンクにより、データ流出に対する徹底的な防御が提供されます。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |プライベート エンドポイント接続は、Azure Database for MariaDB へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database のパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |パブリック ネットワーク アクセス プロパティを無効にすると、Azure SQL Database へのアクセスがプライベート エンドポイントのみに制限されるため、セキュリティが強化されます。 この構成により、IP または仮想ネットワーク ベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MariaDB にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MySQL にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |パブリック ネットワーク アクセス プロパティを無効にしてセキュリティを強化し、プライベート エンドポイントからのみ Azure Database for PostgreSQL にアクセスできるようにします。 この構成では、Azure IP 範囲外のパブリック アドレス空間からのアクセスが無効になり、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[ストレージ アカウントのパブリック アクセスを禁止する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合があります。 好ましくない匿名アクセスによるデータ侵害を防ぐために、Microsoft では、シナリオで必要でない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 |audit、deny、disabled |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[ストレージ アカウントは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP ベースのフィルター処理の代わりに、推奨される方法として仮想ネットワーク規則を使用して、ストレージ アカウントを潜在的な脅威から保護します。 IP ベースのフィルター処理を無効にすると、パブリック IP がストレージ アカウントにアクセスできなくなります。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[ストレージ アカウントではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 ストレージ アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 プライベート リンクの詳細については、[https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 VM Image Builder の構築リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet) を参照してください。 |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="dynamic-information-flow-control"></a>動的な情報フローの制御

**ID**: NIST SP 800-53 Rev. 4 AC-4 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="security-policy-filters"></a>セキュリティ ポリシーのフィルター

**ID**: NIST SP 800-53 Rev. 4 AC-4 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1029 - 情報フローの適用 \| セキュリティ ポリシー フィルター](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="physical--logical-separation-of-information-flows"></a>情報フローの物理的/論理的分離

**ID**: NIST SP 800-53 Rev. 4 AC-4 (21)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1030 - 情報フローの適用 \| 情報フローの物理的/論理的な分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>職務の分離

**ID**: NIST SP 800-53 Rev. 4 AC-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1031 - 職務の分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft マネージド コントロール 1032 - 職務の分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft マネージド コントロール 1033 - 職務の分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[複数の所有者がサブスクリプションに割り当てられている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |管理者アクセスの冗長性を確保するため、複数のサブスクリプション所有者を指定することをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>最小限の特権

**ID**: NIST SP 800-53 Rev. 4 AC-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft マネージド コントロール 1034 - 最小限の特権](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="authorize-access-to-security-functions"></a>セキュリティ機能へのアクセスの認可

**ID**: NIST SP 800-53 Rev. 4 AC-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1035 - 最小限の特権 \| セキュリティ機能へのアクセスの承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="non-privileged-access-for-nonsecurity-functions"></a>セキュリティ以外の機能に対する非特権アクセス

**ID**: NIST SP 800-53 Rev. 4 AC-6 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1036 - 最小限の特権 \| セキュリティ以外の機能に対する特権なしのアクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="network-access-to-privileged-commands"></a>特権コマンドへのネットワーク アクセス

**ID**: NIST SP 800-53 Rev. 4 AC-6 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1037 - 最小限の特権 \| 特権のあるコマンドに対するネットワーク アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="privileged-accounts"></a>特権アカウント

**ID**: NIST SP 800-53 Rev. 4 AC-6 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1038 - 最小限の特権 \| 特権アカウント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="review-of-user-privileges"></a>ユーザー特権のレビュー

**ID**: NIST SP 800-53 Rev. 4 AC-6 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft マネージド コントロール 1039 - 最小限の特権 \| ユーザー特権の確認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft マネージド コントロール 1040 - 最小限の特権 \| ユーザー特権の確認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |

### <a name="privilege-levels-for-code-execution"></a>コード実行の特権レベル

**ID**: NIST SP 800-53 Rev. 4 AC-6 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1041 - 最小限の特権 \| コード実行の特権レベル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="auditing-use-of-privileged-functions"></a>特権機能の使用の監査

**ID**: NIST SP 800-53 Rev. 4 AC-6 (9)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1042 - 最小限の特権 \| 特権のある機能の使用の監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="prohibit-non-privileged-users-from-executing-privileged-functions"></a>非特権ユーザーによる特権機能の実行禁止

**ID**: NIST SP 800-53 Rev. 4 AC-6 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1043 - 最小限の特権 \| 特権のないユーザーに対する特権のある機能の実行の禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>ログオン試行の失敗

**ID**: NIST SP 800-53 Rev. 4 AC-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1044 - ログオン試行の失敗](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft マネージド コントロール 1045 - ログオン試行の失敗](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="purge--wipe-mobile-device"></a>モバイル デバイスの消去/ワイプ

**ID**: NIST SP 800-53 Rev. 4 AC-7 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1046 - アカウントの自動ロック \| モバイル デバイスの消去またはワイプ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>システム使用通知

**ID**: NIST SP 800-53 Rev. 4 AC-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1047 - システム使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft マネージド コントロール 1048 - システム使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft マネージド コントロール 1049 - システム使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>同時セッション制御

**ID**: NIST SP 800-53 Rev. 4 AC-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1050 - 同時セッション制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>セッションのロック

**ID**: NIST SP 800-53 Rev. 4 AC-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1051 - セッションのロック](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft マネージド コントロール 1052 - セッションのロック](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="pattern-hiding-displays"></a>パターン非表示ディスプレイ

**ID**: NIST SP 800-53 Rev. 4 AC-11 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1053 - セッションのロック \| パターン非表示ディスプレイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>セッションの終了

**ID**: NIST SP 800-53 Rev. 4 AC-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1054 - セッションの終了](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="user-initiated-logouts--message-displays"></a>ユーザーによって開始されたログアウト/メッセージの表示

**ID**: NIST SP 800-53 Rev. 4 AC-12 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1055 - セッションの終了 \| ユーザーが開始したログアウト/メッセージの表示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft マネージド コントロール 1056 - セッションの終了 \| ユーザーが開始したログアウト/メッセージの表示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>ID または認証なしで許可されるアクション

**ID**: NIST SP 800-53 Rev. 4 AC-14

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1057 - ID または認証なしで許可されるアクション](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft マネージド コントロール 1058 - ID または認証なしで許可されるアクション](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>セキュリティ属性

**ID**: NIST SP 800-53 Rev. 4 AC-16

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>リモート アクセス

**ID**: NIST SP 800-53 Rev. 4 AC-17

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされているものの、マネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[ユーザー割り当て ID を持つ VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされており、少なくとも 1 つのユーザー割り当て ID を持っているものの、システム割り当てマネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[パスワードなしのアカウントからのリモート接続が許可されている Linux マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードなしのアカウントからのリモート接続が許可されている Linux マシンは非準拠となります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network のデプロイでは、Azure Cache for Redis のためにセキュリティと分離が強化され、アクセスをさらに制限するためのサブネットやアクセス制御ポリシーなどの機能が提供されます。Azure Cache for Redis インスタンスが仮想ネットワークで構成されている場合、パブリック アドレスの指定ができず、仮想ネットワーク内の仮想マシンとアプリケーションからのみアクセスできます。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Cognitive Search にマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Data Factory にはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Data Factory にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid ドメインではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid ドメインにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid トピックではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid トピックにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Machine Learning ワークスペースにマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Service Bus 名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Azure SignalR Service リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが減ります。 プライベート リンクの詳細については、[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md) を参照してください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud でネットワークの挿入を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud インスタンスでは、次の目的で仮想ネットワークの挿入を使用する必要があります。1. Azure Spring Cloud をインターネットから分離する。 2. オンプレミスのデータ センター内のシステム、または他の仮想ネットワーク内の Azure サービスとの Azure Spring Cloud のやりとりを有効化する。 3. Azure Spring Cloud の送受信ネットワーク通信を制御するための権限を顧客に付与する。 |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Synapse ワークスペースにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub サービスではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Web PubSub サービスにマッピングすると、データ漏えいのリスクを軽減できます。 プライベート リンクの詳細については、[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Cognitive Services にマッピングすることにより、データ漏えいの可能性を減らすことができます。 プライベート リンクの詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB アカウントでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 CosmosDB アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[Linux VM でゲスト構成の割り当てを有効にするために Linux ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Linux 仮想マシンに Linux ゲスト構成拡張機能がデプロイされます。 Linux ゲスト構成拡張機能は、すべての Linux ゲスト構成割り当ての前提条件であるため、Linux ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM でゲスト構成の割り当てを有効にするために Windows ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Windows 仮想マシンに Windows ゲスト構成拡張機能がデプロイされます。 Windows ゲスト構成拡張機能は、すべての Windows ゲスト構成割り当ての前提条件であるため、Windows ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[ディスク アクセス リソースにはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをディスク アクセスにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[イベント ハブ名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをイベント ハブ名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT Hub Device Provisioning Service インスタンスでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを IoT Hub Device Provisioning Service にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft マネージド コントロール 1059 - リモート アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft マネージド コントロール 1060 - リモート アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |
|[Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |プライベート エンドポイント接続は、Azure SQL Database へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault 用にプライベート エンドポイントを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |プライベート リンクを使用すると、パブリック インターネット経由でトラフィックを送信せずに、Key Vault を Azure リソースに接続できます。 プライベート リンクにより、データ流出に対する徹底的な防御が提供されます。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |プライベート エンドポイント接続は、Azure Database for MariaDB へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[API アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |リモート デバッグを実行するには、受信ポートが API アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Web アプリケーションのリモート デバッグを無効にする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |リモート デバッグを実行するには、受信ポートが Web アプリケーション上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[ストレージ アカウントではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 ストレージ アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 プライベート リンクの詳細については、[https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 VM Image Builder の構築リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet) を参照してください。 |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="automated-monitoring--control"></a>監視または制御の自動化

**ID**: NIST SP 800-53 Rev. 4 AC-17 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされているものの、マネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[ユーザー割り当て ID を持つ VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされており、少なくとも 1 つのユーザー割り当て ID を持っているものの、システム割り当てマネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[パスワードなしのアカウントからのリモート接続が許可されている Linux マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードなしのアカウントからのリモート接続が許可されている Linux マシンは非準拠となります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network のデプロイでは、Azure Cache for Redis のためにセキュリティと分離が強化され、アクセスをさらに制限するためのサブネットやアクセス制御ポリシーなどの機能が提供されます。Azure Cache for Redis インスタンスが仮想ネットワークで構成されている場合、パブリック アドレスの指定ができず、仮想ネットワーク内の仮想マシンとアプリケーションからのみアクセスできます。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Cognitive Search にマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Data Factory にはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Data Factory にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid ドメインではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid ドメインにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid トピックではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid トピックにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Machine Learning ワークスペースにマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Service Bus 名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Azure SignalR Service リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが減ります。 プライベート リンクの詳細については、[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md) を参照してください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud でネットワークの挿入を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud インスタンスでは、次の目的で仮想ネットワークの挿入を使用する必要があります。1. Azure Spring Cloud をインターネットから分離する。 2. オンプレミスのデータ センター内のシステム、または他の仮想ネットワーク内の Azure サービスとの Azure Spring Cloud のやりとりを有効化する。 3. Azure Spring Cloud の送受信ネットワーク通信を制御するための権限を顧客に付与する。 |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Synapse ワークスペースにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub サービスではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Web PubSub サービスにマッピングすると、データ漏えいのリスクを軽減できます。 プライベート リンクの詳細については、[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Cognitive Services にマッピングすることにより、データ漏えいの可能性を減らすことができます。 プライベート リンクの詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB アカウントでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 CosmosDB アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[Linux VM でゲスト構成の割り当てを有効にするために Linux ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Linux 仮想マシンに Linux ゲスト構成拡張機能がデプロイされます。 Linux ゲスト構成拡張機能は、すべての Linux ゲスト構成割り当ての前提条件であるため、Linux ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM でゲスト構成の割り当てを有効にするために Windows ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Windows 仮想マシンに Windows ゲスト構成拡張機能がデプロイされます。 Windows ゲスト構成拡張機能は、すべての Windows ゲスト構成割り当ての前提条件であるため、Windows ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[ディスク アクセス リソースにはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをディスク アクセスにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[イベント ハブ名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをイベント ハブ名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT Hub Device Provisioning Service インスタンスでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを IoT Hub Device Provisioning Service にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft マネージド コントロール 1061 - リモート アクセス \| 自動監視/制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |プライベート エンドポイント接続は、Azure SQL Database へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault 用にプライベート エンドポイントを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |プライベート リンクを使用すると、パブリック インターネット経由でトラフィックを送信せずに、Key Vault を Azure リソースに接続できます。 プライベート リンクにより、データ流出に対する徹底的な防御が提供されます。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |プライベート エンドポイント接続は、Azure Database for MariaDB へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[API アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |リモート デバッグを実行するには、受信ポートが API アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Web アプリケーションのリモート デバッグを無効にする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |リモート デバッグを実行するには、受信ポートが Web アプリケーション上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[ストレージ アカウントではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 ストレージ アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 プライベート リンクの詳細については、[https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 VM Image Builder の構築リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet) を参照してください。 |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="protection-of-confidentiality--integrity-using-encryption"></a>暗号化を使用した機密性/完全性の保護

**ID**: NIST SP 800-53 Rev. 4 AC-17 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1062 - リモート アクセス \| 暗号化を使用した機密性/整合性の保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="managed-access-control-points"></a>マネージド Access Control ポイント

**ID**: NIST SP 800-53 Rev. 4 AC-17 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1063 - リモート アクセス \| 管理対象のアクセス制御ポイント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="privileged-commands--access"></a>特権のあるコマンド/アクセス

**ID**: NIST SP 800-53 Rev. 4 AC-17 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1064 - リモート アクセス \| 特権のあるコマンド/アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft マネージド コントロール 1065 - リモート アクセス \| 特権のあるコマンド/アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="disconnect--disable-access"></a>アクセスの切断/無効化

**ID**: NIST SP 800-53 Rev. 4 AC-17 (9)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1066 - リモート アクセス \| アクセスの切断/無効化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>ワイヤレス アクセス

**ID**: NIST SP 800-53 Rev. 4 AC-18

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1067 - ワイヤレス アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft マネージド コントロール 1068 - ワイヤレス アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="authentication-and-encryption"></a>認証と暗号化

**ID**: NIST SP 800-53 Rev. 4 AC-18 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1069 - ワイヤレス アクセス \| 認証と暗号化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="disable-wireless-networking"></a>ワイヤレス ネットワークの無効化

**ID**: NIST SP 800-53 Rev. 4 AC-18 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1070 - ワイヤレス アクセス \| ワイヤレス ネットワーキングの無効化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="restrict-configurations-by-users"></a>ユーザーによる構成の制限

**ID**: NIST SP 800-53 Rev. 4 AC-18 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1071 - ワイヤレス アクセス \| ユーザーによる構成の制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="antennas--transmission-power-levels"></a>アンテナ/送信電力レベル

**ID**: NIST SP 800-53 Rev. 4 AC-18 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1072 - ワイヤレス アクセス \| アンテナ/送電レベル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>モバイル デバイスのアクセスの制御

**ID**: NIST SP 800-53 Rev. 4 AC-19

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1073 - モバイル デバイスのアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft マネージド コントロール 1074 - モバイル デバイスのアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="full-device--container-based-encryption"></a>フル デバイス/コンテナーベースの暗号化

**ID**: NIST SP 800-53 Rev. 4 AC-19 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1075 - モバイル デバイスのアクセスの制御 \| 完全なデバイス/コンテナーベースの暗号化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>外部情報システムの使用

**ID**: NIST SP 800-53 Rev. 4 AC-20

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1076 - 外部情報システムの使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft マネージド コントロール 1077 - 外部情報システムの使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="limits-on-authorized-use"></a>許可された使用に関する制限

**ID**: NIST SP 800-53 Rev. 4 AC-20 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1078 - 外部情報システムの使用 \| 権限を持つユーザーの使用の制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft マネージド コントロール 1079 - 外部情報システムの使用 \| 権限を持つユーザーの使用の制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="portable-storage-devices"></a>ポータブル ストレージ デバイス

**ID**: NIST SP 800-53 Rev. 4 AC-20 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1080 - 外部情報システムの使用 \| ポータブル ストレージ デバイス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>情報共有

**ID**: NIST SP 800-53 Rev. 4 AC-21

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1081 - 情報共有](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft マネージド コントロール 1082 - 情報共有](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>パブリックにアクセスできるコンテンツ

**ID**: NIST SP 800-53 Rev. 4 AC-22

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1083 - パブリックにアクセスできるコンテンツ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft マネージド コントロール 1084 - パブリックにアクセスできるコンテンツ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft マネージド コントロール 1085 - パブリックにアクセスできるコンテンツ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft マネージド コントロール 1086 - パブリックにアクセスできるコンテンツ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |このアクセスの制御コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>認識とトレーニング

### <a name="security-awareness-and-training-policy-and-procedures"></a>セキュリティの意識向上とトレーニングに関するポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 AT-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1087 - セキュリティの意識向上とトレーニングに関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft マネージド コントロール 1088 - セキュリティの意識向上とトレーニングに関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>セキュリティ意識向上トレーニング

**ID**: NIST SP 800-53 Rev. 4 AT-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1089 - セキュリティ意識向上トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft マネージド コントロール 1090 - セキュリティ意識向上トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft マネージド コントロール 1091 - セキュリティ意識向上トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="insider-threat"></a>内部関係者による脅威

**ID**: NIST SP 800-53 Rev. 4 AT-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1092 - セキュリティ意識向上トレーニング \| 内部関係者の脅威](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>ロールベースのセキュリティ トレーニング

**ID**: NIST SP 800-53 Rev. 4 AT-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1093 - ロールベースのセキュリティ トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft マネージド コントロール 1094 - ロールベースのセキュリティ トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft マネージド コントロール 1095 - ロールベースのセキュリティ トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="practical-exercises"></a>実践的な演習

**ID**: NIST SP 800-53 Rev. 4 AT-3 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1096 - ロールベースのセキュリティ トレーニング \| 実践的な演習](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="suspicious-communications-and-anomalous-system-behavior"></a>疑わしい通信とシステムの異常な動作

**ID**: NIST SP 800-53 Rev. 4 AT-3 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1097 - ロールベースのセキュリティ トレーニング \| 疑わしい通信とシステムの異常な動作](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>セキュリティ トレーニングの記録

**ID**: NIST SP 800-53 Rev. 4 AT-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1098 - セキュリティ トレーニングの記録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft マネージド コントロール 1099 - セキュリティ トレーニングの記録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |この認識とトレーニング コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>監査とアカウンタビリティ

### <a name="audit-and-accountability-policy-and-procedures"></a>監査とアカウンタビリティのポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 AU-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1100 - 監査とアカウンタビリティのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft マネージド コントロール 1101 - 監査とアカウンタビリティのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>イベントの監査

**ID**: NIST SP 800-53 Rev. 4 AU-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1102 - イベントの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft マネージド コントロール 1103 - イベントの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft マネージド コントロール 1104 - イベントの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft マネージド コントロール 1105 - イベントの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="reviews-and-updates"></a>レビューと更新

**ID**: NIST SP 800-53 Rev. 4 AU-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1106 - イベントの監査\| レビューと更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>監査レコードの内容

**ID**: NIST SP 800-53 Rev. 4 AU-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1107 - 監査レコードの内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="additional-audit-information"></a>その他の監査情報

**ID**: NIST SP 800-53 Rev. 4 AU-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1108 - 監査レコードの内容 \| 追加の監査情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="centralized-management-of-planned-audit-record-content"></a>予定されている監査レコードのコンテンツの一元管理

**ID**: NIST SP 800-53 Rev. 4 AU-3 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1109 - 監査レコードの内容 \| 計画的な監査レコードの内容の集中管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>監査記憶域の容量

**ID**: NIST SP 800-53 Rev. 4 AU-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1110 - 監査記憶域の容量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>監査処理エラーへの対応

**ID**: NIST SP 800-53 Rev. 4 AU-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1111 - 監査処理エラーへの対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft マネージド コントロール 1112 - 監査処理エラーへの対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="audit-storage-capacity"></a>監査記憶域の容量

**ID**: NIST SP 800-53 Rev. 4 AU-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1113 - 監査処理エラーへの対応 \| 監査記憶域の容量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="real-time-alerts"></a>リアルタイム アラート

**ID**: NIST SP 800-53 Rev. 4 AU-5 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1114 - 監査処理エラーへの対応 \| リアルタイム アラート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>監査の確認、分析、および報告

**ID**: NIST SP 800-53 Rev. 4 AU-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft マネージド コントロール 1115 - 監査の確認、分析、および報告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft マネージド コントロール 1116 - 監査の確認、分析、および報告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="process-integration"></a>プロセスの統合

**ID**: NIST SP 800-53 Rev. 4 AU-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1117 - 監査の確認、分析、および報告\| プロセスの統合](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="correlate-audit-repositories"></a>監査リポジトリの相関付け

**ID**: NIST SP 800-53 Rev. 4 AU-6 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1118 - 監査の確認、分析、および報告 \| 監査リポジトリの関連付け](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="central-review-and-analysis"></a>一元的なレビューと分析

**ID**: NIST SP 800-53 Rev. 4 AU-6 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |アプリ上で診断ログが有効になっているかどうかを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[ゲスト構成拡張機能をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |マシンのゲスト内設定について、セキュリティで保護された構成を確保するには、ゲスト構成拡張機能をインストールします。 この拡張機能によって監視されるゲスト内設定には、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境設定が含まれます。 インストールが完了すると、"Windows Exploit Guard を有効にする必要がある" のようなゲスト内ポリシーを使用できるようになります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) をご覧ください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center は、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントを使用します。 仮想マシンが正常に監視されるようにするには、エージェントが仮想マシンにインストールされていること、および構成されているワークスペースにセキュリティ イベントがエージェントによって正しく収集されていることを確認する必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Linux Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center での監視のために、仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |このポリシーは、Log Analytics エージェントがインストールされていない場合にすべての Windows または Linux 仮想マシン (VM) を監査します。Security Center ではこれを使用してセキュリティの脆弱性や脅威を監視します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center での監視のために、仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) からデータを収集します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Windows Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft マネージド コントロール 1119 - 監査の確認、分析、および報告 \| 集中的な確認と分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[イベント ハブのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |ゲスト構成拡張機能には、システム割り当てマネージド ID が必要です。 このポリシーのスコープ内の Azure 仮想マシンは、ゲスト構成拡張機能がインストールされていても、システム割り当てマネージド ID がなければ非準拠になります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="integration--scanning-and-monitoring-capabilities"></a>統合またはスキャンと監視機能

**ID**: NIST SP 800-53 Rev. 4 AU-6 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |アプリ上で診断ログが有効になっているかどうかを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[ゲスト構成拡張機能をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |マシンのゲスト内設定について、セキュリティで保護された構成を確保するには、ゲスト構成拡張機能をインストールします。 この拡張機能によって監視されるゲスト内設定には、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境設定が含まれます。 インストールが完了すると、"Windows Exploit Guard を有効にする必要がある" のようなゲスト内ポリシーを使用できるようになります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) をご覧ください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center は、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントを使用します。 仮想マシンが正常に監視されるようにするには、エージェントが仮想マシンにインストールされていること、および構成されているワークスペースにセキュリティ イベントがエージェントによって正しく収集されていることを確認する必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Linux Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center での監視のために、仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |このポリシーは、Log Analytics エージェントがインストールされていない場合にすべての Windows または Linux 仮想マシン (VM) を監査します。Security Center ではこれを使用してセキュリティの脆弱性や脅威を監視します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center での監視のために、仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) からデータを収集します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Windows Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft マネージド コントロール 1120 - 監査の確認、分析、および報告 \| 統合/スキャンおよび監視機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[イベント ハブのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |ゲスト構成拡張機能には、システム割り当てマネージド ID が必要です。 このポリシーのスコープ内の Azure 仮想マシンは、ゲスト構成拡張機能がインストールされていても、システム割り当てマネージド ID がなければ非準拠になります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="correlation-with-physical-monitoring"></a>物理的監視との関連付け

**ID**: NIST SP 800-53 Rev. 4 AU-6 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1121 - 監査の確認、分析、および報告 \| 物理監視との関連付け](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="permitted-actions"></a>許可された操作

**ID**: NIST SP 800-53 Rev. 4 AU-6 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1122 - 監査の確認、分析、および報告\| 許可されるアクション](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-level-adjustment"></a>監査レベルの調整

**ID**: NIST SP 800-53 Rev. 4 AU-6 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1123 - 監査の確認、分析、および報告\| 監査レベルの調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>監査の削減とレポートの生成

**ID**: NIST SP 800-53 Rev. 4 AU-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1124 - 監査の削減とレポートの生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft マネージド コントロール 1125 - 監査の削減とレポートの生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="automatic-processing"></a>自動処理

**ID**: NIST SP 800-53 Rev. 4 AU-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1126 - 監査の削減とレポートの生成\| 自動処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>タイム スタンプ

**ID**: NIST SP 800-53 Rev. 4 AU-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1127 - タイム スタンプ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft マネージド コントロール 1128 - タイム スタンプ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="synchronization-with-authoritative-time-source"></a>信頼できる時間ソースとの同期

**ID**: NIST SP 800-53 Rev. 4 AU-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1129 - タイム スタンプ \| 信頼できるタイム ソースとの同期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft マネージド コントロール 1130 - タイム スタンプ \| 信頼できるタイム ソースとの同期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>監査情報の保護

**ID**: NIST SP 800-53 Rev. 4 AU-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1131 - 監査情報の保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="audit-backup-on-separate-physical-systems--components"></a>個別の物理システム/コンポーネントのバックアップの監査

**ID**: NIST SP 800-53 Rev. 4 AU-9 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1132 - 監査情報の保護 \| 異なる物理システム/コンポーネントへの監査のバックアップ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="cryptographic-protection"></a>暗号化による保護

**ID**: NIST SP 800-53 Rev. 4 AU-9 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1133 - 監査情報の保護 \| 暗号化による保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="access-by-subset-of-privileged-users"></a>一部の特権ユーザーによるアクセス

**ID**: NIST SP 800-53 Rev. 4 AU-9 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1134 - 監査情報の保護 \| 特権のあるユーザーのサブセットによるアクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>否認防止

**ID**: NIST SP 800-53 Rev. 4 AU-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1135 - 否認不可](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>監査レコードの保持

**ID**: NIST SP 800-53 Rev. 4 AU-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1136 - 監査レコードの保持](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |
|[ストレージ アカウント ターゲットに対する SQL Server の監査データの保持期間を 90 日以上に構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |インシデント調査を目的として、SQL Server の監査のストレージ アカウントの保持先のデータ保持を少なくとも 90 日に設定することをお勧めします。 運用しているリージョンで必要な保持期間の規則を満たしていることを確認します。 これは、規制標準に準拠するために必要になる場合があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="audit-generation"></a>監査の生成

**ID**: NIST SP 800-53 Rev. 4 AU-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |アプリ上で診断ログが有効になっているかどうかを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[ゲスト構成拡張機能をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |マシンのゲスト内設定について、セキュリティで保護された構成を確保するには、ゲスト構成拡張機能をインストールします。 この拡張機能によって監視されるゲスト内設定には、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境設定が含まれます。 インストールが完了すると、"Windows Exploit Guard を有効にする必要がある" のようなゲスト内ポリシーを使用できるようになります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) をご覧ください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center は、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントを使用します。 仮想マシンが正常に監視されるようにするには、エージェントが仮想マシンにインストールされていること、および構成されているワークスペースにセキュリティ イベントがエージェントによって正しく収集されていることを確認する必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Linux Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center での監視のために、仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |このポリシーは、Log Analytics エージェントがインストールされていない場合にすべての Windows または Linux 仮想マシン (VM) を監査します。Security Center ではこれを使用してセキュリティの脆弱性や脅威を監視します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center での監視のために、仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) からデータを収集します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Windows Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft マネージド コントロール 1137 - 監査の生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft マネージド コントロール 1138 - 監査の生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft マネージド コントロール 1139 - 監査の生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[イベント ハブのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |ゲスト構成拡張機能には、システム割り当てマネージド ID が必要です。 このポリシーのスコープ内の Azure 仮想マシンは、ゲスト構成拡張機能がインストールされていても、システム割り当てマネージド ID がなければ非準拠になります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide--time-correlated-audit-trail"></a>システム全体または時間相関の監査証跡

**ID**: NIST SP 800-53 Rev. 4 AU-12 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |アプリ上で診断ログが有効になっているかどうかを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[ゲスト構成拡張機能をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |マシンのゲスト内設定について、セキュリティで保護された構成を確保するには、ゲスト構成拡張機能をインストールします。 この拡張機能によって監視されるゲスト内設定には、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境設定が含まれます。 インストールが完了すると、"Windows Exploit Guard を有効にする必要がある" のようなゲスト内ポリシーを使用できるようになります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) をご覧ください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center は、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントを使用します。 仮想マシンが正常に監視されるようにするには、エージェントが仮想マシンにインストールされていること、および構成されているワークスペースにセキュリティ イベントがエージェントによって正しく収集されていることを確認する必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Linux Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center での監視のために、仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |このポリシーは、Log Analytics エージェントがインストールされていない場合にすべての Windows または Linux 仮想マシン (VM) を監査します。Security Center ではこれを使用してセキュリティの脆弱性や脅威を監視します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center での監視のために、仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) からデータを収集します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Windows Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft マネージド コントロール 1140 - 監査の生成 \| システム全体/時間相関の監査証跡](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[イベント ハブのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |ゲスト構成拡張機能には、システム割り当てマネージド ID が必要です。 このポリシーのスコープ内の Azure 仮想マシンは、ゲスト構成拡張機能がインストールされていても、システム割り当てマネージド ID がなければ非準拠になります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="changes-by-authorized-individuals"></a>許可された個人による変更

**ID**: NIST SP 800-53 Rev. 4 AU-12 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1141 - 監査の生成 \| 承認された個人による変更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |この監査とアカウンタビリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>セキュリティの評価と承認

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>セキュリティ評価と承認に関するポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 CA-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1142 - セキュリティ評価と承認に関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft マネージド コントロール 1143 - セキュリティ評価と承認に関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>セキュリティ評価

**ID**: NIST SP 800-53 Rev. 4 CA-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1144 - セキュリティ評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft マネージド コントロール 1145 - セキュリティ評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft マネージド コントロール 1146 - セキュリティ評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft マネージド コントロール 1147 - セキュリティ評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="independent-assessors"></a>独立した審査機関

**ID**: NIST SP 800-53 Rev. 4 CA-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1148 - セキュリティ評価 \| 独立した評価者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="specialized-assessments"></a>特殊な評価

**ID**: NIST SP 800-53 Rev. 4 CA-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1149 - セキュリティ評価 \| 特別な評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="external-organizations"></a>外部組織

**ID**: NIST SP 800-53 Rev. 4 CA-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1150 - セキュリティ評価 \| 外部組織](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>システムの相互接続

**ID**: NIST SP 800-53 Rev. 4 CA-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1151 - システムの相互接続](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft マネージド コントロール 1152 - システムの相互接続](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft マネージド コントロール 1153 - システムの相互接続](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="unclassified-non-national-security-system-connections"></a>未分類の非国家セキュリティ システム接続

**ID**: NIST SP 800-53 Rev. 4 CA-3 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1154 - システムの相互接続 \| 未分類の非国家セキュリティ システム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="restrictions-on-external-system-connections"></a>外部システム接続に関する制限

**ID**: NIST SP 800-53 Rev. 4 CA-3 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1155 - システムの相互接続 \| 外部システム接続に関する制限事項](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>アクションとマイルストーンのプラン

**ID**: NIST SP 800-53 Rev. 4 CA-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1156 - アクションとマイルストーンのプラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft マネージド コントロール 1157 - アクションとマイルストーンのプラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>セキュリティ承認

**ID**: NIST SP 800-53 Rev. 4 CA-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1158 - セキュリティ承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft マネージド コントロール 1159 - セキュリティ承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft マネージド コントロール 1160 - セキュリティ承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>継続的な監視

**ID**: NIST SP 800-53 Rev. 4 CA-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1161 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft マネージド コントロール 1162 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft マネージド コントロール 1163 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft マネージド コントロール 1164 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft マネージド コントロール 1165 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft マネージド コントロール 1166 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft マネージド コントロール 1167 - 継続的な監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="independent-assessment"></a>独立した評価

**ID**: NIST SP 800-53 Rev. 4 CA-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1168 - 継続的な監視 \| 独立した評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="trend-analyses"></a>傾向分析

**ID**: NIST SP 800-53 Rev. 4 CA-7 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1169 - 継続的な監視 \| 傾向分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>侵入テスト

**ID**: NIST SP 800-53 Rev. 4 CA-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1170 - 侵入テスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="independent-penetration-agent-or-team"></a>独立した侵入エージェントまたはチーム

**ID**: NIST SP 800-53 Rev. 4 CA-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1171 - 侵入テスト \| 独立した侵入エージェントまたはチーム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>内部システム接続

**ID**: NIST SP 800-53 Rev. 4 CA-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1172 - 内部システム接続](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft マネージド コントロール 1173 - 内部システム接続](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |このセキュリティの評価と承認コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>構成管理

### <a name="configuration-management-policy-and-procedures"></a>構成管理ポリシーおよび手順

**ID**: NIST SP 800-53 Rev. 4 CM-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1174 - 構成管理ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft マネージド コントロール 1175 - 構成管理ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>ベースライン構成

**ID**: NIST SP 800-53 Rev. 4 CM-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1176 - ベースライン構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="reviews-and-updates"></a>レビューと更新

**ID**: NIST SP 800-53 Rev. 4 CM-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1177 - ベースライン構成 \| レビューと更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft マネージド コントロール 1178 - ベースライン構成 \| レビューと更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft マネージド コントロール 1179 - ベースライン構成 \| レビューと更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="automation-support-for-accuracy--currency"></a>正確性/通用性の自動化のサポート

**ID**: NIST SP 800-53 Rev. 4 CM-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1180 - ベースライン構成 \| 正確性/最新性に対する自動化サポート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="retention-of-previous-configurations"></a>以前の構成の保持

**ID**: NIST SP 800-53 Rev. 4 CM-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1181 - ベースライン構成 \| 以前の構成の保持](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="configure-systems-components-or-devices-for-high-risk-areas"></a>危険性の高い領域に対するシステム、コンポーネント、またはデバイスの構成

**ID**: NIST SP 800-53 Rev. 4 CM-2 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1182 - ベースライン構成 \| 危険度の高い領域用のシステム、コンポーネント、またはデバイスの構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft マネージド コントロール 1183 - ベースライン構成 \| 危険度の高い領域用のシステム、コンポーネント、またはデバイスの構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>構成変更コントロール

**ID**: NIST SP 800-53 Rev. 4 CM-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1184 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft マネージド コントロール 1185 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft マネージド コントロール 1186 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft マネージド コントロール 1187 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft マネージド コントロール 1188 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft マネージド コントロール 1189 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft マネージド コントロール 1190 - 構成変更コントロール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="automated-document--notification--prohibition-of-changes"></a>変更の文書化/通知/禁止の自動化

**ID**: NIST SP 800-53 Rev. 4 CM-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1191 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft マネージド コントロール 1192 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft マネージド コントロール 1193 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft マネージド コントロール 1194 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft マネージド コントロール 1195 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft マネージド コントロール 1196 - 構成変更コントロール \| 変更の自動化された文書化/通知/禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="test--validate--document-changes"></a>変更のテスト/検証/文書化

**ID**: NIST SP 800-53 Rev. 4 CM-3 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1197 - 構成変更コントロール \| 変更のテスト/検証/文書化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="security-representative"></a>セキュリティ担当者

**ID**: NIST SP 800-53 Rev. 4 CM-3 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1198 - 構成変更コントロール \| セキュリティ担当者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="cryptography-management"></a>暗号化の管理

**ID**: NIST SP 800-53 Rev. 4 CM-3 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1199 - 構成変更コントロール \| 暗号化の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>セキュリティ影響分析

**ID**: NIST SP 800-53 Rev. 4 CM-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1200 - セキュリティ影響分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="separate-test-environments"></a>個別のテスト環境

**ID**: NIST SP 800-53 Rev. 4 CM-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1201 - セキュリティ影響分析 \| 分離されたテスト環境](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>変更に関するアクセス制限

**ID**: NIST SP 800-53 Rev. 4 CM-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1202 - 変更に関するアクセス制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="automated-access-enforcement--auditing"></a>アクセスの適用/監査の自動化

**ID**: NIST SP 800-53 Rev. 4 CM-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1203 - 変更に関するアクセス制限 \| 自動化されたアクセスの適用/監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="review-system-changes"></a>システム変更のレビュー

**ID**: NIST SP 800-53 Rev. 4 CM-5 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1204 - 変更に関するアクセス制限 \| システムの変更のレビュー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="signed-components"></a>署名付きコンポーネント

**ID**: NIST SP 800-53 Rev. 4 CM-5 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1205 - 変更に関するアクセス制限 \| 署名されたコンポーネント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="limit-production--operational-privileges"></a>運用/操作特権の制限

**ID**: NIST SP 800-53 Rev. 4 CM-5 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1206 - 変更に関するアクセス制限 \| 運用/操作特権の制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft マネージド コントロール 1207 - 変更に関するアクセス制限 \| 運用/操作特権の制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>構成設定

**ID**: NIST SP 800-53 Rev. 4 CM-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Service (AKS) 用の Azure Policy アドオンをクラスターにインストールして有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a15ec92-a229-4763-bb14-0ea34a568f8d) |Kubernetes Service (AKS) 用の Azure Policy アドオンを使用すると、Open Policy Agent (OPA) に対する受付制御 Webhook である Gatekeeper V3 を拡張して、整合性のある一元的な方法で、大規模な強制と保護をお使いのクラスターに適用できます。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_AzurePolicyAddOn_Audit.json) |
|[CORS で API アプリへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |クロス オリジン リソース共有 (CORS) では、API アプリへのアクセスをすべてのドメインには許可しないでください。 API アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS で関数アプリへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |クロス オリジン リソース共有 (CORS) で、関数アプリへのアクセスをすべてのドメインには許可しないでください。 関数アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |クロス オリジン リソース共有 (CORS) で、Web アプリケーションへのアクセスをすべてのドメインには許可しないでください。 Web アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[API アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[関数アプリでは、"クライアント証明書 (着信クライアント証明書)" が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみが、そのアプリにアクセスできるようになります。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Kubernetes クラスター コンテナーの CPU およびメモリ リソースの制限は、指定された制限を超えないようにする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |コンテナーの CPU とメモリ リソースの制限を適用して、Kubernetes クラスターでのリソース枯渇攻撃を防ぎます。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits.json) |
|[Kubernetes クラスター コンテナーでは、ホスト プロセス ID またはホスト IPC 名前空間を共有してはいけない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8) |ポッド コンテナーが Kubernetes クラスターでホスト プロセス ID 名前空間とホスト IPC 名前空間を共有できないようにします。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的とした CIS 5.2.2 と CIS 5.2.3 の一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/BlockHostNamespace.json) |
|[Kubernetes クラスター コンテナーは、許可されたポートでのみリッスンする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |Kubernetes クラスターへのアクセスをセキュリティで保護するために、許可されたポートでのみリッスンするようにコンテナーを制限します。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts.json) |
|[Kubernetes クラスター コンテナーでは、許可されている AppArmor プロファイルのみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) |コンテナーでは、Kubernetes クラスターで許可されている AppArmor プロファイルのみを使用する必要があります。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的としたポッド セキュリティ ポリシーの一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/EnforceAppArmorProfile.json) |
|[Kubernetes クラスター コンテナーでは、許可されている機能のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) |Kubernetes クラスター内のコンテナーの攻撃面を縮小するために、機能を制限します。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的とした CIS 5.2.8 と CIS 5.2.9 の一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedCapabilities.json) |
|[Kubernetes クラスター コンテナーでは、許可されているイメージのみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |信頼できるレジストリのイメージを使用して、不明な脆弱性、セキュリティの問題、悪意のあるイメージに対する Kubernetes クラスターの露出リスクを削減します。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages.json) |
|[Kubernetes クラスターコンテナーは、読み取り専用のルート ファイル システムを使用して実行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) |Kubernetes クラスター内のパスに悪意のあるバイナリを追加する実行時の変更から保護するために、読み取り専用のルート ファイル システムでコンテナーを実行します。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ReadOnlyRootFileSystem.json) |
|[Kubernetes クラスター ポッドの hostPath ボリュームでは、許可されているホスト パスのみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75) |ポッドの HostPath ボリューム マウントを、Kubernetes クラスター内の許可されているホスト パスに制限します。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的としたポッド セキュリティ ポリシーの一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedHostPaths.json) |
|[Kubernetes クラスターのポッドとコンテナーは、承認されたユーザーとグループの ID のみを使用して実行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) |Kubernetes クラスターでポッドとコンテナーを実行する際に使用できるユーザー、プライマリ グループ、補助グループ、およびファイル システム グループの ID を制御します。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的としたポッド セキュリティ ポリシーの一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedUsersGroups.json) |
|[Kubernetes クラスターのポッドでは、承認されているホスト ネットワークとポート範囲のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |ポッドのアクセスを、Kubernetes クラスター内のホスト ネットワークおよび許容されるホスト ポート範囲に制限します。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的とした CIS 5.2.4 の一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|[Kubernetes クラスター サービスでは、許可されているポートでのみリッスンする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |Kubernetes クラスターへのアクセスをセキュリティで保護するために、許可されたポートでのみリッスンするようにサービスを制限します。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts.json) |
|[Kubernetes クラスターで特権コンテナーを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |Kubernetes クラスターでの特権コンテナーの作成を許可しません。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的とした CIS 5.2.1 の一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege.json) |
|[Kubernetes クラスターでは、コンテナーの特権エスカレーションを許可してはいけない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) |Kubernetes クラスターにおいて、ルートへの特権エスカレーションでのコンテナーの実行を許可しないでください。 この推奨事項は、Kubernetes 環境のセキュリティを強化することを目的とした CIS 5.2.5 の一部です。 このポリシーは、Kubernetes Service (AKS)、AKS Engine のプレビュー、および Azure Arc 対応 Kubernetes で一般提供されています。 詳細については、「[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)」を参照してください。 |audit、deny、disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilegeEscalation.json) |
|[Linux マシンは Azure のコンピューティング セキュリティ ベースラインの要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 Azure Compute セキュリティ ベースラインの推奨事項のいずれかについて、マシンが正しく構成されていない場合、マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.1.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureLinuxBaseline_AINE.json) |
|[Microsoft マネージド コントロール 1208 - 構成設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft マネージド コントロール 1209 - 構成設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft マネージド コントロール 1210 - 構成設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft マネージド コントロール 1211 - 構成設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |
|[API アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |リモート デバッグを実行するには、受信ポートが API アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Web アプリケーションのリモート デバッグを無効にする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |リモート デバッグを実行するには、受信ポートが Web アプリケーション上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Windows マシンは Azure のコンピューティング セキュリティ ベースラインの要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 Azure Compute セキュリティ ベースラインの推奨事項のいずれかについて、マシンが正しく構成されていない場合、マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureWindowsBaseline_AINE.json) |

### <a name="automated-central-management--application--verification"></a>一元管理/適用/検証の自動化

**ID**: NIST SP 800-53 Rev. 4 CM-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1212 - 構成設定 \| 自動化された集中管理/適用/検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="respond-to-unauthorized-changes"></a>許可されていない変更への対応

**ID**: NIST SP 800-53 Rev. 4 CM-6 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1213 - 構成設定 \| 承認されていない変更への対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>最小限の機能

**ID**: NIST SP 800-53 Rev. 4 CM-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center の適応型アプリケーション制御によって監査用に構成されているマシン グループでの動作の変更を監視します。 Security Center では、Machine Learning を使用して、マシン上の実行中のプロセスを分析し、既知の安全なアプリケーションの一覧を提示します。 これらは、適応型アプリケーション制御ポリシーで許可する推奨アプリとして提供されています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft マネージド コントロール 1214 - 最小限の機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft マネージド コントロール 1215 - 最小限の機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="periodic-review"></a>定期的なレビュー

**ID**: NIST SP 800-53 Rev. 4 CM-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1216 - 最小限の機能 \| 定期的なレビュー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft マネージド コントロール 1217 - 最小限の機能 \| 定期的なレビュー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="prevent-program-execution"></a>プログラムの実行禁止

**ID**: NIST SP 800-53 Rev. 4 CM-7 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center の適応型アプリケーション制御によって監査用に構成されているマシン グループでの動作の変更を監視します。 Security Center では、Machine Learning を使用して、マシン上の実行中のプロセスを分析し、既知の安全なアプリケーションの一覧を提示します。 これらは、適応型アプリケーション制御ポリシーで許可する推奨アプリとして提供されています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft マネージド コントロール 1218 - 最小限の機能 \| プログラムの実行の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="authorized-software--whitelisting"></a>認可されたソフトウェア/ホワイトリスト登録

**ID**: NIST SP 800-53 Rev. 4 CM-7 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center の適応型アプリケーション制御によって監査用に構成されているマシン グループでの動作の変更を監視します。 Security Center では、Machine Learning を使用して、マシン上の実行中のプロセスを分析し、既知の安全なアプリケーションの一覧を提示します。 これらは、適応型アプリケーション制御ポリシーで許可する推奨アプリとして提供されています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft マネージド コントロール 1219 - 最小限の機能 \| 承認されたソフトウェア/ホワイトリスト登録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft マネージド コントロール 1220 - 最小限の機能 \| 承認されたソフトウェア/ホワイトリスト登録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft マネージド コントロール 1221 - 最小限の機能 \| 承認されたソフトウェア/ホワイトリスト登録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>情報システム コンポーネント インベントリ

**ID**: NIST SP 800-53 Rev. 4 CM-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1222 - 情報システム コンポーネント インベントリ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft マネージド コントロール 1223 - 情報システム コンポーネント インベントリ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="updates-during-installations--removals"></a>インストール中/削除中の更新

**ID**: NIST SP 800-53 Rev. 4 CM-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1224 - 情報システム コンポーネント インベントリ \| インストール/削除中の更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="automated-maintenance"></a>メンテナンスの自動化

**ID**: NIST SP 800-53 Rev. 4 CM-8 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1225 - 情報システム コンポーネント インベントリ \| 自動化されたメンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="automated-unauthorized-component-detection"></a>許可されていないコンポーネントの検出の自動化

**ID**: NIST SP 800-53 Rev. 4 CM-8 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1226 - 情報システム コンポーネント インベントリ \| 承認されていないコンポーネントの自動化された検出](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft マネージド コントロール 1227 - 情報システム コンポーネント インベントリ \| 承認されていないコンポーネントの自動化された検出](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="accountability-information"></a>説明責任の情報

**ID**: NIST SP 800-53 Rev. 4 CM-8 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1228 - 情報システム コンポーネント インベントリ \| アカウンタビリティ情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="no-duplicate-accounting-of-components"></a>コンポーネントの重複会計処理なし

**ID**: NIST SP 800-53 Rev. 4 CM-8 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1229 - 情報システム コンポーネント インベントリ \| コンポーネントの複製なしのアカウンティング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>構成管理プラン

**ID**: NIST SP 800-53 Rev. 4 CM-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1230 - 構成管理プラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft マネージド コントロール 1231 - 構成管理プラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft マネージド コントロール 1232 - 構成管理プラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft マネージド コントロール 1233 - 構成管理プラン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>ソフトウェアの使用制限

**ID**: NIST SP 800-53 Rev. 4 CM-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center の適応型アプリケーション制御によって監査用に構成されているマシン グループでの動作の変更を監視します。 Security Center では、Machine Learning を使用して、マシン上の実行中のプロセスを分析し、既知の安全なアプリケーションの一覧を提示します。 これらは、適応型アプリケーション制御ポリシーで許可する推奨アプリとして提供されています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft マネージド コントロール 1234 - ソフトウェアの使用制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft マネージド コントロール 1235 - ソフトウェアの使用制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft マネージド コントロール 1236 - ソフトウェアの使用制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="open-source-software"></a>オープン ソース ソフトウェア

**ID**: NIST SP 800-53 Rev. 4 CM-10 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1237 - ソフトウェアの使用制限 \| オープン ソース ソフトウェア](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>ユーザーがインストールするソフトウェア

**ID**: NIST SP 800-53 Rev. 4 CM-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center の適応型アプリケーション制御によって監査用に構成されているマシン グループでの動作の変更を監視します。 Security Center では、Machine Learning を使用して、マシン上の実行中のプロセスを分析し、既知の安全なアプリケーションの一覧を提示します。 これらは、適応型アプリケーション制御ポリシーで許可する推奨アプリとして提供されています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft マネージド コントロール 1238 - ユーザーがインストールするソフトウェア](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft マネージド コントロール 1239 - ユーザーがインストールするソフトウェア](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft マネージド コントロール 1240 - ユーザーがインストールするソフトウェア](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="alerts-for-unauthorized-installations"></a>許可されていないインストールのアラート

**ID**: NIST SP 800-53 Rev. 4 CM-11 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1241 - ユーザーがインストールするソフトウェア \| 承認されていないインストールに対するアラート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |この構成管理コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>代替計画

### <a name="contingency-planning-policy-and-procedures"></a>コンティンジェンシー計画ポリシーおよび手順

**ID**: NIST SP 800-53 Rev. 4 CP-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1242 - コンティンジェンシー計画ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft マネージド コントロール 1243 - コンティンジェンシー計画ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>コンティンジェンシー計画

**ID**: NIST SP 800-53 Rev. 4 CP-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1244 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft マネージド コントロール 1245 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft マネージド コントロール 1246 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft マネージド コントロール 1247 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft マネージド コントロール 1248 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft マネージド コントロール 1249 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft マネージド コントロール 1250 - コンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="coordinate-with-related-plans"></a>関連する計画との調整

**ID**: NIST SP 800-53 Rev. 4 CP-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1251 - コンティンジェンシー計画 \| 関連する計画との調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="capacity-planning"></a>キャパシティ プランニング

**ID**: NIST SP 800-53 Rev. 4 CP-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1252 - コンティンジェンシー計画 \| 容量計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="resume-essential-missions--business-functions"></a>重要な任務/ビジネス機能の再開

**ID**: NIST SP 800-53 Rev. 4 CP-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1253 - コンティンジェンシー計画 \| 不可欠なミッション/ビジネス機能の再開](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="resume-all-missions--business-functions"></a>すべての任務/ビジネス機能の再開

**ID**: NIST SP 800-53 Rev. 4 CP-2 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1254 - コンティンジェンシー計画 \| すべてのミッション/ビジネス機能の再開](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="continue-essential-missions--business-functions"></a>重要な任務/ビジネス機能の継続

**ID**: NIST SP 800-53 Rev. 4 CP-2 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1255 - コンティンジェンシー計画 \| 不可欠なミッション/ビジネス機能の続行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="identify-critical-assets"></a>重要な資産の識別

**ID**: NIST SP 800-53 Rev. 4 CP-2 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1256 - コンティンジェンシー計画 \| 重要な資産の識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>コンティンジェンシー トレーニング

**ID**: NIST SP 800-53 Rev. 4 CP-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1257 - コンティンジェンシー トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft マネージド コントロール 1258 - コンティンジェンシー トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft マネージド コントロール 1259 - コンティンジェンシー トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="simulated-events"></a>シミュレートされたイベント

**ID**: NIST SP 800-53 Rev. 4 CP-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1260 - コンティンジェンシー トレーニング \| シミュレートされたイベント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>コンティンジェンシー計画のテスト

**ID**: NIST SP 800-53 Rev. 4 CP-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1261 - コンティンジェンシー計画のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft マネージド コントロール 1262 - コンティンジェンシー計画のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft マネージド コントロール 1263 - コンティンジェンシー計画のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="coordinate-with-related-plans"></a>関連する計画との調整

**ID**: NIST SP 800-53 Rev. 4 CP-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1264 - コンティンジェンシー計画のテスト \| 関連する計画との調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="alternate-processing-site"></a>代替処理サイト

**ID**: NIST SP 800-53 Rev. 4 CP-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1265 - コンティンジェンシー計画のテスト \| 代替処理サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft マネージド コントロール 1266 - コンティンジェンシー計画のテスト \| 代替処理サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>代替ストレージ サイト

**ID**: NIST SP 800-53 Rev. 4 CP-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[ストレージ アカウントの geo 冗長ストレージを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |Geo 冗長を使用して高可用性アプリケーションを作成します |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |このポリシーは、長期的な geo 冗長バックアップが有効になっていないすべての Azure SQL データベースを監査します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft マネージド コントロール 1267 - 代替ストレージ サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft マネージド コントロール 1268 - 代替ストレージ サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="separation-from-primary-site"></a>プライマリ サイトからの分離

**ID**: NIST SP 800-53 Rev. 4 CP-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[ストレージ アカウントの geo 冗長ストレージを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |Geo 冗長を使用して高可用性アプリケーションを作成します |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |このポリシーは、長期的な geo 冗長バックアップが有効になっていないすべての Azure SQL データベースを監査します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft マネージド コントロール 1269 - 代替ストレージ サイト \| プライマリ サイトからの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="recovery-time--point-objectives"></a>目標復旧時間/目標復旧時点

**ID**: NIST SP 800-53 Rev. 4 CP-6 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1270 - 代替ストレージ サイト \| 目標復旧時間/目標復旧時点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="accessibility"></a>ユーザー補助

**ID**: NIST SP 800-53 Rev. 4 CP-6 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1271 - 代替ストレージ サイト \| アクセシビリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>代替処理サイト

**ID**: NIST SP 800-53 Rev. 4 CP-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ディザスター リカバリーを構成されていない仮想マシンの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |ディザスター リカバリーが構成されていない仮想マシンを監査します。 ディザスター リカバリーの詳細については、[https://aka.ms/asr-doc](../../../site-recovery/index.yml) にアクセスしてください。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft マネージド コントロール 1272 - 代替処理サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft マネージド コントロール 1273 - 代替処理サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft マネージド コントロール 1274 - 代替処理サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="separation-from-primary-site"></a>プライマリ サイトからの分離

**ID**: NIST SP 800-53 Rev. 4 CP-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1275 - 代替処理サイト \| プライマリ サイトからの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="accessibility"></a>ユーザー補助

**ID**: NIST SP 800-53 Rev. 4 CP-7 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1276 - 代替処理サイト \| アクセシビリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="priority-of-service"></a>サービスの優先順位

**ID**: NIST SP 800-53 Rev. 4 CP-7 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1277 - 代替処理サイト \| サービスの優先順位](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="preparation-for-use"></a>使用の準備

**ID**: NIST SP 800-53 Rev. 4 CP-7 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1278 - 代替処理サイト \| 使用の準備](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>電気通信サービス

**ID**: NIST SP 800-53 Rev. 4 CP-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1279 - 電気通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="priority-of-service-provisions"></a>サービスのプロビジョニングの優先度

**ID**: NIST SP 800-53 Rev. 4 CP-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1280 - 電気通信サービス \| サービスの優先順位プロビジョニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft マネージド コントロール 1281 - 電気通信サービス \| サービスの優先順位プロビジョニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="single-points-of-failure"></a>単一障害点

**ID**: NIST SP 800-53 Rev. 4 CP-8 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1282 - 電気通信サービス \| 単一障害点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="separation-of-primary--alternate-providers"></a>プライマリ/代替プロバイダーの分離

**ID**: NIST SP 800-53 Rev. 4 CP-8 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1283 - 電気通信サービス \| プライマリ/代替プロバイダーの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="provider-contingency-plan"></a>プロバイダーのコンティンジェンシー計画

**ID**: NIST SP 800-53 Rev. 4 CP-8 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1284 - 電気通信サービス \| プロバイダーのコンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft マネージド コントロール 1285 - 電気通信サービス \| プロバイダーのコンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft マネージド コントロール 1286 - 電気通信サービス \| プロバイダーのコンティンジェンシー計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>情報システムのバックアップ

**ID**: NIST SP 800-53 Rev. 4 CP-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに対して Azure Backup を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup を有効にして、Azure Virtual Machines を保護します。 Azure Backup は、Azure 向けの安全で費用対効果の高いデータ保護ソリューションです。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[キー コンテナーで消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |悪意でキー コンテナーが削除されると、データが完全に失われる可能性があります。 組織内の悪意のある内部関係者が、キー コンテナーの削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除されたキー コンテナーに必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中にキー コンテナーを消去することはできなくなります。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|[キー コンテナーで論理的な削除が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d) |論理的な削除が有効になっていない状態でキー コンテナーを削除すると、キー コンテナーに格納されているすべてのシークレット、キー、証明書が完全に削除されます。 誤ってキー コンテナーが削除されると、データが完全に失われる可能性があります。 論理的な削除を使用すると、構成可能な保有期間の間は、誤って削除されたキー コンテナーを復旧できます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_SoftDeleteMustBeEnabled_Audit.json) |
|[Microsoft マネージド コントロール 1287 - 情報システムのバックアップ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft マネージド コントロール 1288 - 情報システムのバックアップ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft マネージド コントロール 1289 - 情報システムのバックアップ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft マネージド コントロール 1290 - 情報システムのバックアップ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="testing-for-reliability--integrity"></a>信頼性/整合性に対するテスト

**ID**: NIST SP 800-53 Rev. 4 CP-9 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1291 - 情報システムのバックアップ \| 信頼性/整合性に対するテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="test-restoration-using-sampling"></a>サンプリングを使用した復元のテスト

**ID**: NIST SP 800-53 Rev. 4 CP-9 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1292 - 情報システムのバックアップ \| サンプリングを使用した復元のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="separate-storage-for-critical-information"></a>重要な情報用の個別のストレージ

**ID**: NIST SP 800-53 Rev. 4 CP-9 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1293 - 情報システムのバックアップ \| 重要な情報のための個別のストレージ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="transfer-to-alternate-storage-site"></a>代替ストレージ サイトへの移動

**ID**: NIST SP 800-53 Rev. 4 CP-9 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1294 - 情報システムのバックアップ \| 代替ストレージ サイトへの転送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>情報システムの復旧および再構成

**ID**: NIST SP 800-53 Rev. 4 CP-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1295 - 情報システムの復旧および再構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="transaction-recovery"></a>トランザクションの回復

**ID**: NIST SP 800-53 Rev. 4 CP-10 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1296 - 情報システムの復旧および再構成 \| トランザクション復旧](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="restore-within-time-period"></a>一定期間内の復元

**ID**: NIST SP 800-53 Rev. 4 CP-10 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1297 - 情報システムの復旧および再構成 \| 期間内の復元](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |このコンティンジェンシー計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>識別と認証

### <a name="identification-and-authentication-policy-and-procedures"></a>識別と認証のポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 IA-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1298 - 識別と認証のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft マネージド コントロール 1299 - 識別と認証のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>識別と認証 (組織のユーザー)

**ID**: NIST SP 800-53 Rev. 4 IA-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[API アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[関数アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft マネージド コントロール 1300 - 識別と認証 (組織のユーザー)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[サブスクリプションを保護するために、管理証明書ではなくサービス プリンシパルを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |管理証明書を使用すると、それを使用して認証するすべてのユーザーが、証明書に関連付けられているサブスクリプションを管理できます。 サブスクリプションをより安全に管理するには、証明書の侵害の影響を限定するため、Resource Manager でサービス プリンシパルを使用することをお勧めします。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="network-access-to-privileged-accounts"></a>特権アカウントへのネットワーク アクセス

**ID**: NIST SP 800-53 Rev. 4 IA-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft マネージド コントロール 1301 - 識別と認証 (組織のユーザー) \| 特権のあるアカウントへのネットワーク アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="network-access-to-non-privileged-accounts"></a>非特権アカウントへのネットワーク アクセス

**ID**: NIST SP 800-53 Rev. 4 IA-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft マネージド コントロール 1302 - 識別と認証 (組織のユーザー) \| 特権のないアカウントへのネットワーク アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="local-access-to-privileged-accounts"></a>特権アカウントへのローカル アクセス

**ID**: NIST SP 800-53 Rev. 4 IA-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1303 - 識別と認証 (組織のユーザー) \| 特権のあるアカウントへのローカル アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="local-access-to-non-privileged-accounts"></a>非特権アカウントへのローカル アクセス

**ID**: NIST SP 800-53 Rev. 4 IA-2 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1304 - 識別と認証 (組織のユーザー) \| 特権のないアカウントへのローカル アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="group-authentication"></a>グループ認証

**ID**: NIST SP 800-53 Rev. 4 IA-2 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1305 - 識別と認証 (組織のユーザー) \| グループの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="network-access-to-privileged-accounts---replay-resistant"></a>特権アカウントへのネットワーク アクセス - リプレイ耐性

**ID**: NIST SP 800-53 Rev. 4 IA-2 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1306 - 識別と認証 (組織のユーザー) \| 特権のあるアカウントへのネットワーク アクセス - リプレイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="network-access-to-non-privileged-accounts---replay-resistant"></a>非特権アカウントへのネットワーク アクセス - リプレイ耐性

**ID**: NIST SP 800-53 Rev. 4 IA-2 (9)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1307 - 識別と認証 (組織のユーザー) \| 特権のないアカウントへのネットワーク アクセス - リプレイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="remote-access---separate-device"></a>リモート アクセス - 別個のデバイス

**ID**: NIST SP 800-53 Rev. 4 IA-2 (11)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1308 - 識別と認証 (組織のユーザー) \| リモート アクセス - 別個のデバイス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="acceptance-of-piv-credentials"></a>PIV 資格情報の承諾

**ID**: NIST SP 800-53 Rev. 4 IA-2 (12)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1309 - 識別と認証 (組織のユーザー) \| PIV 資格情報の受け入れ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>デバイスの識別と認証

**ID**: NIST SP 800-53 Rev. 4 IA-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1310 - デバイスの識別と認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>識別子の管理

**ID**: NIST SP 800-53 Rev. 4 IA-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services アカウントでは、ローカル認証方法を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |ローカル認証方法を無効にすると、Cognitive Services アカウントで Azure Active Directory ID を認証専用で求めることにより、セキュリティが向上します。 詳細については、[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[API アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[関数アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft マネージド コントロール 1311 - 識別子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft マネージド コントロール 1312 - 識別子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft マネージド コントロール 1313 - 識別子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft マネージド コントロール 1314 - 識別子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft マネージド コントロール 1315 - 識別子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |
|[Service Fabric クラスターでは、クライアント認証に Azure Active Directory のみを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric で Azure Active Directory によるクライアント認証のみを使用していることを監査します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[サブスクリプションを保護するために、管理証明書ではなくサービス プリンシパルを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |管理証明書を使用すると、それを使用して認証するすべてのユーザーが、証明書に関連付けられているサブスクリプションを管理できます。 サブスクリプションをより安全に管理するには、証明書の侵害の影響を限定するため、Resource Manager でサービス プリンシパルを使用することをお勧めします。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="identify-user-status"></a>ユーザーの状態の識別

**ID**: NIST SP 800-53 Rev. 4 IA-4 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1316 - 識別子の管理 \| ユーザーの状態の識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>認証子の管理

**ID**: NIST SP 800-53 Rev. 4 IA-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされているものの、マネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[ユーザー割り当て ID を持つ VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされており、少なくとも 1 つのユーザー割り当て ID を持っているものの、システム割り当てマネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[passwd ファイルのアクセス許可が 0644 に設定されていない Linux マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 passwd ファイルのアクセス許可が 0644 に設定されていない Linux マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[可逆的暗号化を使用してパスワードを格納しない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 可逆的暗号化を使用してパスワードを格納しない Windows マシンは非準拠となります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux マシンに対する認証では SSH キーを要求する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |SSH 自体は暗号化された接続を提供しますが、SSH でパスワードを使用すると、VM はブルートフォース攻撃に対して脆弱になります。 Azure Linux 仮想マシンに対して SSH による認証を行うための最も安全な方法は、公開キー/秘密キー ペア (SSH キーとも呼ばれます) を使用することです。 詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[証明書には最長有効期間を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |キー コンテナー内での証明書の最長有効期間を指定して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[Linux VM でゲスト構成の割り当てを有効にするために Linux ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Linux 仮想マシンに Linux ゲスト構成拡張機能がデプロイされます。 Linux ゲスト構成拡張機能は、すべての Linux ゲスト構成割り当ての前提条件であるため、Linux ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM でゲスト構成の割り当てを有効にするために Windows ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Windows 仮想マシンに Windows ゲスト構成拡張機能がデプロイされます。 Windows ゲスト構成拡張機能は、すべての Windows ゲスト構成割り当ての前提条件であるため、Windows ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Key Vault キーには有効期限が必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |暗号化キーには有効期限を定義する必要があり、永続的なものにしてはいけません。 無期限に有効なキーを使用すると、攻撃者がキーを侵害できる時間がそれだけ長くなります。 セキュリティ プラクティスとして、暗号化キーには有効期限を設定することをお勧めします。 |Audit、Deny、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |
|[Key Vault シークレットには有効期限が必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |シークレットには有効期限を定義する必要があり、永続的なものにしてはいけません。 シークレットを無期限に有効にすると、潜在的な攻撃者にそれを侵害する時間を多く与えることになります。 セキュリティ プラクティスとして、シークレットには有効期限を設定することをお勧めします。 |Audit、Deny、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |
|[Microsoft マネージド コントロール 1317 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft マネージド コントロール 1318 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft マネージド コントロール 1319 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft マネージド コントロール 1320 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft マネージド コントロール 1321 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft マネージド コントロール 1322 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft マネージド コントロール 1323 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft マネージド コントロール 1324 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft マネージド コントロール 1325 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft マネージド コントロール 1326 - 認証子の管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="password-based-authentication"></a>パスワードベースの認証

**ID**: NIST SP 800-53 Rev. 4 IA-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされているものの、マネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[ユーザー割り当て ID を持つ VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされており、少なくとも 1 つのユーザー割り当て ID を持っているものの、システム割り当てマネージド ID を持たない仮想マシンに、システム割り当てマネージド ID が追加されます。 システム割り当てマネージド ID は、すべてのゲスト構成割り当てに対する前提条件であるため、ゲスト構成ポリシー定義を使用する前にマシンに追加する必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |変更 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[passwd ファイルのアクセス許可が 0644 に設定されていない Linux マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 passwd ファイルのアクセス許可が 0644 に設定されていない Linux マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[以前の 24 個のパスワードの再利用が許可されている Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 以前の 24 個のパスワードの再利用が許可されている Windows マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[パスワードの有効期間が 70 日になっていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードの有効期間が 70 日になっていない Windows マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[パスワードの変更禁止期間が 1 日になっていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードの変更禁止期間が 1 日になっていない Windows マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[パスワードの複雑さの設定が有効になっていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードの複雑さの設定が有効になっていない Windows マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[パスワードの最小文字数が 14 文字に制限されていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 パスワードの最小文字数が 14 文字に制限されていない Windows マシンは非準拠となります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[可逆的暗号化を使用してパスワードを格納しない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 可逆的暗号化を使用してパスワードを格納しない Windows マシンは非準拠となります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux VM でゲスト構成の割り当てを有効にするために Linux ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Linux 仮想マシンに Linux ゲスト構成拡張機能がデプロイされます。 Linux ゲスト構成拡張機能は、すべての Linux ゲスト構成割り当ての前提条件であるため、Linux ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows VM でゲスト構成の割り当てを有効にするために Windows ゲスト構成拡張機能をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |このポリシーでは、Azure でホストされ、ゲスト構成でサポートされている Windows 仮想マシンに Windows ゲスト構成拡張機能がデプロイされます。 Windows ゲスト構成拡張機能は、すべての Windows ゲスト構成割り当ての前提条件であるため、Windows ゲスト構成ポリシー定義を使用する前にマシンにデプロイする必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft マネージド コントロール 1327 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft マネージド コントロール 1328 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft マネージド コントロール 1329 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft マネージド コントロール 1330 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft マネージド コントロール 1331 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft マネージド コントロール 1332 - 認証子の管理 \| パスワード ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="pki-based-authentication"></a>PKI ベースの認証

**ID**: NIST SP 800-53 Rev. 4 IA-5 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1333 - 認証子の管理 \| PKI ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft マネージド コントロール 1334 - 認証子の管理 \| PKI ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft マネージド コントロール 1335 - 認証子の管理 \| PKI ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft マネージド コントロール 1336 - 認証子の管理 \| PKI ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="in-person-or-trusted-third-party-registration"></a>直接または信頼できるサードパーティによる登録

**ID**: NIST SP 800-53 Rev. 4 IA-5 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1337 - 認証子の管理 \| 直接または信頼できるサードパーティによる登録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="automated-support-for-password-strength-determination"></a>パスワードの強度決定のための自動化されたサポート

**ID**: NIST SP 800-53 Rev. 4 IA-5 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1338 - 認証子の管理 \| パスワードの強度決定のための自動化されたサポート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="protection-of-authenticators"></a>認証子の保護

**ID**: NIST SP 800-53 Rev. 4 IA-5 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1339 - 認証子の管理 \| 認証子の保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="no-embedded-unencrypted-static-authenticators"></a>暗号化されていない静的認証子の埋め込みなし

**ID**: NIST SP 800-53 Rev. 4 IA-5 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1340 - 認証子の管理 \| 暗号化されていない静的認証子の埋め込みなし](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="multiple-information-system-accounts"></a>複数の情報システム アカウント

**ID**: NIST SP 800-53 Rev. 4 IA-5 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1341 - 認証子の管理 \| 情報システムの複数のアカウント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="hardware-token-based-authentication"></a>ハードウェア トークンベースの認証

**ID**: NIST SP 800-53 Rev. 4 IA-5 (11)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1342 - 認証子の管理 \| ハードウェア トークン ベースの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="expiration-of-cached-authenticators"></a>キャッシュされた認証子の有効期限

**ID**: NIST SP 800-53 Rev. 4 IA-5 (13)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1343 - 認証子の管理 \| キャッシュされた認証子の有効期限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>認証子のフィードバック

**ID**: NIST SP 800-53 Rev. 4 IA-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1344 - 認証子のフィードバック](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>暗号化モジュールの認証

**ID**: NIST SP 800-53 Rev. 4 IA-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1345 - 暗号化モジュールの認証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>識別と認証 (組織外のユーザー)

**ID**: NIST SP 800-53 Rev. 4 IA-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1346 - 識別と認証 (組織外のユーザー)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="acceptance-of-piv-credentials-from-other-agencies"></a>他の政府機関からの PIV 資格情報の受け入れ

**ID**: NIST SP 800-53 Rev. 4 IA-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1347 - 識別と認証 (組織外のユーザー) \| PIV 資格情報の他の政府機関からの受け入れ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="acceptance-of-third-party-credentials"></a>サードパーティの資格情報の承諾

**ID**: NIST SP 800-53 Rev. 4 IA-8 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1348 - 識別と認証 (組織外のユーザー) \| サードパーティ資格情報の受け入れ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="use-of-ficam-approved-products"></a>FICAM 承認済みの製品の使用

**ID**: NIST SP 800-53 Rev. 4 IA-8 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1349 - 識別と認証 (組織外のユーザー) \| FICAM 承認済みの製品の使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="use-of-ficam-issued-profiles"></a>FICAM 発行のプロファイルの使用

**ID**: NIST SP 800-53 Rev. 4 IA-8 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1350 - 識別と認証 (組織外のユーザー) \| FICAM 発行のプロファイルの使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |この識別と認証コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>インシデント対応

### <a name="incident-response-policy-and-procedures"></a>インシデント対応ポリシーおよび手順

**ID**: NIST SP 800-53 Rev. 4 IR-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1351 - インシデント対応ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft マネージド コントロール 1352 - インシデント対応ポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>インシデント対応トレーニング

**ID**: NIST SP 800-53 Rev. 4 IR-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1353 - インシデント対応トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft マネージド コントロール 1354 - インシデント対応トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft マネージド コントロール 1355 - インシデント対応トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="simulated-events"></a>シミュレートされたイベント

**ID**: NIST SP 800-53 Rev. 4 IR-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1356 - インシデント対応トレーニング \| シミュレートされたイベント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="automated-training-environments"></a>トレーニング環境の自動化

**ID**: NIST SP 800-53 Rev. 4 IR-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1357 - インシデント対応トレーニング \| 自動化されたトレーニング環境](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>インシデント対応のテスト

**ID**: NIST SP 800-53 Rev. 4 IR-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1358 - インシデント対応のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="coordination-with-related-plans"></a>関連する計画との調整

**ID**: NIST SP 800-53 Rev. 4 IR-3 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1359 - インシデント対応のテスト \| 関連する計画との調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>インシデント処理

**ID**: NIST SP 800-53 Rev. 4 IR-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center で重要度の高いアラートに関するメール通知を有効にします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[サブスクリプション所有者に対する重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |サブスクリプションにセキュリティ違反のおそれがある場合にサブスクリプションの所有者に通知が送信されるようにするには、Security Center で、重要度の高いアラートに関するメール通知をサブスクリプションの所有者に設定します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft マネージド コントロール 1360 - インシデント処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft マネージド コントロール 1361 - インシデント処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft マネージド コントロール 1362 - インシデント処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |
|[サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center からのメール通知を受信するセキュリティの連絡先を設定します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-incident-handling-processes"></a>自動化されたインシデント処理プロセス

**ID**: NIST SP 800-53 Rev. 4 IR-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1363 - インシデント処理 \| 自動化されたインシデント処理プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="dynamic-reconfiguration"></a>動的再構成

**ID**: NIST SP 800-53 Rev. 4 IR-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1364 - インシデント処理 \| 動的再構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="continuity-of-operations"></a>運用の継続性

**ID**: NIST SP 800-53 Rev. 4 IR-4 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1365 - インシデント処理 \| 事業の継続性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="information-correlation"></a>情報の相関関係

**ID**: NIST SP 800-53 Rev. 4 IR-4 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1366 - インシデント処理 \| 情報の相関関係](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="insider-threats---specific-capabilities"></a>内部関係者の脅威 - 特定の機能

**ID**: NIST SP 800-53 Rev. 4 IR-4 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1367 - インシデント処理 \| 内部関係者の脅威 - 特定の機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="correlation-with-external-organizations"></a>外部組織との相関関係

**ID**: NIST SP 800-53 Rev. 4 IR-4 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1368 - インシデント処理 \| 外部組織との相関関係](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>インシデント監視

**ID**: NIST SP 800-53 Rev. 4 IR-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center で重要度の高いアラートに関するメール通知を有効にします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[サブスクリプション所有者に対する重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |サブスクリプションにセキュリティ違反のおそれがある場合にサブスクリプションの所有者に通知が送信されるようにするには、Security Center で、重要度の高いアラートに関するメール通知をサブスクリプションの所有者に設定します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft マネージド コントロール 1369 - インシデント監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |
|[サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center からのメール通知を受信するセキュリティの連絡先を設定します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-tracking--data-collection--analysis"></a>自動化された追跡/データ収集/分析

**ID**: NIST SP 800-53 Rev. 4 IR-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1370 - インシデント監視 \| 自動化された追跡/データ収集/分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>インシデント報告

**ID**: NIST SP 800-53 Rev. 4 IR-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1371 - インシデント報告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft マネージド コントロール 1372 - インシデント報告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="automated-reporting"></a>自動化された報告

**ID**: NIST SP 800-53 Rev. 4 IR-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1373 - インシデント報告 \| 自動化された報告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="vulnerabilities-related-to-incidents"></a>インシデントに関連する脆弱性

**ID**: NIST SP 800-53 Rev. 4 IR-6 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center で重要度の高いアラートに関するメール通知を有効にします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[サブスクリプション所有者に対する重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |サブスクリプションにセキュリティ違反のおそれがある場合にサブスクリプションの所有者に通知が送信されるようにするには、Security Center で、重要度の高いアラートに関するメール通知をサブスクリプションの所有者に設定します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center からのメール通知を受信するセキュリティの連絡先を設定します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="incident-response-assistance"></a>インシデント対応サポート

**ID**: NIST SP 800-53 Rev. 4 IR-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1374 - インシデント対応サポート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="automation-support-for-availability-of-information--support"></a>情報/サポートの可用性に対する自動化サポート

**ID**: NIST SP 800-53 Rev. 4 IR-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1375 - インシデント対応サポート \| 情報/サポートの可用性に対する自動化サポート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="coordination-with-external-providers"></a>外部プロバイダーとの調整

**ID**: NIST SP 800-53 Rev. 4 IR-7 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1376 - インシデント対応サポート \| 外部プロバイダーとの調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft マネージド コントロール 1377 - インシデント対応サポート \| 外部プロバイダーとの調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>インシデント対応計画

**ID**: NIST SP 800-53 Rev. 4 IR-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1378 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft マネージド コントロール 1379 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft マネージド コントロール 1380 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft マネージド コントロール 1381 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft マネージド コントロール 1382 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft マネージド コントロール 1383 - インシデント対応計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>情報流出対応

**ID**: NIST SP 800-53 Rev. 4 IR-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1384 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft マネージド コントロール 1385 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft マネージド コントロール 1386 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft マネージド コントロール 1387 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft マネージド コントロール 1388 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft マネージド コントロール 1389 - 情報流出対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="responsible-personnel"></a>責任者

**ID**: NIST SP 800-53 Rev. 4 IR-9 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1390 - 情報流出対応 \| 責任を負う担当者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="training"></a>トレーニング

**ID**: NIST SP 800-53 Rev. 4 IR-9 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1391 - 情報流出対応 \| トレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="post-spill-operations"></a>流出後の運用

**ID**: NIST SP 800-53 Rev. 4 IR-9 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1392 - 情報流出対応 \| 流出後の操作](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="exposure-to-unauthorized-personnel"></a>許可されていない職員への公開

**ID**: NIST SP 800-53 Rev. 4 IR-9 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1393 - 情報流出対応 \| 承認されていない担当者への公開](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |このインシデント対応コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>メンテナンス

### <a name="system-maintenance-policy-and-procedures"></a>システム メンテナンスのポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 MA-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1394 - システム メンテナンスのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft マネージド コントロール 1395 - システム メンテナンスのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>メンテナンスの管理

**ID**: NIST SP 800-53 Rev. 4 MA-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1396 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft マネージド コントロール 1397 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft マネージド コントロール 1398 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft マネージド コントロール 1399 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft マネージド コントロール 1400 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft マネージド コントロール 1401 - メンテナンスの管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="automated-maintenance-activities"></a>メンテナンス作業の自動化

**ID**: NIST SP 800-53 Rev. 4 MA-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1402 - メンテナンスの管理 \| メンテナンス作業の自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft マネージド コントロール 1403 - メンテナンスの管理 \| メンテナンス作業の自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>メンテナンス ツール

**ID**: NIST SP 800-53 Rev. 4 MA-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1404 - メンテナンス ツール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="inspect-tools"></a>ツールの検査

**ID**: NIST SP 800-53 Rev. 4 MA-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1405 - メンテナンス ツール \| ツールの検査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="inspect-media"></a>メディアの検査

**ID**: NIST SP 800-53 Rev. 4 MA-3 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1406 - メンテナンス ツール \| メディアの検査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="prevent-unauthorized-removal"></a>未承認の削除の防止

**ID**: NIST SP 800-53 Rev. 4 MA-3 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1407 - メンテナンス ツール \| 未承認の移動の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft マネージド コントロール 1408 - メンテナンス ツール \| 未承認の移動の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft マネージド コントロール 1409 - メンテナンス ツール \| 未承認の移動の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft マネージド コントロール 1410 - メンテナンス ツール \| 未承認の移動の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>非ローカル メンテナンス

**ID**: NIST SP 800-53 Rev. 4 MA-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1411 - 非ローカル メンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft マネージド コントロール 1412 - 非ローカル メンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft マネージド コントロール 1413 - 非ローカル メンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft マネージド コントロール 1414 - 非ローカル メンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft マネージド コントロール 1415 - 非ローカル メンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="document-nonlocal-maintenance"></a>非ローカル メンテナンスの文書化

**ID**: NIST SP 800-53 Rev. 4 MA-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1416 - 非ローカル メンテナンス \| 非ローカル メンテナンスの文書化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="comparable-security--sanitization"></a>同等のセキュリティ/サニタイズ

**ID**: NIST SP 800-53 Rev. 4 MA-4 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1417 - 非ローカル メンテナンス \| 同等のセキュリティ/サニタイズ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft マネージド コントロール 1418 - 非ローカル メンテナンス \| 同等のセキュリティ/サニタイズ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="cryptographic-protection"></a>暗号化による保護

**ID**: NIST SP 800-53 Rev. 4 MA-4 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1419 - 非ローカル メンテナンス \| 暗号化による保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>メンテナンス担当者

**ID**: NIST SP 800-53 Rev. 4 MA-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1420 - メンテナンス担当者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft マネージド コントロール 1421 - メンテナンス担当者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft マネージド コントロール 1422 - メンテナンス担当者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="individuals-without-appropriate-access"></a>適切なアクセス権のない個人

**ID**: NIST SP 800-53 Rev. 4 MA-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1423 - メンテナンス担当者 \| 適切なアクセス権のない個人](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft マネージド コントロール 1424 - メンテナンス担当者 \| 適切なアクセス権のない個人](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>タイムリーなメンテナンス

**ID**: NIST SP 800-53 Rev. 4 MA-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1425 - タイムリーなメンテナンス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |このメンテナンス コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>メディア保護

### <a name="media-protection-policy-and-procedures"></a>メディア保護のポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 MP-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1426 - メディア保護のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft マネージド コントロール 1427 - メディア保護のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>メディア アクセス

**ID**: NIST SP 800-53 Rev. 4 MP-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1428 - メディア アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>メディアのマーキング

**ID**: NIST SP 800-53 Rev. 4 MP-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1429 - メディアのマーキング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft マネージド コントロール 1430 - メディアのマーキング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>メディアの保管

**ID**: NIST SP 800-53 Rev. 4 MP-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1431 - メディアの保管](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft マネージド コントロール 1432 - メディアの保管](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>メディアの輸送

**ID**: NIST SP 800-53 Rev. 4 MP-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1433 - メディアの輸送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft マネージド コントロール 1434 - メディアの輸送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft マネージド コントロール 1435 - メディアの輸送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft マネージド コントロール 1436 - メディアの輸送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="cryptographic-protection"></a>暗号化による保護

**ID**: NIST SP 800-53 Rev. 4 MP-5 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1437 - メディアの輸送 \| 暗号化による保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>メディアのサニタイズ

**ID**: NIST SP 800-53 Rev. 4 MP-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1438 - メディアのサニタイズ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft マネージド コントロール 1439 - メディアのサニタイズ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="review--approve--track--document--verify"></a>レビュー/承認/追跡/文書化/検証

**ID**: NIST SP 800-53 Rev. 4 MP-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1440 - メディアのサニタイズ \| レビュー/承認/追跡/文書化/検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="equipment-testing"></a>機器のテスト

**ID**: NIST SP 800-53 Rev. 4 MP-6 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1441 - メディアのサニタイズ \| 機器のテスト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="nondestructive-techniques"></a>非破壊法

**ID**: NIST SP 800-53 Rev. 4 MP-6 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1442 - メディアのサニタイズ \| 非破壊法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>メディアの使用

**ID**: NIST SP 800-53 Rev. 4 MP-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1443 - メディアの使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="prohibit-use-without-owner"></a>所有者なしでの使用の禁止

**ID**: NIST SP 800-53 Rev. 4 MP-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1444 - メディアの使用 \| 所有者なしでの使用の禁止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |このメディア保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>物理的および環境的な保護

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>物理的および環境的保護に関するポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 PE-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1445 - 物理的および環境的保護に関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft マネージド コントロール 1446 - 物理的および環境的保護に関するポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>物理的なアクセスの承認

**ID**: NIST SP 800-53 Rev. 4 PE-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1447 - 物理的なアクセスの承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft マネージド コントロール 1448 - 物理的なアクセスの承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft マネージド コントロール 1449 - 物理的なアクセスの承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft マネージド コントロール 1450 - 物理的なアクセスの承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>物理的なアクセスの制御

**ID**: NIST SP 800-53 Rev. 4 PE-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1451 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft マネージド コントロール 1452 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft マネージド コントロール 1453 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft マネージド コントロール 1454 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft マネージド コントロール 1455 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft マネージド コントロール 1456 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft マネージド コントロール 1457 - 物理的なアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="information-system-access"></a>情報システムへのアクセス

**ID**: NIST SP 800-53 Rev. 4 PE-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1458 - 物理的なアクセスの制御 \| 情報システムへのアクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>転送メディアに対するアクセスの制御

**ID**: NIST SP 800-53 Rev. 4 PE-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1459 - 転送メディアに対するアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>出力デバイスのアクセスの制御

**ID**: NIST SP 800-53 Rev. 4 PE-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1460 - 出力デバイスのアクセスの制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>物理的なアクセスの監視

**ID**: NIST SP 800-53 Rev. 4 PE-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1461 - 物理的なアクセスの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft マネージド コントロール 1462 - 物理的なアクセスの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft マネージド コントロール 1463 - 物理的なアクセスの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="intrusion-alarms--surveillance-equipment"></a>侵入警報装置/監視装置

**ID**: NIST SP 800-53 Rev. 4 PE-6 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1464 - 物理的なアクセスの監視 \| 侵入警報装置/監視装置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access-to-information-systems"></a>情報システムへの物理的なアクセスの監視

**ID**: NIST SP 800-53 Rev. 4 PE-6 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1465 - 物理的なアクセスの監視 \| 情報システムへの物理的なアクセスの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>訪問者のアクセスの記録

**ID**: NIST SP 800-53 Rev. 4 PE-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1466 - 訪問者のアクセスの記録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft マネージド コントロール 1467 - 訪問者のアクセスの記録](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="automated-records-maintenance--review"></a>記録の自動メンテナンス/レビュー

**ID**: NIST SP 800-53 Rev. 4 PE-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1468 - 訪問者のアクセス記録 \| 記録の自動メンテナンス/レビュー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>電源装置およびケーブル

**ID**: NIST SP 800-53 Rev. 4 PE-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1469 - 電源装置およびケーブル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>緊急遮断

**ID**: NIST SP 800-53 Rev. 4 PE-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1470 - 緊急遮断](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft マネージド コントロール 1471 - 緊急遮断](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft マネージド コントロール 1472 - 緊急遮断](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>非常用電源

**ID**: NIST SP 800-53 Rev. 4 PE-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1473 - 非常用電源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="long-term-alternate-power-supply---minimal-operational-capability"></a>長期的な代替電源装置 - 最小限の運用機能

**ID**: NIST SP 800-53 Rev. 4 PE-11 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1474 - 非常用電源 \| 長期的な代替電源装置 - 最小限の運用機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>非常用照明

**ID**: NIST SP 800-53 Rev. 4 PE-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1475 - 非常用照明](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>防火

**ID**: NIST SP 800-53 Rev. 4 PE-13

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1476 - 防火](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="detection-devices--systems"></a>探知装置/システム

**ID**: NIST SP 800-53 Rev. 4 PE-13 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1477 - 防火 \| 探知装置/システム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="suppression-devices--systems"></a>抑止装置/システム

**ID**: NIST SP 800-53 Rev. 4 PE-13 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1478 - 防火 \| 消火装置/システム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="automatic-fire-suppression"></a>自動消火装置

**ID**: NIST SP 800-53 Rev. 4 PE-13 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1479 - 防火 \| 自動消火装置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>温湿度制御

**ID**: NIST SP 800-53 Rev. 4 PE-14

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1480 - 温湿度制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft マネージド コントロール 1481 - 温湿度制御](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="monitoring-with-alarms--notifications"></a>警報/通知による監視

**ID**: NIST SP 800-53 Rev. 4 PE-14 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1482 - 温湿度制御 \| 監視と警報/通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>水害からの保護

**ID**: NIST SP 800-53 Rev. 4 PE-15

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1483 - 水害からの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="automation-support"></a>自動化サポート

**ID**: NIST SP 800-53 Rev. 4 PE-15 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1484 - 水害からの保護 \| 自動サポート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>納入と撤去

**ID**: NIST SP 800-53 Rev. 4 PE-16

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1485 - 納入と撤去](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>代替作業サイト

**ID**: NIST SP 800-53 Rev. 4 PE-17

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1486 - 代替作業サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft マネージド コントロール 1487 - 代替作業サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft マネージド コントロール 1488 - 代替作業サイト](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>情報システム コンポーネントの場所

**ID**: NIST SP 800-53 Rev. 4 PE-18

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1489 - 情報システム コンポーネントの場所](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |この物理的および環境的な保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>計画

### <a name="security-planning-policy-and-procedures"></a>セキュリティ計画のポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 PL-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1490 - セキュリティ計画のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft マネージド コントロール 1491 - セキュリティ計画のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>システムセキュリティ計画

**ID**: NIST SP 800-53 Rev. 4 PL-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1492 - システム セキュリティ計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft マネージド コントロール 1493 - システム セキュリティ計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft マネージド コントロール 1494 - システム セキュリティ計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft マネージド コントロール 1495 - システム セキュリティ計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft マネージド コントロール 1496 - システム セキュリティ計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="plan--coordinate-with-other-organizational-entities"></a>他の組織エンティティとの計画/調整

**ID**: NIST SP 800-53 Rev. 4 PL-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1497 - システム セキュリティ プラン \| 計画/他の組織エンティティとの調整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>行動規範

**ID**: NIST SP 800-53 Rev. 4 PL-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1498 - 行動規範](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft マネージド コントロール 1499 - 行動規範](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft マネージド コントロール 1500 - 行動規範](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft マネージド コントロール 1501 - 行動規範](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="social-media-and-networking-restrictions"></a>ソーシャル メディアとネットワーキングの制限

**ID**: NIST SP 800-53 Rev. 4 PL-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1502 - 行動規範 \| ソーシャルメディア/ネットワーキングの制限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>情報セキュリティアーキテクチャ

**ID**: NIST SP 800-53 Rev. 4 PL-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1503 - 情報セキュリティ アーキテクチャ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft マネージド コントロール 1504 - 情報セキュリティ アーキテクチャ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft マネージド コントロール 1505 - 情報セキュリティ アーキテクチャ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |この計画コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>人的セキュリティ

### <a name="personnel-security-policy-and-procedures"></a>人的セキュリティのポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 PS-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1506 - 人的セキュリティのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft マネージド コントロール 1507 - 人的セキュリティのポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>職位に対するリスク指定

**ID**: NIST SP 800-53 Rev. 4 PS-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1508 - 職位に対するリスク指定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft マネージド コントロール 1509 - 職位に対するリスク指定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft マネージド コントロール 1510 - 職位に対するリスク指定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>職員のスクリーニング

**ID**: NIST SP 800-53 Rev. 4 PS-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1511 - 職員のスクリーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft マネージド コントロール 1512 - 職員のスクリーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="information-with-special-protection-measures"></a>特殊な保護対策のある情報

**ID**: NIST SP 800-53 Rev. 4 PS-3 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1513 - 職員のスクリーニング \| 特殊な保護対策のある情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft マネージド コントロール 1514 - 職員のスクリーニング \| 特殊な保護対策のある情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>職員の離職

**ID**: NIST SP 800-53 Rev. 4 PS-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1515 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft マネージド コントロール 1516 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft マネージド コントロール 1517 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft マネージド コントロール 1518 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft マネージド コントロール 1519 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft マネージド コントロール 1520 - 職員の離職](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="automated-notification"></a>自動通知

**ID**: NIST SP 800-53 Rev. 4 PS-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1521 - 職員の離職 \| 自動通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>職員の異動

**ID**: NIST SP 800-53 Rev. 4 PS-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1522 - 職員の異動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft マネージド コントロール 1523 - 職員の異動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft マネージド コントロール 1524 - 職員の異動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft マネージド コントロール 1525 - 職員の異動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>アクセス契約

**ID**: NIST SP 800-53 Rev. 4 PS-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1526 - アクセス契約](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft マネージド コントロール 1527 - アクセス契約](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft マネージド コントロール 1528 - アクセス契約](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>サードパーティの人的セキュリティ

**ID**: NIST SP 800-53 Rev. 4 PS-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1529 - サードパーティの人的セキュリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft マネージド コントロール 1530 - サードパーティの人的セキュリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft マネージド コントロール 1531 - サードパーティの人的セキュリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft マネージド コントロール 1532 - サードパーティの人的セキュリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft マネージド コントロール 1533 - サードパーティの人的セキュリティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>職員の処罰

**ID**: NIST SP 800-53 Rev. 4 PS-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1534 - 職員の処罰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft マネージド コントロール 1535 - 職員の処罰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |この人的セキュリティ コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>リスク評価

### <a name="risk-assessment-policy-and-procedures"></a>リスク評価のポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 RA-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1536 - リスク評価のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft マネージド コントロール 1537 - リスク評価のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>セキュリティ分類

**ID**: NIST SP 800-53 Rev. 4 RA-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1538 - セキュリティ分類](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft マネージド コントロール 1539 - セキュリティ分類](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft マネージド コントロール 1540 - セキュリティ分類](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>リスク評価

**ID**: NIST SP 800-53 Rev. 4 RA-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1541 - リスク評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft マネージド コントロール 1542 - リスク評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft マネージド コントロール 1543 - リスク評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft マネージド コントロール 1544 - リスク評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft マネージド コントロール 1545 - リスク評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>脆弱性のスキャン

**ID**: NIST SP 800-53 Rev. 4 RA-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft マネージド コントロール 1546 - 脆弱性のスキャン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft マネージド コントロール 1547 - 脆弱性のスキャン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft マネージド コントロール 1548 - 脆弱性のスキャン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft マネージド コントロール 1549 - 脆弱性のスキャン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft マネージド コントロール 1550 - 脆弱性のスキャン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[SQL データベースでは脆弱性の検出結果を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |脆弱性評価スキャン結果と、データベースの脆弱性を修正する方法についての推奨事項を監視します。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ba6d016-e7c3-4842-b8f2-4992ebc0d72d) |SQL の脆弱性評価では、データベースのセキュリティの脆弱性をスキャンすることで、構成の誤り、過剰なアクセス許可、機密データの未保護など、ベスト プラクティスからの逸脱をすべて明らかにします。 見つかった脆弱性を解決すると、データベースのセキュリティ態勢が大幅に向上する可能性があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerSQLVulnerabilityAssessment_Audit.json) |
|[Azure Container Registry イメージの脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |コンテナー イメージの脆弱性評価では、プッシュされた各コンテナー イメージのセキュリティ脆弱性についてご利用のレジストリがスキャンされ、各イメージの詳細な結果が公開されます (Qualys を使用)。 脆弱性を解決することで、お使いのコンテナーのセキュリティ体制が大幅に向上し、それらを攻撃から保護できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
|[コンテナーのセキュリティ構成の脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker がインストールされているマシンのセキュリティ構成の脆弱性を監査し、Azure Security Center で推奨事項として表示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL Managed Instance で脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |定期的な脆弱性評価スキャンが有効になっていない各 SQL Managed Instance を監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[脆弱性評価を SQL サーバー上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |定期的な脆弱性評価スキャンが有効になっていない Azure SQL サーバーを監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Synapse ワークスペースで脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0049a6b3-a662-4f3e-8635-39cf44ace45a) |Synapse ワークスペースで定期的な SQL 脆弱性評価スキャンを構成することで、潜在的な脆弱性を検出、追跡、修復します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/ASC_SQLVulnerabilityAssessmentOnSynapse_Audit.json) |

### <a name="update-tool-capability"></a>更新ツールの機能

**ID**: NIST SP 800-53 Rev. 4 RA-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1551 - 脆弱性のスキャン \| 更新ツールの機能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="update-by-frequency--prior-to-new-scan--when-identified"></a>頻度による更新/新規スキャン前の更新/識別時の更新

**ID**: NIST SP 800-53 Rev. 4 RA-5 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1552 - 脆弱性のスキャン \| 頻度による/新規スキャンの前/識別時の更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="breadth--depth-of-coverage"></a>対象範囲の広さと深さ

**ID**: NIST SP 800-53 Rev. 4 RA-5 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1553 - 脆弱性のスキャン \| 対象範囲の広さ/深さ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="discoverable-information"></a>検出可能な情報

**ID**: NIST SP 800-53 Rev. 4 RA-5 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1554 - 脆弱性のスキャン \| 検出可能な情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="privileged-access"></a>特権アクセス

**ID**: NIST SP 800-53 Rev. 4 RA-5 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1555 - 脆弱性のスキャン \| 特権アクセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="automated-trend-analyses"></a>自動傾向分析

**ID**: NIST SP 800-53 Rev. 4 RA-5 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1556 - 脆弱性のスキャン \| 自動傾向分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="review-historic-audit-logs"></a>履歴監査ログの確認

**ID**: NIST SP 800-53 Rev. 4 RA-5 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1557 - 脆弱性のスキャン \| 履歴監査ログの確認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="correlate-scanning-information"></a>スキャン情報の関連付け

**ID**: NIST SP 800-53 Rev. 4 RA-5 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1558 - 脆弱性のスキャン \| スキャン情報の関連付け](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |このリスク評価コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>システムとサービスの取得

### <a name="system-and-services-acquisition-policy-and-procedures"></a>システムとサービスの取得のポリシーおよび手順

**ID**: NIST SP 800-53 Rev. 4 SA-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1559 - システムとサービスの取得のポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft マネージド コントロール 1560 - システムとサービスの取得のポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>リソースの割り当て

**ID**: NIST SP 800-53 Rev. 4 SA-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1561 - リソースの割り当て](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft マネージド コントロール 1562 - リソースの割り当て](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft マネージド コントロール 1563 - リソースの割り当て](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>システム開発ライフ サイクル

**ID**: NIST SP 800-53 Rev. 4 SA-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1564 - システム開発ライフ サイクル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft マネージド コントロール 1565 - システム開発ライフ サイクル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft マネージド コントロール 1566 - システム開発ライフ サイクル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft マネージド コントロール 1567 - システム開発ライフ サイクル](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>取得プロセス

**ID**: NIST SP 800-53 Rev. 4 SA-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1568 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft マネージド コントロール 1569 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft マネージド コントロール 1570 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft マネージド コントロール 1571 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft マネージド コントロール 1572 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft マネージド コントロール 1573 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft マネージド コントロール 1574 - 取得プロセス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="functional-properties-of-security-controls"></a>セキュリティ コントロールの機能プロパティ

**ID**: NIST SP 800-53 Rev. 4 SA-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1575 - 取得プロセス \| セキュリティ コントロールの機能プロパティ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="design--implementation-information-for-security-controls"></a>セキュリティ コントロールの設計/実装の情報

**ID**: NIST SP 800-53 Rev. 4 SA-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1576 - 取得プロセス \| セキュリティ コントロールの設計/実装の情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="continuous-monitoring-plan"></a>継続的な監視計画

**ID**: NIST SP 800-53 Rev. 4 SA-4 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1577 - 取得プロセス \| 継続的な監視計画](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="functions--ports--protocols--services-in-use"></a>使用される機能/ポート/プロトコル/サービス

**ID**: NIST SP 800-53 Rev. 4 SA-4 (9)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1578 - 取得プロセス \| 使用される機能/ポート/プロトコル/サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="use-of-approved-piv-products"></a>承認済み PIV 製品の使用

**ID**: NIST SP 800-53 Rev. 4 SA-4 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1579 - 取得プロセス \| 承認済み PIV 製品の使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>情報システム ドキュメント

**ID**: NIST SP 800-53 Rev. 4 SA-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1580 - 情報システム ドキュメント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft マネージド コントロール 1581 - 情報システム ドキュメント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft マネージド コントロール 1582 - 情報システム ドキュメント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft マネージド コントロール 1583 - 情報システム ドキュメント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft マネージド コントロール 1584 - 情報システム ドキュメント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>セキュリティ エンジニアリングの原則

**ID**: NIST SP 800-53 Rev. 4 SA-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1585 - セキュリティ エンジニアリングの原則](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>外部の情報システム サービス

**ID**: NIST SP 800-53 Rev. 4 SA-9

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1586 - 外部の情報システム サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft マネージド コントロール 1587 - 外部の情報システム サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft マネージド コントロール 1588 - 外部の情報システム サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="risk-assessments--organizational-approvals"></a>リスク評価または組織の承認

**ID**: NIST SP 800-53 Rev. 4 SA-9 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1589 - 外部の情報システム サービス \| リスク評価/組織の承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft マネージド コントロール 1590 - 外部の情報システム サービス \| リスク評価/組織の承認](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="identification-of-functions--ports--protocols--services"></a>機能、ポート、プロトコル、サービスの識別

**ID**: NIST SP 800-53 Rev. 4 SA-9 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1591 - 外部の情報システム サービス \| 機能/ポート/プロトコル/サービスの識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="consistent-interests-of-consumers-and-providers"></a>コンシューマーとプロバイダーの関心の整合性

**ID**: NIST SP 800-53 Rev. 4 SA-9 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1592 - 外部の情報システム サービス \| コンシューマーとプロバイダーの関心の整合性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="processing-storage-and-service-location"></a>処理、ストレージ、サービスの場所

**ID**: NIST SP 800-53 Rev. 4 SA-9 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1593 - 外部の情報システム サービス \| 処理、ストレージ、およびサービスの場所](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>開発者による構成管理

**ID**: NIST SP 800-53 Rev. 4 SA-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1594 - 開発者による構成管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft マネージド コントロール 1595 - 開発者による構成管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft マネージド コントロール 1596 - 開発者による構成管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft マネージド コントロール 1597 - 開発者による構成管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft マネージド コントロール 1598 - 開発者による構成管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="software--firmware-integrity-verification"></a>ソフトウェア/ファームウェアの整合性の検証

**ID**: NIST SP 800-53 Rev. 4 SA-10 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1599 - 開発者の構成管理 \| ソフトウェア/ファームウェアの整合性の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>開発者によるセキュリティのテストと評価

**ID**: NIST SP 800-53 Rev. 4 SA-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1600 - 開発者によるセキュリティのテストと評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft マネージド コントロール 1601 - 開発者によるセキュリティのテストと評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft マネージド コントロール 1602 - 開発者によるセキュリティのテストと評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft マネージド コントロール 1603 - 開発者によるセキュリティのテストと評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft マネージド コントロール 1604 - 開発者によるセキュリティのテストと評価](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="static-code-analysis"></a>スタティック コード分析

**ID**: NIST SP 800-53 Rev. 4 SA-11 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1605 - 開発者によるセキュリティのテストと評価 \| 静的コード分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="threat-and-vulnerability-analyses"></a>脅威と脆弱性の分析

**ID**: NIST SP 800-53 Rev. 4 SA-11 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1606 - 開発者によるセキュリティのテストと評価 \| 脅威と脆弱性の分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="dynamic-code-analysis"></a>動的コード分析

**ID**: NIST SP 800-53 Rev. 4 SA-11 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1607 - 開発者によるセキュリティのテストと評価 \| 動的コード分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>サプライ チェーンの保護

**ID**: NIST SP 800-53 Rev. 4 SA-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1608 - サプライ チェーンの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>開発プロセス、標準、およびツール

**ID**: NIST SP 800-53 Rev. 4 SA-15

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1609 - 開発プロセス、標準、およびツール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft マネージド コントロール 1610 - 開発プロセス、標準、およびツール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>開発者が提供するトレーニング

**ID**: NIST SP 800-53 Rev. 4 SA-16

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1611 - 開発者が提供するトレーニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>開発者によるセキュリティ アーキテクチャと設計

**ID**: NIST SP 800-53 Rev. 4 SA-17

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1612 - 開発者によるセキュリティ アーキテクチャと設計](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft マネージド コントロール 1613 - 開発者によるセキュリティ アーキテクチャと設計](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft マネージド コントロール 1614 - 開発者によるセキュリティ アーキテクチャと設計](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |このシステムとサービスの取得コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>システムと通信の保護

### <a name="system-and-communications-protection-policy-and-procedures"></a>システムと通信の保護のポリシーと手順

**ID**: NIST SP 800-53 Rev. 4 SC-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1615 - システムと通信の保護のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft マネージド コントロール 1616 - システムと通信の保護のポリシーと手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>アプリケーションのパーティション分割

**ID**: NIST SP 800-53 Rev. 4 SC-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1617 - アプリケーションのパーティション分割](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>セキュリティ機能の分離

**ID**: NIST SP 800-53 Rev. 4 SC-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |脅威と脆弱性から保護するため、お使いの仮想マシン スケール セットでのエンドポイント保護ソリューションの存在と正常性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft マネージド コントロール 1618 - セキュリティ機能の分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[マシンで Windows Defender Exploit Guard を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントを使用します。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="information-in-shared-resources"></a>共有リソースの情報

**ID**: NIST SP 800-53 Rev. 4 SC-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1619 - 共有リソースの情報](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>サービス拒否の防止

**ID**: NIST SP 800-53 Rev. 4 SC-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection Standard を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |パブリック IP を持つアプリケーション ゲートウェイに属するサブネットがあるすべての仮想ネットワークで、DDoS Protection Standard が有効でなければなりません。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[仮想マシン上の IP 転送を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |仮想マシンの NIC で IP 転送を有効にすると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Microsoft マネージド コントロール 1620 - サービス拒否の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |
|[Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="resource-availability"></a>リソースの可用性

**ID**: NIST SP 800-53 Rev. 4 SC-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1621 - リソースの可用性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>境界保護

**ID**: NIST SP 800-53 Rev. 4 SC-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |一部のサブネットが次世代のファイアウォールで保護されていないことを Azure Security Center で確認しました。 Azure Firewall またはサポートされている次世代のファイアウォールを使用してアクセスを制限することにより、潜在的な脅威からサブネットを保護してください |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center により、お使いのネットワーク セキュリティ グループの一部の受信規則について制限が少なすぎると判断されました。 受信規則では、"Any" または "Internet" の範囲からのアクセスを許可しないでください。 これにより、攻撃者がお使いのリソースをターゲットにできる可能性があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management サービスには仮想ネットワークが使用されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network のデプロイにより、セキュリティが強化され、分離が行われ、インターネット ルーティングできないネットワークに API Management サービスを配置して、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用してオンプレミス ネットワークに接続できます。これにより、ネットワークやオンプレミス内のバックエンド サービスにアクセスできるようになります。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[Kubernetes Services では、許可する IP の範囲を定義する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |Kubernetes Service Management API へのアクセスは、特定の範囲の IP にのみ API アクセスに許可して制限する必要があります。 許可されたネットワークのアプリケーションのみがクラスターにアクセスできるよう、許可する IP 範囲にアクセスを制限することをお勧めします。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスではパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Azure Cognitive Search サービスが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成すると、Search サービスの露出を制限できます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search サービスはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Cognitive Search にマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB のアカウントにはファイアウォール規則を含める必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |承認されていないソースからのトラフィックを防ぐために、Azure Cosmos DB アカウントに対してファイアウォール規則を定義する必要があります。 仮想ネットワーク フィルターを有効にして定義されている IP 規則が少なくとも 1 つあるアカウントは、準拠していると見なされます。 パブリック アクセスを無効にしているアカウントも、準拠していると見なされます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory にはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Data Factory にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid ドメインではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid ドメインにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid トピックではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid トピックにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault should disable public network access (Azure Key Vault で公衆ネットワーク アクセスを無効にする必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |キー コンテナーの公衆ネットワーク アクセスを無効にして、パブリック インターネット経由でアクセスできないようにします。 これにより、データ漏えいのリスクを軽減することができます。 詳細については、[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md) を参照してください。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Machine Learning ワークスペースにマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Service Bus 名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Azure SignalR Service リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが減ります。 プライベート リンクの詳細については、[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md) を参照してください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Synapse ワークスペースにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub サービスではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Web PubSub サービスにマッピングすると、データ漏えいのリスクを軽減できます。 プライベート リンクの詳細については、[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services アカウントでパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Cognitive Services アカウントが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成することで、Cognitive Services アカウントの露出を制限できます。 詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。  |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services アカウントでネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみが Cognitive Services アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Cognitive Services にマッピングすることにより、データ漏えいの可能性を減らすことができます。 プライベート リンクの詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 潜在的な脅威からレジストリを保護するには、特定のパブリック IP アドレスまたはアドレス範囲のみからのアクセスを許可します。 レジストリに IP またはファイアウォール規則、あるいは構成済みの仮想ネットワークがない場合は、異常なリソースとして表示されます。 Container Registry ネットワーク規則の詳細については、[https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) と [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB アカウントでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 CosmosDB アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[ディスク アクセス リソースにはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをディスク アクセスにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[イベント ハブ名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをイベント ハブ名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub Device Provisioning Service インスタンスでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを IoT Hub Device Provisioning Service にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[仮想マシン上の IP 転送を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |仮想マシンの NIC で IP 転送を有効にすると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft マネージド コントロール 1622 - 境界保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft マネージド コントロール 1623 - 境界保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft マネージド コントロール 1624 - 境界保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、インターネットに接続されていない仮想マシンを潜在的な脅威から保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |プライベート エンドポイント接続は、Azure SQL Database へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault 用にプライベート エンドポイントを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |プライベート リンクを使用すると、パブリック インターネット経由でトラフィックを送信せずに、Key Vault を Azure リソースに接続できます。 プライベート リンクにより、データ流出に対する徹底的な防御が提供されます。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |プライベート エンドポイント接続は、Azure Database for MariaDB へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database のパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |パブリック ネットワーク アクセス プロパティを無効にすると、Azure SQL Database へのアクセスがプライベート エンドポイントのみに制限されるため、セキュリティが強化されます。 この構成により、IP または仮想ネットワーク ベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MariaDB にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MySQL にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |パブリック ネットワーク アクセス プロパティを無効にしてセキュリティを強化し、プライベート エンドポイントからのみ Azure Database for PostgreSQL にアクセスできるようにします。 この構成では、Azure IP 範囲外のパブリック アドレス空間からのアクセスが無効になり、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[ストレージ アカウントのパブリック アクセスを禁止する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合があります。 好ましくない匿名アクセスによるデータ侵害を防ぐために、Microsoft では、シナリオで必要でない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 |audit、deny、disabled |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[ストレージ アカウントは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP ベースのフィルター処理の代わりに、推奨される方法として仮想ネットワーク規則を使用して、ストレージ アカウントを潜在的な脅威から保護します。 IP ベースのフィルター処理を無効にすると、パブリック IP がストレージ アカウントにアクセスできなくなります。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[ストレージ アカウントではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 ストレージ アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 プライベート リンクの詳細については、[https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 VM Image Builder の構築リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet) を参照してください。 |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="access-points"></a>アクセス ポイント

**ID**: NIST SP 800-53 Rev. 4 SC-7 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |一部のサブネットが次世代のファイアウォールで保護されていないことを Azure Security Center で確認しました。 Azure Firewall またはサポートされている次世代のファイアウォールを使用してアクセスを制限することにより、潜在的な脅威からサブネットを保護してください |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center により、お使いのネットワーク セキュリティ グループの一部の受信規則について制限が少なすぎると判断されました。 受信規則では、"Any" または "Internet" の範囲からのアクセスを許可しないでください。 これにより、攻撃者がお使いのリソースをターゲットにできる可能性があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management サービスには仮想ネットワークが使用されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network のデプロイにより、セキュリティが強化され、分離が行われ、インターネット ルーティングできないネットワークに API Management サービスを配置して、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用してオンプレミス ネットワークに接続できます。これにより、ネットワークやオンプレミス内のバックエンド サービスにアクセスできるようになります。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[Kubernetes Services では、許可する IP の範囲を定義する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |Kubernetes Service Management API へのアクセスは、特定の範囲の IP にのみ API アクセスに許可して制限する必要があります。 許可されたネットワークのアプリケーションのみがクラスターにアクセスできるよう、許可する IP 範囲にアクセスを制限することをお勧めします。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスではパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Azure Cognitive Search サービスが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成すると、Search サービスの露出を制限できます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search サービスはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Cognitive Search にマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB のアカウントにはファイアウォール規則を含める必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |承認されていないソースからのトラフィックを防ぐために、Azure Cosmos DB アカウントに対してファイアウォール規則を定義する必要があります。 仮想ネットワーク フィルターを有効にして定義されている IP 規則が少なくとも 1 つあるアカウントは、準拠していると見なされます。 パブリック アクセスを無効にしているアカウントも、準拠していると見なされます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory にはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Data Factory にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid ドメインではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid ドメインにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid トピックではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Event Grid トピックにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault should disable public network access (Azure Key Vault で公衆ネットワーク アクセスを無効にする必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |キー コンテナーの公衆ネットワーク アクセスを無効にして、パブリック インターネット経由でアクセスできないようにします。 これにより、データ漏えいのリスクを軽減することができます。 詳細については、[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md) を参照してください。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Machine Learning ワークスペースにマッピングすると、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Service Bus 名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、Azure SignalR Service リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが減ります。 プライベート リンクの詳細については、[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md) を参照してください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse ワークスペースではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Synapse ワークスペースにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub サービスではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Web PubSub サービスにマッピングすると、データ漏えいのリスクを軽減できます。 プライベート リンクの詳細については、[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services アカウントでパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Cognitive Services アカウントが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成することで、Cognitive Services アカウントの露出を制限できます。 詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。  |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services アカウントでネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみが Cognitive Services アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Cognitive Services にマッピングすることにより、データ漏えいの可能性を減らすことができます。 プライベート リンクの詳細については、[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml) を参照してください。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 潜在的な脅威からレジストリを保護するには、特定のパブリック IP アドレスまたはアドレス範囲のみからのアクセスを許可します。 レジストリに IP またはファイアウォール規則、あるいは構成済みの仮想ネットワークがない場合は、異常なリソースとして表示されます。 Container Registry ネットワーク規則の詳細については、[https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) と [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md) を参照してください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB アカウントでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 CosmosDB アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[ディスク アクセス リソースにはプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをディスク アクセスにマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を参照してください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[イベント ハブ名前空間でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントをイベント ハブ名前空間にマッピングすることにより、データ漏えいのリスクが軽減されます。 詳細については、[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub Device Provisioning Service インスタンスでプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを IoT Hub Device Provisioning Service にマッピングすることにより、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[仮想マシン上の IP 転送を無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |仮想マシンの NIC で IP 転送を有効にすると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft マネージド コントロール 1625 - 境界保護 \| アクセス ポイント](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |
|[インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、インターネットに接続されていない仮想マシンを潜在的な脅威から保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database のプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |プライベート エンドポイント接続は、Azure SQL Database へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault 用にプライベート エンドポイントを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |プライベート リンクを使用すると、パブリック インターネット経由でトラフィックを送信せずに、Key Vault を Azure リソースに接続できます。 プライベート リンクにより、データ流出に対する徹底的な防御が提供されます。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |プライベート エンドポイント接続は、Azure Database for MariaDB へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database のパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |パブリック ネットワーク アクセス プロパティを無効にすると、Azure SQL Database へのアクセスがプライベート エンドポイントのみに制限されるため、セキュリティが強化されます。 この構成により、IP または仮想ネットワーク ベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MariaDB にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |パブリック ネットワーク アクセス プロパティを無効にして、セキュリティを強化し、プライベート エンドポイントからのみ Azure Database for MySQL にアクセスできるようにします。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |パブリック ネットワーク アクセス プロパティを無効にしてセキュリティを強化し、プライベート エンドポイントからのみ Azure Database for PostgreSQL にアクセスできるようにします。 この構成では、Azure IP 範囲外のパブリック アドレス空間からのアクセスが無効になり、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインが拒否されます。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[ストレージ アカウントのパブリック アクセスを禁止する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合があります。 好ましくない匿名アクセスによるデータ侵害を防ぐために、Microsoft では、シナリオで必要でない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 |audit、deny、disabled |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[ストレージ アカウントは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP ベースのフィルター処理の代わりに、推奨される方法として仮想ネットワーク規則を使用して、ストレージ アカウントを潜在的な脅威から保護します。 IP ベースのフィルター処理を無効にすると、パブリック IP がストレージ アカウントにアクセスできなくなります。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[ストレージ アカウントではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 ストレージ アカウントにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 プライベート リンクの詳細については、[https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 VM Image Builder の構築リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet) を参照してください。 |Audit, Disabled, Deny |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |受信トラフィックをさらに検査するために、公開されている Web アプリケーションの前に Azure Web Application Firewall (WAF) をデプロイします。 Web Application Firewall (WAF) を使用すると、SQL インジェクション、クロスサイト スクリプティング、ローカルおよびリモートのファイル実行などの一般的な悪用や脆弱性から Web アプリケーションが一元的に保護されます。 また、カスタム ルールを使用して、国、IP アドレス範囲、およびその他の HTTP(S) パラメーターによって Web アプリケーションへのアクセスを制限することもできます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="external-telecommunications-services"></a>外部通信サービス

**ID**: NIST SP 800-53 Rev. 4 SC-7 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1626 - 境界保護 \| 外部通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft マネージド コントロール 1627 - 境界保護 \| 外部通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft マネージド コントロール 1628 - 境界保護 \| 外部通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft マネージド コントロール 1629 - 境界保護 \| 外部通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft マネージド コントロール 1630 - 境界保護 \| 外部通信サービス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="deny-by-default--allow-by-exception"></a>既定で拒否/例外的に許可

**ID**: NIST SP 800-53 Rev. 4 SC-7 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1631 - 境界保護 \| 既定で拒否/例外的に許可](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="prevent-split-tunneling-for-remote-devices"></a>リモート デバイスの分割トンネリングの防止

**ID**: NIST SP 800-53 Rev. 4 SC-7 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1632 - 境界保護 \| リモート デバイスの分割トンネリングの防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="route-traffic-to-authenticated-proxy-servers"></a>認証済みプロキシ サーバーへのトラフィックのルーティング

**ID**: NIST SP 800-53 Rev. 4 SC-7 (8)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1633 - 境界保護 \| 認証済みプロキシ サーバーへのトラフィックのルーティング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="prevent-unauthorized-exfiltration"></a>不正流出の防止

**ID**: NIST SP 800-53 Rev. 4 SC-7 (10)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1634 - 境界保護 \| 不正流出の防止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="host-based-protection"></a>ホストベースの保護

**ID**: NIST SP 800-53 Rev. 4 SC-7 (12)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1635 - 境界保護 \| ホストベースの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="isolation-of-security-tools--mechanisms--support-components"></a>セキュリティ ツール/メカニズム/サポート コンポーネントの分離

**ID**: NIST SP 800-53 Rev. 4 SC-7 (13)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1636 - 境界保護 \| セキュリティ ツール/メカニズム/サポート コンポーネントの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="fail-secure"></a>フェール セキュア

**ID**: NIST SP 800-53 Rev. 4 SC-7 (18)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1637 - 境界保護 \| フェール セキュア](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="dynamic-isolation--segregation"></a>動的な分離または隔離

**ID**: NIST SP 800-53 Rev. 4 SC-7 (20)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1638 - 境界保護 \| 動的な分離/隔離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="isolation-of-information-system-components"></a>情報システム コンポーネントの分離

**ID**: NIST SP 800-53 Rev. 4 SC-7 (21)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1639 - 境界保護 \| 情報システム コンポーネントの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>送信の機密性と整合性

**ID**: NIST SP 800-53 Rev. 4 SC-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight クラスターでは、転送中の暗号化を使用して、Azure HDInsight クラスター ノード間の通信を暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Azure HDInsight クラスター ノード間での転送中に、データが改ざんされる可能性があります。 転送中の暗号化を有効にすると、この転送中の悪用や改ざんの問題に対処できます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[API アプリでは FTPS のみを必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[関数アプリでは FTPS のみを必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Web アプリでは FTPS を必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes クラスターは HTTPS 経由でのみアクセス可能である必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |HTTPS を使用すると、認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されます。 この機能は、Kubernetes Service (AKS) と、AKS Engine および Azure Arc 対応 Kubernetes のプレビューで現在一般提供されています。 詳細については、[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md) を参照してください。 |audit、deny、disabled |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft マネージド コントロール 1640 - 送信の機密性と整合性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows Web サーバーはセキュリティで保護された通信プロトコルを使用するように構成される必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |インターネット経由で通信する情報のプライバシーを保護するために、お客様の Web サーバーでは業界標準の暗号化プロトコルであるトランスポート層セキュリティ (TLS) の最新バージョンを使用する必要があります。 TLS によって、セキュリティ証明書を使用してマシン間の接続が暗号化されることにより、ネットワーク経由の通信がセキュリティで保護されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="cryptographic-or-alternate-physical-protection"></a>暗号化または代替の物理的保護

**ID**: NIST SP 800-53 Rev. 4 SC-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight クラスターでは、転送中の暗号化を使用して、Azure HDInsight クラスター ノード間の通信を暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Azure HDInsight クラスター ノード間での転送中に、データが改ざんされる可能性があります。 転送中の暗号化を有効にすると、この転送中の悪用や改ざんの問題に対処できます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[API アプリでは FTPS のみを必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[関数アプリでは FTPS のみを必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Web アプリでは FTPS を必須とする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |セキュリティを強化するために FTPS 強制を有効にします |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes クラスターは HTTPS 経由でのみアクセス可能である必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |HTTPS を使用すると、認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されます。 この機能は、Kubernetes Service (AKS) と、AKS Engine および Azure Arc 対応 Kubernetes のプレビューで現在一般提供されています。 詳細については、[https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md) を参照してください。 |audit、deny、disabled |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft マネージド コントロール 1641 - 送信の機密性と整合性 \| 暗号化または代替の物理的保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows Web サーバーはセキュリティで保護された通信プロトコルを使用するように構成される必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |インターネット経由で通信する情報のプライバシーを保護するために、お客様の Web サーバーでは業界標準の暗号化プロトコルであるトランスポート層セキュリティ (TLS) の最新バージョンを使用する必要があります。 TLS によって、セキュリティ証明書を使用してマシン間の接続が暗号化されることにより、ネットワーク経由の通信がセキュリティで保護されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>ネットワークの切断

**ID**: NIST SP 800-53 Rev. 4 SC-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1642 - ネットワークの切断](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>暗号化キーの確立と管理

**ID**: NIST SP 800-53 Rev. 4 SC-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR では、保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |カスタマー マネージド キーを使用して、Azure API for FHIR に格納されるデータの保存時の暗号化を制御します (これが規制またはコンプライアンスの要件である場合)。 カスタマー マネージド キーを使用すると、サービス マネージド キーで実行される既定の暗号化レイヤーの上に 2 番目の暗号化レイヤーが追加されて、二重の暗号化が提供されることにもなります。 |audit、disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure Automation アカウントでは、保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |カスタマー マネージド キーを使用して、Azure Automation アカウントの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/automation-cmk](../../../automation/automation-secure-asset-encryption.md#:~:text=Secure assets in Azure Automation include credentials, certificates, connections,,Using Microsoft-managed keys) をご覧ください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
|[Azure Batch アカウントではデータの暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |カスタマー マネージド キーを使用して、Batch アカウントのデータの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK) をご覧ください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_CustomerManagedKey_Audit.json) |
|[Azure Container Instance コンテナー グループでは、暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |カスタマー マネージド キーを使用して、コンテナーをより柔軟にセキュリティで保護します。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、キー暗号化キーのローテーションを制御したり、データを暗号的に消去したりするための追加機能が提供されます。 |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
|[Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |カスタマー マネージド キーを使用して、Azure Cosmos DB の保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/cosmosdb-cmk](../../../cosmos-db/how-to-setup-cmk.md) をご覧ください。 |audit、deny、disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Data Box ジョブは、カスタマー マネージド キーを使用してデバイスのロック解除パスワードを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Azure Data Box に使用するデバイスのロック解除パスワードの暗号化は、カスタマー マネージド キーを使用して管理してください。 また、デバイスの準備とデータのコピーを自動化する目的で、デバイスのロック解除パスワードに対する Data Box サービスのアクセスを管理する際にも、カスタマー マネージド キーが役立ちます。 デバイス上の保存データ自体は、あらかじめ Advanced Encryption Standard 256 ビット暗号化を使用して暗号化されており、また、デバイスのロック解除パスワードも既定で Microsoft マネージド キーを使用して暗号化されています。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|[Azure Data Explorer における保存時の暗号化でカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure Data Explorer クラスターでカスタマー マネージド キーを使用した保存時の暗号化を有効にすると、保存時の暗号化で使用されるキーをさらに制御できるようになります。 この機能は、多くの場合、特別なコンプライアンス要件を持つ顧客に適用でき、キーの管理に Key Vault を必要とします。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure データ ファクトリは、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |カスタマー マネージド キーを使用して、Azure データ ファクトリの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/adf-cmk](../../../data-factory/enable-customer-managed-key.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Azure HDInsight クラスターでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |カスタマー マネージド キーを使用して、Azure HDInsight クラスターの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/hdi.cmk](../../../hdinsight/disk-encryption.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight クラスターでは、ホストでの暗号化を使用して保存データを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |ホストでの暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure Machine Learning ワークスペースは、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8) |カスタマー マネージド キーを使用して、Azure Machine Learning ワークスペース データの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/azureml-workspaces-cmk](../../../machine-learning/how-to-create-workspace-template.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_CMKEnabled_Audit.json) |
|[Azure Monitor ログ クラスターは、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f68a601-6e6d-4e42-babf-3f643a047ea2) |Azure Monitor ログ クラスターは、カスタマー マネージド キー暗号化を使用して作成します。 既定では、ログ データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンスを満たすには、一般にカスタマー マネージド キーが必要です。 Azure Monitor にカスタマー マネージド キーを使用することで、データへのアクセスをより細かく制御することができます。[https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys](../../../azure-monitor/logs/customer-managed-keys.md) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKEnabled_Deny.json) |
|[Azure Recovery Services コンテナーは、バックアップ データを暗号化するために、カスタマー マネージド キーを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |カスタマー マネージド キーを使用して、バックアップ データの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/AB-CmkEncryption](../../../backup/encryption-at-rest-with-cmk.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |
|[Azure Stream Analytics ジョブでは、データの暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |ストレージ アカウントに Stream Analytics ジョブのメタデータとプライベート データ資産を安全に格納したい場合は、カスタマー マネージド キーを使用します。 これで、Stream Analytics データが暗号化される方法を完全に制御できるようになります。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Azure Synapse ワークスペースでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |カスタマー マネージド キーは、Azure Synapse ワークスペースに格納されているデータの保存時の暗号化を制御するために使用されます。 カスタマー マネージド キーを使用すると、サービス マネージド キーによる既定の暗号化の上に 2 番目の暗号化レイヤーが追加されて、二重の暗号化が提供されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Bot Service は、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service は、リソースを自動的に暗号化してデータを保護し、組織のセキュリティとコンプライアンスのコミットメントを満たします。 既定では、Microsoft マネージド暗号化キーが使用されます。 キーの管理やサブスクリプションへのアクセスの制御の柔軟性を高めるには、カスタマー マネージド キーを選択します。これは、Bring Your Own Key (BYOK) とも呼ばれます。 Azure Bot Service 暗号化の詳細については、[https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[Azure Kubernetes Service クラスターのオペレーティング システムとデータ ディスクは両方とも、カスタマー マネージド キーで暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d7be79c-23ba-4033-84dd-45e2a5ccdd67) |カスタマー マネージド キーを使用して OS とデータ ディスクを暗号化することで、キー管理をより細かく制御し、柔軟性を高めることができます。 これは、さまざまな規制や業界のコンプライアンス標準での一般的な要件です。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_CMK_Deny.json) |
|[Cognitive Services アカウントでカスタマー マネージド キーによるデータ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用して、Cognitive Services に格納されているデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 カスタマー マネージド キーの詳細については、[https://go.microsoft.com/fwlink/?linkid=2121321](../../../cognitive-services/encryption/cognitive-services-encryption-keys-portal.md) をご覧ください。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[コンテナー レジストリは、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |カスタマー マネージド キーを使用して、レジストリのコンテンツ保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/acr/CMK](../../../container-registry/container-registry-customer-managed-keys.md) をご覧ください。 |Audit、Deny、Disabled |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[イベント ハブの名前空間では、暗号化のためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1ad735a-e96f-45d2-a7b2-9a4932cab7ec) |Azure Event Hubs では、Microsoft マネージド キー (既定) またはカスタマー マネージド キーのいずれかを使用した保存データの暗号化のオプションがサポートされています。 カスタマー マネージド キーを使用してデータを暗号化することを選択すると、名前空間内のデータを暗号化するためにイベント ハブで使用するキーへのアクセスを割り当て、ローテーション、無効化、および取り消すことができます。 イベント ハブでは、専用クラスター内の名前空間のカスタマー マネージド キーを使用した暗号化のみをサポートしていることに注意してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_CustomerManagedKeyEnabled_Audit.json) |
|[HPC Cache アカウントでは、暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |カスタマー マネージド キーを使用して、Azure HPC Cache の保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 |Audit, Disabled, Deny |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
|[IoT Hub Device Provisioning サービスのデータはカスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |カスタマー マネージド キーを使用して、IoT Hub Device Provisioning Service の保存時の暗号化を管理します。 データはサービス マネージド キーを使用して保存時に自動的に暗号化されますが、規制コンプライアンス標準を満たすためには一般に、カスタマー マネージド キー (CMK) が必要です。 CMK を使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 CMK 暗号化の詳細については、[https://aka.ms/dps/CMK](../../../iot-dps/iot-dps-customer-managed-keys.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |
|[Logic Apps 統合サービス環境は、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |統合サービス環境にデプロイし、カスタマー マネージド キーを使用して Logic Apps データの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[マネージド ディスクはプラットフォーム マネージドおよびカスタマー マネージド キーの両方を使用して二重暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca91455f-eace-4f96-be59-e6e2c35b4816) |セキュリティに対する要件が高く、特定の暗号化アルゴリズム、実装、または侵害されたキーに関連するリスクを懸念しているお客様は、プラットフォーム マネージド暗号化キーを使用してインフラストラクチャ レイヤーに異なる暗号化アルゴリズムまたはモードを使用することにより、暗号化の追加レイヤーを設けることを選ぶことができます。 二重暗号化を使用するには、ディスク暗号化セットが必要です。 詳細については、[https://aka.ms/disks-doubleEncryption](../../../virtual-machines/disk-encryption.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DoubleEncryptionRequired_Deny.json) |
|[Microsoft マネージド コントロール 1643 - 暗号化キーの確立と管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |
|[MySQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |カスタマー マネージド キーを使用して、MySQL サーバーの保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 |AuditIfNotExists、Disabled |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[OS およびデータ ディスクはカスタマー マネージド キーで暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F702dd420-7fcc-42c5-afe8-4026edd20fe0) |カスタマー マネージド キーを使用して、マネージド ディスクのコンテンツ保存時の暗号化を管理します。 既定では、データはプラットフォーム マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/disks-cmk](../../../virtual-machines/disk-encryption.md) をご覧ください。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/OSAndDataDiskCMKRequired_Deny.json) |
|[PostgreSQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18adea5e-f416-4d0f-8aa8-d24321e3e274) |カスタマー マネージド キーを使用して、PostgreSQL サーバーの保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 |AuditIfNotExists、Disabled |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableByok_Audit.json) |
|[Azure Monitor 内の保存されたクエリはログ暗号化のためにカスタマー ストレージ アカウントに保存する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa298e57-9444-42ba-bf04-86e8470e32c7) |保存済みのクエリをストレージ アカウントの暗号化によって保護するには、Log Analytics ワークスペースにストレージ アカウントをリンクします。 一般に、規制コンプライアンスを満たすと共に、Azure Monitor の保存済みのクエリに対するアクセスをより細かく制御するためには、カスタマー マネージド キーが必要です。 上記の点について詳しくは、[https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys?tabs=portal](../../../azure-monitor/logs/customer-managed-keys.md?tabs=portal) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsWorkspaces_CMKBYOSQueryEnabled_Deny.json) |
|[Service Bus Premium の名前空間では、暗号化のためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus では、Microsoft マネージド キー (既定) またはカスタマー マネージド キーのいずれかを使用した保存データの暗号化のオプションがサポートされています。 カスタマー マネージド キーを使用してデータを暗号化することを選択すると、名前空間内のデータを暗号化するために Service Bus で使用するキーへのアクセスを割り当て、ローテーション、無効化、および取り消すことができます。 Service Bus では、Premium 名前空間のカスタマー マネージド キーを使用した暗号化のみをサポートしていることに注意してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
|[SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[ストレージ アカウントの暗号化スコープでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5ec538c-daa0-4006-8596-35468b9148e8) |ストレージ アカウントの暗号化スコープの保存データを管理するには、カスタマー マネージド キーを使用します。 カスタマー マネージド キーを使用すると、自分で作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 ストレージ アカウントの暗号化スコープの詳細については、[https://aka.ms/encryption-scopes-overview](../../../storage/blobs/encryption-scope-overview.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_EncryptionScopesShouldUseCMK_Audit.json) |
|[ストレージ アカウントでは、暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |カスタマー マネージド キーを使用して、より柔軟に BLOB とファイル ストレージ アカウントをセキュリティで保護します。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、キー暗号化キーのローテーションを制御したり、データを暗号的に消去したりするための追加機能が提供されます。 |Audit、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

### <a name="availability"></a>可用性

**ID**: NIST SP 800-53 Rev. 4 SC-12 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1644 - 暗号化キーの確立と管理 \| 可用性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="symmetric-keys"></a>対称キー

**ID**: NIST SP 800-53 Rev. 4 SC-12 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1645 - 暗号化キーの確立と管理 \| 対称キー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="asymmetric-keys"></a>非対称キー

**ID**: NIST SP 800-53 Rev. 4 SC-12 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1646 - 暗号化キーの確立と管理 \| 非対称キー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>暗号化による保護

**ID**: NIST SP 800-53 Rev. 4 SC-13

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1647 - 暗号化による保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>コラボレーション コンピューティング デバイス

**ID**: NIST SP 800-53 Rev. 4 SC-15

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1648 - コラボレーション コンピューティング デバイス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft マネージド コントロール 1649 - コラボレーション コンピューティング デバイス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>公開キー基盤証明書

**ID**: NIST SP 800-53 Rev. 4 SC-17

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1650 - 公開キー基盤証明書](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>モバイル コード

**ID**: NIST SP 800-53 Rev. 4 SC-18

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1651 - モバイル コード](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft マネージド コントロール 1652 - モバイル コード](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft マネージド コントロール 1653 - モバイル コード](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**ID**: NIST SP 800-53 Rev. 4 SC-19

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1654 - Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft マネージド コントロール 1655 - Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>セキュリティで保護された名前/アドレス解決サービス (権限のあるソース)

**ID**: NIST SP 800-53 Rev. 4 SC-20

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1656 - セキュリティで保護された名前/アドレス解決サービス (権限のあるソース)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft マネージド コントロール 1657 - セキュリティで保護された名前/アドレス解決サービス (権限のあるソース)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>セキュリティで保護された名前/アドレス解決サービス (再帰的リゾルバーまたはキャッシュ リゾルバー)

**ID**: NIST SP 800-53 Rev. 4 SC-21

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1658 - セキュリティで保護された名前/アドレス解決サービス (再帰的リゾルバーまたはキャッシュ リゾルバー)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>名前/アドレス解決サービスのアーキテクチャとプロビジョニング

**ID**: NIST SP 800-53 Rev. 4 SC-22

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1659 - 名前/アドレス解決サービスのアーキテクチャとプロビジョニング](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>セッションの信頼性

**ID**: NIST SP 800-53 Rev. 4 SC-23

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1660 - セッションの信頼性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="invalidate-session-identifiers-at-logout"></a>ログアウト時のセッション識別子の無効化

**ID**: NIST SP 800-53 Rev. 4 SC-23 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1661 - セッションの信頼性 \| ログアウト時のセッション識別子の無効化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>既知の状態での機能停止

**ID**: NIST SP 800-53 Rev. 4 SC-24

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1662 - 既知の状態での機能停止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>保存情報の保護

**ID**: NIST SP 800-53 Rev. 4 SC-28

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service Environment では内部暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |InternalEncryption を true に設定すると、App Service Environment のフロント エンドと worker の間で、ページファイル、worker ディスク、内部ネットワーク トラフィックが暗号化されます。 詳細については、[https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[Automation アカウント変数は、暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |機密データを格納するときには、Automation アカウント変数資産の暗号化を有効にすることが重要です |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box ジョブは、デバイス上の保存データに対する二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |デバイス上の保存データに対し、ソフトウェアベースの暗号化の第 2 のレイヤーを有効にしてください。 デバイスの保存データはあらかじめ、Advanced Encryption Standard 256 ビット暗号化で保護されています。 このオプションは、第 2 のデータ暗号化レイヤーを追加するものです。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Monitor ログ クラスターはインフラストラクチャ暗号化を有効にして作成する必要がある (二重暗号化)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |2 種類の暗号化アルゴリズムと 2 種類のキーを使用を使用して、サービス レベルおよびインフラストラクチャ レベルでデータ暗号化の安全を確保するには、Azure Monitor 専用クラスターを使用します。 このオプションは、リージョンでサポートされていれば既定で有効になります。[https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge デバイスは二重暗号化を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |デバイス上の保存データのセキュリティを確保するには、そのデータが二重に暗号化されていること、データへのアクセスが制御されていること、また、デバイスが非アクティブになったときにデータがデータ ディスクから安全な方法で消去されることが必要です。 二重暗号化とは、2 つの暗号化レイヤーを使用することです (データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化と、ハード ドライブに対する組み込みの暗号化)。 詳細については、特定の Stack Edge デバイスのセキュリティの概要ドキュメントを参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |ディスク暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |二重暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 二重暗号化が有効になっている場合、ストレージ アカウント内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回、インフラストラクチャ レベルで 1 回) 暗号化されます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure Database for MySQL サーバーでは、インフラストラクチャ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |Azure Database for MySQL のインフラストラクチャ暗号化を有効にして、データが安全であるというより高いレベルの保証を確保します。 インフラストラクチャ暗号化が有効になっている場合、保存データは、FIPS 140-2 準拠の Microsoft マネージド キーを使用して 2 回暗号化されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[Azure Database for PostgreSQL サーバーでは、インフラストラクチャ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |Azure Database for PostgreSQL のインフラストラクチャ暗号化を有効にして、データが安全であるというより高いレベルの保証を確保します。 インフラストラクチャ暗号化が有効になっている場合、保存データは、FIPS 140-2 準拠の Microsoft マネージド キーを使用して 2 回暗号化されます |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft マネージド コントロール 1663 - 保存情報の保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |
|[SQL データベースの機密データを分類する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure Security Center は、SQL データベースのデータ検出と分類スキャンの結果を監視し、監視とセキュリティの向上のためにデータベース内の機密データを分類するように推奨します |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric では、プライマリ クラスターの証明書を使用して、ノード間通信に 3 つのレベルの保護 (None、Sign、EncryptAndSign) が提供されます。 すべてのノード間メッセージが暗号化され、デジタル署名されるように保護レベルを設定します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[ストレージ アカウントはインフラストラクチャ暗号化を行う必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |インフラストラクチャ暗号化を有効にして、データが安全であることをより高いレベルで保証します。 インフラストラクチャ暗号化が有効な場合、ストレージ アカウントのデータは 2 回暗号化されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[Azure Kubernetes Service クラスターのエージェント ノード プールのための一時ディスクおよびキャッシュは、ホストで暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |データ セキュリティを強化するために、Azure Kubernetes Service ノード仮想マシン (VM) の VM ホストに格納されているデータは、保存時に暗号化する必要があります。 これは、さまざまな規制や業界のコンプライアンス標準での一般的な要件です。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[Transparent Data Encryption を SQL データベース上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[仮想マシンおよび仮想マシン スケール セットでは、ホストでの暗号化が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |ホストで暗号化を使用して、仮想マシンと仮想マシン スケール セットのデータのためのエンドツーエンドの暗号化を取得します。 ホストでの暗号化を使用すると、一時ディスクと OS およびデータ ディスクのキャッシュの保存時の暗号化が有効になります。 ホストでの暗号化が有効になっている場合、一時およびエフェメラル OS ディスクはプラットフォーム マネージド キーを使用して暗号化されます。 OS とデータ ディスクのキャッシュは、ディスクで選択された暗号化の種類に応じて、カスタマー マネージドまたはプラットフォーム マネージド キーのいずれかを使用して保存時に暗号化されます。 詳細については、[https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |既定では、仮想マシンの OS とデータのディスクは、保存時にプラットフォーム マネージド キーを使用して暗号化されます。 一時ディスク、データ キャッシュ、コンピューティングとストレージの間を流れているデータは暗号化されません。 次のような場合は、この推奨事項を無視してください。1. ホストでの暗号化を使用している場合。または 2. マネージド ディスクでのサーバー側暗号化がセキュリティ要件を満たしている場合。 詳細については、「[Azure Disk Storage のサーバー側暗号化](../../../virtual-machines/disk-encryption.md)」および[さまざまなディスク暗号化オファリング](../../../virtual-machines/disk-encryption-overview.md#comparison)に関する記事を参照してください。 |AuditIfNotExists、Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="cryptographic-protection"></a>暗号化による保護

**ID**: NIST SP 800-53 Rev. 4 SC-28 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service Environment では内部暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |InternalEncryption を true に設定すると、App Service Environment のフロント エンドと worker の間で、ページファイル、worker ディスク、内部ネットワーク トラフィックが暗号化されます。 詳細については、[https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[Automation アカウント変数は、暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |機密データを格納するときには、Automation アカウント変数資産の暗号化を有効にすることが重要です |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box ジョブは、デバイス上の保存データに対する二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |デバイス上の保存データに対し、ソフトウェアベースの暗号化の第 2 のレイヤーを有効にしてください。 デバイスの保存データはあらかじめ、Advanced Encryption Standard 256 ビット暗号化で保護されています。 このオプションは、第 2 のデータ暗号化レイヤーを追加するものです。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Monitor ログ クラスターはインフラストラクチャ暗号化を有効にして作成する必要がある (二重暗号化)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |2 種類の暗号化アルゴリズムと 2 種類のキーを使用を使用して、サービス レベルおよびインフラストラクチャ レベルでデータ暗号化の安全を確保するには、Azure Monitor 専用クラスターを使用します。 このオプションは、リージョンでサポートされていれば既定で有効になります。[https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge デバイスは二重暗号化を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |デバイス上の保存データのセキュリティを確保するには、そのデータが二重に暗号化されていること、データへのアクセスが制御されていること、また、デバイスが非アクティブになったときにデータがデータ ディスクから安全な方法で消去されることが必要です。 二重暗号化とは、2 つの暗号化レイヤーを使用することです (データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化と、ハード ドライブに対する組み込みの暗号化)。 詳細については、特定の Stack Edge デバイスのセキュリティの概要ドキュメントを参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |ディスク暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |二重暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 二重暗号化が有効になっている場合、ストレージ アカウント内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回、インフラストラクチャ レベルで 1 回) 暗号化されます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure Database for MySQL サーバーでは、インフラストラクチャ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |Azure Database for MySQL のインフラストラクチャ暗号化を有効にして、データが安全であるというより高いレベルの保証を確保します。 インフラストラクチャ暗号化が有効になっている場合、保存データは、FIPS 140-2 準拠の Microsoft マネージド キーを使用して 2 回暗号化されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[Azure Database for PostgreSQL サーバーでは、インフラストラクチャ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |Azure Database for PostgreSQL のインフラストラクチャ暗号化を有効にして、データが安全であるというより高いレベルの保証を確保します。 インフラストラクチャ暗号化が有効になっている場合、保存データは、FIPS 140-2 準拠の Microsoft マネージド キーを使用して 2 回暗号化されます |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft マネージド コントロール 1664 - 保存情報の保護 \| 暗号化による保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[SQL データベースの機密データを分類する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure Security Center は、SQL データベースのデータ検出と分類スキャンの結果を監視し、監視とセキュリティの向上のためにデータベース内の機密データを分類するように推奨します |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric では、プライマリ クラスターの証明書を使用して、ノード間通信に 3 つのレベルの保護 (None、Sign、EncryptAndSign) が提供されます。 すべてのノード間メッセージが暗号化され、デジタル署名されるように保護レベルを設定します |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[ストレージ アカウントはインフラストラクチャ暗号化を行う必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |インフラストラクチャ暗号化を有効にして、データが安全であることをより高いレベルで保証します。 インフラストラクチャ暗号化が有効な場合、ストレージ アカウントのデータは 2 回暗号化されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[Azure Kubernetes Service クラスターのエージェント ノード プールのための一時ディスクおよびキャッシュは、ホストで暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |データ セキュリティを強化するために、Azure Kubernetes Service ノード仮想マシン (VM) の VM ホストに格納されているデータは、保存時に暗号化する必要があります。 これは、さまざまな規制や業界のコンプライアンス標準での一般的な要件です。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[Transparent Data Encryption を SQL データベース上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[仮想マシンおよび仮想マシン スケール セットでは、ホストでの暗号化が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |ホストで暗号化を使用して、仮想マシンと仮想マシン スケール セットのデータのためのエンドツーエンドの暗号化を取得します。 ホストでの暗号化を使用すると、一時ディスクと OS およびデータ ディスクのキャッシュの保存時の暗号化が有効になります。 ホストでの暗号化が有効になっている場合、一時およびエフェメラル OS ディスクはプラットフォーム マネージド キーを使用して暗号化されます。 OS とデータ ディスクのキャッシュは、ディスクで選択された暗号化の種類に応じて、カスタマー マネージドまたはプラットフォーム マネージド キーのいずれかを使用して保存時に暗号化されます。 詳細については、[https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md) をご覧ください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |既定では、仮想マシンの OS とデータのディスクは、保存時にプラットフォーム マネージド キーを使用して暗号化されます。 一時ディスク、データ キャッシュ、コンピューティングとストレージの間を流れているデータは暗号化されません。 次のような場合は、この推奨事項を無視してください。1. ホストでの暗号化を使用している場合。または 2. マネージド ディスクでのサーバー側暗号化がセキュリティ要件を満たしている場合。 詳細については、「[Azure Disk Storage のサーバー側暗号化](../../../virtual-machines/disk-encryption.md)」および[さまざまなディスク暗号化オファリング](../../../virtual-machines/disk-encryption-overview.md#comparison)に関する記事を参照してください。 |AuditIfNotExists、Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="process-isolation"></a>プロセスによる分離

**ID**: NIST SP 800-53 Rev. 4 SC-39

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1665 - プロセスの分離](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |このシステムと通信の保護コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>システムと情報の整合性

### <a name="system-and-information-integrity-policy-and-procedures"></a>システムと情報の整合性のポリシーおよび手順

**ID**: NIST SP 800-53 Rev. 4 SI-1

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1666 - システムと情報の整合性のポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft マネージド コントロール 1667 - システムと情報の整合性のポリシーおよび手順](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>欠陥の修復

**ID**: NIST SP 800-53 Rev. 4 SI-2

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[API アプリの実行に使用される "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[API アプリの一部として使用される "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java の新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Java バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の Java バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[API アプリの一部として使用される "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[API アプリの一部として使用される "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |現在の Kubernetes バージョンの既知の脆弱性から保護するため、Kubernetes Service クラスターを新しい Kubernetes バージョンにアップグレードしてください。 脆弱性 CVE-2019-9946 は、Kubernetes バージョン 1.11.9 以上、1.12.7 以上、1.13.5 以上、および 1.14.0 以上でパッチが適用されています |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|[Microsoft マネージド コントロール 1668 - 欠陥の修復](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft マネージド コントロール 1669 - 欠陥の修復](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft マネージド コントロール 1670 - 欠陥の修復](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft マネージド コントロール 1671 - 欠陥の修復](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[SQL データベースでは脆弱性の検出結果を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |脆弱性評価スキャン結果と、データベースの脆弱性を修正する方法についての推奨事項を監視します。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |お使いの Windows と Linux の仮想マシン スケール セットが確実にセキュリティで保護されるようにするため、インストールする必要のあるシステムのセキュリティ更新プログラムおよび重要な更新プログラムが不足していないかどうかを監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[システム更新プログラムをマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="central-management"></a>中央管理

**ID**: NIST SP 800-53 Rev. 4 SI-2 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1672 - 欠陥の修復 \| 一元管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="automated-flaw-remediation-status"></a>自動的な欠陥修復状態

**ID**: NIST SP 800-53 Rev. 4 SI-2 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1673 - 欠陥の修復 \| 自動的な欠陥修復状態](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>欠陥を修復するまでの時間/是正措置のベンチマーク

**ID**: NIST SP 800-53 Rev. 4 SI-2 (3)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1674 - 欠陥の修復 \| 欠陥を修復するまでの時間/是正措置のベンチマーク](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft マネージド コントロール 1675 - 欠陥の修復 \| 欠陥を修復するまでの時間/是正措置のベンチマーク](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="removal-of-previous-versions-of-software--firmware"></a>以前のバージョンのソフトウェアまたはファームウェアの削除

**ID**: NIST SP 800-53 Rev. 4 SI-2 (6)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリの実行に使用される "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[API アプリの一部として使用される "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java の新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Java バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の Java バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[API アプリの一部として使用される "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[API アプリの一部として使用される "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |現在の Kubernetes バージョンの既知の脆弱性から保護するため、Kubernetes Service クラスターを新しい Kubernetes バージョンにアップグレードしてください。 脆弱性 CVE-2019-9946 は、Kubernetes バージョン 1.11.9 以上、1.12.7 以上、1.13.5 以上、および 1.14.0 以上でパッチが適用されています |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

### <a name="malicious-code-protection"></a>悪意のあるコードからの保護

**ID**: NIST SP 800-53 Rev. 4 SI-3

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |脅威と脆弱性から保護するため、お使いの仮想マシン スケール セットでのエンドポイント保護ソリューションの存在と正常性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft マネージド コントロール 1676 - 悪意のあるコードからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft マネージド コントロール 1677 - 悪意のあるコードからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft マネージド コントロール 1678 - 悪意のあるコードからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft マネージド コントロール 1679 - 悪意のあるコードからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[マシンで Windows Defender Exploit Guard を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントを使用します。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="central-management"></a>中央管理

**ID**: NIST SP 800-53 Rev. 4 SI-3 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |脅威と脆弱性から保護するため、お使いの仮想マシン スケール セットでのエンドポイント保護ソリューションの存在と正常性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft マネージド コントロール 1680 - 悪意のあるコードの保護 \| 一元的な管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[マシンで Windows Defender Exploit Guard を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントを使用します。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="automatic-updates"></a>自動更新

**ID**: NIST SP 800-53 Rev. 4 SI-3 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1681 - 悪意のあるコードの保護 \| 自動更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="nonsignature-based-detection"></a>シグネチャ ベースではない検出

**ID**: NIST SP 800-53 Rev. 4 SI-3 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1682 - 悪意のあるコードの保護 \| シグネチャ ベースではない検出](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>情報システムの監視

**ID**: NIST SP 800-53 Rev. 4 SI-4

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |一部のサブネットが次世代のファイアウォールで保護されていないことを Azure Security Center で確認しました。 Azure Firewall またはサポートされている次世代のファイアウォールを使用してアクセスを制限することにより、潜在的な脅威からサブネットを保護してください |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Defender の Azure Arc 拡張機能により、Arc 対応 Kubernetes クラスターの脅威保護が提供されます。 拡張機能は、クラスター内のノードからデータを収集し、それを詳しく分析するためにクラウド内の Azure Defender for Kubernetes バックエンドに送信します。 詳細については、「[https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)」を参照してください。 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service は、Azure がクラウド プロバイダーとして提供するクラウドの規模と可視性を活用して、Web アプリへの一般的な攻撃を監視します。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender for Azure SQL Database サーバーを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[コンテナー レジストリ用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |コンテナー レジストリ用の Azure Defender では、過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージの脆弱性をスキャンする機能が提供され、イメージごとの詳細な結果が公開されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS では、Azure リソースからすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。 Azure Defender for DNS の機能に関する詳細については、[https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault では、キー コンテナー アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性のある試行を検出することにより、追加の保護レイヤーとセキュリティ インテリジェンスが提供されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager では、組織内尾のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。 Azure Defender for Resource Manager の機能に関する詳細については、[https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md) を参照してください。 この Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページを参照してください: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender for SQL servers on machines を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL により、潜在的なデータベースの脆弱性を明示および軽減するための機能が提供され、SQL データベースへの脅威を示す可能性のある異常なアクティビティが検出され、機密データの検出と分類が行われます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[保護されていない Azure SQL サーバーに対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[保護されていない SQL Managed Instance に対して Azure Defender for SQL を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Azure Defender for Storage を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender for Storage により、ストレージ アカウントにアクセスしたりそれを悪用したりする、異常で害を及ぼす可能性がある試行を検出できます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[ゲスト構成拡張機能をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |マシンのゲスト内設定について、セキュリティで保護された構成を確保するには、ゲスト構成拡張機能をインストールします。 この拡張機能によって監視されるゲスト内設定には、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境設定が含まれます。 インストールが完了すると、"Windows Exploit Guard を有効にする必要がある" のようなゲスト内ポリシーを使用できるようになります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) をご覧ください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center は、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントを使用します。 仮想マシンが正常に監視されるようにするには、エージェントが仮想マシンにインストールされていること、および構成されているワークスペースにセキュリティ イベントがエージェントによって正しく収集されていることを確認する必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Linux Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center での監視のために、仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |このポリシーは、Log Analytics エージェントがインストールされていない場合にすべての Windows または Linux 仮想マシン (VM) を監査します。Security Center ではこれを使用してセキュリティの脆弱性や脅威を監視します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center での監視のために、仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) からデータを収集します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |このポリシーでは、Log Analytics エージェントがインストールされていない場合に、Windows Azure Arc マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft マネージド コントロール 1683 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft マネージド コントロール 1684 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft マネージド コントロール 1685 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft マネージド コントロール 1686 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft マネージド コントロール 1687 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft マネージド コントロール 1688 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft マネージド コントロール 1689 - 情報システムの監視](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |ゲスト構成拡張機能には、システム割り当てマネージド ID が必要です。 このポリシーのスコープ内の Azure 仮想マシンは、ゲスト構成拡張機能がインストールされていても、システム割り当てマネージド ID がなければ非準拠になります。 詳細については、[https://aka.ms/gcpol](../concepts/guest-configuration.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide-intrusion-detection-system"></a>システム全体の侵入検出システム

**ID**: NIST SP 800-53 Rev. 4 SI-4 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1690 - 情報システムの監視 \| システム全体の侵入検出システム](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="automated-tools-for-real-time-analysis"></a>リアルタイム分析のための自動化ツール

**ID**: NIST SP 800-53 Rev. 4 SI-4 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1691 - 情報システムの監視 \| リアルタイム分析のための自動化ツール](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="inbound-and-outbound-communications-traffic"></a>受信と送信の通信トラフィック

**ID**: NIST SP 800-53 Rev. 4 SI-4 (4)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1692 - 情報システムの監視 \| 受信と送信の通信トラフィック](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="system-generated-alerts"></a>システム生成のアラート

**ID**: NIST SP 800-53 Rev. 4 SI-4 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1693 - 情報システムの監視 \| システム生成のアラート](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="analyze-communications-traffic-anomalies"></a>通信トラフィックの異常の分析

**ID**: NIST SP 800-53 Rev. 4 SI-4 (11)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1694 - 情報システムの監視 \| 通信トラフィックの異常の分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="automated-alerts"></a>自動アラート

**ID**: NIST SP 800-53 Rev. 4 SI-4 (12)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center で重要度の高いアラートに関するメール通知を有効にします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[サブスクリプション所有者に対する重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |サブスクリプションにセキュリティ違反のおそれがある場合にサブスクリプションの所有者に通知が送信されるようにするには、Security Center で、重要度の高いアラートに関するメール通知をサブスクリプションの所有者に設定します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center からのメール通知を受信するセキュリティの連絡先を設定します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="wireless-intrusion-detection"></a>ワイヤレス侵入の検出

**ID**: NIST SP 800-53 Rev. 4 SI-4 (14)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1695 - 情報システムの監視 \| ワイヤレス侵入の検出](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="correlate-monitoring-information"></a>監視情報の関連付け

**ID**: NIST SP 800-53 Rev. 4 SI-4 (16)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1696 - 情報システムの監視 \| 監視情報の関連付け](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="analyze-traffic--covert-exfiltration"></a>トラフィックまたは隠ぺいされた流出の分析

**ID**: NIST SP 800-53 Rev. 4 SI-4 (18)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1697 - 情報システムの監視 \| トラフィック/隠ぺいされた流出の分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="individuals-posing-greater-risk"></a>重大なリスクをもたらしている個人

**ID**: NIST SP 800-53 Rev. 4 SI-4 (19)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1698 - 情報システムの監視 \| 重大なリスクをもたらしている個人](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="privileged-users"></a>特権のあるユーザー

**ID**: NIST SP 800-53 Rev. 4 SI-4 (20)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1699 - 情報システムの監視 \| 特権のあるユーザー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="unauthorized-network-services"></a>承認されていないネットワーク サービス

**ID**: NIST SP 800-53 Rev. 4 SI-4 (22)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1700 - 情報システムの監視 \| 承認されていない Network Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="host-based-devices"></a>ホストベースのデバイス

**ID**: NIST SP 800-53 Rev. 4 SI-4 (23)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1701 - 情報システムの監視 \| ホストベースのデバイス](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="indicators-of-compromise"></a>セキュリティ侵害のインジケーター

**ID**: NIST SP 800-53 Rev. 4 SI-4 (24)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1702 - 情報システムの監視 \| セキュリティ侵害のインジケーター](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>セキュリティに関する警告、勧告、命令

**ID**: NIST SP 800-53 Rev. 4 SI-5

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1703 - セキュリティに関する警告、勧告、命令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft マネージド コントロール 1704 - セキュリティに関する警告、勧告、命令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft マネージド コントロール 1705 - セキュリティに関する警告、勧告、命令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft マネージド コントロール 1706 - セキュリティに関する警告、勧告、命令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="automated-alerts-and-advisories"></a>警告および勧告の自動化

**ID**: NIST SP 800-53 Rev. 4 SI-5 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1707 - セキュリティに関する警告、勧告、命令 \| 自動的な警告および勧告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>セキュリティ機能の検証

**ID**: NIST SP 800-53 Rev. 4 SI-6

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1708 - セキュリティ機能の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft マネージド コントロール 1709 - セキュリティ機能の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft マネージド コントロール 1710 - セキュリティ機能の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft マネージド コントロール 1711 - セキュリティ機能の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>ソフトウェア、ファームウェア、情報の整合性

**ID**: NIST SP 800-53 Rev. 4 SI-7

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1712 - ソフトウェア、ファームウェア、情報の整合性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="integrity-checks"></a>整合性チェック

**ID**: NIST SP 800-53 Rev. 4 SI-7 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1713 - ソフトウェア、ファームウェア、情報の整合性 \| 整合性チェック](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="automated-notifications-of-integrity-violations"></a>整合性違反の自動通知

**ID**: NIST SP 800-53 Rev. 4 SI-7 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1714 - ソフトウェア、ファームウェア、情報の整合性 \| 整合性違反の自動通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="automated-response-to-integrity-violations"></a>整合性違反への自動対応

**ID**: NIST SP 800-53 Rev. 4 SI-7 (5)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1715 - ソフトウェア、ファームウェア、情報の整合性 \| 整合性違反への自動対応](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="integration-of-detection-and-response"></a>検出と応答の統合

**ID**: NIST SP 800-53 Rev. 4 SI-7 (7)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1716 - ソフトウェア、ファームウェア、情報の整合性 \| 検出と応答の統合](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="binary-or-machine-executable-code"></a>バイナリまたはマシン実行可能コード

**ID**: NIST SP 800-53 Rev. 4 SI-7 (14)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1717 - ソフトウェア、ファームウェア、情報の整合性 \| バイナリ コードまたはマシン実行可能コード](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft マネージド コントロール 1718 - ソフトウェア、ファームウェア、情報の整合性 \| バイナリ コードまたはマシン実行可能コード](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>スパムからの保護

**ID**: NIST SP 800-53 Rev. 4 SI-8

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1719 - スパムからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft マネージド コントロール 1720 - スパムからの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="central-management"></a>中央管理

**ID**: NIST SP 800-53 Rev. 4 SI-8 (1)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1721 - スパムからの保護 \| 一元的な管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="automatic-updates"></a>自動更新

**ID**: NIST SP 800-53 Rev. 4 SI-8 (2)

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1722 - スパムからの保護 \| 自動更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>情報入力の検証

**ID**: NIST SP 800-53 Rev. 4 SI-10

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1723 - 情報入力の検証](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>エラー処理

**ID**: NIST SP 800-53 Rev. 4 SI-11

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1724 - エラー処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft マネージド コントロール 1725 - エラー処理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>情報の処理とリテンション期間

**ID**: NIST SP 800-53 Rev. 4 SI-12

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft マネージド コントロール 1726 - 情報の処理とリテンション期間](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>メモリの保護

**ID**: NIST SP 800-53 Rev. 4 SI-16

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サーバー用 Azure Defender を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |サーバー用 Azure Defender では、サーバーのワークロードに対するリアルタイムの脅威の防止が提供され、セキュリティ強化の推奨事項と、不審なアクティビティに関するアラートが生成されます。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft マネージド コントロール 1727 - メモリの保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |このシステムと情報の整合性コントロールは、Microsoft によって実装されています |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |
|[マシンで Windows Defender Exploit Guard を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントを使用します。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

## <a name="next-steps"></a>次のステップ

Azure Policy に関するその他の記事:

- [規制コンプライアンス](../concepts/regulatory-compliance.md)の概要。
- [イニシアチブ定義の構造](../concepts/initiative-definition-structure.md)を参照してください。
- [Azure Policy のサンプル](./index.md)の他の例を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。