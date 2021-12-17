---
title: Azure Synapse Analytics での条件付きアクセス
description: この記事では、Azure Synapse Analytics での条件付きアクセスについて説明します。
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594475"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Azure Synapse Analytics での条件付きアクセス

Azure Synapse ワークスペースの条件付きアクセス ポリシーを構成できるようになりました。 条件付きアクセスは、Azure Active Directory によって提供されるツールであり、デバイスの種類やデバイスの IP の場所などのいくつかのシグナルをまとめて、リソースに対するアクセスの許可、アクセスのブロック、または多要素認証の適用を決定します。 条件付きアクセス ポリシーは、Azure Active Directory で構成されます。 [条件付きアクセス](../../active-directory/conditional-access/overview.md)の詳細を確認してください。


## <a name="configure-conditional-access"></a>条件付きのアクセスの構成
次の手順は、Azure Synapse ワークスペースの条件付きアクセス ポリシーを構成する方法を示しています。

1. "*全体管理者のアクセス許可*" を持つアカウントを使用して、Azure portal にサインインします。 **[Azure Active Directory]** を選択し、メニューから **[セキュリティ]** を選択します。 
2. **[条件付きアクセス]** を選択し、 **[+ 新しいポリシー]** を選択して、ポリシーの名前を指定します。
3. **[割り当て]** で、 **[ユーザーとグループ]** を選択し、 **[ユーザーとグループの選択]** オプションをオンにして、条件付きアクセスの対象となる Azure AD ユーザーまたはグループを選択します。 [選択] をクリックし、[完了] をクリックします。
4. **[クラウド アプリ]** を選択し、 **[アプリを選択]** をクリックします。 **[Microsoft Azure Synapse Gateway]** を選択します。 その後、 [選択] 、 [完了] の順にクリックします。
5. **[アクセス制御]** で **[許可]** を選択し、適用するポリシーを確認して、[完了] を選択します。
6. **[ポリシーを有効にする]** トグルを **[オン]** に設定し、[作成] を選択します。


## <a name="next-steps"></a>次のステップ
条件付きアクセス ポリシーとそのコンポーネントの詳細を確認します。
- [一般的な条件付きアクセス ポリシー](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [条件付きアクセス ポリシーの構築](../../active-directory/conditional-access/concept-conditional-access-policies.md)