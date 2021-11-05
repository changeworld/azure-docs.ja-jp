---
title: ワークロード ID フェデレーション
titleSuffix: Microsoft identity platform
description: ワークロード ID フェデレーションを使用して、Azure の外部で実行されているワークロードに、シークレットや証明書を使用せずにAzure ADリソースへのアクセス権を付与します。 これにより、開発者は、Azure の外部で、長期のシークレットまたは証明書を格納して維持する必要が排除されます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: ryanwi
ms.reviewer: keyam, udayh, vakarand
ms.custom: aaddev
ms.openlocfilehash: ff1161e41965c92b469372038c4ba0cd602ee074
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091746"
---
# <a name="workload-identity-federation-preview"></a>ワークロード ID フェデレーション (プレビュー)
この記事では、ソフトウェア ワークロードのワークロード ID フェデレーションの概要について説明します。 ワークロード ID フェデレーションを使用すると、(サポートされているシナリオのために) シークレットを管理することなくAzure Active Directory (Azure AD) 保護されたリソースにアクセスできます。

ワークロード ID フェデレーションは、アクションの実行などのシナリオGitHubできます。

## <a name="why-use-workload-identity-federation"></a>ワークロード ID フェデレーションを使用する理由

通常、ソフトウェア ワークロード (アプリケーション、サービス、スクリプト、コンテナーベースのアプリケーションなど) では、リソースを認証してアクセスしたり、他のサービスと通信したりするために ID が必要です。  これらのワークロードが Azure で実行されている場合は、マネージド ID を使用できます。Azure プラットフォームによって資格情報が管理されます。  Azure の外部で実行されているソフトウェア ワークロードの場合は、アプリケーション資格情報 (シークレットまたは証明書) を使用して、Azure AD で保護されたリソース (Azure、Microsoft Graph、Microsoft 365、サード パーティのリソースなど) にアクセスする必要があります。  これらの資格情報はセキュリティ 上のリスクを伴い、安全に格納し、定期的にローテーションする必要があります。 また、資格情報の有効期限が切れると、サービスのダウンタイムのリスクも発生します。

ワークロード ID フェデレーションを使用して、Azure AD などの外部 ID プロバイダー (IdP) からのトークンを信頼するアプリの登録を構成GitHub。  その信頼関係が作成されると、ソフトウェア ワークロードは、外部 IdP から信頼されたトークンを、外部 IdP から信頼されたトークンと交換して、Microsoft ID プラットフォーム。  その後、ソフトウェア ワークロードはそのアクセス トークンを使用して、ワークロードAzure ADアクセス権が付与されている保護されたリソースにアクセスします。 これにより、資格情報を手動で管理するメンテナンスの負担が解消され、シークレットが漏洩したり、証明書の有効期限が切れるリスクが排除されます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

ワークロード ID フェデレーションを使用して保護されたリソースAzure ADアクセスする場合は、次のシナリオがサポートされています：

- GitHubアクション。 まず[、Azure AD](workload-identity-federation-create-trust-github.md)内のアプリと、Azure AD 内の GitHub Azure portal または Microsoft Graph を使用して、アプリ間の信頼関係を構成します。 次[に、GitHub Actions](/azure/developer/github/connect-from-azure)ワークフローを構成して、Microsoft ID プロバイダーからアクセス トークンを取得し、Azure リソースにアクセスします。
- Azure の外部のコンピューティング プラットフォームで実行されているワークロード。 [コンピューティング プラットフォームのアプリケーション登録](workload-identity-federation-create-trust.md)Azure AD外部 IdP の間に信頼関係を構成します。 そのプラットフォームによって発行されたトークンを使用して、Microsoft エコシステムMicrosoft ID プラットフォーム API を呼び出す認証を行います。 クライアント資格情報[フローを](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential)使用して Microsoft ID プラットフォーム からアクセス トークンを取得し、保存された証明書を使用して自分で作成するのではなく、ID プロバイダーの JWT を渡します。

## <a name="how-it-works"></a>しくみ
フェデレーション ID 資格情報 を構成して、外部 IdP とアプリAzure ADの間[に信頼関係を作成します](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true)。 フェデレーション ID 資格情報は、アプリケーションによって信頼する必要がある外部 IdP のトークンを示すために使用されます。 フェデレーション ID 資格情報は、アプリの登録時に、Azure portal Microsoft Graph を使用して構成します。  信頼関係を構成する手順は、シナリオと外部 IdP によって異なります。

ただし、アクセス トークンの外部トークンを交換するためのワークフローは、すべてのシナリオで同じです。 次の図は、アクセス トークンの外部トークンを交換し、保護されたリソースにアクセスするワークロードの一般的Azure AD示しています。

:::image type="content" source="media/workload-identity-federation/workflow.svg" alt-text="アクセス トークンと交換され、Azure にアクセスする外部トークンを表示します" border="false":::

1. 外部ワークロード (GitHub Actions ワークフローなど) は、外部 IdP (GitHub など) からトークンを要求します。
1. 外部 IdP は、外部ワークロードにトークンを発行します。
1. 外部ワークロード (たとえば、GitHub ワークフローのログイン[アクション)](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential)は、トークンを Microsoft ID プラットフォーム に送信し、アクセス トークンを要求します。
1. Microsoft ID プラットフォームアプリの登録で[信頼関係](workload-identity-federation-create-trust.md)を確認し、外部 IdP の Open ID Connect (OIDC) 発行者 URL に対して外部トークンを検証します。
1. チェックが完了すると、Microsoft ID プラットフォームアクセス トークンが外部ワークロードに発行されます。
1. 外部ワークロードは、Azure ADからアクセス トークンを使用して、保護されたリソースにMicrosoft ID プラットフォーム。 たとえばGitHub Actions ワークフローでは、アクセス トークンを使用して Web アプリを発行し、Azure App Service。

## <a name="next-steps"></a>次の手順
ワークロード ID フェデレーションのしくみの詳細については、次を参照してください。
- このAzure AD [OAuth 2.0](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential)クライアント資格情報付与と、別の IdP によって発行されたクライアント アサーションを使用してトークンを取得する方法。
- Microsoft Graph を使用して、アプリの登録でフェデレーション[ID](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true)資格情報を作成、削除、取得、または更新する方法。
- microsoft ID[プロバイダーからアクセス トークンを](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)取得し、Azure リソースにアクセスするように GitHub Actions ワークフローを構成する方法の詳細については、GitHub Actions のドキュメントを参照してください。
