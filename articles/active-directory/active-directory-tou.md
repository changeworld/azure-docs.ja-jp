---
title: Azure Active Directory Terms of Use| Microsoft Docs
description: Azure AD Terms of Use を使うと、Azure AD サービスのユーザーに使用条件を提供できます。
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/15/2018
ms.author: rolyon
ms.openlocfilehash: 8fea445a4cd02da3cf3c3239a119b491327abf54
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234119"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory Terms of Use 機能
Azure AD Terms of Use は、エンド ユーザーに情報を提示するために使うことができる簡単な方法を提供します。  この方法で情報を提示することにより、法律上やコンプライアンス上の要件を満たすうえで重要な免責事項が確実にユーザーに表示されます。

Azure AD Terms of Use は、pdf 形式で内容を提示します。   この pdf はどのような内容でもよく (既存の契約ドキュメントなど)、エンド ユーザーのサインイン時にユーザーの同意を得ることができます。  使用条件は、アプリケーション、ユーザーのグループ、または複数の使用条件がある場合はさまざまな目的に対して、使うことができます。

以降では、Azure AD Terms of Use の使い方について説明します。  

## <a name="why-use-azure-ad-terms-of-use"></a>Azure AD Terms of Use を使う理由
従業員やゲストからアクセス前に使用条件への同意を得ることが難しくはありませんか。 会社の使用条件に同意したユーザーと同意していないユーザーを確認する手助けが必要ではありませんか。  Azure AD Terms of Use は、エンド ユーザーに情報を提示するために使うことができる簡単な方法を提供します。  この方法で情報を提示することにより、法律上やコンプライアンス上の要件を満たすうえで重要な免責事項が確実にユーザーに表示されます。

Azure AD Terms of Use は、次のシナリオで使用できます。
-   組織内のすべてのユーザーに対する一般的な使用条件。
-   ユーザーの属性に基づく特定の使用条件 (例:  [動的グループ](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)によって行われる、医師と看護師や、国内従業員と国際従業員などの区別)。
-   ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスに基づく特定の使用条件。


## <a name="prerequisites"></a>前提条件
Azure AD Terms of Use を構成する手順は次のとおりです。

