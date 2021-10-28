---
title: Azure Policy を使用して Azure Active Directory 専用認証を適用する
description: この記事では、Azure SQL Database と Azure SQL Managed Instance で Azure Policy を使用して Azure Active Directory (Azure AD) 専用認証を適用する方法について説明します。
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 6ad1e2819a2bd71d00943e4df11b50eb2f0ceb18
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256147"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy を使用して Azure SQL で Azure Active Directory 専用認証を適用する

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> この記事で説明している **Azure AD 専用認証** と、関連付けられている Azure Policy 機能は、**パブリック プレビュー** 段階です。 

この記事では、ユーザーによって Azure SQL マネージド インスタンスが作成されるとき、または Azure SQL Database 用の[論理サーバー](logical-servers.md)が作成されるときに Azure AD 専用認証を適用する Azure ポリシーの作成方法について説明します。 リソース作成時における Azure AD 専用認証の詳細については、「[Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)」を参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> - 論理サーバーまたはマネージド インスタンスの作成時に [Azure AD 専用認証](authentication-azure-ad-only-authentication.md)を強制的に有効にする Azure ポリシーを作成する
> - Azure Policy のコンプライアンスをチェックする

## <a name="prerequisite"></a>前提条件

- Azure Policy を管理するためのアクセス許可があること。 詳細については、「[Azure Policy における Azure RBAC アクセス許可](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。

## <a name="create-an-azure-policy"></a>Azure ポリシーを作成する

まず、SQL Database または Managed Instance のプロビジョニング時に Azure AD 専用認証を強制的に有効にする Azure ポリシーを作成します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **Policy** というサービスを検索します。
1. [作成] 設定で **[定義]** を選択します。
1. **[検索]** ボックスで、「*Azure Active Directory 専用認証*」を検索します。

   Azure AD 専用認証を適用する組み込みポリシーが 2 つ用意されています。 1 つは SQL Database 用で、もう 1 つは Managed Instance 用です。

   - Azure SQL Database で、Azure Active Directory 専用認証が有効になっている必要があります
   - Azure SQL Managed Instance で Azure Active Directory 専用認証を有効にする必要がある

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy のスクリーンショット":::

1. サービスのポリシー名を選択します。 この例では、Azure SQL Database を使用します。 **[Azure SQL Database should have Azure Active Directory Only Authentication enabled]\(Azure SQL Database で、Azure Active Directory 専用認証が有効になっている必要があります\)** を選択します。
1. 新しいメニューで **[割り当て]** を選択します。

   > [!NOTE]
   > このメニューの JSON スクリプトに、SQL Database 用のカスタム Azure ポリシーを作成するためのテンプレートとして使用できる組み込みのポリシー定義が表示されます。 既定値は `Audit` に設定されています。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy を割り当てる画面のスクリーンショット":::

1. **[基本]** タブで、ボックスの横にあるセレクター ( **...** ) を使用して **スコープ** を追加します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy のスコープを選択する画面のスクリーンショット":::

1. **[スコープ]** ペインのドロップダウン メニューから **[サブスクリプション]** を選択し、このポリシーの **リソース グループ** を選択します。 それが済んだら、 **[選択]** ボタンを使用して選択内容を保存します。

   > [!NOTE]
   > リソース グループを選択しなかった場合、ポリシーはサブスクリプション全体に適用されます。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy のスコープを追加する画面のスクリーンショット":::

1. **[基本]** タブに戻って **[割り当て名]** をカスタマイズし、必要に応じて **[説明]** を入力します。 **[ポリシーの適用]** が **[有効]** になっていることを確認します。
1. **[パラメーター]** タブに移動し、 **[Only show parameters that require input]\(入力が必要なパラメーターのみを表示する\)** オプションをオフにします。
1. **[Effect]\(効果\)** で、 **[禁止]** を選択します。 この設定により、Azure AD 専用認証が有効にならずに論理サーバーが作成されるのを防ぐことができます。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy の [Effect]\(効果\) パラメーターのスクリーンショット":::

1. **[Non-compliance messages]\(コンプライアンス違反メッセージ\)** タブで、ポリシーの違反が生じた場合に表示されるポリシーのメッセージをカスタマイズできます。 サーバーの作成中に何のポリシーが適用されたかが、このメッセージでユーザーに伝えられます。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy コンプライアンス違反メッセージのスクリーンショット":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。 ポリシーをレビューし、 **[作成]** ボタンを選択します。

> [!NOTE]
> 新しく作成したポリシーが適用されるまでには、少し時間がかかることがあります。

## <a name="check-policy-compliance"></a>ポリシーのコンプライアンスを確認する

**ポリシー** サービスの **[コンプライアンス]** 設定をチェックして、コンプライアンスの状態を確認できます。

ポリシーに割り当てておいた割り当て名を検索します。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy コンプライアンスのスクリーンショット":::

Azure AD 専用認証で論理サーバーが作成されると、ポリシー レポートの **[コンプライアンスの状態別のリソース]** ビジュアルにあるカウンターが増やされます。 どのリソースが準拠していて、どのリソースが準拠していないかがわかります。

ポリシーの適用範囲内として選択したリソース グループに、既に作成済みのサーバーが含まれていた場合、準拠しているリソースと準拠していないリソースがポリシー レポートに表示されます。

> [!NOTE]
> コンプライアンス レポートの更新には、少し時間がかかることがあります。 リソースの作成や Azure AD 専用認証の設定に関連した変更は、すぐにはレポートされません。    

## <a name="provision-a-server"></a>サーバーをプロビジョニングする

これで、Azure Policy を割り当てたリソース グループに、論理サーバーまたはマネージド インスタンスをプロビジョニングすることができます。 サーバーの作成中に Azure AD 専用認証が有効になっていれば、プロビジョニングは成功します。 Azure AD 専用認証が有効になっていないと、プロビジョニングは失敗します。

詳細については、「[Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure AD 専用認証の Azure Policy](authentication-azure-ad-only-authentication-policy.md) の概要
- [Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)
- [Azure AD 専用認証](authentication-azure-ad-only-authentication.md)の概要