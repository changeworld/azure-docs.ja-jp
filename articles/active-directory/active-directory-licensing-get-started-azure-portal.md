---
title: "Azure Active Directory のライセンスの概要 | Microsoft Docs"
description: "Azure Active Directory のライセンスのしくみ、ベスト プラクティスで使用を開始する方法"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Azure Active Directory での自分とユーザーのライセンスの取得

> [!div class="op_single_selector"]
> * [Azure Portal での手順](active-directory-licensing-get-started-azure-portal.md)
> * [Azure クラシック ポータルの情報を確認する](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) は、Microsoft が提供する、サービスとしての ID (IDaaS) ソリューションおよびプラットフォームです。 Azure AD は、以下のさまざまなサービス エディションで提供されています。

* Azure Active Directory Free。Office 365、Dynamics、Microsoft Intune、Azure などの Microsoft サービスで使用できます。 Azure AD では、このモードでの使用料金は発生しません。

* Azure AD の有料版には次のようなものがあります。
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 および P2)
  - Azure AD Basic
  - Azure Multi-Factor Authentication

多くの Microsoft オンライン サービスと同様、ほとんどの Azure AD の有料バージョンは、Office 365、Microsoft Intune、Azure AD のように、ユーザーごとの資格を通じて提供されます。 このような場合、サービスの購入は 1 つ以上のサブスクリプションによって表され、各サブスクリプションには、テナントで事前に購入したライセンスが含まれます。 各ユーザーの権利は以下によって実現されます。

* ライセンスを削除します。 
* ユーザーと製品の間にリンクを作成します。
* ユーザーのサービス コンポーネントを有効にします。
* 前払いライセンスの 1 つを使用します。

