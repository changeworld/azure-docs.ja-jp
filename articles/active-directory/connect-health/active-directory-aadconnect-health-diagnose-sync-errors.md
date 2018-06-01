---
title: Azure AD Connect Health - 重複する属性の同期エラーの診断 | Microsoft Docs
description: このドキュメントでは、重複する属性の同期エラーの診断プロセスと Azure Portal から直接孤立したオブジェクトのシナリオを潜在的に修正する方法について説明します。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305599"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>重複する属性の同期エラーの診断と修復 

## <a name="overview"></a>概要
同期エラーに着目して一歩進んだ対応をするために、Azure Active Directory Connect Health では、重複する属性の同期エラーをトラブルシューティングして、Azure AD から孤立したオブジェクトを修正するための自己サービス修復のエクスペリエンスを導入しています。 診断機能の主な利点を次に示します。
- 重複する属性の同期エラーのシナリオを絞り込み、特定の解決策を指示する診断手順を提示する
- Azure AD から重複するシナリオに修正を適用して、1 回のクリックでエラーを解決する
- この機能を有効にするために、アップグレードや構成の必要がない。
詳細については、Azure AD の[重複の復元性](https://aka.ms/dupattributeresdocs)に関するページを参照してください。

## <a name="problems"></a>問題が発生した場合
### <a name="common-scenario"></a>一般的なシナリオ
**QuarantinedAttributeValueMustBeUnique** と **AttributeValueMustBeUnique** の同期エラーが発生した場合、一般的には、ユーザー プリンシパル名またはプロキシ アドレスの競合を Azure AD で確認します。 オンプレミス側から競合するソース オブジェクトを更新することで、同期エラーを解決できます。 同期エラーは、次の同期の後に解決されます。 以下の図は、例として 2 人のユーザーに *Joe.J@contoso.com* での UserPrincipalName の競合があることを示しています。競合するオブジェクトは、Azure AD で検査されます。 

![同期エラーの一般的な診断シナリオ](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>孤立したオブジェクトのシナリオ
既存のユーザーのソース アンカーが失われていることに気が付く場合があります。 つまり、オンプレミス AD でソース オブジェクトの削除が行われましたが、削除シグナルの変更は Azure AD に同期されませんでした。 同期エンジンの問題やドメイン移行などに起因して、この状況が発生する可能性があります。 同じオブジェクトが復元されて再作成されるときは、論理的に既存のユーザーが、ソース アンカーから同期するユーザーであることが必要になります。 また、クラウド限定オブジェクトとしての既存のユーザーでは、競合ユーザーが Azure AD に同期されたことがわかり、同期では既存のオブジェクトとは一致しません。 ソース アンカーを再マップする直接的な方法はありません。 [既存の KB](https://support.microsoft.com/help/2647098) に関するページを参照してください。 たとえば、Azure AD の既存のオブジェクトが、Joe のライセンスを保持しているとします。 Azure AD 内で、異なるソース アンカーで新しく同期されたオブジェクトに、重複属性の状態が発生しました。 オンプレミス AD での Joe の変更は、Azure AD での Joe の元のユーザー (既存のオブジェクト) には適用できません。  

![同期エラーの孤立したオブジェクトの診断シナリオ](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health における診断とトラブルシューティングの手順 
診断機能では、次のような重複する属性を持つユーザー オブジェクトをサポートします。

| 属性名 | 同期エラーの種類|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique または AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique または AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> RBAC 設定からこの機能にアクセスできるように、**全体管理者**の権限または**共同作成者**が必要です。 
>

Azure Portal から次の手順に従って、同期エラーの詳細を絞り込むと、さらに特定のソリューションを提示できます。

![同期エラーを診断する診断手順](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Azure Portal から次の手順を実行して、修正可能な固有のシナリオを特定できます。  
1.  [Diagnose status]\(診断の状態\) 列の状態は、エラー ケースを絞り込んで Azure Active Directory から潜在的に直接修正を行える、潜在的なトラブルシューティング フローがあるかどうかを示します。
| 状態 | これはどういうことですか? |
| ------------------ | -----------------|
| 未開始 | この診断プロセスをまだ使用していません。 診断結果に応じて、ポータルから直接同期エラーを修正する潜在的な方法があります。 |
| 手動修正が必要 | エラーが、ポータルから使用可能な修正の条件に適合しません。 (1) 競合するオブジェクトの種類がユーザーではない、または (2) 既に診断手順を完了しポータルから使用可能な修正の解決策がない場合が考えられます。 この場合、オンプレミス側からの修正が、引き続き解決策の 1 つになります。 [オンプレミスでの修正](https://support.microsoft.com/help/2647098)に関するページを参照してください。 | 
| 同期保留中 | 修正が適用されました。 次の同期サイクルでエラーがクリアされるまで待機しています。 |
>[!IMPORTANT]
> 診断の状態の列は、同期サイクルごとにリセットされます。 
>

2.  エラーの詳細ブレードで **[診断]** ボタンをクリックして、いくつかの質問に回答し、同期エラーの詳細を特定する必要があります。 質問に回答すると、孤立したオブジェクト シナリオのケースを特定するうえで役立ちます。 

3.  診断の最後に **[閉じる]** ボタンがある場合、指定された回答に基づくポータルから使用可能な簡易的な修正はないことを意味します。 最後の手順に示したソリューションを参照してください。 オンプレミスからの修正が、引き続きソリューションになります。 [閉じる] ボタンをクリックすると、現在の同期エラーの状態が **[手動修正が必要]** に切り替わることがわかります。 現在の同期サイクル中は、状態が保持されます。

4.  孤立したオブジェクトが特定されると、重複する属性の同期エラーをポータルから直接修正できます。 **[修正の適用]** ボタンをクリックして、プロセスをトリガーします。 現在の同期エラーの状態が更新され、**[同期保留中]** になります。

5.  次の同期サイクルの後には、エラーが一覧から削除されています。

## <a name="how-to-answer-the-diagnosis-questions"></a>診断の質問に回答する方法 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>オンプレミスの Active Directory にユーザーは存在しますか。

質問では、オンプレミスの Active Directory から既存のユーザーのソース オブジェクトの特定を試行しています。  
1.  指定された UserPrincipalName のオブジェクトが Active Directory に存在するかどうか確認してください。 存在しない場合、[いいえ] と答えてください。
2.  存在する場合、オブジェクトがまだ同期のスコープ内にあるかどうか確認してください。  
  - DN を使用して Azure AD のコネクタ スペースを検索します。
  - **[追加の保留中]** 状態のオブジェクトが見つかった場合、[いいえ] と答えてください。 Azure AD Connect が適切な AD オブジェクトにオブジェクトを接続できません。
  - オブジェクトが見つからない場合、[はい] と答えてください。

> たとえば、次の図を参照すると、質問では *Joe Jackson* がオンプレミスの Active Directory にまだ存在するかどうかの特定を試行しています。
**一般的なシナリオ**では、*Joe Johnson* と *Joe Jackson* 両方のユーザーがオンプレミスの Active Directory に存在します。 検査されているオブジェクトは、2 つの異なるユーザーです。

![同期エラーの一般的な診断シナリオ](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> **孤立したオブジェクト のシナリオ**では、オンプレミスの Active Directory から単一のユーザーのみ (*Joe Johnson*) が表示されます。

![同期エラーの孤立したオブジェクトの診断シナリオ](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>これら両方のアカウントは同じユーザーに属していますか。
質問では、競合している受信ユーザーと Azure AD の既存のユーザー オブジェクトをチェックして、同じユーザーに属しているかどうかをチェックしています。  
1.  競合しているオブジェクトは、Azure Active Directory に新しく同期されます。 次の項目で、オブジェクトを比較します。  
  - 表示名
  - ユーザー プリンシパル名
  - オブジェクト ID
2.  比較できなかった場合は、指定された UserPrincipalNames を持つオブジェクトが Active Directory に存在するか確認してください。 両方見つかる場合は [いいえ] と答えてください。  

> 以下のケースでは、2 つのオブジェクトが同じユーザーである *Joe Johnson* に属しています。

![同期エラーの孤立したオブジェクトの診断シナリオ](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>孤立したオブジェクトのシナリオの場合、修正後に発生した処理が適用されます。
挙げられた質問への回答に基づいて、Azure AD から使用可能な修正がある場合は、**[修正の適用]** ボタンを表示できます。 このケースでは、オンプレミスのオブジェクトが予期しない Azure AD オブジェクトと同期しています。 2 つのオブジェクトは、"ソース アンカー" を使用してマップされます。 変更の適用は、次のような手順で実行されます。
- Azure AD で適切なオブジェクトへのソース アンカーを更新します。
- 存在する場合、Azure AD で競合するオブジェクトを削除します。

![修正後の同期エラーの診断](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> [修正の適用] による変更は、孤立したオブジェクトのケースにのみ適用されます。
>

上記の手順の後、ユーザーは、既存のオブジェクトにリンクされている元のリソースにアクセスできます。 リスト ビューにある **[Diagnose status]\(診断の状態\)** の値は、**[同期保留中]** に更新されます。同期エラーは、次の同期の後に解決されます。 Connect Health では、解決済みの同期エラーがリスト ビューに表示されなくなります。 


## <a name="faq"></a>FAQ
 -  適用の実行に失敗した場合はどうなりますか。  
実行に失敗した場合、Azure AD Connect がその時点で、実行時のエクスポート エラーになっている可能性があります。 次の同期の後に、ポータル ページを更新して再試行してください。 既定の同期サイクルは 30 分です。 

 -  **既存のオブジェクト**が、削除する必要があるオブジェクトの場合はどうなりますか。  
既存のオブジェクトを削除する必要があるこのケースの場合、プロセスではソース アンカーの変更は行われません。 このオブジェクトをオンプレミス AD から修正できる必要があります。  

 -  修正を適用できるユーザーの権限は何ですか。  
RBAC 設定のグローバル管理者または共同作成者には、診断とトラブルシューティングのプロセスにアクセスするための権限があります。

 -  AAD Connect を構成するか、またはこの機能の Azure AD Connect Health エージェントを更新する必要はありますか。  
いいえ、診断プロセスは、完全なクラウド ベース機能です。

 -  既存のオブジェクトが論理的に削除された場合、診断プロセスはオブジェクトを復元して再度アクティブにしますか。  
いいえ、修正では、ソース アンカー以外のオブジェクト属性を更新しません。 
