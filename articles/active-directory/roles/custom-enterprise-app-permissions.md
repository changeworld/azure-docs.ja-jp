---
title: Azure Active Directory のカスタム ロールに対するアプリのアクセス許可 | Microsoft Docs
description: Azure portal、PowerShell、Graph API におけるカスタム Azure AD ロールに対するエンタープライズ アプリのアクセス許可について見ていきます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: b0a8b3abf4e119096959b640be4b44051a198916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736145"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory のカスタム ロールに対するエンタープライズ アプリケーションのアクセス許可

この記事では、Azure Active Directory (Azure AD) のカスタム ロール定義に対して現在利用可能なエンタープライズ アプリケーションのアクセス許可について説明します。 この記事には、いくつかの一般的なシナリオで使用されるアクセス許可の一覧のほか、エンタープライズ アプリのアクセス許可を網羅した一覧が記載されています。

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="enterprise-application-permissions"></a>エンタープライズ アプリケーションのアクセス許可

これらのアクセス許可を使用する方法について詳しくは、[カスタム ロールを割り当ててエンタープライズ アプリを管理する](custom-enterprise-apps.md)方法に関するページを参照してください。

#### <a name="assigning-users-or-groups-to-an-application"></a>アプリケーションにユーザーまたはグループを割り当てる

SAML ベースのシングル サインオン アプリケーションにアクセスできるユーザーとグループの割り当てを委任します。 必要なアクセス許可

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

#### <a name="creating-gallery-applications"></a>ギャラリー アプリケーションを作成する

ServiceNow、F5、Salesforce など、Azure AD ギャラリー アプリケーションの作成を委任します。 必要なアクセス許可:

- microsoft.directory/applicationTemplates/instantiate

#### <a name="configuring-basic-saml-urls"></a>基本的な SAML URL を構成する

SAML ベースのシングル サインオン アプリケーションに使用される基本的な SAML 構成の更新と読み取りを委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

#### <a name="rolling-over-or-creating-signing-certs"></a>署名証明書をロール オーバーまたは作成する

SAML ベースのシングル サインオン アプリケーションに使用される署名証明書の管理を委任します。 必要なアクセス許可:

microsoft.directory/applications/credentials/update

#### <a name="update-expiring-sign-in-cert-notification-email-address"></a>期限切れが迫っているサインイン証明書の通知メール アドレスを更新する

SAML ベースのシングル サインオン アプリケーションについて、期限切れが迫っているサインイン証明書の通知メール アドレスの更新を委任します。 必要なアクセス許可:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

#### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML トークン署名とサインイン アルゴリズムを管理する

SAML ベースのシングル サインオン アプリケーションに使用される SAML トークン署名とサインイン アルゴリズムの更新を委任します。 必要なアクセス許可:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

#### <a name="manage-user-attributes-and-claims"></a>ユーザー属性と要求を管理する

SAML ベースのシングル サインオン アプリケーションに使用されるユーザー属性と要求の作成、削除、更新を委任します。 必要なアクセス許可:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>アプリのプロビジョニングのアクセス許可

ジョブ、スキーマ、資格情報の管理など、UI を使用した書き込み操作を実行するためには、プロビジョニング ページを表示するための読み取りアクセス許可も必要となります。

現在、"すべてのユーザーとグループ" または "割り当てられたユーザーとグループ" にスコープを設定するためには、synchronizationJob と synchronizationCredentials の両方のアクセス許可が必要となります。

#### <a name="turn-on-or-restart-provisioning-jobs"></a>プロビジョニング ジョブを有効化または再開する

プロビジョニング ジョブを有効化、無効化、再開する権限を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

#### <a name="configure-the-provisioning-schema"></a>プロビジョニング スキーマを構成する  

属性マッピングに対する更新を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

#### <a name="read-provisioning-settings-associated-with-the-application-object"></a>アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る

オブジェクトに関連付けられているプロビジョニング設定を読み取る権限を委任します。 必要なアクセス許可:

- microsoft.directory/applications/synchronization/standard/read

#### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る

サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る権限を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronization/standard/read

#### <a name="authorize-application-access-for-provisioning"></a>プロビジョニングのためのアプリケーション アクセスを承認する  

プロビジョニングのためのアプリケーション アクセスを承認する権限を委任します (例: OAuth ベアラー トークンの入力など)。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="application-proxy-permissions"></a>アプリケーション プロキシ アクセス許可

アプリケーションのアプリケーション プロキシ プロパティへの書き込み操作を実行するには、アプリケーションの基本プロパティと認証を更新するためのアクセス許可も必要です。

アプリケーションのアプリケーション プロキシ プロパティを読み取り、書き込み操作を実行するには、コネクタ グループを表示するための読み取りアクセス許可も必要です。これは、このページに表示されるプロパティのリストの一部であるためです。

#### <a name="delegate-application-proxy--connector-management"></a>アプリケーション プロキシ コネクタ管理を委任する

コネクタ管理の作成、読み取り、更新、削除のアクションを委任する場合。 必要なアクセス許可:

- microsoft.directory/connectorGroups/allProperties/read
- microsoft.directory/connectorGroups/allProperties/update
- microsoft.directory/connectorGroups/create
- microsoft.directory/connectorGroups/delete
- microsoft.directory/connectors/allProperties/read
- microsoft.directory/connectors/create


#### <a name="delegate-application-proxy-settings-management"></a>アプリケーション プロキシ設定の管理を委任する