1. グローバル管理者、セキュリティ管理者、または Azure AD Terms of Use を構成するディレクトリの条件付きアクセス管理者を使って、Azure AD にサインインします。
2. ディレクトリに、Azure AD Premium P1、P2、EMS E3、EMS E5 のいずれかのサブスクリプションがあることを確認します。  ない場合は、[Azure AD Premium を入手](active-directory-get-started-premium.md)するか、[試用版の利用を開始](https://azure.microsoft.com/trial/get-started-active-directory/)します。
3. [https://aka.ms/catou](https://aka.ms/catou) で Azure AD Terms of User ダッシュボードを表示します。

>[!IMPORTANT]
>条件付きアクセス ポリシー規制 (使用条件を含む) は、サービス アカウントに対する強制をサポートしていません。  サービス アカウントはすべて、条件付きアクセス ポリシーから除外することをお勧めします。

## <a name="add-company-terms-of-use"></a>会社の使用条件を追加する
使用条件が完成した後は、次の手順を使って追加します。

### <a name="to-add-terms-of-use"></a>使用条件を追加するには
1. [https://aka.ms/catou](https://aka.ms/catou) でダッシュボードに移動します。
2. [追加] をクリックします。</br>
![TOU の追加](media/active-directory-tou/tou12.png)
3. 使用条件の **[名前]** を入力します。
4. **[表示名]** を入力します。  このヘッダーは、ユーザーがサインインするときに表示されます。
5. 完成した使用条件の pdf を**参照**して選びます。  推奨フォント サイズは 24 です。
6. 使用条件の**言語**を選択します。  言語オプションを使用して、言語が異なる複数の使用条件をアップロードできます。  エンドユーザーに表示される使用条件のバージョンは、ブラウザーの設定に基づきます。
7. **[ユーザーは使用条件を展開する必要があります]** をオンにするかオフにするかを選択します。  オンに設定された場合、エンド ユーザーは、同意する前に使用条件を表示する必要があります。
8. **[条件付きアクセス]** で、ドロップダウンからテンプレートを選択するか、またはカスタム条件付きアクセス ポリシーを選択して、アップロードした使用条件を**適用**できます。  カスタム条件付きアクセス ポリシーを使うと、特定のクラウド アプリケーションやユーザー グループまで、きめ細かい使用条件を有効にできます。  詳しくは、[条件付きアクセス ポリシーの構成](active-directory-conditional-access-best-practices.md)に関するページをご覧ください。
9. **Create** をクリックしてください。
10. カスタム条件付きアクセス テンプレートを選んだ場合、表示される新しい画面で CA のポリシーをカスタマイズできます。
11. 新しい使用条件が表示されるようになります。</br>

![TOU の追加](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>使用条件を削除する
次の手順を使って、古い使用条件を削除できます。

### <a name="to-delete-terms-of-use"></a>使用条件を削除するには
1. [https://aka.ms/catou](https://aka.ms/catou) でダッシュボードに移動します。
2. 削除する使用条件を選びます。
3. **[削除]** をクリックします。
4. 使用条件が表示されなくなります。


## <a name="viewing-current-user-status"></a>現在のユーザーの状態を表示する
使用条件には、同意したユーザーと拒否したユーザーの数が表示されていることがわかります。

![監査イベント](media/active-directory-tou/tou15.png)

**[同意]** または **[拒否]** の下に表示される数値をクリックすると、現在のユーザーの状態が表示されます。

![監査イベント](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>使用条件を監査する
Azure AD Terms of Use には使いやすい監査機能が備わっており、現在の状態だけでなく過去の同意と拒否の履歴を確認する必要がある場合に利用できます。  この監査機能により、だれがいつ使用条件に同意したかを確認することができます。  

監査機能には、そのときそのときの目的に応じて 2 とおりの使い方があります。  


監査を使い始める手順は次のとおりです。

### <a name="to-audit-terms-of-use"></a>使用条件を監査するには
1. [https://aka.ms/catou](https://aka.ms/catou) でダッシュボードに移動します。
2. [監査ログの表示] をクリックします。</br>
![監査イベント](media/active-directory-tou/tou8.png)
3.  Azure AD の [監査ログ] 画面では、ドロップダウンを使って情報をフィルターし、特定の監査ログ情報だけを対象にすることができます。
[監査イベント](media/active-directory-tou/tou9.png)
4.  .csv ファイルで情報をダウンロードしてローカルに使うこともできます。

## 

## <a name="what-users-see"></a>ユーザーに対する表示
使用条件を作成して適用すると、対象ユーザーには次のように表示されます。  これらの画面はサインインの間に表示されます。
-   PDF のフォント サイズは 24 にすることをお勧めします。
![監査イベント](media/active-directory-tou/tou10.png)
-   モバイル デバイスでは次のように表示されます</br></br>
![監査イベント](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>使用条件の確認
ユーザーは、同意した使用条件を確認できます。  使用条件を確認するには、次の手順に従います。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) に移動してサインインします。
2. 右上隅に表示される自分の名前をクリックし、ドロップダウンから **[プロファイル]** を選択します。
![プロファイル](media/active-directory-tou/tou14.png)

3. プロファイルで、**[使用条件の確認]** をクリックします。
![監査イベント](media/active-directory-tou/tou13a.png)

4.  そこから同意した使用条件を確認できます。 

## <a name="removing-users-from-an-active-terms-of-use"></a>アクティブな使用条件からユーザーを削除する

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

既定では、削除されたユーザーが Azure AD に 30 日間、削除状態で維持されます。その間は、管理者が必要に応じて復元することができます。  30 日後、そのユーザーは完全に削除されます。  その期間に達していなくても、全体管理者は、Azure Active Directory ポータルを使用して明示的に、[最近削除したユーザーを完全に削除](active-directory-users-restore.md)することができます。  ユーザーが完全に削除されると、以降そのユーザーに関するデータは、アクティブな使用条件から削除されます。  監査ログには、削除済みユーザーに関する監査情報が維持されます。



## <a name="additional-information"></a>追加情報
使用条件を使うときに知っておくべき情報と役に立つ情報を次に示します。

>[!IMPORTANT]
> 次の場合、新しいポリシーを満たすには、対象ユーザーはいったんサインアウトしてからサインインし直す必要があります。
> - 使用条件で条件付きアクセス ポリシーが有効になった場合
> - または、第 2 の使用条件が作成された場合
>
>条件付きアクセス ポリシーは直ちに有効になるためです。 このような場合、管理者には "不適切なクラウド" または "Azure AD トークンの問題" が表示されるようになります。 新しいポリシーを満たすには、管理者はサインアウトしてサインインし直す必要があります。





## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q: ユーザーが使用条件を受け入れたかどうか、および受け入れた日時を確認するにはどうすればよいですか?**</br>
A: 該当する使用条件の欄で、[同意] の下に表示される数値をクリックしてください。  詳細については、「[現在のユーザーの状態を表示する](#viewing-current-user-status)」を参照してください。  また、使用条件に同意したユーザーは監査ログに書き込まれます。 Azure AD の監査ログを検索して結果を確認できます。  

**Q: 使用条件を変更した場合、ユーザーはもう一度同意する必要がありますか?**</br>
A: はい、管理者は使用条件を変更でき、ユーザーは新しい条件に同意し直す必要があります。

**Q: 使用条件は複数の言語をサポートしていますか?**</br>
A: はい。  現時点では、管理者が 1 つの使用条件に対して構成できる言語が 18 あります。 

**Q: 使用条件はいつトリガーされますか?**</br>
A: 使用条件は、サインイン エクスペリエンスの間にトリガーされます。

**Q: どのようなアプリケーションを使用条件の対象にできますか?**</br>
A: 最新の認証を使ったエンタープライズ アプリケーションに条件付きアクセス ポリシーを作成できます。  詳細については、[エンタープライズ アプリケーション](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal)に関するページをご覧ください。

**Q: 特定のユーザーまたはアプリケーションに複数の使用条件を追加できますか?**</br>
A: はい、そのグループやアプリを対象とする複数の条件付きアクセス ポリシーを作成することで可能です。 複数の使用条件の対象になっているユーザーは、一度に 1 つの使用条件に同意します。
 
**Q: ユーザーが使用条件を拒否した場合はどうなりますか?**</br>
A: ユーザーは、アプリケーションへのアクセスをブロックされます。 ユーザーは、アクセスするには、もう一度サインインして使用条件に同意する必要があります。
