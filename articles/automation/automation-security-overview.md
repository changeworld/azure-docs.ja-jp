---
title: Azure Automation アカウントの認証の概要
description: この記事では、Azure Automation アカウントの認証について概要を紹介します。
keywords: Automation のセキュリティ, セキュリティで保護された Automation; Automation の認証
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 08f265d4e2af8fe985db3ceab78b535db2f73924
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470872"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation アカウントの認証の概要

Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、Amazon Web Services (AWS) などの他のクラウド プロバイダーのリソースに対するタスクを自動化できます。 タスクの自動化には Runbook を使用できるほか、Azure 以外で管理する必要があるビジネスや運用プロセスがあれば Hybrid Runbook Worker を使用することもできます。 どの環境においても、必要な最低限の権限だけで操作対象のリソースに安全にアクセスするためのアクセス許可が必要です。

この記事では、Azure Automation でサポートされる認証シナリオを紹介すると共に、基本的な取り組み方を管理対象の環境ごとに説明します。

## <a name="automation-account"></a>Automation アカウント

Azure Automation を初めて開始するときに、少なくとも 1 つの Automation アカウントを作成する必要があります。 Automation アカウントを使用すると、他のアカウントのリソースから Automation のリソース、Runbook、資産、構成を分離できます。 Automation アカウントを使用して、リソースを個別の論理環境または委任された役割に分けることができます。 たとえば、開発環境用、本番環境用、オンプレミス環境用に、それぞれ異なるアカウントを使用できます。 または、[Update Management](update-management/overview.md) を使用して、すべてのコンピューターでオペレーティング システムの更新プログラムを管理するように 1 つの Automation アカウントを専用にすることもできます。

Azure Automation アカウントは、Microsoft アカウントや、Azure サブスクリプションで作成されたアカウントとは異なります。 Automation アカウントの作成の概要については、「[Automation アカウントを作成する](./quickstarts/create-account-portal.md)」を参照してください。

## <a name="automation-resources"></a>Automation リソース

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Azure サブスクリプション内のリソースはすべて Automation アカウントで管理することができます。 異なるリージョンで Automation アカウントを作成する主な理由としては、特定のリージョンに分離しなければならないデータやリソースを必要とするポリシーがある場合が挙げられます。

Azure Automation で Azure Resource Manager と PowerShell コマンドレットを使用してリソースに対して作成するすべてのタスクは、Azure Active Directory (Azure AD) の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行う必要があります。

## <a name="managed-identities"></a>マネージド ID

Azure Active Directory (Azure AD) のマネージド ID を使用すると、Runbook が Azure AD で保護された他のリソースに簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、シークレットをプロビジョニングまたはローテーションする必要はありません。 Azure AD のマネージド ID の詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

マネージド ID は、Runbook で認証を行うための推奨される方法であり、Automation アカウントの既定の認証方法です。

以下に、マネージド ID を使用するベネフィットをいくつか紹介します。

- Automation 実行アカウントの代わりにマネージド ID を使用すると、管理が簡単になる。 実行アカウントで使用される証明書を更新する必要がありません。

- マネージド ID の使用に関して追加コストは一切かからない。

- Runbook コードで実行接続オブジェクトを指定する必要がない。 証明書、接続、実行アカウントなどを作成せずに、Runbook から Automation アカウントのマネージド ID を使用してリソースにアクセスできます。

Automation アカウントは、次の 2 種類のマネージド ID を使用して認証できます。

- システム割り当て ID はアプリケーションに関連付けられているため、アプリが削除されると削除されます。 アプリは 1 つのシステム割り当て ID しか持つことはできません。

- ユーザー割り当て ID は、アプリに割り当てることができるスタンドアロン Azure リソースです。 アプリは複数のユーザー割り当て ID を持つことができます。

> [!NOTE]
> ユーザー割り当て ID がサポートされているのはクラウド ジョブの場合だけです。 さまざまなマネージド ID の詳細については、「[ID の種類の管理](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)」を参照してください。

マネージド ID の使用の詳細については、[Azure Automation のマネージド ID の有効化](enable-managed-identity-for-automation.md)に関するページを参照してください。

## <a name="run-as-accounts"></a>実行アカウント

