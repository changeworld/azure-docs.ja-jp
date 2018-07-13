---
title: Azure Active Directory で Microsoft のアプリとサービスに対する LinkedIn 接続を有効にする | Microsoft Docs
description: Azure Active Directory で Microsoft のアプリに対する LinkedIn アカウント接続を有効または無効にする方法について説明します
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871336"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Microsoft のアプリとサービスに対する LinkedIn アカウント接続
この記事では、Azure Active Directory (Azure AD) 管理センターで、テナントに対する LinkedIn アカウント接続を管理する方法を説明します。 

> [!IMPORTANT]
> LinkedIn アカウント接続機能は、現在 Azure AD テナントにロールアウト中です。 テナントにロールアウトされると、既定で有効になります。 米国政府機関向けの顧客、およびオーストラリア、カナダ、中国、フランス、ドイツ、インド、韓国、イギリス、日本、および南アフリカでホストされている Exchange Online メールボックスを使用する組織は使用できません。 これらのメールボックスの場所に対するサポートは、近日対応予定です。  ロールアウト情報の最新にビューについては、「[Office 365 ロードマップ](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)」ページを参照してください。

## <a name="benefit-to-users"></a>ユーザーにとっての利点
ユーザーが自分の LinkedIn アカウントに接続すると、さまざまな Microsoft アプリまたはサービスで個人用に設定された情報と機能を表示するために LinkedIn 情報が使用されます。 ユーザーは、Microsoft プロファイル カードで、一緒に作業をしている人が組織外の人であっても、その人に関する詳細情報を確認できます。 時間の経過と共に、LinkedIn エクスペリエンスはユーザーの作業により関連性が高く、カスタマイズされるようになります。 たとえば、LinkedIn は、一緒に作業している人に基づいて、新しいつながりを提案したり、その日のその人の予定表でその人に関する詳細情報を明らかにすることができます。

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>ユーザーへの LinkedIn アカウント接続の表示方法
LinkedIn アカウント接続では、ユーザーは、一部の Microsoft のアプリの中で、パブリックな LinkedIn プロファイル情報を表示できます。 テナントのユーザーは、自分の LinkedIn と 職場または学校の Microsoft アカウントに接続して、追加の LinkedIn プロファイル情報を表示することを選択できます。 詳細については、「[LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740)」(Microsoft のアプリとサービスにおける LinkedIn 情報と機能) を参照してください。

組織のユーザーが、自分の LinkedIn アカウントと職場または学校の Microsoft アカウントに接続する際は、2 つのオプションがあります。 
* 両方のアカウント間でデータを共有する許可を与える。 これは、LinkedIn アカウントに職場または学校の Microsoft アカウントとデータを共有する許可を与え、職場または学校の Microsoft アカウントに LinkedIn アカウントとデータを共有する許可を与えることを意味します。 LinkedIn と共有されるデータは、オンライン サービスから離れます。 
* データ共有の許可を、LinkedIn アカウントから職場または学校の Microsoft アカウントにのみ与える。

ユーザーの LinkedIn と職場または学校の Microsoft アカウント間で共有されるデータの詳細については、「[LinkedIn in Microsoft applications at your work or school](https://www.linkedin.com/help/linkedin/answer/84077)」(職場または学校での Microsoft アプリケーションにおける LinkedIn) を参照してください。 
* いつでも[ユーザーは、アカウントとの接続を切断](https://www.linkedin.com/help/linkedin/answer/85097)し、データ共有許可を削除できます。 
* ユーザーは各自の LinkedIn プロフィールを Microsoft のアプリで閲覧できるかどうかなど、[自分の LinkedIn プロフィールの公開方法を制御](https://www.linkedin.com/help/linkedin/answer/83)することができます。

## <a name="privacy-considerations"></a>プライバシーに関する考慮事項
LinkedIn アカウント接続を有効にすると、Microsoft のアプリとサービスは、ユーザーの LinkedIn 情報の一部にアクセスできるようになります。 Azure AD で LinkedIn アカウント接続を有効にする場合のプライバシーの考慮事項については、「[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement/)」を参照してください。 

## <a name="manage-linkedin-account-connections"></a>LinkedIn アカウント接続を管理する
LinkedIn アカウント接続機能は、既定ではテナント全体で有効になっています。 テナント全体で LinkedIn アカウント接続を無効にするか、テナント内の選択したユーザーに対する LinkedIn アカウント接続を有効にできます。 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Azure Portal でテナントに対する LinkedIn アカウント接続を有効または無効にする

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com/)に、Azure AD テナントの全体管理者のアカウントでサインインします。
2. **[ユーザー]** を選択します。
3. **[ユーザー]** ウィンドウで、**[ユーザー設定]** を選択します。
4. **[LinkedIn アカウント接続]** で次の操作を行います。
  * テナント内のすべてのユーザーに対する LinkedIn アカウント接続を有効にするには、**[はい]** を選択します。
  * テナントの選択したユーザーのみに対する LinkedIn アカウント接続を有効にするには、**[選択済み]** を選択します。
  * すべてのユーザーの LinkedIn アカウント接続を無効にするようには、**[いいえ]** を選択します。![LinkedIn アカウント接続の有効化](./media/linkedin-integration/linkedin-integration.png)
5. **[保存]** を選択して完了し、設定を保存する。

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>組織の Office 2016 アプリに対する LinkedIn アカウント接続をグループ ポリシーで有効または無効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
2. **ADMX** ファイルを抽出して中央のストアにコピーします。
3. [グループ ポリシーの管理] を開きます。
4. **[ユーザーの構成]** > **[管理用テンプレート]** > **[Microsoft Office 2016]** > **[その他]** > **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** の設定でグループ ポリシー オブジェクトを作成します。
5. **[有効]** または **[無効]** を選択します。
  * このポリシーを **[有効]** にすると、Office 2016 の [オプション] ダイアログにある **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** 設定が有効になります。 これは同時に、組織内のユーザーが各自の Office アプリケーションで LinkedIn の機能を使用できることを意味します。
  * このポリシーを **[無効]** にすると、Office 2016 の [オプション] ダイアログにある **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** 設定が無効の状態になります。エンド ユーザーがこの設定を変更することはできません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Office 365 全体でプロフィール カードに LinkedIn の機能が表示されます。 

### <a name="learn-more"></a>詳細情報 
* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>次の手順
現在の LinkedIn アカウント接続設定を Azure Portal で確認するには、次のリンクをクリックします。

[LinkedIn アカウント接続を管理する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 