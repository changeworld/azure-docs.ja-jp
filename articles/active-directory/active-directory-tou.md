---
title: Azure Active Directory Terms of use| Microsoft Docs
description: Azure AD Terms of use を使うと、Azure AD サービスのユーザーに使用条件を提示できます。
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/31/2018
ms.author: rolyon
ms.openlocfilehash: edf8eccf8c0a1cba70ac178ac5863a68b26f14a3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622077"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory Terms of Use 機能
Azure AD Terms of use は、エンド ユーザーに情報を提示するために使うことができる簡単な方法を提供します。 この方法で情報を提示することにより、法律上やコンプライアンス上の要件を満たすうえで重要な免責事項が確実にユーザーに表示されます。 この記事では、Azure AD Terms of use の使用を開始する方法について説明します。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Terms of use を使用してできること
Azure AD Terms of use を使用すると、以下のことができます。
- 従業員やゲストに、アクセス前に使用条件への同意を求める。
- 組織内のすべてのユーザーに一般的な使用条件を提示する。
- ユーザーの属性 (例:  [動的グループ](users-groups-roles/groups-dynamic-membership.md)を使用して、医師と看護師や、国内従業員と国際従業員など) に基づいて特定の使用条件を提示する。
- ビジネスへの影響が大きいアプリケーション (Salesforce など) にアクセスする際に特定の使用条件を提示する。
- 使用条件を異なる言語で提示する。
- 使用条件に同意したユーザーと同意していないユーザーを一覧表示する。
- 使用条件アクティビティの監査ログを表示する。

## <a name="prerequisites"></a>前提条件
Azure AD Terms of use を使用および構成するには、以下が必要です。

