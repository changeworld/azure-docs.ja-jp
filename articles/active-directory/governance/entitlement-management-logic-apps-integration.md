---
title: Azure AD エンタイトルメント管理を使用してカスタム Logic Apps をトリガーする
description: Azure Active Directory のエンタイトルメント管理でカスタム Logic Apps を構成して使用する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 11/02/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 397809ed8717cb323836ee6ca1243bff1e4df6fb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709958"
---
# <a name="trigger-custom-logic-apps-with-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理を使用してカスタム Logic Apps をトリガーする


[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) を使用すると、カスタム ワークフローを自動化し、アプリやサービスを 1 か所で接続できます。 ユーザーは、Logic Apps をエンタイトルメント管理と統合し、コアのエンタイトルメント管理のユース ケースを超えてガバナンス ワークフローを広げることができます。

これらの Logic Apps は、エンタイトルメント管理のユース ケース (アクセス パッケージが許可または要求されたときなど) に従って実行されるようにトリガーできます。 たとえば、管理者は、ユーザーがアクセス パッケージを要求したときに、サード パーティの SAAS アプリ (Salesforce など) の特定の特性を割り当てるか、カスタム電子メールを送信するロジック アプリがトリガーされるように、カスタム ロジック アプリを作成してエンタイトルメント管理にリンクすることができます。

Logic Apps と統合できるエンタイトルメント管理のユース ケースには次のものがあります。  

- アクセス パッケージが要求されたとき  

- アクセス パッケージの要求が許可されたとき  

- アクセス パッケージの割り当てが期限切れになったとき  

Logic Apps へのこれらのトリガーは、 **[ルール]** というアクセス パッケージ ポリシー内の新しいタブで制御します。 また、[カタログ] ページの **[カスタム拡張機能]** タブには、特定のカタログに対して追加されたすべての Logic Apps が表示されます。 この記事では、エンタイトルメント管理でロジック アプリを作成してカタログに追加し、パッケージにアクセスする方法について説明します。

## <a name="create-and-add-a-logic-app-to-a-catalog-for-use-in-entitlement-management"></a>エンタイトルメント管理で使用するロジック アプリを作成してカタログに追加する 

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、カタログ所有者、またはリソース グループ所有者 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。 

1. 左側のメニューで、 **[カタログ]** を選択します。 

1. 左側のメニューで、 **[カスタム拡張機能 (プレビュー)]** を選択します。 

1. ヘッダーのナビゲーション バーで、 **[カスタム拡張機能の追加]** を選択します。  

1. **[基本]** タブで、カスタム拡張機能 (追加するリンクされたロジック アプリ) の名前とワークフローの説明を入力します。 これらのフィールドは、今後カタログの **[カスタム拡張機能]** タブに表示されます。 

    ![カスタム拡張機能を作成するためのペイン](./media/entitlement-management-logic-apps/create-custom-extension.png)


1. 次に、 **[詳細]** タブに移動します。 

1. [新しいロジック アプリの作成] フィールドで **[はい]** を選択します。 または、既存のロジック アプリを使用する場合は、 **[いいえ]** を選択してステップ 9 に進みます。 [はい] を選択した場合は、以下のいずれかのオプションを選択して、ステップ 9 に進みます。 

    1. 新しいアプリケーションを新しいロジック アプリのベースとして使用する場合は、 **[新しい Azure AD アプリケーションの作成]** を選択します。または
    
        ![ロジック アプリ用の新しいアプリを選択するためのペイン](./media/entitlement-management-logic-apps/new-app-selection.png)

    1. 既存のアプリケーションを新しいロジック アプリのベースとして使用する場合は、 **[既存の Azure AD アプリケーション]** を選択します。
    
        ![ロジック アプリ用の既存のアプリを選択するためのペイン](./media/entitlement-management-logic-apps/existing-app-selection.png)

    > [!Note]    
    > その後、ロジック アプリ デザイナーでロジック アプリの動作を編集できます。 これを行うには、 **[カタログ]** の **[カスタム拡張機能]** タブで作成したロジック アプリを選択します。  

1. 次に、 **[サブスクリプション ID]** 、 **[リソース グループ]** 、 **[ロジック アプリ名]** を入力します。 

