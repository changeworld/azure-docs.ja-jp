---
title: 個々の VSS Azure サブスクリプションでディレクトリ テナントを変更する
description: Azure サブスクリプションを使用してディレクトリ テナントを変更します。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: f2d1bb309a841beac4149e48ed48dcfa21f27c34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092217"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Azure サブスクリプションを使用してディレクトリ テナントを変更する  

組織は、複数の Azure クレジット サブスクリプションを持っている場合があります。 組織が設定する各サブスクリプションは、[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) に関連付けられます。 (Azure AD)  

Azure AD は、Microsoft のクラウドベースの ID およびアクセス管理サービスであり、従業員のサインインや社内外のリソースへのアクセスを支援します。  

使用している Active Directory を変更したり、[サブスクリプションを別の Active Directory に移したりする](../../role-based-access-control/transfer-subscription.md)必要があるかもしれません。  

サブスクリプションをアクティブ化すると、使用する電子メールに基づいて ID が作成されます。 その ID は、組織の Active Directory テナントに関連付けられるか、その ID のために新しいディレクトリ テナントが作成されます。 使用している ID は、Azure portal の右側に表示されます。  

![右上隅に Azure portal が強調表示されている画面のスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "ログインした ID は、Azure portal の右上隅です。")  

ここから、ID を切り替えるか、ディレクトリを切り替えます。 特定のディレクトリにアクセスするには、ID の変更が必要な場合があります。  

ログインしている ID が複数のディレクトリに関連付けられている場合は、[ディレクトリの切り替え] を選択して切り替えます。 現在の ID が関連付けられているディレクトリが表示されます。  

![Azure portal のディレクトリとサブスクリプション ウィンドウのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "[ディレクトリの切り替え] をクリックしてディレクトリを切り替えます。必要なディレクトリを選択します。")  

ポータル内のエクスペリエンスは、使用した ID に関連付けられているディレクトリに大きく依存します。 ディレクトリ テナントを変更するには、管理者がターゲット ディレクトリ内にユーザーとして ID を追加する必要があります。  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Azure Active Directory テナントの変更の重要性  

Visual Studio ライセンスを使用して Azure クレジット サブスクリプションを設定する場合は、仕事用メールまたは個人用メールを使用して ID を作成できます。  

個人アカウントを使用してクレジット サブスクリプションを設定した場合、お客様のアイデンティティと作業は組織のアクティブ ディレクトリから分離されます。 例えば、デプロイ前にテストと学習を行うための独自のサブスクリプションが必要なアプリを開発していたとします。 さて、あなたは自分の組織の作品や文献にアクセスする必要があります。 ディレクトリのテナントを変更することで、自分は組織のリソースにアクセスし、相手は自分のリソースにアクセスできるようになります。  

サブスクリプションを変更または譲渡するときに実行される基本的な手順を示す簡単な図を次に示します。

![サブスクリプションの変更または譲渡を示す図。](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "サブスクリプションを変更または譲渡するとどうなるかを示す図。")  

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

アクセス権を持つ場所と方法によって、ログインした資格情報に基づいて表示される内容が決まります。 このアクセス権は、組織の階層内のさまざまなレベルで与えることができます。 ディレクトリ レベル、サブスクリプション レベル、またはリソース グループ内でアクセス権を与えられる場合があります。  

![Azure アクセス レベルのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Azure で使用できるアクセス レベル。")  

Access Control 内のアクセス レベルは、アクセス レベル内で表示および管理できます。 また、アクセス レベルに応じて、サブスクリプションへの他のユーザーのアクセスを管理できます。  

![Visual Studio Subscription のアクセス制御ページのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "サブスクリプションへのアクセスを管理します。")

## <a name="how-to-change-your-azure-directory-tenant"></a>Azure Directory テナントを変更する方法

別の Active Directory にアクセスするには、ディレクトリ テナントを変更するために必要なアクセス許可とアクセス権を持つアクティブなアカウントが必要です。 アクセスするディレクトリ テナント内の管理者は、次のように追加できます。

* User
* ゲスト  

お客様が追加され、適切な権限が与えられると、サブスクリプション内でディレクトリを切り替えることができます。  

1. Azure portal にサインインし、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で使用するサブスクリプションを選択します  
2. [ディレクトリの変更] を選択します。  

    ![[ディレクトリの変更] が強調表示されている Contoso Enterprise サブスクリプション ページのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "[ディレクトリの変更] を選択します。") 
3. 新しいディレクトリを選択するボックスが表示されます  
4. [変更] を選択します。  

    > [!NOTE]
    > ターゲット ディレクトリへのアクセス権がない場合は表示されません。 サブスクリプションのディレクトリが変更されると、成功メッセージが表示されます。  

    ![[ディレクトリの変更] 検証ページのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "ドロップダウンからディレクトリを選択し、[変更] ボタンをクリックします。")
5. サブスクリプション ページで [ディレクトリの切り替え] を選択し、新しいディレクトリにアクセスする  

  ![[ディレクトリの切り替え] が強調表示されているサブスクリプション ページのスクリーンショット。](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "[ディレクトリの切り替え] をクリックして、新しいディレクトリにアクセスします。")

ターゲット ディレクトリにアクセスし、テナントを変更するには、ターゲット ディレクトリに移動して管理者を追加します。サブスクリプションに管理者を追加する方法については、[これらの手順](/visualstudio/subscriptions/cloud-admin.md)に従ってください。 これが完了すると、管理者は両方のディレクトリにアクセスできるようになり、テナントのディレクトリを変更することができます。  