- Azure AD Premium P1、P2、EMS E3、EMS E5 のいずれかのサブスクリプション。
    - これらのサブスクリプションをお持ちでない場合は、[Azure AD Premium を入手する](fundamentals/active-directory-get-started-premium.md)か、[Azure AD Premium 試用版を有効にする](https://azure.microsoft.com/trial/get-started-active-directory/)ことができます。
- 構成するディレクトリに対する次のいずれかの管理者アカウント。
    - 全体管理者
    - セキュリティ管理者
    - 条件付きアクセス管理者

## <a name="terms-of-use-document"></a>使用条件のドキュメント

Azure AD Terms of use は、PDF 形式で内容を提示します。 この PDF はどのような内容でもよく (既存の契約ドキュメントなど)、エンド ユーザーのサインイン時にユーザーの同意を得ることができます。 PDF の推奨フォント サイズは 24 です。

## <a name="add-terms-of-use"></a>使用条件の追加
使用条件のドキュメントが完成した後は、次の手順を使って追加します。

1. Azure にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。

1. [https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

    ![[使用条件] ブレード](media/active-directory-tou/tou-blade.png)

1. **[新しい条件]** をクリックします。

    ![TOU の追加](media/active-directory-tou/new-tou.png)

1. 使用条件の **[名前]** を入力します。

2. **[表示名]** を入力します。  これは、ユーザーがサインインするときに表示されるヘッダーです。

3. 完成した使用条件の PDF を**参照**して選びます。

4. 使用条件の**言語**を選択します。  言語オプションを使用して、言語が異なる複数の使用条件をアップロードできます。  エンド ユーザーに表示される使用条件のバージョンは、ブラウザーの設定に基づきます。

5. **[ユーザーは使用条件を展開する必要があります]** をオンにするかオフにするかを選択します。  オンに設定した場合、エンド ユーザーは同意する前に使用条件を表示する必要があります。

6. **[条件付きアクセス]** で、ドロップダウン リストからテンプレートを選択するか、カスタム条件付きアクセス ポリシーを選択して、アップロードした使用条件を**適用**できます。  カスタム条件付きアクセス ポリシーを使うと、特定のクラウド アプリケーションやユーザー グループまで、きめ細かい使用条件を有効にできます。  詳しくは、[条件付きアクセス ポリシーの構成](conditional-access/best-practices.md)に関するページをご覧ください。

    >[!IMPORTANT]
    >条件付きアクセス ポリシー規制 (使用条件を含む) は、サービス アカウントに対する強制をサポートしていません。  サービス アカウントはすべて、条件付きアクセス ポリシーから除外することをお勧めします。

7. **Create** をクリックしてください。

8. カスタム条件付きアクセス テンプレートを選んだ場合、表示される新しい画面で条件付きアクセス ポリシーをカスタマイズできます。

    新しい使用条件が表示されるようになります。

    ![TOU の追加](media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>同意したユーザーと拒否したユーザーのレポートの表示
[使用条件] ブレードには、同意したユーザーと拒否したユーザーの数が表示されます。 これらの数および同意したユーザーと拒否したユーザーは、使用条件が有効な間、保存されます。

1. Azure にサインインし、[https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

    ![監査イベント](media/active-directory-tou/view-tou.png)

1. **[同意]** または **[拒否]** の下に表示される数値をクリックすると、現在のユーザーの状態が表示されます。

    ![監査イベント](media/active-directory-tou/accepted-tou.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 監査ログの表示
追加のアクティビティを確認できるように、Azure AD Terms of use には監査ログが用意されています。 ユーザーが同意するたびに、イベントがトリガーされます。そのイベントは、監査ログに記録され、30 日間保存されます。 これらのログはポータルで表示することや、.csv ファイルとしてダウンロードすることができます。

Azure AD 監査ログを使い始める手順は次のとおりです。

1. Azure にサインインし、[https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

1. **[監査ログの表示]** をクリックします。

    ![監査イベント](media/active-directory-tou/audit-tou.png)

1. Azure AD の [監査ログ] 画面では、ドロップダウン リストを使って情報をフィルターし、特定の監査ログ情報だけを対象にすることができます。

    ![監査イベント](media/active-directory-tou/audit-logs-tou.png)

1. **[ダウンロード]** をクリックして、.csv ファイルで情報をダウンロードしてローカルに使うこともできます。

## <a name="what-terms-of-use-looks-like-for-users"></a>ユーザーに表示される使用条件の外観
使用条件を作成して適用すると、対象ユーザーにはサインイン時に次の画面が表示されます。

![監査イベント](media/active-directory-tou/user-tou.png)

次の画面は、使用条件がモバイル デバイス上でどのように表示されるかを示しています。

![監査イベント](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>ユーザーが使用条件を確認する方法
ユーザーは、以下の手順を使用して、同意した使用条件を確認できます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

1. 右上隅に表示される自分の名前をクリックし、ドロップダウンから **[プロファイル]** を選択します。

    ![プロファイル](media/active-directory-tou/tou14.png)

1. [プロファイル] ページで、**[使用条件の確認]** をクリックします。

    ![監査イベント](media/active-directory-tou/tou13a.png)

1. そこから同意した使用条件を確認できます。 

## <a name="edit-terms-of-use-details"></a>利用規約の詳細を編集する
利用規約の詳細の一部を編集できますが、既存のドキュメントを変更することはできません。 詳細の編集方法を次の手順で説明します。

1. Azure にサインインし、[https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

1. 編集する利用規約を選択します。

1. **[条件の編集]** をクリックします。

1. [利用規約の編集] ウィンドウで、[名前]、[表示名]、または [require users to expand values]\(値の展開をユーザーに要求する\) を変更します。

    ![TOU の追加](media/active-directory-tou/edit-tou.png)

1. **[保存]** をクリックして変更を保存します。

    変更を保存すると、ユーザーは、新しいご契約条件を再度受け入れる必要があります。

## <a name="add-a-terms-of-use-language"></a>利用規約の言語を追加する
利用規約の言語を追加する方法を次の手順で説明します。

1. Azure にサインインし、[https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

1. 編集する利用規約を選択します。

1. [詳細] ウィンドウで、**[言語]** タブをクリックします。

    ![TOU の追加](media/active-directory-tou/languages-tou.png)

1. **[言語の追加]** をクリックします。

1. [Add terms of use language]\(利用規約の言語の追加\) ウィンドウで、ローカライズされた PDF をアップロードして、言語を選択します。

    ![TOU の追加](media/active-directory-tou/language-add-tou.png)

1. **[追加]** をクリックして、言語を追加します。

## <a name="delete-terms-of-use"></a>使用条件の削除
次の手順を使って、古い使用条件を削除できます。

1. Azure にサインインし、[https://aka.ms/catou](https://aka.ms/catou) から **[使用条件]** に移動します。

1. 削除する使用条件を選びます。

1. **[条件の削除]** をクリックします。

1. 続行するかどうかを確認するメッセージが表示されたら、**[はい]** をクリックします。

    ![TOU の追加](media/active-directory-tou/delete-tou.png)

    使用条件が表示されなくなります。

## <a name="deleted-users-and-active-terms-of-use"></a>削除されたユーザーとアクティブな使用条件
既定では、削除されたユーザーは Azure AD に 30 日間、削除状態で維持されます。その間は、管理者が必要に応じて復元することができます。  30 日後、そのユーザーは完全に削除されます。  その期間に達していなくても、全体管理者は、Azure Active Directory ポータルを使用して明示的に、[最近削除したユーザーを完全に削除](fundamentals/active-directory-users-restore.md)することができます。  ユーザーが完全に削除されると、以降そのユーザーに関するデータは、アクティブな使用条件から削除されます。  監査ログには、削除済みユーザーに関する監査情報が維持されます。

## <a name="policy-changes"></a>ポリシーの変更
条件付きアクセス ポリシーは直ちに有効になるためです。 このような場合、管理者には "不適切なクラウド" または "Azure AD トークンの問題" が表示されるようになります。 新しいポリシーを満たすには、管理者はサインアウトしてサインインし直す必要があります。

>[!IMPORTANT]
> 次の場合、新しいポリシーを満たすには、対象ユーザーはいったんサインアウトしてからサインインし直す必要があります。
> - 使用条件で条件付きアクセス ポリシーが有効になった場合
> - または、第 2 の使用条件が作成された場合

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q: ユーザーが使用条件に同意したかどうか、および同意した日時を確認するにはどうすればよいですか?**</br>
A: [使用条件] ブレードで **承認済み**の下の数字をクリックします。 Azure AD 監査ログでも同意アクティビティを表示または検索できます。 詳しくは、「[同意したユーザーと拒否したユーザーのレポートの表示](#view-who-has-accepted-and-declined)」および「[Azure AD 監査ログの表示](#view-azure-ad-audit-logs)」をご覧ください。
 
**Q: 情報はどのくらいの期間保存されますか?**</br>
使用条件レポートのユーザーの数および同意したユーザーと拒否したユーザーの数は、使用条件が有効な間、保存されます。 Azure AD 監査ログは 30 日間保存されます。

**Q: 使用条件レポートの同意数と Azure AD 監査ログの同意数が異なります。**</br>
A: 使用条件レポートはその使用条件の有効期間中保存されるのに対し、Azure AD 監査ログは 30 日間保存されます。 また、使用条件レポートには、ユーザーの現在の同意状態のみが表示されます。 たとえば、あるユーザーが拒否してから同意した場合、使用条件レポートにはそのユーザーの同意のみが表示されます。 履歴を確認するには、Azure AD 監視ログを使用できます。

**Q: 利用規約の詳細を編集した場合、ユーザーはもう一度同意する必要がありますか?**</br>
A: はい、管理者が利用規約の詳細を編集した場合、ユーザーは新しい条件に同意し直す必要があります。

**Q: 既存の利用規約のドキュメントを更新できますか?**</br>
A: 現在、既存の利用規約のドキュメントを更新することはできません。 利用規約のドキュメントを変更するには、新しい利用規約のインスタンスを作成する必要があります。

**Q: ハイパーリンクが使用条件の PDF ドキュメント内にある場合、エンド ユーザーはそれらをクリックできますか?**</br>
A: PDF は既定で JPEG としてレンダリングされるため、ハイパーリンクをクリックすることはできません。 必要に応じて、ユーザーは **[表示に問題がある場合は、ここをクリックします]** を選択できます。ハイパーリンクがサポートされていれば、PDF がネイティブでレンダリングされます。

**Q: 使用条件は複数の言語をサポートしていますか?**</br>
A: はい。  現時点では、管理者が 1 つの使用条件に対して構成できる言語が 18 あります。 

**Q: 使用条件はいつトリガーされますか?**</br>
A: 使用条件は、サインイン エクスペリエンスの間にトリガーされます。

**Q: どのようなアプリケーションを使用条件の対象にできますか?**</br>
A: 最新の認証を使ったエンタープライズ アプリケーションに条件付きアクセス ポリシーを作成できます。  詳細については、[エンタープライズ アプリケーション](./manage-apps/view-applications-portal.md)に関するページをご覧ください。

**Q: 特定のユーザーまたはアプリケーションに複数の使用条件を追加できますか?**</br>
A: はい、そのグループやアプリケーションを対象とする複数の条件付きアクセス ポリシーを作成することで可能です。 複数の使用条件の対象になっているユーザーは、一度に 1 つの使用条件に同意します。
 
**Q: ユーザーが使用条件を拒否した場合はどうなりますか?**</br>
A: ユーザーは、アプリケーションへのアクセスをブロックされます。 ユーザーは、アクセスするには、もう一度サインインして使用条件に同意する必要があります。
 
**Q: 以前に同意した使用条件の同意を取り消すことはできますか?**</br>
A: [以前に同意した使用条件を確認する](#how-users-can-review-their-terms-of-use)ことはできますが、現在、同意を取り消す方法はありません。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory の条件付きアクセスのベスト プラクティス](conditional-access/best-practices.md)