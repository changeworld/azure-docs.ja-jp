---
title: Azure Active Directory の条件付きアクセスのベスト プラクティス | Microsoft Docs
description: 条件付きアクセス ポリシーを構成するときに知っておくべきことと避けるべきことについてご確認ください。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d21a6dc7a460e07fe7530b58bef887241a694b25
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628088"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセスのベスト プラクティス

[Azure Active Directory (Azure AD) の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 この記事では、次の項目に関する情報を提供します。

- 知っておくべきこと 
- 条件付きアクセス ポリシーを構成するときに避けるべきこと 

この記事は、「[Azure Active Directory の条件付きアクセスの概要](../active-directory-conditional-access-azure-portal.md)」で説明されている概念と用語について理解していることを前提としています



## <a name="whats-required-to-make-a-policy-work"></a>ポリシーを機能させるために必要なこと

新しいポリシーを作成するとき、ユーザー、グループ、アプリケーション、またはアクセスの制御は選択されていません。

![クラウド アプリ](./media/best-practices/02.png)


ポリシーを機能させるには、以下を構成する必要があります。


|対象           | 方法                                  | 理由|
|:--            | :--                                  | :-- |
|**クラウド アプリ** |1 つまたは複数のアプリを選択する必要があります。  | 条件付きアクセス ポリシーの目的は、許可されたユーザーによるクラウド アプリへのアクセス方法を、ご自身で制御できるようにすることです。|
| **ユーザーとグループ** | 選択したクラウド アプリにアクセスする権限が付与されたユーザーまたはグループを、少なくとも 1 つ選択する必要があります。 | ユーザーとグループが割り当てられていない条件付きアクセス ポリシーは、トリガーされることはありません。 |
| **アクセスの制御** | アクセスの制御を少なくとも 1 つ選択する必要があります。 | 条件が満たされた場合のポリシー プロセッサの対応を決める必要があります。|




## <a name="what-you-should-know"></a>知っておくべきこと

### <a name="how-are-assignments-evaluated"></a>割り当てはどのように評価されますか。

すべての割り当ては、論理的に **AND** 処理されます。 複数の割り当てを構成した場合、ポリシーをトリガーするには、すべての割り当てが満たされている必要があります。  

組織のネットワークの外部から行われたすべての接続に適用される場所の条件を構成する必要がある場合は、次を行います。

- **すべての場所**を含める
- **すべての信頼できる IP** を除外する


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Azure AD 管理ポータルからロックアウトされた場合はどのように対処すればよいですか。

条件付きアクセス ポリシーの設定が正しくないため Azure AD ポータルからロックアウトされた場合は、次の操作を行います。

- まだブロックされていない管理者が組織にいないかどうかを確認します。 Azure Portal へのアクセス権を持つ管理者であれば、ご自身のサインインに影響するポリシーを無効にできます。 

- ポリシーを更新できる管理者が組織にいない場合は、サポート要求を送信する必要があります。 Microsoft サポートが、アクセスを妨げている条件付きアクセス ポリシーを確認して、更新できます。


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Azure クラシック ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。  

Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。

Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>同じユーザーに対して複数のポリシーが構成されている場合はどうなりますか。  

毎回のサインインで、すべてのポリシーが Azure Active Directory によって評価され、すべての要件が満たされていることを確認したうえで、ユーザーのアクセスが許可されます。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件付きアクセスは、Exchange ActiveSync と連携しますか。

はい。条件付きアクセス ポリシーで Exchange ActiveSync を使用できます。






## <a name="what-you-should-avoid-doing"></a>避けるべきこと

条件付きアクセス フレームワークは、優れた構成柔軟性を提供します。 ただし、柔軟性が高いということは、リリースの前に各構成ポリシーを慎重に見直して、望ましくない結果を避ける必要があることも意味します。 この状況では、**すべてのユーザー/グループ/クラウド アプリ**などの完全なセットに影響する割り当てには特に注意を払う必要があります。

お客様の環境では、次の構成を避ける必要があります。


**すべてのユーザー、すべてのクラウド アプリに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。

- **準拠しているデバイスが必要です** - 登録されたデバイスをまだ持っていないユーザーに対して、このポリシーは Intune ポータルへのアクセスを含むすべてのアクセスをブロックします。 このポリシーにより、登録済みのデバイスを持たない管理者は、Azure Portal に戻ってポリシーを変更することができなくなります。

- **ドメインへの参加が必要** - このポリシーでは、ドメイン参加済みデバイスをまだ持っていない場合に、組織内のすべてのユーザーのアクセスがブロックされる可能性もあります。


**すべてのユーザー、すべてのクラウド アプリ、すべてのデバイス プラットフォームに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。


## <a name="how-should-you-deploy-a-new-policy"></a>新しいポリシーはどのように展開する必要がありますか。

最初に、[what if ツール](what-if-tool.md)を使用してポリシーを評価します。

新しいポリシーをご利用環境に展開する準備ができたら、段階的に展開します。

1. 少数のユーザーにポリシーを適用し、想定どおりに動作することを確認します。 

2.  より多くのユーザーが含まれるようにポリシーを拡げます。このとき、管理者は引き続きそのポリシーから除外します。 これにより、変更が必要になった場合に、管理者がポリシーにアクセスして更新することができます。

3. 本当に必要な場合にのみ、すべてのユーザーにポリシーを適用します。 

ベスト プラクティスとして、次のようなユーザー アカウントを作成します。

- ポリシー管理専用である 
- すべてのポリシーから除外されている


## <a name="policy-migration"></a>ポリシーの移行

次の理由から、Azure Portal で作成していないポリシーの移行を検討します。

- 以前は処理できなかったシナリオに対処できるようになった可能性があります。

- ポリシーを統合することで、管理する必要のあるポリシーの数を減らすことができます。   

- 一元的な場所ですべての条件付きアクセス ポリシーを管理することができます。

- Azure クラシック ポータルは廃止されました。   


詳しくは、「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」をご覧ください。


## <a name="next-steps"></a>次の手順

条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
