---
title: Azure AD の条件付きアクセスに関する復元の既定値群
description: 復元の既定値群と Azure AD Backup Authentication Service
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2bebe9769d38502a37134326228d41979cad5a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432683"
---
# <a name="conditional-access-resilience-defaults-preview"></a>条件付きアクセス: 復元の既定値群 (プレビュー)

プライマリ認証サービスが停止した場合、Azure Active Directory (Azure AD) Backup Authentication Service によって、既存のセッション用のアクセス トークンをアプリケーションに自動的に発行できます。 既存のセッションの再認証が、Azure AD に対する認証の 90% 以上を占めるので、この機能により、Azure AD の回復性が大幅に向上する可能性があります。 ゲスト ユーザーによる新しいセッションや認証は、Backup Authentication Service ではサポートされません。

条件付きアクセスによって保護されている認証の場合、アクセス トークンが発行される前に、ポリシーが再評価されて以下のことが決定されます。

1.  適用される条件付きアクセス ポリシー
1.  適用されるポリシーについて、必要な制御が満たされているか

停止している間、条件付きアクセス ポリシーを適用する必要があるかどうかを判断するため、Backup Authentication Service によってすべての条件をリアルタイムで評価できるわけではありません。 条件付きアクセスの復元の既定値群は新しいセッション制御であり、管理者は、ポリシー条件をリアルタイムで評価できないときは常に、停止中に認証をブロックするか、またはユーザーのセッションの開始時に収集されたデータを使用してポリシーを評価できるようにするかを、決定できます。 

> [!IMPORTANT]
> 復元の既定値群は、新規と既存のすべてのポリシーに対して自動的に有効になります。Microsoft は、停止の影響を軽減するため、復元の既定値群を有効のままにすることを強くお勧めします。 管理者は、個々の条件付きアクセス ポリシーについて、復元の既定値群を無効にできます。 

## <a name="how-does-it-work"></a>それはどのように機能するのでしょうか。

停止の間、Backup Authentication Service によって特定のセッションのアクセス トークンが自動的に再発行されます。

| Session description (セッションの説明) | アクセスが許可されます |
| --- | --- |
| 新しいセッション | いいえ |
| 既存のセッション – 条件付きアクセス ポリシーが構成されていない | はい |
| 既存のセッション – 条件付きアクセス ポリシーが構成されており、MFA などの必要な制御が既に満たされた | はい |
| 既存のセッション – 条件付きアクセス ポリシーが構成されており、MFA などの必要な制御がまだ満たされていない | 復元の既定値群によって決定されます |

Azure AD の停止中に既存のセッションの有効期限が切れると、新しいアクセス トークンの要求は Backup Authentication Service にルーティングされ、すべての条件付きアクセス ポリシーが再評価されます。 条件付きアクセス ポリシーがない場合、または MFA などの必要なすべての制御がセッションの開始時に既に満たされていた場合は、Backup Authentication Service によって新しいアクセス トークンが発行されて、セッションが延長されます。 

ポリシーの必要な制御がまだ満たされていない場合は、アクセスを許可するか拒否するかを判断するため、ポリシーが再評価されます。 ただし、停止中にすべての条件をリアルタイムで再評価できるわけではありません。 異常な状態には次のようなものがあります。 

- グループのメンバーシップ
- ロールのメンバーシップ
- サインイン リスク
- ユーザー リスク
- 国の場所 (新しい IP または GPS 座標の解決)

## <a name="resilience-defaults-enabled"></a>復元の既定値群が有効

復元の既定値群が有効になっている場合、Backup Authentication Service により、セッションの開始時に収集されたデータを使用して、リアルタイム データがない場合にポリシーを適用する必要があるかどうかを評価できます。 既定では、すべてのポリシーで復元の既定値群が有効になります。 停止中に機密性の高いアプリケーションにアクセスするためにリアルタイムのポリシー評価が必要な場合は、個々のポリシーに対して設定を無効にすることができます。

**例**: 復元の既定値群が有効になっているポリシーで、すべてのグローバル管理者が MFA を行うために Azure portal にアクセスする必要があります。 停止前に、グローバル管理者ではないユーザーが Azure portal にアクセスした場合、ポリシーは適用されず、ユーザーには MFA を求めることなくアクセス権が付与されます。 停止中は、Backup Authentication Service によってポリシーが再評価され、ユーザーに MFA を求めるかどうか判断されます。 **Backup Authentication Service では、ロールのメンバーシップをリアルタイムで評価できないので、ユーザーのセッションの開始時に収集されたデータを使用して、ポリシーをまだ適用する必要がないかどうかが判断されます。その結果、MFA を求めることなく、ユーザーにアクセス権が付与されます。**

## <a name="resilience-defaults-disabled"></a>復元の既定値群が無効

