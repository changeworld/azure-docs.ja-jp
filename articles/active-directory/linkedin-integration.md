---
title: "Azure Active Directory で Office アプリケーションの LinkedIn の統合を有効または無効にする | Microsoft Docs"
description: "Azure Active Directory で Microsoft アプリの LinkedIn の統合を有効または無効にする方法について説明します。"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Office アプリケーションの LinkedIn の統合
この記事では、LinkedIn の統合機能が利用できるユーザーを Azure Active Directory (Azure AD) で制限する方法について説明します。 LinkedIn の統合は、テナントに追加されたときに既定で有効になるため、ユーザーは一部の Microsoft アプリからパブリックな LinkedIn データにアクセスすることができます。 それぞれのユーザーが個別に、その職場または学校アカウントを LinkedIn アカウントに接続することもできます。

> [!IMPORTANT]
> LinkedIn の統合は、すべての Azure AD テナントに対して同時にデプロイされるわけではありません。 Azure テナントへのロールアウト後に、LinkedIn の統合が既定で有効になります。 LinkedIn の統合は、go-local、Sovereign、および政府機関の各テナントでは使用できません。 ロールアウト情報の最新にビューについては、「[Office 365 ロードマップ](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)」ページを参照してください。

## <a name="linkedin-integration-from-the-user-perspective"></a>ユーザーの視点から見た LinkedIn の統合
組織内のユーザーは、その LinkedIn アカウントを職場または学校アカウントに接続するとき、その組織が用意している Microsoft のアプリやサービスで使用される[データの提供を LinkedIn に許可](https://www.linkedin.com/help/linkedin/answer/84077)することになります。 [ユーザーはアカウントを切断](https://www.linkedin.com/help/linkedin/answer/85097)することができ、その場合は、LinkedIn が Microsoft とデータを共有するために必要なアクセス許可が削除されます。 LinkedIn の統合では、公開されている LinkedIn プロフィールの情報が使用されます。 LinkedIn のプライバシー設定を使用することで、ユーザーはそのプロフィールを Microsoft のアプリで閲覧できるかどうかなど、[自分の LinkedIn プロフィールの公開方法を制御](https://www.linkedin.com/help/linkedin/answer/83)することができます。

## <a name="privacy-considerations"></a>プライバシーに関する考慮事項
Azure AD で LinkedIn の統合を有効にすると、Microsoft のアプリとサービスはユーザーの LinkedIn 情報の一部にアクセスできるようになります。 Azure AD で LinkedIn の統合を有効にする場合のプライバシーの考慮事項については、「[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement/)」を参照してください。 

## <a name="manage-linkedin-integration"></a>LinkedIn の統合を管理する
Azure AD の既定では、企業の LinkedIn の統合は有効です。 LinkedIn の統合を有効にすると、組織内のすべてのユーザーは、Outlook で LinkedIn のプロフィールを表示するなど、Microsoft サービス内で LinkedIn 機能を使用できるようになります。 LinkedIn の統合を無効にすると、Microsoft のアプリやサービスから LinkedIn の機能が削除され、Microsoft のサービスを介して LinkedIn と組織との間で実現されていたデータの共有が中止されます。

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>組織に対する LinkedIn の統合を Azure Portal で有効または無効にする

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com/)に、Azure AD テナントの全体管理者のアカウントでサインインします。
2. **[ユーザー]** を選択します。
3. **[ユーザー]** ウィンドウで、**[ユーザー設定]** を選択します。
4. **[LinkedIn の統合]** で **[はい]** を選択すると LinkedIn が有効に、**[いいえ]** を選択すると無効になります。
   ![LinkedIn の統合を有効にする](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>組織の Office 2016 アプリに対する LinkedIn の統合をグループ ポリシーで有効または無効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
2. **ADMX** ファイルを抽出して**中央のリポジトリ**にコピーします。
3. [グループ ポリシーの管理] を開きます。
4. **[ユーザーの構成]** > **[管理用テンプレート]** > **[Microsoft Office 2016]** > **[その他]** > **[Allow LinkedIn Integration]\(LinkedIn の統合を許可する\)** の設定でグループ ポリシー オブジェクトを作成します。
5. **[有効]** または **[無効]** を選択します。
  * このポリシーを **[有効]** にすると、Office 2016 の [オプション] ダイアログにある **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** 設定が有効になります。 これは同時に、組織内のユーザーが各自の Office アプリケーションで LinkedIn の機能を使用できることを意味します。
  * このポリシーを **[無効]** にすると、Office 2016 の [オプション] ダイアログにある **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** 設定が無効の状態になります。エンド ユーザーがこの設定を変更することはできません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。 

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Office 365 全体でプロフィール カードに LinkedIn の機能が表示されます。 

### <a name="learn-more"></a>詳細情報 
* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>次の手順
次のリンクをクリックすると、現在の LinkedIn の統合設定を Azure Portal で確認できます。

[LinkedIn の統合を構成する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 