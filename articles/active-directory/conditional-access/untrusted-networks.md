---
title: 方法 - 信頼されていないネットワークからのアクセスの試行に関する Azure Active Directory 条件付きアクセス ポリシーを構成する | Microsoft Docs
description: 信頼されていないネットワークからのアクセスの試行について、Azure Active Directory (Azure AD) で条件付きアクセス ポリシーを構成する方法を説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5ddde65b2a68e71d86af6ce3dcd2847736cf5823
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627187"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>方法: 信頼されていないネットワークからのアクセスの試行に関する条件付きアクセス ポリシーを構成する   

モバイル ファースト、クラウド ファーストの世界では、Azure Active Directory (Azure AD) を使用して、デバイス、アプリ、およびサービスにどこからでもシングル サインオンできます。 この結果として、ユーザーは組織のネットワークからだけでなく、信頼されていない任意のインターネットの場所からも、クラウド アプリにアクセスできるようになります。 [Azure Active Directory (Azure AD) の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 この状況での 1 つの一般的な要件は、信頼されていないネットワークから開始されたアクセスの試行を管理することです。 この記事では、この要件に対応する条件付きアクセス ポリシーを構成するために必要な情報を提供します。 

## <a name="prerequisites"></a>前提条件

この記事では、次の内容を熟知していることを前提としています。 

- Azure AD の条件付きアクセスの基本的な概念 
- Azure Portal における条件付きアクセス ポリシーの構成

参照:

- 条件付きアクセスの概要については、「[Azure Active Directory の条件付きアクセスの概要](../active-directory-conditional-access-azure-portal.md)」を参照してください。 

- 条件付きアクセス ポリシーの構成を体験するには、「[クイック スタート : Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。 


## <a name="scenario-description"></a>シナリオの説明

セキュリティと生産性のバランスを得るには、パスワードを使ったユーザーの認証を必須にするだけで十分な場合があります。 しかし、信頼されていないネットワークの場所からアクセスが試行された場合、正当なユーザーによってサインインが行われないリスクが高まります。 この問題に対処するために、信頼されていないネットワークからのアクセス試行をブロックできます。 また、別の方法として、多要素認証 (MFA) を必須にして、アカウントの正当な所有者によって試行されたというさらなる保証を得ることもできます。 

Azure AD 条件付きアクセスを使用すると、アクセス許可を付与するための次に示すポリシーを 1 つ使って、この要件に対応できます。 

- 選択したクラウド アプリに対して

- 選択したユーザーおよびグループに対して  

- 多要素認証を必須にする 

- 次の場所からアクセスの試行が行われた場合: 

    - 信頼されていない場所


## <a name="considerations"></a>考慮事項

このシナリオの課題は、"*信頼されていない場所からアクセスの試行が行われた場合*" を条件付きアクセスの条件に変換することです。 条件付きアクセス ポリシーでは、[場所の条件](location-condition.md)を構成して、ネットワークの場所に関連するシナリオに対応できます。 場所の条件を利用して、IP アドレスの範囲、国、およびリージョンの論理グループ化を示している名前付きの場所を選択できます。  

通常、組織では、1 つまたは複数アドレスの範囲 (たとえば、199.30.16.0 ～ 199.30.16.24) を保持しています。
名前付きの場所は、次の方法で構成できます。

- この範囲 (199.30.16.0 ～ 24) を指定する 

- **企業ネットワーク**など、わかりやすい名前を割り当てる 


信頼されていないすべての場所が何かの定義を試みる代わりに、次のことを実行できます。

- Include (含める) 

    ![条件付きアクセス](./media/untrusted-networks/02.png)

- すべての信頼できる場所を除外する 

    ![条件付きアクセス](./media/untrusted-networks/01.png)



## <a name="implementation"></a>実装

この記事で説明した方法を使って、信頼されていない場所に対する条件付きアクセス ポリシーを構成できるようになりました。 運用環境に公開して期待どおりに動作することを確かめる前に、必ずポリシーをテストする必要があります。 理想的には、可能な場合はテスト テナントで初期テストを実行することをお勧めします。 詳細については、「[新しいポリシーはどのように展開する必要がありますか](best-practices.md#how-should-you-deploy-a-new-policy)」を参照してください。 



## <a name="next-steps"></a>次の手順

条件付きアクセスの詳細については、「[Azure Active Directory の条件付きアクセスの概要](../active-directory-conditional-access-azure-portal.md)」を参照してください。