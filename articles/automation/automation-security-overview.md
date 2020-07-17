---
title: Azure Automation での認証の概要
description: この記事では、Azure Automation の Automation アカウントで利用できる Automation のセキュリティとさまざまな認証方法の概要について説明します。
keywords: Automation のセキュリティ, セキュリティで保護された Automation; Automation の認証
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 3d3dbaad18f6acbe1ddf17d81f54e4232c838dd7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787415"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure Automation での認証の概要

Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、Amazon Web Services (AWS) などの他のクラウド プロバイダーのリソースに対するタスクを自動化できます。 Runbook が必要な操作を実行するためには、操作対象のリソースに安全に、サブスクリプション内で必要な最低限の権限だけでアクセスするための、アクセス許可が必要です。

この記事では、Azure Automation でサポートされるさまざまな認証シナリオと、管理する必要のある環境に基づいて開始する方法について説明します。  

## <a name="automation-account-overview"></a>Automation アカウントの概要

Azure Automation を初めて開始するときに、少なくとも 1 つの Automation アカウントを作成する必要があります。 Automation アカウントを使用すると、Automation アカウントごとに Automation リソース (Runbook、資産、構成) を分離できます。 Automation アカウントを使用してリソースを異なる論理環境に分けることができます。 たとえば、開発環境用、本番環境用、オンプレミス環境用に、それぞれ異なるアカウントを使用できます。 Azure Automation アカウントは、Microsoft アカウントや、Azure サブスクリプションで作成されたアカウントとは異なります。

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Automation アカウントではサブスクリプションのすべてのリソースを管理できます。 異なるリージョンで Automation アカウントを作成する主な理由としては、特定のリージョンに分離しなければならないデータやリソースを必要とするポリシーがある場合が挙げられます。

Azure Automation で Azure Resource Manager と Azure コマンドレットを使用してリソースに対して実行するすべてのタスクは、Azure Active Directory の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行う必要があります。 Azure Automation の実行アカウントでは、Azure コマンドレットを使用して Azure のリソースを管理するための認証が提供されます。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory (AD) に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。 Azure 仮想マシン上で Hybrid Runbook Worker を使用する Runbook の場合は、実行アカウントの代わりに[マネージド ID による Runbook の認証](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用して Azure リソースに対して認証できます。

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可はありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

ロール ベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。 Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。  

データセンター内の Hybrid Runbook Worker で、または AWS などの他のクラウド環境内にあるコンピューティング サービスに対して実行される Runbook は、Runbook が Azure のリソースを認証するために通常使用される方法と同じものを使用することはできません。 これは、これらのリソースは Azure の外部で実行しており、そのため、ローカルにアクセスするリソースに対する認証を行うには Automation で定義されている個別のセキュリティ資格情報を必要とするためです。 runbook worker を使用した Runbook 認証の詳細については、[Hybrid Runbook Worker の Runbook の認証](automation-hrw-run-runbooks.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

* [Azure portal から Automation アカウントを作成する](automation-create-standalone-account.md)。

* [Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](automation-create-account-template.md)。

* [アマゾン ウェブ サービス (AWS) で認証する](automation-config-aws-account.md)。
