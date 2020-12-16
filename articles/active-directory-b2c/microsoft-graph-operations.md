---
title: サポートされている Microsoft Graph 操作
titleSuffix: Azure AD B2C
description: ユーザー、ユーザーフロー、ID プロバイダー、カスタム ポリシー、ポリシー キーなど、Azure AD B2C リソースの管理に対してサポートされている Microsoft Graph 操作のインデックスです。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e4463b932c95e224981b74093effbb355de24f6b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509820"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Azure AD B2C に使用可能な Microsoft Graph 操作

次の Microsoft Graph API 操作は、ユーザー、ID プロバイダー、ユーザーフロー、カスタム ポリシー、およびポリシー キーなど、Azure AD B2C リソースの管理に対してサポートされています。

次のセクションの各リンクは、その操作の Microsoft Graph API リファレンス内の対応するページを対象としています。

## <a name="user-management"></a>[ユーザー管理]

- [ユーザーの一覧表示](/graph/api/user-list)
- [コンシューマー ユーザーを作成する](/graph/api/user-post-users)
- [ユーザーの取得](/graph/api/user-get)
- [ユーザーの更新](/graph/api/user-update)
- [ユーザーの削除](/graph/api/user-delete)

Microsoft Graph API での Azure AD B2C ユーザー アカウントの管理の詳細については、「[Microsoft Graph での Azure AD B2C ユーザー アカウントの管理](manage-user-accounts-graph-api.md)」を参照してください。

## <a name="user-phone-number-management"></a>ユーザーの電話番号の管理

- [追加](/graph/api/authentication-post-phonemethods)
- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [アップデート](/graph/api/b2cauthenticationmethodspolicy-update)
- [削除](/graph/api/phoneauthenticationmethod-delete)

Microsoft Graph API を使用したユーザーのサインイン電話番号の管理の詳細については、[B2C 認証方法](/graph/api/resources/b2cauthenticationmethodspolicy)に関する記事を参照してください。

## <a name="identity-providers-user-flow"></a>ID プロバイダー（ユーザーフロー）

Azure AD B2C テナントのユーザーフローで使用できる ID プロバイダーを管理します。

- [Azure AD B2C テナントに登録されている ID プロバイダーの一覧表示](/graph/api/identityprovider-list)
- [ID プロバイダーの追加](/graph/api/identityprovider-post-identityproviders)
- [ID プロバイダーの取得](/graph/api/identityprovider-get)
- [ID プロバイダーの更新](/graph/api/identityprovider-update)
- [ID プロバイダーの削除](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>ユーザー フロー

サインアップ、サインイン、パスワードのリセット、プロファイルの更新のための事前構築されたポリシーを構成します。

- [ユーザー フローの一覧表示](/graph/api/identityuserflow-list)
- [ユーザー フローを作成する](/graph/api/identityuserflow-post-userflows)
- [ユーザー フローの取得](/graph/api/identityuserflow-get)
- [ユーザー フローの削除](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>カスタム ポリシー

次の操作を使用すると、[カスタムポリシー](custom-policy-overview.md)と呼ばれる Azure AD B2C 信頼フレームワーク ポリシーを管理できます。

- [テナントで構成されているすべての信頼フレームワーク ポリシーを一覧表示する](/graph/api/trustframework-list-trustframeworkpolicies)
- [信頼フレームワーク ポリシーの作成](/graph/api/trustframework-post-trustframeworkpolicy)
- [既存の信頼フレームワーク ポリシーのプロパティの読み取り](/graph/api/trustframeworkpolicy-get)
- [信頼フレームワーク ポリシーを更新または作成します。](/graph/api/trustframework-put-trustframeworkpolicy)
- [既存の信頼フレームワークポリシーを削除する](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>ポリシー キー

Identity Experience Framework では、カスタムポリシーで参照されているシークレットを格納して、コンポーネント間の信頼を確立します。 これらのシークレットは、対称キー/値または非対称キー/値にすることができます。 Azure portal では、これらのエンティティは **ポリシーキー** として表示されます。

Microsoft Graph API のポリシー キーの最上位レベルのリソースは、[Trusted Framework Keyset](/graph/api/resources/trustframeworkkeyset)です。 各 **キーセット** には、少なくとも1つの **キー** が含まれています。 キーを作成するには、最初に空のキーセットを作成してから、キーセットにキーを生成します。 手動シークレットを作成したり、証明書をアップロードしたり、または PKCS12 キーを使用したりすることができます。 キーには、生成されたシークレット、定義する文字列 (Facebook アプリケーション シークレットなど)、またはアップロードする証明書を指定できます。 キーセットに複数のキーがある場合、1つのキーのみがアクティブになります。

### <a name="trust-framework-policy-keyset"></a>信頼フレームワーク ポリシー キーセット

- [信頼フレームワーク キーセットの一覧表示](/graph/api/trustframework-list-keysets)
- [信頼フレームワーク キーセットの作成](/graph/api/trustframework-post-keysets)
- [キーセットの取得](/graph/api/trustframeworkkeyset-get)
- [信頼フレームワーク キーセットの更新](/graph/api/trustframeworkkeyset-update)
- [信頼フレームワーク キーセットの削除](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信頼フレームワーク ポリシー キー

- [キーセットで現在アクティブなキーの取得](/graph/api/trustframeworkkeyset-getactivekey)
- [キーセットでキーの生成](/graph/api/trustframeworkkeyset-generatekey)
- [文字列ベースのシークレットのアップロード](/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 証明書のアップロード](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 形式の証明書のアップロード](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>アプリケーション

- [List applications](/graph/api/application-list)
- [アプリケーションの作成](/graph/api/resources/application)
- [アプリケーションの更新](/graph/api/application-update)
- [servicePrincipal の作成](/graph/api/resources/serviceprincipal)
- [oauth2Permission Grant の作成](/graph/api/resources/oauth2permissiongrant)
- [アプリケーションを削除する](/graph/api/application-delete)

## <a name="application-extension-properties"></a>アプリケーション拡張機能プロパティ

- [拡張機能プロパティの一覧表示](/graph/api/application-list-extensionproperty)

Azure AD B2C で提供されているディレクトリには、ユーザーごとに 100 個のカスタム属性を保持できます。 ユーザーフローの場合、これらの拡張機能プロパティは、[Azure portal を使用して管理します](user-flow-custom-attributes.md)。 カスタム ポリシーの場合、ポリシーが拡張機能プロパティに値を初めて書き込むときに、Azure AD B2C によってプロパティが作成されます。

## <a name="audit-logs"></a>監査ログ

- [監査ログの一覧表示](/graph/api/directoryaudit-list)

Microsoft Graph API を使用して Azure AD B2C 監査ログにアクセスする方法の詳細については、「[Azure AD B2C 監査ログにアクセスする](view-audit-logs.md)」を参照してください。