[今すぐ Azure AD Premium をお試しください。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Azure AD サービスの機能の概要については、「[Azure AD とは](active-directory-whatis.md)」を参照してください。 詳細については、[サービス レベル アグリーメントのページ](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/)を参照してください。

> [!NOTE]
> Azure の従量課金制サブスクリプションは、Azure リソースを作成でき、支払い方法にマップされます。 サブスクリプションに関連付けられているライセンス数はありません。 ユーザーに、サブスクリプションにマップされている Azure リソースを操作するアクセス許可を付与すると、サブスクリプションに関連付けられ、サブスクリプション リソースを管理することができます。

## <a name="how-does-azure-active-directory-licensing-work"></a>Azure Active Directory のライセンスのしくみ

ライセンス ベースの Azure AD サービスは、Azure AD サービスのテナントのサブスクリプションをアクティブにすることで有効になります。 サブスクリプションがアクティブになると、サービスの機能は、Azure AD 管理者が管理し、ライセンスを付与されたユーザーが使用できるようになります。

### <a name="manage-subscription-information"></a>サブスクリプション情報の管理

Enterprise Mobility + Security、Azure AD Premium、Azure AD Basic を購入すると、有効期間や前払いライセンスを含め、サブスクリプションでテナントが更新されます。 割り当てられているライセンスの数や使用可能なライセンスの数などのサブスクリプション情報は Azure Portal の **Azure Active Directory** で、該当するディレクトリの **[ライセンス]** タイルを開くと入手できます。 **[ライセンス]** ブレードは、ライセンスの割り当ての管理にも最適です。

各サブスクリプションは 1 つ以上のサービス プラン (Azure AD、Multi-Factor Authentication、Intune、Exchange Online、SharePoint Online など) によって構成されます。  Azure AD のライセンス管理では、サービス プラン レベルの管理は "*不要*" です。 Office 365 では、含まれるサービスのアクセス管理に詳細な構成モードを利用するため、異なります。 Azure AD では、機能の有効化と個々のアクセス許可の管理には、使用中の構成を利用します。

> [!IMPORTANT]
> Azure AD Premium、Azure AD Basic、Enterprise Mobility + Security サブスクリプションは、プロビジョニングされたディレクトリまたはテナントに限定されます。 サブスクリプションをディレクトリ間で分割することや、他のディレクトリ内のユーザーに資格を与えるために使用することはできません。 ディレクトリ間でサブスクリプションを移動することはできますが、直接購入には、サポート チケットの送信、または取り消しと再購入が必要になります。
>
> Azure AD または Enterprise Mobility + Security をボリューム ライセンス サブスクリプションを通して購入したときに、契約に他の Microsoft Online サービス (Office 365 など) が含まれている場合、アクティブ化は自動的に行われます。 

### <a name="assign-licenses"></a>ライセンスを割り当てる

有料機能を構成するうえで必要なことはサブスクリプションの入手だけですが、さらに Azure AD の有料機能のライセンスをユーザーに配布する必要があります。 Azure AD の有料機能へのアクセスが必要なユーザーや Azure AD の有料機能で管理されているユーザーには、ライセンスを割り当てる必要があります。 ライセンスの割り当てとは、ユーザーと、Azure AD Premium、Basic、Enterprise Mobility + Security などの購入済みのサービスとのマッピングです。


ディレクトリ内のどのユーザーにライセンスを付与する必要があるかを管理するには、以下を行います。 

* [Azure Portal](active-directory-licensing-whatis-azure-portal.md) でグループにライセンスを割り当てます。
* ポータル、PowerShell、または API を使用してユーザーに直接ライセンスを割り当てます。 

ライセンスをグループに割り当てると、グループ メンバー全員にライセンスが割り当てられます。 グループに追加されたユーザーには該当するライセンスが割り当てられ、グループから削除されたユーザーは該当するライセンスが削除されます。 グループの割り当てには、使用可能な任意のグループ管理を利用できます。これは、アプリケーションへのグループベースの割り当てと一貫しています。

[グループベースのライセンス割り当て](active-directory-licensing-whatis-azure-portal.md)を使用して、次のような規則を設定することができます。
* ディレクトリ内のすべてのユーザーが自動的にライセンスを取得する
* 適切な役職を持つすべてのユーザーがライセンスを取得する
* 組織内の他の管理者に決定を委任することができる ([セルフサービス グループ](active-directory-accessmanagement-self-service-group-management.md)を使用)

高度なシナリオや Office 365 ライセンスのシナリオを含め、グループにライセンスを割り当てる方法の詳細については、「[Azure Active Directory でのユーザーのグループへのライセンスの割り当て](active-directory-licensing-group-assignment-azure-portal.md)」を参照してください。

## <a name="get-started-with-azure-ad-licensing"></a>Azure AD ライセンスを使ってみる

Azure AD Connect の使い方は簡単です。 [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)のサインアップの一部として、いつでもディレクトリを作成することができます。

次のベスト プラクティスによって、テナントが、使用している可能性のある他の Microsoft サービスや、サービスの目標に沿っていることを確認することができます。

- マイクロソフトが提供するいずれかの組織向けサービスを既に使用している場合は、Azure AD テナントを既に使用しています。 プロビジョニングやハイブリッド シングル サインオン (SSO) など、中心的な ID 管理をそれらのサービスでも利用できるように、他のサービスでも同じテナントを使用すると便利です。 シングル サインオンを使用すると、ユーザーはさまざまなサービスで豊富な機能を利用することができます。 そのため、従業員用に Azure AD の有料サービスの購入を決定した場合は、同じテナントを再使用することをお勧めします。

- 次の内容を計画している場合は、Azure Portal で新しいテナントを使用することをお勧めします。
  - 別のユーザー グループ (パートナーや顧客など) に Azure AD を使用する。
  - 運用サービスから分離して Azure AD サービスを評価する。
  - サービスのサンドボックス環境を設定する。

  新しいディレクトリは、グローバル管理者のアクセス許可を持つ外部ユーザーとしてのアカウントと共に作成されます。 このアカウントで Azure Portal にサインインすると、このテナントが表示され、すべての管理タスクにアクセスすることができます。

> [!NOTE]
> Azure AD では "ゲスト ユーザー" をサポートしています。ゲスト ユーザーとは、Microsoft アカウントまたは別のテナントの Azure AD の ID を使用して作成された Azure AD テナント内のユーザー アカウントです。 現在、Office 365 管理ポータルでは、これらのユーザーはサポートされていません。 Microsoft アカウントを持つゲスト ユーザーは Office 365 管理ポータルに一切アクセスできず、他の Azure AD テナントのゲスト ユーザーは無視されます。 後者の場合、ユーザーを最初に作成した Azure AD または Office 365 テナント内のユーザーのローカル アカウントのみがアクセスできます。

### <a name="select-one-or-more-license-trials"></a>1 つ以上のライセンスの評価版を選択する

**Azure Active Directory** &gt; **[クイック スタート]** で、Azure AD Premium または Enterprise Mobility + Security 試用版サブスクリプションをアクティブ化することができます。

![ライセンスの試用版を選択する](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

試用版ライセンスは **[ライセンス]** ブレードから入手できます。

### <a name="assign-licenses-to-users-and-groups"></a>ユーザーとグループにライセンスを割り当てる

サブスクリプションがアクティブになった後、ライセンスを自分に割り当てる必要があります。 次に、ブラウザを更新して、すべての機能が表示されていることを確認します。 次の手順では、Azure AD の有料機能へのアクセスが必要なユーザーにライセンスを割り当てます。 「[ライセンスを割り当てる](#assign-licenses)」で説明したように、ライセンスを割り当てる簡単な方法の 1 つは、対象となるユーザーを表すグループを特定し、そのグループにライセンスを割り当てることです。 この方法で、ライフ サイクル全体を通してユーザーをグループに追加または削除すると、そのユーザーはそれぞれライセンスが割り当てられたリ削除されたりします。

> [!NOTE]
> 一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 このプロパティは、Azure Portal の **[ユーザー]** &gt; **[プロファイル]** &gt; **[設定]** で設定できます。 グループ ライセンスの割り当てを使用するとき、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。

ライセンスを割り当てるには、**Azure Active Directory** &gt; **[ライセンス]** &gt; **[すべての製品]** で、1 つ以上の製品を選択し、コマンド バーの **[割り当て]** を選択します。

![割り当てるライセンスを選択する](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

**[ユーザーとグループ]** ブレードを使用して、複数のユーザーまたはグループを選択したり、製品内のサービス プランを無効にしたりできます。 上部の検索ボックスを使用して、ユーザーやグループの名前を検索します。

![ライセンスを割り当てるユーザーまたはグループを選択する](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

ライセンスをグループに割り当てるとき、グループ内のユーザー数によっては、すべてのユーザーがライセンスを継承するまでに少し時間がかかることがあります。 処理の状態は、**[ライセンス]** タイルの下の **[グループ]** ブレードで確認できます。

![ライセンスの割り当て状態](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Azure AD および Enterprise Mobility + Security 製品を管理しているとき、Azure AD ライセンスの割り当て中に割り当てエラーが発生する可能性がありますが、発生するのは比較的まれです。 割り当てで発生する可能性があるエラーは次の内容に限定されます。
- 割り当て競合: ユーザーが、現在のライセンスと互換性のないライセンスに以前から割り当てられている状況です。 この場合は、新しいライセンスを割り当てるために現在のライセンスを削除する必要があります。
- "利用可能なライセンス数の上限を超えました" は、割り当てられているグループ内のユーザーの数が利用可能なライセンスを超えたときに、ライセンスの不足のため、ユーザーの割り当ての状態に割り当ての失敗が反映されます。

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B コラボレーションのライセンス

B2B コラボレーションでは、Azure AD テナントにゲスト ユーザーを招待して、Azure AD サービスおよび利用可能な Azure リソースへのアクセスを提供することができます。  

B2B ユーザーを招待し、Azure AD でアプリケーションに割り当てる際に料金は発生しません。 また、ゲスト ユーザーあたり最大 10 個のアプリと、3 つの基本レポートが、B2B コラボレーション ユーザーに対して無料となります。 パートナーの Azure AD テナントでゲスト ユーザーに適切なライセンスが割り当てられている場合は、ご使用のテナントでもライセンスが適用されます。

必須ではありませんが、Azure AD の有料機能へのアクセスを提供する場合は、それらの B2B ゲスト ユーザーは適切な Azure AD ライセンスを持っている必要があります。 Azure AD の有料ライセンスを持つ招待元のテナントは、そのテナントに招待された追加の 5 人のゲスト ユーザーに B2B コラボレーション ユーザーの権利を割り当てることができます。 シナリオおよび情報については、[B2B コラボレーションのライセンスに関するガイダンス](active-directory-b2b-licensing.md)を参照してください。

### <a name="view-assigned-licenses"></a>割り当てられたライセンスを表示する

割り当て済みライセンスと割り当て可能なライセンスの概要ビューが、**[Azure Active Directory]** &gt; **[ライセンス]** &gt; **[すべての製品]** に表示されます。

![ライセンスの概要を表示する](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

特定の製品を選択すると、割り当てられたユーザーとグループの詳しい一覧を表示できます。 **[ライセンスされているユーザー]** 一覧に、現在ライセンスを使用しているすべてのユーザーと、ライセンスがユーザーに直接割り当てられたかどうか、またはグループから継承されているかどうかが表示されます。

![ライセンスの詳細を表示する](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同様に、**[ライセンスされているグループ]** 一覧には、ライセンスが割り当てられているすべてのグループが表示されます。 ユーザーまたはグループを選択して、**[ライセンス]** ブレードを開くと、そのオブジェクトに割り当てられているすべてのライセンスが表示されます。

### <a name="remove-a-license"></a>ライセンスを削除する

ライセンスを削除するには、ユーザーまたはグループに移動して、**[ライセンス]** タイルを開きます。 ライセンスを選択し、**[削除]** をクリックします。

![ライセンスを削除する](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

ユーザーがグループから継承したライセンスを直接削除することはできません。 代わりに、ライセンスを継承したグループからユーザーを削除します。

### <a name="extend-trials"></a>試用期間を延長する

顧客向けの試用版の試用期間の延長は、Office 365 ポータルからセルフ サービス サインアップで使用できます。 顧客の管理者は、Office ポータル (Office ポータルに対するアクセス許可によってアクセス権は異なります) に移動し、Azure AD Premium 試用版を選択できます。 **[Extend trial (試用期間を延長する)]** リンクをクリックすると延長プロセスが開始します。 クレジット カードが必要ですが、請求されません。

![Azure Portal で試用期間を延長する](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>次のステップ

グループを使用したライセンス管理の詳細なシナリオについては、[グループへのライセンスの割り当て](active-directory-licensing-group-assignment-azure-portal.md)に関する記事をご覧ください。

その他の Azure AD 有料機能の構成および使用方法に関する情報を次に示します。

* [セルフサービスのパスワード リセット](active-directory-manage-passwords.md)
* [セルフサービスのグループ管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure AD Premium ライセンスの直接購入](http://aka.ms/buyaadp)