復元の既定値群が無効になっている場合、セッションの開始時に収集されたデータを使用した Backup Authentication Service による条件の評価は行われません。 停止中にポリシー条件をリアルタイムで評価できない場合、アクセスは拒否されます。

**例**: 復元の既定値群が無効になっているポリシーで、すべてのグローバル管理者が MFA を行うために Azure portal にアクセスする必要があります。 停止前に、グローバル管理者ではないユーザーが Azure portal にアクセスした場合、ポリシーは適用されず、ユーザーには MFA を求めることなくアクセス権が付与されます。 停止中は、Backup Authentication Service によってポリシーが再評価され、ユーザーに MFA を求めるかどうか判断されます。 **Backup Authentication Service ではロールのメンバーシップをリアルタイムで評価できないので、ユーザーの Azure Portal へのアクセスはブロックされます。**

> [!WARNING]
> グループまたはロールに適用されるポリシーの復元の既定値群を無効にすると、テナント内のすべてのユーザーの回復性が低下します。 停止中はグループとロールのメンバーシップをリアルタイムで評価できないので、ポリシー割り当てのグループまたはロールに属していないユーザーでも、ポリシーのスコープ内のアプリケーションへのアクセスが拒否されます。 ポリシーのスコープに含まれないすべてのユーザーについて回復性が低下しないようにするには、グループまたはロールではなく、個々のユーザーにポリシーを適用することを検討します。 

## <a name="testing-resilience-defaults"></a>復元の既定値群のテスト

現時点では、Backup Authentication Service を使用してドライ ランを実行したり、復元の既定値群が有効または無効になっているポリシーの結果をシミュレートしたりすることはできません。 Azure AD により Backup Authentication Service の使用についてのテストが毎月行われ、Backup Authentication Service を使用してアクセス トークンが発行されたかどうかがサインイン ログで示されます。

## <a name="configuring-resilience-defaults"></a>復元の既定値群の構成

条件付きアクセスの復元の既定値群は、Azure portal、MS Graph API、または PowerShell から構成できます。 

### <a name="azure-portal"></a>Azure portal

1.  **Azure portal** >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します
1.  新しいポリシーを作成するか、既存のポリシーを選択します
1.  セッション制御の設定を開きます
1.  このポリシーの設定を無効にするには、[復元の既定値群を無効にする] を選択します。 ポリシーのスコープ内のサインインは、Azure AD の停止中はブロックされるようになります
1.  ポリシーの変更を保存します

### <a name="ms-graph-apis"></a>MS Graph API

MS Graph API と [Microsoft Graph Explorer](/graph/graph-explorer/graph-explorer-overview) を使用して、条件付きアクセスのポリシーに対する復元の既定値群を管理することもできます。 

要求 URL の例: 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

要求本文の例: 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

このパッチ操作は、Microsoft.Graph.Authentication モジュールをインストールした後で Microsoft PowerShell を使用してデプロイできます。 このモジュールをインストールするには、管理者特権の PowerShell プロンプトを開いて以下を実行します

`Install-Module Microsoft.Graph.Authentication`

Microsoft Graph に接続し、必要なスコープを要求します。

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

プロンプトが表示されたら認証を行います。

PATCH 要求の JSON 本文を作成します。

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

パッチ操作を実行します。

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>推奨事項

Microsoft は、復元の既定値群を有効にすることをお勧めします。 直接的なセキュリティ上の懸念はありませんが、お客様は、停止している間に、リアルタイムではなく、セッション開始時に収集されたデータを使用した、Backup Authentication Service による条件付きアクセス ポリシーの評価を許可するかどうかを、評価する必要があります。 

セッションが開始した後で、ユーザーのロールまたはグループのメンバーシップが変更された可能性があります。 [継続的アクセス評価 (CAE)](concept-continuous-access-evaluation.md) では、アクセス トークンは 24 時間有効ですが、即時失効イベントの対象となります。 Backup Authentication Service では、同じ失効イベントの CAE がサブスクライブされます。 CAE の一部としてユーザーのトークンが取り消された場合、ユーザーは停止中にサインインできません。 復元の既定値群を有効にすると、停止中に期限が切れる既存のセッションが延長されます。 サインイン頻度を強制するようにセッション制御でポリシーが構成されている場合でも、セッションは延長されます。 たとえば、復元の既定値群が有効になっているポリシーでは、ユーザーは SharePoint サイトにアクセスするために 1 時間ごとに再認証が必要になる場合があります。 停止中は、ユーザーの再認証に Azure AD を使用できない場合でも、ユーザーのセッションが延長されます。 

## <a name="next-steps"></a>次の手順

- [継続的アクセス評価 (CAE)](concept-continuous-access-evaluation.md)