1. 次に、 **[検証と作成]** を選択します。 

1. カスタム拡張機能の要約を確認し、ロジック アプリのコールアウトの詳細が正しいことを確認してください。 **[作成]** を選択します。

    ![カスタム拡張機能の要約の例](./media/entitlement-management-logic-apps/custom-extension-summary.png)

1. リンクされたロジック アプリに対するこのカスタム拡張機能が、[カタログ] の下の [カスタム拡張機能] タブに表示されるようになります。 これは、アクセス パッケージ ポリシーで呼び出すことができます。


## <a name="edit-a-linked-logic-app"></a>リンクされたロジック アプリを編集する 

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、またはカタログ所有者 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。 

1. 左側のメニューで、 **[カタログ]** を選択します。 

1. 左側のメニューで、 **[カスタム拡張機能]** を選択します。 

1. ここでは、このカタログに追加したすべてのカスタム拡張機能 (Logic Apps) を表示できます。 ロジック アプリのワークフローを編集したり、新しく追加したロジック アプリのワークフローを作成したりするには、 **[エンドポイント]** でロジック アプリのカスタム拡張機能を選択します。 これにより、ロジック アプリ デザイナーが開き、ワークフローを作成できるようになります。  

 ロジック アプリのワークフローの作成の詳細については、[Azure portal で Azure Logic Apps を使用して自動化されたワークフローを作成する方法](../../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="add-custom-extension-to-access-package-policy"></a>アクセス パッケージのポリシーにカスタム拡張機能を追加する 

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、カタログ所有者、またはアクセス パッケージ マネージャー 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。 

1. 左側のメニューで、 **[アクセス パッケージ]** を選択します。 

1. 新しいアクセス パッケージにカスタム拡張機能 (ロジック アプリ) を追加する場合は、 **[新しいアクセス パッケージ]** を選択します。 または、既に作成されているアクセス パッケージの一覧から、カスタム拡張機能 (ロジック アプリ) を追加するアクセス パッケージを選択します。  

    > [!NOTE]  
    > アクセス パッケージを作成する方法の詳細については、「[エンタイトルメント管理で新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md)」を参照してください。  既存のアクセス パッケージを編集する方法の詳細については、「[Azure AD エンタイトルメント管理でアクセス パッケージの要求設定を変更する](entitlement-management-access-package-request-policy.md#open-and-edit-an-existing-policy-of-request-settings)」を参照してください。 

1. アクセス パッケージのポリシー設定で、 **[ルール (プレビュー)]** タブに移動します。 

1. **[タイミング]** の下のメニューで、このカスタム拡張機能 (ロジック アプリ) のトリガーとして使用するアクセス パッケージ イベントを選択します。 たとえば、ユーザーがアクセス パッケージを要求したときにカスタム拡張機能ロジック アプリのワークフローをトリガーするだけの場合は、 **[要求の作成時]** を選択します。 

1. **[実行]** の下のメニューで、アクセス パッケージに追加するカスタム拡張機能 (ロジック アプリ) を選択します。 選択した実行アクションは、[タイミング] フィールドで選択したイベントが発生したときに実行されます。  

1. 新しいアクセス パッケージにカスタム拡張機能を追加する場合は、 **[作成]** を選択します。 既存のアクセス パッケージに追加する場合は、 **[更新]** を選択します。

    ![アクセス パッケージにロジック アプリを追加します](./media/entitlement-management-logic-apps/add-logic-apps-access-package.png)

## <a name="troubleshooting-and-validation"></a>トラブルシューティングと検証 

アクセス パッケージの **[実行]** オプションで呼び出されたときに、関連付けられたロジック アプリがカスタム拡張機能によって正しくトリガーされたことを確認するには、ロジック アプリのログを表示します。 

特定のロジック アプリの概要ページには、ロジック アプリが最後に実行されたときのタイムスタンプが表示されます。 また、リンクされたカスタム拡張機能があるリソース グループの概要では、そのカスタム拡張機能が正しく構成されている場合、カスタム拡張機能の名前が表示されます。  

## <a name="next-steps"></a>次の手順