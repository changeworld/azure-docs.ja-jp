---
title: Azure Active Directory Graph API | Microsoft Docs
description: REST API エンドポイントを介して Azure AD にプログラムでアクセスできる Azure AD Graph API の概要およびクイック スタート ガイドです。
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 3ec6bbcb9250626f8d6ef75330991c5ebac053a7
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504719"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Azure Active Directory リソースにアクセスする場合、Azure AD Graph API ではなく [Microsoft Graph](https://graph.microsoft.io/) を使用することを強くお勧めします。 開発作業は現在 Microsoft Graph に集中しており、Azure AD Graph API の追加の機能強化は予定されていません。 Azure AD Graph API の使用が適切なシナリオの数は非常に限られています。詳しくは、Office デベロッパー センターのブログ投稿「[Microsoft Graph or the Azure AD Graph (Microsoft Graph または Azure AD Graph)](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph)」をご覧ください。
> 
> 

Azure Active Directory Graph API を使用すると、REST API エンドポイントを介して Azure AD にプログラムによってアクセスできます。 アプリケーションでは、Azure AD Graph API を使って、ディレクトリのデータとオブジェクトに対して、作成、読み取り、更新、および削除 (CRUD) の各操作を実行できます。 たとえば、Azure AD Graph API では、ユーザー オブジェクトへの次のような一般的な操作がサポートされています。

* ディレクトリに新しいユーザーを作成する
* ユーザーの詳細なプロパティ (所属するグループなど) を取得する
* ユーザーのプロパティ (場所や電話番号など) を更新するか、またはユーザーのパスワードを変更する
* ロールベースのアクセスに対応しているかどうか、ユーザーのグループ メンバーシップを確認する
* ユーザーのアカウントを無効にするか、または完全に削除する

さらに、グループやアプリケーションなどのその他のオブジェクトに対しても同様の操作を実行できます。 ディレクトリで Azure AD Graph API を呼び出すには、アプリケーションを Azure AD に登録する必要があります。 また、Azure AD Graph API へのアクセスをアプリケーションに許可する必要もあります。 通常、このアクセスは、ユーザーまたは管理者の同意フローによって実現されます。

Azure Active Directory Graph API を使い始めるには、「[Azure AD Graph API のクイック スタート](active-directory-graph-api-quickstart.md)」または[対話型の Azure AD Graph API リファレンス ドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)をご覧ください。

## <a name="features"></a>機能
Azure AD Graph API には、次の機能が用意されています。

* **REST API エンドポイント**: Azure AD Graph API は、標準の HTTP 要求を使ってアクセスされるエンドポイントで構成された REST ベースのサービスです。 Azure AD Graph API では、要求と応答のコンテンツの種類として XML または Javascript Object Notation (JSON) をサポートしています。 詳細については、「 [Azure AD Graph REST API リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)」を参照してください。
* **Azure AD による認証**: Azure AD Graph API に対するすべての要求は、その要求の Authorization ヘッダーに JSON Web トークン (JWT) を追加することによって認証する必要があります。 このトークンを取得するには、Azure AD のトークン エンドポイントに対して要求を送信し、有効な資格情報を提供します。 OAuth 2.0 クライアント資格情報フローまたは承認コード付与フローを使用して、Graph を呼び出すためのトークンを取得することができます。 詳細については、「 [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)」を参照してください。
* **ロールベースの承認 (RBAC)**: セキュリティ グループは、Azure AD Graph API 内で RBAC を実行するときに使われます。 たとえば、ユーザーが特定のリソースへのアクセス権を持っているかどうかを判断する場合、アプリケーションから[グループ メンバーシップの確認 (推移的)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) 操作を呼び出すことができます。この操作では、true または false が返されます。
* **差分クエリ**: 差分クエリ を使うと、Azure AD Graph API に対して頻繁にクエリを行わなくても、2 つの期間の間でのディレクトリの変更を追跡できます。 この種の要求は、前回の差分クエリ要求と現在の要求の間で行われた変更のみを返します。 詳細については、「 [Azure AD Graph API の差分クエリ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)」を参照してください。
* **ディレクトリ拡張機能**: 外部データ ストアを必要とせずに、ディレクトリ オブジェクトにカスタム プロパティを追加できます。 たとえば、アプリケーションがユーザーごとに Skype ID プロパティを必要とする場合は、ディレクトリに新しいプロパティを登録することができ、そのプロパティはすべてのユーザー オブジェクトで使用できるようになります。 詳細については、「 [Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)」を参照してください。
* **アクセス許可スコープによる保護**: Azure AD Graph API は、OAuth 2.0 を使って Azure AD のデータに安全にアクセスできるアクセス許可スコープを公開します。 次のようなさまざまな種類のクライアント アプリをサポートします。
  
  * サインインしたユーザー (代理) からの承認によってデータへの委任アクセスを付与されるユーザー インターフェイス
  * サインインしたユーザーがいない状態でバックグラウンドで動作し、アプリケーションで定義されたロールベースのアクセス制御を使うサービス/デーモン アプリケーション
    
    委任およびアプリケーションのアクセス許可は、Azure AD Graph API によって公開されている権限を表し、[Azure Portal](https://portal.azure.com) のアプリケーション登録のアクセス許可を通じてクライアント アプリケーションによって要求できます。 [Azure AD Graph API のアクセス許可スコープ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)は、クライアント アプリケーションで使用可能なものについての情報を提供します。

## <a name="scenarios"></a>シナリオ
Azure AD Graph API により、多くのアプリケーション シナリオを実現できます。 最も一般的なシナリオを次に示します。

* **基幹業務 (シングル テナント) アプリケーション**: このシナリオでは、エンタープライズ開発者は、Office 365 サブスクリプションを持つ組織に勤めています。 開発者は、Azure AD を操作してユーザーへのライセンスの割り当てなどのタスクを実行する Web アプリケーションを作成します。 このタスクでは、Azure AD Graph API へのアクセスが必要となるため、開発者は、Azure AD にこのシングル テナント アプリケーションを登録し、Azure AD Graph API の読み取りおよび書き込みのアクセス許可を構成します。 その後、アプリケーション独自の資格情報または現在サインインしているユーザーの資格情報を使って、Azure AD Graph API を呼び出すトークンを取得するように、アプリケーションは構成されます。
* **サービス アプリケーション (マルチテナント) としてのソフトウェア**: このシナリオでは、独立系ソフトウェア ベンダー (ISV) が、Azure AD を使用する他の組織にユーザー管理機能を提供するホスト型のマルチテナント Web アプリケーションを開発しています。 これらの機能にはディレクトリ オブジェクトへのアクセスが必要になるため、アプリケーションは Azure AD Graph API を呼び出す必要があります。 開発者は、Azure AD にアプリケーションを登録し、Azure AD Graph API に対する読み取りと書き込みのアクセス許可を必要とするように構成して、他の組織がディレクトリ内のアプリケーションを使用することに同意できるように外部アクセスを有効にします。 別の組織のユーザーが初めてアプリケーションに対する認証を行うとき、アプリケーションが要求しているアクセス許可に関する同意ダイアログが表示されます。 同意すると、アプリケーションには、ユーザーのディレクトリでの Azure AD Graph API に対する要求されたアクセス許可が付与されます。 同意フレームワークの詳細については、「 [同意フレームワークの概要](quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。

## <a name="see-also"></a>関連項目
[Azure AD Graph API のクイック スタート ガイド](active-directory-graph-api-quickstart.md)

[Azure AD Graph REST のドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 開発者ガイド](azure-ad-developers-guide.md)

