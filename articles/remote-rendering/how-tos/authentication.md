---
title: 認証
description: 認証のしくみについて説明します
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195299"
---
# <a name="configure-authentication"></a>認証を構成する。

Azure Remote Rendering には、[Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) と同じ認証メカニズムが使用されます。 REST API を正常に呼び出すには、クライアントで *AccountKey*、*AuthenticationToken*、または *AccessToken* を設定する必要があります。 *AccountKey* は、Azure portal の Remote Rendering アカウントの [キー] タブで取得できます。 *AuthenticationToken* は Azure AD トークンであり、[ADAL ライブラリ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)を使用して取得できます。 *AccessToken* は MR トークンであり、Azure Mixed Reality Security Token Service (STS) から取得できます。

## <a name="authentication-for-deployed-applications"></a>デプロイされたアプリケーションの認証

 すばやいオンボーディングにはアカウント キーの使用をお勧めしますが、開発/プロトタイプ作成時に限ります。 アプリケーションに埋め込んだアカウント キーを使用して、アプリケーションを運用環境に出荷しないことを強くお勧めします、代わりに、ユーザー ベースまたはサービス ベースの Azure AD 認証の方法を使用します。

 Azure AD 認証については、[Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) サービスに関するページの「[Azure AD ユーザー認証](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)」セクションを参照してください。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

アプリケーション、サービス、またはサービスの Azure AD ユーザーに付与されるアクセスのレベルを制御するため、必要に応じてご使用の Azure Remote Rendering アカウントに対して割り当てるために次のロールが作成されています。

* **Remote Rendering 管理者**:ユーザーに、Azure Remote Rendering の変換、セッション管理、レンダリング、診断の機能を提供します。
* **Remote Rendering クライアント**:ユーザーに、Azure Remote Rendering のセッション管理、レンダリング、診断の機能を提供します。

## <a name="next-steps"></a>次のステップ

* [アカウントの作成](create-an-account.md)
* [認証に Azure フロントエンド API を使用する](frontend-apis.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
