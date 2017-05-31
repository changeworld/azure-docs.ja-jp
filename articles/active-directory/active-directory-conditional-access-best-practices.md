---
title: "Azure Active Directory の条件付きアクセスのベスト プラクティス | Microsoft Docs"
description: "条件付きアクセス ポリシーを構成するときに知っておくべきことと避けるべきことについてご確認ください。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8564f75f4e90aa7c3b4f93823b5202354c8bba3a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセスのベスト プラクティス

このトピックでは、条件付きアクセス ポリシーを構成するときに知っておくべきことと避けるべきことについて説明します。 このトピックを読む前に、概念と用語について理解しておいてください。「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」に説明があります。

## <a name="what-you-should-know"></a>知っておくべきこと

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>ポリシーにユーザーを割り当てる必要がありますか。

条件付きアクセス ポリシーを構成するときは、少なくとも 1 つのグループを割り当てる必要があります。 ユーザーとグループが割り当てられていない条件付きアクセス ポリシーは、トリガーされることはありません。

ポリシーに複数のユーザーとグループを割り当てる予定の場合は、ユーザーまたはグループを 1 つだけ割り当てて構成をテストすることから始めてください。 ポリシーが期待どおりに動作したら、追加の割り当てを行うことができます。  


### <a name="how-are-assignments-evaluated"></a>割り当てはどのように評価されますか。

すべての割り当ては、論理的に **AND** 処理されます。 複数の割り当てを構成した場合、ポリシーをトリガーするには、すべての割り当てを満たす必要があります。  

組織のネットワークの外部から行われたすべての接続に適用される場所の条件を構成する必要がある場合は、次の割り当てを行うことでこれを実現できます。

- **すべての場所**を含める
- **すべての信頼できる IP** を除外する

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Azure クラシック ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。  
Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。
Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>同じユーザーに対して複数のポリシーが構成されている場合はどうなりますか。  
毎回のサインインで、すべてのポリシーが Azure Active Directory によって評価され、すべての要件が満たされていることを確認したうえで、ユーザーのアクセスが許可されます。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件付きアクセスは、Exchange ActiveSync と連携しますか。

はい。条件付きアクセス ポリシーで Exchange ActiveSync を使用できます。


### <a name="what-happens-if-i-require-multi-factor-authentication-or-a-compliant-device"></a>多要素認証または準拠デバイスが必要な場合は、どうなりますか。

現時点では、ユーザーは、デバイスに関係なく多要素認証が求められます。


## <a name="what-you-should-avoid-doing"></a>避けるべきこと

条件付きアクセス フレームワークは、優れた構成柔軟性を提供します。 ただし、柔軟性が高いということは、リリースの前に各構成ポリシーを慎重に見直して、望ましくない結果を避ける必要があることも意味します。 この状況では、**すべてのユーザー/グループ/クラウド アプリ**などの完全なセットに影響する割り当てには特に注意を払う必要があります。

お客様の環境では、次の構成を避ける必要があります。


**すべてのユーザー、すべてのクラウド アプリに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。

- **準拠デバイスが必要です** - 登録されたデバイスをまだ持っていないユーザーに対して、このポリシーは Intune ポータルへのアクセスを含むすべてのアクセスをブロックします。 このポリシーにより、登録済みのデバイスを持たない管理者は、Azure Portal に戻ってポリシーを変更することができなくなります。

- **ドメインへの参加が必要** - このポリシーでは、ドメイン参加済みデバイスをまだ持っていない場合に、組織内のすべてのユーザーのアクセスがブロックされる可能性もあります。


**すべてのユーザー、すべてのクラウド アプリ、すべてのデバイス プラットフォームに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。


## <a name="common-scenarios"></a>一般的なシナリオ

### <a name="requiring-multi-factor-authentication-for-apps"></a>アプリに対する多要素認証の要求

多くの環境には、他のアプリよりも高いレベルの保護を必要とするアプリがあります。
たとえば、機密データにアクセスするアプリがそうです。
これらのアプリに対して別の保護層を追加する場合は、ユーザーがこれらのアプリにアクセスするときに多要素認証を要求する条件付きアクセス ポリシーを構成できます。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>信頼されていないネットワークからのアクセスに対する多要素認証の要求

このシナリオは、多要素認証要件を追加するという点で前のシナリオに似ています。
ただし、主な違いは、この要件の条件です。  
前のシナリオの焦点は機密データにアクセスするアプリにありますが、このこのシナリオの焦点は信頼している場所にあります。  
つまり、信頼していないネットワークからユーザーがアプリにアクセスする場合に、多要素認証を要求します。


### <a name="only-trusted-devices-can-access-office-365-services"></a>信頼済みデバイスのみが Office 365 サービスにアクセス可能

環境内で Intune を使用している場合は、Azure コンソールで条件付きアクセス ポリシー インターフェイスの使用をすぐに開始できます。

多数の Intune ユーザーは、条件付きアクセスを使用して、信頼済みデバイスのみが Office 365 サービスにアクセスできるようにしています。 これは、モバイル デバイスが Intune に登録されていること、コンプライアンス ポリシーの要件を満たしていること、および Windows PC がオンプレミス ドメインに参加していることを意味します。 重要な機能強化は、Office 365 サービスごとに同じポリシーを設定する必要がないことです。  新しいポリシーを作成するときは、条件付きアクセスによって保護する O365 アプリが含まれるようにクラウド アプリを構成します。

## <a name="next-steps"></a>次のステップ

条件付きアクセスポリシーの構成方法については、[Azure Active Directory での条件付きアクセスの使用](active-directory-conditional-access-azure-portal-get-started.md)に関する記事を参照してください。

