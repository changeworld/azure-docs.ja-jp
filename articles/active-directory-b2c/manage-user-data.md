---
title: Azure Active Directory B2C でのユーザー データの管理 | Microsoft Docs
description: Azure AD B2C 内のユーザー データを削除またはエクスポートする方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dacff48be3fbf16fc719f5a0395937b1f5acc979
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712540"
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Active Directory B2C 内のユーザー データを管理する

 この記事では、[Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) によって提供される操作を使用して、Azure Active Directory (AD) B2C 内のユーザー データを管理する方法について説明します。 ユーザー データの管理には、データを削除したり、監査ログからデータをエクスポートしたりする機能が含まれます。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>ユーザー データを削除する

ユーザー データは、Azure AD B2C ディレクトリおよび監査ログに格納されます。 すべてのユーザー監査データは、30 日のデータ保有期間中、Azure AD B2C に保持されます。 その 30 日間にユーザー データを削除する場合は、[ユーザーの削除](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser)操作を使用できます。 データが存在する Azure AD B2C テナントごとに、DELETE 操作を行う必要があります。 

Azure AD B2C 内のすべてのユーザーには、オブジェクト ID が割り当てられています。 オブジェクト ID は、Azure AD B2C 内のユーザー データの削除に使用する明確な識別子を提供します。  アーキテクチャによっては、オブジェクト ID は、財務、マーケティング、顧客リレーションシップ管理データベースなどの他のサービス間の、便利な相関関係識別子になる可能性があります。  

ユーザーのオブジェクト ID を取得する最も確実な方法は、Azure AD B2C での認証過程の一部として入手することです。  他の方法を使っているユーザーからデータの有効な要求を受け取った場合、顧客サービス サポート エージェントによる検索などのオフライン プロセスでは、ユーザーを検索して、関連付けられているオブジェクト ID を記録することが必要な場合があります。 

次の例では、可能なデータ削除フローを示します。

1. ユーザーがサインインし、**[自分のデータを削除]** を選びます。
2. アプリケーションは、アプリケーションの管理セクション内のデータを削除するオプションを提供します。
3. アプリケーションは、Azure AD B2C に対する認証を強制します。 Azure AD B2C は、ユーザーのオブジェクト ID を含むトークンをアプリケーションに提供します。 
4. アプリケーションはトークンを受け取り、オブジェクト ID を使って Azure AD Graph API を呼び出すことによってユーザー データを削除します。 Azure AD Graph API は、ユーザー データを削除し、状態コード 200 OK を返します。
5. アプリケーションは、必要に応じて、オブジェクト ID または他の識別子を使って、他の組織システム内のユーザー データの削除を調整します。
6. アプリケーションは、データの削除を確認し、ユーザーに次の手順を提供します。

## <a name="export-customer-data"></a>顧客データをエクスポートする

Azure AD B2C から顧客データをエクスポートするプロセスは、削除処理と似ています。

Azure AD B2C のユーザー データは以下に限定されます。

- **Azure Active Directory に格納されたデータ** - オブジェクト ID またはメール アドレスやユーザー名などの任意のサインイン名を使って、Azure AD B2C の認証ユーザー体験でデータを取得できます。  
- **ユーザー固有の監査イベント レポート** - データにはオブジェクト ID を使ってインデックスが付けられます。

エクスポート データ フローの次の例では、アプリケーションで実行されるように説明されている手順は、バックエンド プロセスまたはディレクトリの管理者ロールを持つユーザーでも実行できます。

1. ユーザーがアプリケーションにサインインします。 必要な場合は、Azure AD B2C は多要素認証による認証を強制します。
2. アプリケーションは、ユーザーの資格情報を使って Azure AD Graph API 操作を呼び出してユーザー属性を取得します。 Azure AD Graph API は、JSON 形式で属性データを提供します。 スキーマによっては、ユーザーに関するすべての個人データを含むように、ID トークンの内容を設定することができます。
3. アプリケーションは、エンド ユーザーの監査アクティビティを取得します。 Azure AD Graph API は、アプリケーションにイベント データを提供します。
4. アプリケーションは、データを集計し、ユーザーが使用できるようにします。

## <a name="next-steps"></a>次の手順

- 「[ユーザー アクセスを管理する](manage-user-access.md)」で、ユーザーがアプリケーションにアクセスできる方法を管理する方法を学習してください




