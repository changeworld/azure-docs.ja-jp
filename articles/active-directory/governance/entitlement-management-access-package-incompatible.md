---
title: Azure AD エンタイトルメント管理でアクセス パッケージの職務の分離を構成する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理で、アクセス パッケージの要求に対して職務の分離の適用を構成する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730172"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理でアクセス パッケージに対する職務の分離の確認を構成する (プレビュー)

Azure AD エンタイトルメント管理では、アクセス パッケージを使用して、アクセスする必要があるユーザー コミュニティごとに異なる設定を指定して複数のポリシーを構成できます。  たとえば、従業員は特定のアプリへのアクセスを取得するためにマネージャーの承認のみを必要とする場合がありますが、他の組織から来るゲストには、スポンサーと、リソース チームの部門マネージャーの両方の承認が必要な場合があります。 ディレクトリに既に存在するユーザーのポリシーでは、アクセスを要求できる特定のユーザー グループを指定できます。 ただし、ユーザーが過剰なアクセスを取得しないようにするための要件がある場合があります。  この要件を満たすには、要求元が既に持っているアクセスに基づいて、アクセスを要求できるユーザーをさらに制限する必要があります。

アクセス パッケージで職務の分離の設定を行うことで、あるグループのメンバーであるユーザーや、既に 1 つのアクセス パッケージへの割り当てが行われているユーザーが、追加のアクセス パッケージを要求できないように構成できます。

![互換性のないアクセスを要求しようとしているアクセス操作](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>職務の分離を確認する場合のシナリオ

たとえば、マーケティング キャンペーンの実施中は、組織全体のユーザーと他の組織のユーザーが組織のマーケティング部門へのアクセスを要求して共同作業することができる、*Marketing Campaign* というアクセス パッケージがあるとします。 マーケティング部門の従業員は既にそのマーケティング キャンペーン資料へのアクセスを持っているはずなので、マーケティング部門の従業員が、そのアクセス パッケージへのアクセスを要求する必要はありません。  または、マーケティング部門の従業員全員が含まれる動的なグループ *Marketing department employees* が既に存在しているかもしれません。 アクセス パッケージが、その動的グループのメンバーシップと互換性がないことを指定できます。 その後は、マーケティング部門の従業員は要求するアクセス パッケージを探していても、*Marketing campaign* アクセス パッケージへのアクセスを要求できません。

同様に、**Western Sales** と **Eastern Sales** という 2 つのロールを持つアプリケーションがあるとして、ユーザーが一度に 1 つの販売区域しか担当できないようにしたい場合があります。  2 つのアクセス パッケージを用意し、1 つのアクセス パッケージ **Western Territory** では **Western Sales** ロールを付与し、別のアクセス パッケージ **Eastern Territory** では **Eastern Sales** ロールを付与すると、以下の構成が可能です
 - **Western Territory** アクセス パッケージには **Eastern Territory** パッケージとは互換性がないと指定し、
 - **Eastern Territory** アクセス パッケージには **Western Territory** パッケージとは互換性がないと指定します。

オンプレミス アプリへのアクセスを自動化するために、Microsoft Identity Manager や、その他のオンプレミス ID 管理システムを使用してきた場合は、それらのシステムを Azure AD エンタイトルメント管理に統合することもできます。  Azure AD に統合されたアプリへのアクセスを、エンタイトルメント管理を通じて制御しようとしている場合は、ユーザーが互換性のないアクセスを持たないようにする必要があり、アクセス パッケージに特定のグループとの互換性がないように構成できます。 それを、オンプレミスの ID 管理システムから Azure AD Connect 経由で Azure AD に送信されるグループとすることができます。 このチェックにより、そのアクセス パッケージで、ユーザーがオンプレミス アプリで持っているアクセスと互換性のないアクセスが付与される場合は、ユーザーがアクセス パッケージを要求できなくなるようにできます。

## <a name="prerequisites"></a>前提条件

Azure AD エンタイトルメント管理を使用し、ユーザーをアクセス パッケージに割り当てるには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>アクセス パッケージへのアクセスを要求する別のアクセス パッケージまたはグループ メンバーシップには互換性がないと構成する

**必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

以下の手順に従って、既存のアクセス パッケージとは互換性のないグループまたはその他のアクセス パッケージの一覧を変更します。

1.  [Azure portal](https://portal.azure.com) にサインインします。

1.  **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1.  左側のメニューで **[アクセス パッケージ]** をクリックしてから、ユーザーが要求することになるアクセス パッケージを開きます。

1.  左側のメニューで、 **[職務の分離 (プレビュー)]** をクリックします。

1.  既に別のアクセス パッケージの割り当てを持っているユーザーが、このアクセス パッケージを要求しないようにする場合は、 **[アクセス パッケージの追加]** をクリックし、そのユーザーに既に割り当てられているアクセス パッケージを選択します。


    ![互換性のないアクセス パッケージの構成](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  既存のグループ メンバーシップを持っているユーザーが、このアクセス パッケージを要求しないようにする場合は、 **[グループの追加]** をクリックし、そのユーザーが既に所属しているグループを選択します。

### <a name="configure-incompatible-access-packages-programmatically"></a>互換性のないアクセス パッケージをプログラムで構成する

Microsoft Graph を使用して、アクセス パッケージと互換性のないグループおよびその他のアクセス パッケージを構成することもできます。  アプリケーションで、委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持っている適切なロールのユーザー、またはそのアプリケーション アクセス許可を持っているアプリケーションは、API を呼び出して、[あるアクセス パッケージとは](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)互換性のないグループやアクセス パッケージを追加、削除、一覧表示することができます。


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>このアクセス パッケージとは互換性がないと構成されている他のアクセス パッケージを表示する

**必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

既存のアクセス パッケージとは互換性がないことが示されたその他のアクセス パッケージの一覧を表示するには、次の手順に従います。

1.  [Azure portal](https://portal.azure.com) にサインインします。

1.  **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1.  左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1.  左側のメニューで、 **[職務の分離 (プレビュー)]** をクリックします。

1. **[互換性なし]** をクリックします。

## <a name="monitor-and-report-on-access-assignments"></a>アクセスの割り当てを監視してレポートを表示する

Azure Monitor ブックを使用して、ユーザーがどのようにアクセスを取得してきたかに関する分析情報を得ることができます。

1. [Azure Monitor に監査イベントを送信する](entitlement-management-logs-and-reporting.md)ように Azure AD を構成します。

1. *Access Package Activity* という名前のブックには、特定のアクセス パッケージに関連する各イベントが表示されます。

    ![アクセス パッケージ イベントを表示する](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. あるアプリケーションについて、アクセス パッケージの割り当てが理由で作成されたのではないアプリケーション ロールの割り当に対して変更があったかどうかを確認するには、*Application role assignment activity* という名前のブックを選択します。  エンタイトルメント アクティビティを省略することを選択した場合は、エンタイトルメント管理によって作成されたのではないアプリケーション ロールに対する変更のみが表示されます。 たとえば、全体管理者がユーザーを特定のアプリケーション ロールに直接割り当てた場合は、行が表示されます。

    ![アプリ ロールの割り当てを表示する](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>次のステップ

- [アクセス パッケージの割り当てを表示、追加、および削除する](entitlement-management-access-package-assignments.md)
- [レポートとログを表示する](entitlement-management-reports.md)
