---
title: Azure EA Portal のアクセスのトラブルシューティング
description: この記事では、Azure Enterprise Agreement (EA) に関して Azure EA Portal で発生する可能性のある一般的な問題について説明します。
author: bandersmsft
ms.author: banders
ms.date: 07/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: e18c4707a0de97c696aa4ca93ce4c3ca818eb929
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707529"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA Portal のアクセスのトラブルシューティング

この記事では、Azure Enterprise Agreement (EA) に関して発生する可能性のある一般的な問題について説明します。 Azure EA Portal は、Enterprise Agreement のユーザーとコストを管理するために使用されます。 Azure EA Portal へのアクセスを構成または更新しているときに、次のような問題が発生する可能性があります。

## <a name="issues-adding-a-user-to-an-enrollment"></a>ユーザーを登録に追加するときの問題

エンタープライズ登録には、さまざまな種類の認証レベルがあります。 認証レベルが正しく適用されていない場合、Azure EA Portal にサインインしようとすると問題が発生することがあります。

Azure EA Portal を使用して、さまざまな認証レベルのユーザーにアクセス権を付与します。 エンタープライズ管理者は、組織のセキュリティ要件を満たすように認証レベルを更新できます。

### <a name="authentication-level-types"></a>認証レベルの種類

- Microsoft アカウントのみ - Microsoft アカウントを使用してユーザーを使用、作成、管理する組織向け。
- 職場または学校アカウント - クラウドへのフェデレーションを使用して Active Directory を設定しており、1 つのテナントにすべてのアカウントが存在する組織。
- テナント間の職場または学校アカウント - クラウドへのフェデレーションを使用して Active Directory を設定しており、複数のテナントにアカウントが存在する組織。
- 混在アカウント - Microsoft アカウントおよび/または職場または学校アカウントを使用してユーザーを追加できます。

登録に追加された最初の職場または学校アカウントによって、"_既定_" のドメインが決まります。 職場または学校アカウントを別のテナントに追加するには、登録の認証レベルをテナント間の認証に変更する必要があります。

認証レベルを更新するには、次の手順を実行します。

1. エンタープライズ管理者として Azure EA Portal にサインインします。
2. 左側のナビゲーション パネルで **[管理]** をクリックします。
3. **[登録]** タブをクリックします。
4. **[登録の詳細]** で、 **[認証レベル]** を選択します。
5. 鉛筆アイコンをクリックします。
6. **[保存]** をクリックします。

![認証レベルの表示例 ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft アカウントには、[https://signup.live.com](https://signup.live.com/) で作成した関連 ID 必要です。

職場または学校アカウントは、フェデレーションを使用して Active Directory を設定し、1 つのテナントにすべてのアカウントが存在する組織で使用できます。 社内の Active Directory がフェデレーションされている場合は、職場または学校のフェデレーション ユーザー認証を使用してユーザーを追加できます。

組織が Active Directory フェデレーションを使用していない場合、職場または学校の電子メール アドレスを使用することはできません。 代わりに、新しい電子メール アドレスを作成し、Microsoft アカウントとして登録します。

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA Portal にアクセスできない

Azure EA Portal にサインインしようとしたときにエラー メッセージが表示される場合は、次のトラブルシューティングの手順を使用します。

- 正しい Azure EA Portal の URL (https://ea.azure.com ) を使用していることを確認します。
- Azure EA Portal へのアクセス権が職場または学校アカウントとして追加されているか、それとも Microsoft アカウントとして追加されているかを確認します。
  - 職場のアカウントを使用している場合は、職場の電子メール アドレスと職場のパスワードを入力します。 職場のパスワードは組織から提供されます。 問題が発生した場合は、パスワードをリセットする方法について IT 部門に確認してください。
  - Microsoft アカウントを使用している場合は、Microsoft アカウントの電子メール アドレスとパスワードを入力します。 Microsoft アカウントのパスワードを忘れた場合は、[https://account.live.com/password/reset](https://account.live.com/password/reset) でリセットできます。
- InPrivate または Incognito のブラウザー セッションを使用してサインインします。これにより、以前のセッションまたは既存セッションの Cookie やキャッシュされた情報は保持されません。 ブラウザーのキャッシュを消去し、InPrivate または Incognito ウィンドウを使用して https://ea.azure.com を開きます。
- Microsoft アカウントの使用時に "_無効なユーザー_" エラーが発生する場合、複数の Microsoft アカウントを持っていることが原因である可能性があります。 サインインに使用しているアドレスが、プライマリ電子メール アドレスではありません。
また、"_無効なユーザー_" エラーが発生する場合、ユーザーを登録に追加したときに、間違ったアカウントの種類を使用したことが原因である可能性があります。 たとえば、Microsoft アカウントの代わりに職場または学校アカウントを使用した場合です。 この場合、別の EA 管理者に適切なアカウントを追加してもらうか、[サポート](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c)に連絡する必要があります。
  - プライマリ エイリアスを確認する必要がある場合は、[https://account.live.com](https://account.live.com) にアクセスしてください。 次に、 **[ユーザーの情報]** をクリックし、 **[Manage how to sign in to Microsoft] (Microsoft にサインインする方法の管理)** をクリックします。 画面の指示に従って連絡用メール アドレスを確認し、機密情報にアクセスするためのコードを取得します。 セキュリティ コードを入力します。 2 要素認証を設定しない場合は、 **[後で設定する]** を選択します。
  - **[Microsoft にサインインする方法の管理]** ページが表示され、ご自分のアカウント エイリアスを確認できます。 プライマリ エイリアスが、Azure EA Portal へのサインインに使用しているエイリアスであることを確認します。 そうでない場合は、これをプライマリ エイリアスにすることができます。 また、代わりに Azure EA Portal のプライマリ エイリアスを使用することもできます。

## <a name="next-steps"></a>次のステップ

- Azure EA ポータルの管理者は「[Azure EA ポータルの管理](ea-portal-administration.md)」を読んで、一般的な管理タスクについて学習する必要があります。
- Azure EA アクティブ化の一般的な問題に関する質問と回答については、[Cost Management + Billing に関する FAQ](../cost-management-billing-faq.yml) を参照してください。
