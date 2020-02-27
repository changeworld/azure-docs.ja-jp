---
title: サポートされている Microsoft Graph 操作
titleSuffix: Azure AD B2C
description: ユーザー、ユーザーフロー、ID プロバイダー、カスタム ポリシー、ポリシー キーなど、Azure AD B2C リソースの管理に対してサポートされている Microsoft Graph 操作のインデックスです。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5eeae1ab6866435311eeec944b4a51ecf2793dee
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522970"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Azure AD B2C に使用可能な Microsoft Graph 操作

次の Microsoft Graph API 操作は、ユーザー、ID プロバイダー、ユーザーフロー、カスタム ポリシー、およびポリシー キーなど、Azure AD B2C リソースの管理に対してサポートされています。

次のセクションの各リンクは、その操作の Microsoft Graph API リファレンス内の対応するページを対象としています。

## <a name="user-management"></a>[ユーザー管理]

- [ユーザーの一覧表示](https://docs.microsoft.com/graph/api/user-list)
- [コンシューマー ユーザーを作成する](https://docs.microsoft.com/graph/api/user-post-users)
- [ユーザーの取得](https://docs.microsoft.com/graph/api/user-get)
- [ユーザーの更新](https://docs.microsoft.com/graph/api/user-update)
- [ユーザーの削除](https://docs.microsoft.com/graph/api/user-delete)

Microsoft Graph API での Azure AD B2C ユーザー アカウントの管理の詳細については、「[Microsoft Graph での Azure AD B2C ユーザー アカウントの管理](manage-user-accounts-graph-api.md)」を参照してください。

## <a name="identity-providers-user-flow"></a>ID プロバイダー（ユーザーフロー）

Azure AD B2C テナントのユーザーフローで使用できる ID プロバイダーを管理します。

- [Azure AD B2C テナントに登録されている ID プロバイダーの一覧表示](https://docs.microsoft.com/graph/api/identityprovider-list)
- [ID プロバイダーの追加](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [ID プロバイダーの取得](https://docs.microsoft.com/graph/api/identityprovider-get)
- [ID プロバイダーの更新](https://docs.microsoft.com/graph/api/identityprovider-update)
- [ID プロバイダーの削除](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>ユーザー フロー

サインアップ、サインイン、パスワードのリセット、プロファイルの更新のための事前構築されたポリシーを構成します。

- [ユーザー フローの一覧表示](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [ユーザー フローの作成](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [ユーザー フローの取得](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [ユーザー フローの削除](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>カスタム ポリシー

次の操作を使用すると、[カスタムポリシー](custom-policy-overview.md)と呼ばれる Azure AD B2C 信頼フレームワーク ポリシーを管理できます。

- [テナントで構成されているすべての信頼フレームワーク ポリシーを一覧表示する](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [信頼フレームワーク ポリシーの作成](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [既存の信頼フレームワーク ポリシーのプロパティの読み取り](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [信頼フレームワーク ポリシーを更新または作成します。](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [既存の信頼フレームワークポリシーを削除する](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>ポリシー キー

Identity Experience Framework では、カスタムポリシーで参照されているシークレットを格納して、コンポーネント間の信頼を確立します。 これらのシークレットは、対称キー/値または非対称キー/値にすることができます。 Azure portal では、これらのエンティティは**ポリシーキー**として表示されます。

Microsoft Graph API のポリシー キーの最上位レベルのリソースは、[Trusted Framework Keyset](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)です。 各 **キーセット** には、少なくとも1つの **キー**が含まれています。 キーを作成するには、最初に空のキーセットを作成してから、キーセットにキーを生成します。 手動シークレットを作成したり、証明書をアップロードしたり、または PKCS12 キーを使用したりすることができます。 キーには、生成されたシークレット、定義する文字列 (Facebook アプリケーション シークレットなど)、またはアップロードする証明書を指定できます。 キーセットに複数のキーがある場合、1つのキーのみがアクティブになります。

### <a name="trust-framework-policy-keyset"></a>信頼フレームワーク ポリシー キーセット

- [信頼フレームワーク キーセットの一覧表示](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [信頼フレームワーク キーセットの作成](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [キーセットの取得](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [信頼フレームワーク キーセットの更新](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [信頼フレームワーク キーセットの削除](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信頼フレームワーク ポリシー キー

- [キーセットで現在アクティブなキーの取得](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [キーセットでキーの生成](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [文字列ベースのシークレットのアップロード](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 証明書のアップロード](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 形式の証明書のアップロード](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>[アプリケーション]

- [List applications](https://docs.microsoft.com/graph/api/application-list)
- [アプリケーションの作成](https://docs.microsoft.com/graph/api/resources/application)
- [アプリケーションの更新](https://docs.microsoft.com/graph/api/application-update)
- [servicePrincipal の作成](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [oauth2Permission Grant の作成](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [アプリケーションを削除する](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>アプリケーション拡張機能プロパティ

- [拡張機能プロパティの一覧表示](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C で提供されているディレクトリには、ユーザーごとに 100 個のカスタム属性を保持できます。 ユーザーフローの場合、これらの拡張機能プロパティは、[Azure portal を使用して管理します](custom-policy-custom-attributes.md)。 カスタム ポリシーの場合、ポリシーが拡張機能プロパティに値を初めて書き込むときに、Azure AD B2C によってプロパティが作成されます。

## <a name="audit-logs"></a>監査ログ

- [監査ログの一覧表示](https://docs.microsoft.com/graph/api/directoryaudit-list)

Microsoft Graph API を使用して Azure AD B2C 監査ログにアクセスする方法の詳細については、「[Azure AD B2C 監査ログにアクセスする](view-audit-logs.md)」を参照してください。