Azure Automation の実行アカウントを使用すると、Azure Resource Manager リソースまたはクラシック デプロイ モデルにデプロイされたリソースを管理するための認証を行うことができるようになります。 Azure Automation には、次の 2 種類の実行アカウントがあります。

実行アカウントを作成または更新するには、アクセス許可が 3 つのレベルで必要です。

- サブスクリプション、
- Azure Active Directory (Azure AD)、および
- Automation アカウント

> [!NOTE]
> Azure Automation では、実行アカウントが自動的には作成されません。 これは、マネージド ID を使用して置き換えられました。

### <a name="subscription-permissions"></a>サブスクリプションのアクセス許可

`Microsoft.Authorization/*/Write` アクセス許可が必要です。 このアクセス許可は、次のいずれかの Azure 組み込みロールのメンバーシップを通じて取得されます。

- [所有者](../role-based-access-control/built-in-roles.md#owner)
- [User Access Administrator](../role-based-access-control/built-in-roles.md#user-access-administrator)

クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

### <a name="azure-ad-permissions"></a>Azure AD のアクセス許可

サービス プリンシパルの作成や更新ができるようになるためには、次のいずれかの Azure AD 組み込みロールのメンバーになる必要があります。

- [アプリケーション管理者](../active-directory/roles/permissions-reference.md#application-administrator)
- [アプリケーション開発者](../active-directory/roles/permissions-reference.md#application-developer)

メンバーシップは、テナント内の **すべての** ユーザーにディレクトリ レベルで割り当てることができます。これが既定の動作です。 メンバーシップをどちらのロールにも、ディレクトリ レベルで付与できます。 詳細については、「[Azure AD インスタンスにアプリケーションを追加する権限のあるユーザー](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)」を参照してください。

### <a name="automation-account-permissions"></a>Automation アカウントのアクセス許可

Automation アカウントの作成や更新ができるようになるためには、次のいずれかの Automation アカウント ロールのメンバーになる必要があります。

- [[所有者]](./automation-role-based-access-control.md#owner)
- [Contributor](./automation-role-based-access-control.md#contributor)
- [カスタム Azure Automation 共同作成者](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)

Azure Resource Manager とクラシック デプロイ モデルの詳細については、[Azure Resource Manager とクラシック デプロイの比較](../azure-resource-manager/management/deployment-models.md)に関するページを参照してください。

>[!NOTE]
>Azure Cloud Solution Provider (CSP) サブスクリプションでは、Azure Resource Manager モデルのみがサポートされます。 Azure Resource Manager 以外のサービスは、プログラムでは使用できません。 CSP サブスクリプションを使用する場合、Azure クラシック実行アカウントは作成されませんが、Azure 実行アカウントは作成されます。 CSP サブスクリプションの詳細については、[CSP サブスクリプションで利用可能なサービス](/azure/cloud-solution-provider/overview/azure-csp-available-services)に関するページを参照してください。

Automation アカウントを作成するときに、既定では、実行アカウントが自己署名証明書を使用して同時に作成されます。 Automation アカウントと共に作成しないことを選択した場合は、後で個別に作成できます。 Azure クラシック実行アカウントはオプションです。クラシック リソースを管理する必要がある場合は、個別に作成されます。

> [!NOTE]
> Azure Automation では、実行アカウントが自動的には作成されません。 これは、マネージド ID を使用して置き換えられました。

既定の自己署名証明書ではなく、エンタープライズまたはサードパーティの証明機関 (CA) によって発行された証明書を使用する場合、実行およびクラシック実行アカウントに対して [[実行アカウントを作成する PowerShell スクリプト]](create-run-as-account.md#powershell-script-to-create-a-run-as-account) オプションを使用できます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>実行アカウント

実行アカウントを作成すると、次のタスクが実行されます。

* 自己署名証明書を含む Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールが割り当てられます。 証明書の設定は、[閲覧者](../role-based-access-control/built-in-roles.md#reader)や他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。

* 指定された Automation アカウントに `AzureRunAsCertificate` という名前の Automation 証明書資産を作成します。 証明書資産には、Azure AD アプリケーションで使用される証明書の秘密キーが保持されます。

* 指定された Automation アカウントに `AzureRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

### <a name="azure-classic-run-as-account"></a>Azure クラシック実行アカウント

Azure クラシック実行アカウントを作成すると、次のタスクが実行されます。

> [!NOTE]
> この種類の実行アカウントを作成または更新するには、サブスクリプションの共同管理者である必要があります。

* サブスクリプションに管理証明書を作成します。

* 指定された Automation アカウントに `AzureClassicRunAsCertificate` という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。

* 指定された Automation アカウントに `AzureClassicRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、および証明書資産名が保持されます。

## <a name="service-principal-for-run-as-account"></a>実行アカウント用のサービス プリンシパル

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可がありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)」を参照してください。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>実行アカウントのアクセス許可

このセクションでは、通常の実行アカウントとクラシック実行アカウントの両方に対するアクセス許可を定義します。

* 実行アカウントを作成または更新するには、Azure Active Directory のアプリケーション管理者とサブスクリプションの所有者がすべてのタスクを完了する必要があります。
* クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

職務が分離されている状況について、次の表にタスク、同等のコマンドレット、必要なアクセス許可の一覧を示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|指定されたプリンシパルの Azure ロールを割り当てるまたは取得する|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | ユーザー アクセス管理者または所有者、あるいは次のアクセス許可がある:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<subscription name\> - アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | リソース グループの共同作成者         |Automation アカウント リソース グループ|
|Automation の接続を作成または削除する|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|リソース グループの共同作成者 |Automation アカウント リソース グループ|

<sup>1</sup> **[ユーザー設定]** ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録することができます](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。 アプリケーション登録設定が **[いいえ]** の場合は、この操作を行うユーザーがこの表で定義されている必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者ロールに追加された場合は、ゲストとして追加されます。 このような状況では、 **[Automation アカウントの追加]** ページに `You do not have permissions to create…` 警告が表示されます。

エラー メッセージが生成される状況が解決されたことを確認するには、次のようにします。

1. Azure portal の Azure Active Directory ペインで、 **[ユーザーとグループ]** を選択します。
2. **[すべてのユーザー]** を選択します。
3. ご自分の名前を選んでから、 **[プロファイル]** を選択します。
4. ユーザーのプロファイルの下にある **[ユーザーの種類]** 属性の値が **[ゲスト]** に設定されていないことを確認します。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

ロールベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。 Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。

リソース グループのアクセス許可の割り当てに対して厳密なセキュリティ制御がある場合は、実行アカウントのメンバーシップをリソース グループの **共同作成者** ロールに割り当てる必要があります。

> [!NOTE]
> **Log Analytics 共同作成者** ロールを利用して Automation ジョブを実行しないことをお勧めします。 代わりに、Azure Automation 共同作成者カスタム ロールを作成し、Automation アカウント関連のアクションに利用してください。 詳細については、[Azure Automation 共同作成者カスタム ロール](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)に関するページを参照してください。

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用した Runbook の認証

データセンター内の Hybrid Runbook Worker で、または AWS などの他のクラウド環境内にあるコンピューティング サービスに対して実行される Runbook は、Runbook が Azure のリソースを認証するために通常使用される方法と同じものを使用することはできません。 これは、これらのリソースは Azure の外部で実行しており、そのため、ローカルにアクセスするリソースに対する認証を行うには Automation で定義されている個別のセキュリティ資格情報を必要とするためです。 runbook worker を使用した Runbook の認証について詳しくは、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。

Azure VM 上で Hybrid Runbook Worker を使用する Runbook の場合は、実行アカウントの代わりに[マネージド ID による Runbook の認証](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用して Azure リソースに対して認証できます。

## <a name="next-steps"></a>次のステップ

* Azure portal からの Automation アカウントの作成については、「[スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)」を参照してください。
* テンプレートを使用してアカウントを作成したい場合は、「[Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](quickstart-create-automation-account-template.md)」を参照してください。
* アマゾン ウェブ サービスを使用した認証については、[アマゾン ウェブ サービスによる Runbook の認証](automation-config-aws-account.md)に関するページを参照してください。
* Azure リソースのマネージド ID 機能をサポートする Azure サービスの一覧については、「[Services that support managed identities for Azure resources (Azure リソースのマネージド ID をサポートするサービス)](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。