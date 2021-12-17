---
title: 条件付きアクセス - 承認されたアプリまたはアプリ保護ポリシーを必須にする - Azure Active Directory
description: カスタム条件付きアクセス ポリシーを作成するには承認済みのアプリまたはアプリ保護ポリシーが必要です
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/08/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo, spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 071c1e3ae0a2cd1eb352103839b9c32e39902985
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725241"
---
# <a name="conditional-access-require-approved-client-apps-or-app-protection-policy"></a>条件付きアクセス: 承認済みのクライアント アプリまたはアプリ保護ポリシーが必要です

人々は、個人的な作業と業務上の作業のどちらにもモバイル デバイスを日常的に使用します。 組織は、スタッフの生産性を確保する一方で、完全には管理できないデバイス上のアプリケーションによるデータ損失を防止する必要があります。 

条件付きアクセスを使用すると、組織は、Intune アプリ保護ポリシーを備えた[承認済み (先進認証対応) クライアント アプリ](concept-conditional-access-grant.md#require-app-protection-policy)のみにアクセスを制限できます。 アプリ保護ポリシーをサポートしていない場合がある古いクライアント アプリの場合、管理者は[承認されたクライアント アプリ](concept-conditional-access-grant.md#require-approved-client-app)のみにアクセスを制限できます。

> [!WARNING]
> アプリ保護ポリシーは、iOS と Android でのみサポートされています。
>
> 一部のアプリケーションは、承認済みアプリケーションとしてサポートされないか、アプリケーション保護ポリシーをサポートしません。 いくつかの共通クライアント アプリの一覧については、[アプリ保護ポリシーの要件](concept-conditional-access-grant.md#require-app-protection-policy)に関するページを参照してください。 ご使用のアプリケーションがリストされていない場合は、アプリケーション開発者にお問い合わせください。
> 
> iOS および Android デバイス用の承認済みクライアント アプリを要求するには、まずそれらのデバイスを Azure AD に登録する必要があります。

> [!NOTE]
> 付与の制御の [選択したコントロールのいずれかが必要] は、 OR 句と似ています。 これは、ポリシー内で使用され、ユーザーが **[アプリの保護ポリシーが必要]** または **[承認済みクライアント アプリを必須にする]** のいずれかの付与の制御をサポートするアプリを利用できるようにします。 アプリでその付与の制御がサポートされている場合は、 **[アプリの保護ポリシーが必要]** が適用されます。

アプリ保護ポリシーを使用するメリットについて詳しくは、「[アプリ保護ポリシーの概要](/mem/intune/apps/app-protection-policy)」を参照してください。

## <a name="template-deployment"></a>テンプレートのデプロイ

組織は、このポリシーをデプロイするのに以下に示す手順を使用するか、[条件付きアクセス テンプレート (プレビュー) ](concept-conditional-access-policy-common.md#conditional-access-templates-preview)を使用するかを選ぶことができます。 

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順を実行すると、承認済みのクライアント アプリ **または** アプリ保護ポリシーを必要とする条件付きアクセス ポリシーを作成できます。 このポリシーは、 [Microsoft Intune で作成されたアプリ保護ポリシー](/mem/intune/apps/app-protection-policies)と連携して機能します。

このポリシーは[レポート専用モード](howto-conditional-access-insights-reporting.md)となり、管理者が既存のユーザーに与える影響を判断できるようになります。 ポリシーが意図したとおりに適用されると管理者が判断した場合は、**オン** に切り替えたり、特定のグループを追加し他のグループを除外することでデプロイをステージングしたりすることができます。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で **[ユーザーとグループ]** を選択し、少なくとも 1 つのアカウントを除外して、自身がロックアウトされないようにします。アカウントを除外しない場合、ポリシーを作成することはできません。
   1. **[Done]** を選択します。
1. **[クラウド アプリまたはアクション]** で、 **[すべてのクラウド アプリ]** を選択します。
   1. **[Done]** を選択します。
1. **[条件]**  >  **[デバイス プラットフォーム]** で、 **[構成]** を **[はい]** に設定します。
   1. **[Include]\(含める\)** で、 **[デバイス プラットフォーム] を選択します**。
   1. **Android** と **iOS** を選択します
   1. **[Done]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** を選択します。
   1. **[承認済みのクライアント アプリ]** および **[アプリ保護ポリシーを必須にする]** を選択します
   1. **複数のコントロールの場合**、 **[選択したコントロールのいずれかが必要]** を選択します
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

## <a name="next-steps"></a>次のステップ

[アプリ保護ポリシーの概要](/intune/apps/app-protection-policy)

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
