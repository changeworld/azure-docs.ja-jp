---
title: Azure Active Directory でマイ アプリ ポータルのワークスペースを作成する | Microsoft Docs
description: マイ アプリ ワークスペースを使用してマイ アプリ ページをカスタマイズし、エンド ユーザーのマイ アプリ エクスペリエンスをシンプルにします。 別個のタブを使用してアプリケーションをグループに整理します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199828"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>マイ アプリ (プレビュー) ポータルでワークスペースを作成する

ユーザーは、マイ アプリ (プレビュー) ポータルを使用して、自分がアクセス権を持つクラウドベースのアプリケーションを表示および起動できます。 既定では、ユーザーがアクセスできるすべてのアプリケーションが 1 つのページにまとめて表示されます。 Azure AD Premium P1 または P2 ライセンスをお持ちの場合は、このページをご自分のユーザー向けに整理するために、ワークスペースを設定できます。 ワークスペースを使用すると、関連するアプリケーションを (たとえば、ジョブのロール、タスク、またはプロジェクトごとに) グループ化して、別のタブに表示できます。ワークスペースでは基本的に、ユーザーが既にアクセスできるアプリケーションがフィルター選択されます。そのため、ワークスペースには、ユーザーに割り当てられているアプリケーションのみが表示されます。

> [!NOTE]
> この記事では、管理者がワークスペースを有効にして作成する方法について説明します。 マイ アプリ ポータルとワークスペースの使用方法に関するエンド ユーザー向けの情報については、[ワークスペースにアクセスして使用する方法](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)に関するページを参照してください。

## <a name="enable-my-apps-preview-features"></a>マイ アプリのプレビュー機能を有効にする

1. [**Azure portal**](https://portal.azure.com/) を開いて、ユーザー管理者またはグローバル管理者としてサインインします。

2. **[Azure Active Directory]**  >  **[ユーザー設定]** に移動します。

3. **[ユーザー機能プレビュー]** で、 **[ユーザー機能プレビュー設定の管理]** を選択します。

4. **[ユーザーはマイ アプリのプレビュー機能を使用できます]** で、次のいずれかのオプションを選択します。
   * **[選択済み]** - 特定のグループのプレビュー機能を有効にします。 **[グループを選ぶ]** オプションを使用して、プレビュー機能を有効にしたいグループを選択します。  
   * **[すべて]** - すべてのユーザーに対してプレビュー機能を有効にします。

   ![ユーザー プレビュー機能](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> ユーザーがマイ アプリ ポータルを開くには、リンク `https://myapps.microsoft.com` または組織用にカスタマイズされたリンク (`https://myapps.microsoft.com/contoso.com` など) を使用できます。 マイ アプリ プレビュー バージョンにリダイレクトされない場合、ユーザーは `https://myapplications.microsoft.com` または `https://myapplications.microsoft.com/contoso.com` を試す必要があります。

## <a name="create-a-workspace"></a>ワークスペースの作成

ワークスペースを作成するには、Azure AD Premium P1 または P2 ライセンスが必要です。

1. [**Azure portal**](https://portal.azure.com/) を開いて、Azure AD Premium P1 または P2 ライセンスを持つ管理者としてサインインします。

2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。

3. **[管理]** で、 **[ワークスペース (プレビュー)]** を選択します。

4. **[新しいワークスペース]** を選択します。 **[新しいワークスペース]** ページで、ワークスペースの**名前**を入力します (名前に "workspace" を使用しないことをお勧めします)。 次に、**説明**を入力します。

   ![新しいワークスペースを作成する](media/access-panel-workspaces/new-workspace.png)

5. **[確認および作成]** を選択します。 新しいワークスペースのプロパティが表示されます。

6. **[アプリケーション]** タブを選択します。 **[アプリケーションの追加]** で、ワークスペースに追加するすべてのアプリケーションを選択するか、**検索**ボックスを使用してアプリケーションを検索します。 

   ![アプリケーションをワークスペースに追加する](media/access-panel-workspaces/add-applications.png)

7. **[追加]** を選択します。 選択したアプリケーションの一覧が表示されます。 上矢印と下矢印を使用して、一覧内のアプリケーションの順序を変更できます。

   ![ワークスペース内のアプリケーションの一覧](media/access-panel-workspaces/add-applications-list.png)

8. **[ユーザーとグループ]** タブを選択します。ユーザーまたはグループを追加するには、 **[ユーザーの追加]** を選択します。 

9. **[メンバーの選択]** ページで、ワークスペースを割り当てたいユーザーまたはグループを選択します。 または、**検索**ボックスを使用して、ユーザーまたはグループを見つけます。

   ![ユーザーとグループをワークスペースに割り当てる](media/access-panel-workspaces/add-users-and-groups.png)

10. ユーザーとグループの選択が終了したら、 **[選択]** を選択します。

11. ユーザーのロールを **[読み取りアクセス]** から **[所有者]** に (またはその逆に) 変更するには、現在のロールをクリックし、新しいロールを選択します。

    ![ユーザーとグループにロールを割り当てる](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>監査ログの表示

監査ログには、ワークスペース作成エンド ユーザー アクションなど、マイ アプリ ワークスペースの操作が記録されます。 マイ アプリから次のイベントが生成されます。

* ワークスペースの作成
* ワークスペースの編集
* ワークスペースの削除
* アプリケーションの起動 (エンド ユーザー)
* セルフサービス アプリケーションの追加 (エンド ユーザー)
* セルフサービス アプリケーションの削除 (エンド ユーザー)

[Azure portal](https://portal.azure.com) で監査ログにアクセスするには、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[監査ログ]** ([アクティビティ] セクション内) の順に選択します。 **[サービス]** で、 **[マイ アプリ]** を選択します。

   ![ユーザーとグループにロールを割り当てる](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>次の手順
[Azure Active Directory でのアプリケーションのエンドユーザー エクスペリエンス](end-user-experiences.md)