アプリのアプリケーション プロキシ プロパティの作成、読み取り、更新、削除のアクションを委任する場合。 必要なアクセス許可:

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/applications/applicationProxy/update 
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update 
- microsoft.directory/applications/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/connectorGroups/allProperties/read

#### <a name="read-application-proxy-settings-for-an-app"></a>アプリのアプリケーション プロキシ設定を読み取る

アプリのアプリケーション プロキシ プロパティの読み取りアクセス許可を委任する場合。 必要なアクセス許可:
 
- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 

#### <a name="update-url-configuration-application-proxy-settings-for-an-app"></a>アプリの URL 構成アプリケーション プロキシ設定を更新する 

アプリケーション プロキシの外部 URL、内部 URL、および SSL 証明書のプロパティを更新するための作成、読み取り、更新、および削除 (CRUD) アクセス許可を委任する場合。 必要なアクセス許可: 

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 
- microsoft.directory/applications/basic/update 
- microsoft.directory/applications/authentication/update
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update

## <a name="full-list-of-permissions"></a>アクセス許可の全一覧

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/applicationPolicies/allProperties/read | アプリケーション ポリシーのすべてのプロパティを読み取る |
> | microsoft.directory/applicationPolicies/allProperties/update | アプリケーション ポリシーのすべてのプロパティを更新する |
> | microsoft.directory/applicationPolicies/basic/update | アプリケーション ポリシーの標準プロパティを更新する |
> | microsoft.directory/applicationPolicies/create | アプリケーション ポリシーを作成する |
> | microsoft.directory/applicationPolicies/createAsOwner | アプリケーション ポリシーを作成します。 作成者は最初の所有者として追加されます |
> | microsoft.directory/applicationPolicies/delete | アプリケーション ポリシーを削除する |
> | microsoft.directory/applicationPolicies/owners/read | アプリケーション ポリシーの所有者を読み取る |
> | microsoft.directory/applicationPolicies/owners/update | アプリケーション ポリシーの所有者プロパティを更新する |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | オブジェクト リストに適用されているアプリケーション ポリシーを読み取る |
> | microsoft.directory/applicationPolicies/standard/read | アプリケーション ポリシーの標準プロパティを読み取る |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory での servicePrincipals の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/servicePrincipals/allProperties/read | servicePrincipals のすべてのプロパティを読み取る |
> | microsoft.directory/servicePrincipals/allProperties/update | servicePrincipals のすべてのプロパティを更新する |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | サービス プリンシパルのロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | サービス プリンシパルに割り当てられたロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルで対象ユーザー プロパティを更新する |
> | microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルで認証プロパティを更新する |
> | microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新する |
> | microsoft.directory/servicePrincipals/create | サービス プリンシパルの作成 |
> | microsoft.directory/servicePrincipals/createAsOwner | サービス プリンシパルを作成します。 作成者は最初の所有者として追加されます |
> | microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報プロパティを更新する |
> | microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除する |
> | microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にする |
> | microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にする |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を読み取る |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を管理する |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | サービス プリンシパルの委任されたアクセス許可付与を読み取る |
> | microsoft.directory/servicePrincipals/owners/read | サービス プリンシパルの所有者を読み取る |
> | microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新する |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/policies/read | サービス プリンシパルのポリシーを読み取る |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/servicePrincipals/standard/read | サービス プリンシパルの標準プロパティを読み取る |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/servicePrincipals/tag/update | サービス プリンシパルのタグ プロパティを更新する |
> | microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンスを作成する |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログを読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン レポートを読み取る |
> | microsoft.directory/applications/applicationProxy/read | すべての種類のアプリケーションのすべてのアプリケーション プロキシ プロパティを読み取る |
> | microsoft.directory/applications/applicationProxy/update | すべての種類のアプリケーションのすべてのアプリケーション プロキシ プロパティを更新する |
> | microsoft.directory/applications/applicationProxyAuthentication/update | すべての種類のアプリケーションのアプリケーション プロキシの認証プロパティを更新する |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | すべての種類のアプリケーションのアプリケーション プロキシの内部および外部 URL を更新する |
> | microsoft.directory/applications/applicationProxySslCertificate/update | すべての種類のアプリケーションのアプリケーション プロキシ カスタム ドメインを更新する |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/connectorGroups/create | アプリケーション プロキシ コネクタ グループを作成する |
> | microsoft.directory/connectorGroups/delete | アプリケーション プロキシ コネクタ グループを削除する |
> | microsoft.directory/connectorGroups/allProperties/read | アプリケーション プロキシ コネクタ グループのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/allProperties/update | アプリケーション プロキシ コネクタ グループのすべてのプロパティを更新する |
> | microsoft.directory/connectors/create | アプリケーション プロキシ コネクタを作成する |
> | microsoft.directory/connectors/allProperties/read | アプリケーション プロキシ コネクタのすべてのプロパティを読み取る |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | サービス プリンシパル リソースのジョブ同期の側面をすべて管理します。 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取ります。 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | サービス プリンシパル リソースのスキーマ同期の側面をすべて管理します。 |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |

## <a name="next-steps"></a>次のステップ

- [Azure portal、Azure AD PowerShell、および Graph API を使用してカスタム ロールを作成する](custom-create.md)
- [ロールの割り当てを一覧表示する](view-assignments.md)
