---
title: Azure Active Directory でログをダウンロードする方法 | Microsoft Docs
description: Azure Active Directory でアクティビティ ログをダウンロードする方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/14/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbf95fe288bb81ac8a1580e56244bea11e52821
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995069"
---
# <a name="how-to-download-logs-in-azure-active-directory"></a>方法: Azure Active Directory でログをダウンロードする

Azure Active Directory (Azure AD) ポータルを使用すると、次の 3 種類のアクティビティ ログにアクセスできます。

- **[サインイン](concept-sign-ins.md)** - サインインとユーザーのリソース使用状況に関する情報。
- **[監査](concept-audit-logs.md)** - ユーザーやグループの管理、テナントのリソースに適用された更新など、テナントに適用された変更に関する情報。
- **[プロビジョニング](concept-provisioning-logs.md)** - ServiceNow でのグループの作成や、Workday からインポートされたユーザーなど、プロビジョニング サービスによって実行されるアクティビティ。

Azure AD では、これらのログに限られた時間の間データが格納されます。 IT 管理者は、長期的にバックアップできるよう、アクティビティ ログをダウンロードすることができます。

この記事では、Azure AD でアクティビティ ログをダウンロードする方法について説明します。  

## <a name="what-you-should-know"></a>知っておくべきこと

- Azure AD ポータルからアクティビティ ログにアクセスする方法はいくつかあります。 たとえば、[[ユーザー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) または [[グループ]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) ページの **[アクティビティ]** セクションなどです。 ただし、初期のフィルター処理されていないログのビューにアクセスできるのは、[[Azure AD]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ページの **[監視]** セクション 1 箇所だけです。    

- Azure AD では、特定の期間だけアクティビティ ログが保存されます。 詳細については、「[Azure AD にレポート データが保存される期間](reference-reports-data-retention.md)」を参照してください。 

- ログをダウンロードすれば、ログの保存期間を制御できます。 

- 最大 250,000 個のレコードをダウンロードすることができます。 さらに多くのデータをダウンロードする場合は、レポート API を使用します。

- ダウンロードは、設定したフィルターに基づいて行われます。 

- Azure AD では、次の形式でのダウンロードがサポートされています。

    - **CSV** 

    - **JSON** 

- ダウンロードしたファイルのタイムスタンプは、常に UTC に基づいています。



## <a name="what-license-do-you-need"></a>必要なライセンス

アクティビティ ログのデータをダウンロードするオプションは、Azure AD のすべてのエディションで使用できます。

Microsoft Graph を使用してアクティビティ ログをダウンロードすることもできます。ただし、ログをプログラムでダウンロードするには、Premium ライセンスが必要です。


## <a name="who-can-do-it"></a>実行できるユーザー

全体管理者でも可能ですが、このタスクを実行する際は権限が低いアカウントを使用するようにしてください。 監査ログにアクセスするには、次のいずれかのロールが必要です。

- レポート閲覧者
- グローバル閲覧者
- セキュリティ管理者
- セキュリティ閲覧者


## <a name="how-to-do-it"></a>方法


**アクティビティ ログをダウンロードするには:**

1. 必要なアクティビティ ログ ビューに移動します。
 
    - [サインイン ログ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)
    
    - [監査ログ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)    
       
    - [プロビジョニング ログ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)    
   

2.  必要なフィルターを **追加** します。  

    ![[フィルターの追加]](./media/\howto-download-logs/add-filter.png)    

3. データを **ダウンロード** します。

    ![ログのダウンロード](./media/\howto-download-logs/download-log.png)

## <a name="next-steps"></a>次のステップ

- [Azure AD のサインイン ログ](concept-sign-ins.md)
- [Azure AD の監査ログ](concept-audit-logs.md)