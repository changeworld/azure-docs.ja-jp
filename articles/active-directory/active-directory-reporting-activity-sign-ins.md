---
title: Azure Active Directory ポータルのサインイン アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのサインイン アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 78d3399aab8e3c3b1d98946cb3ac6ffab353d95c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257715"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

環境の動作状況を判断するために必要な情報は、[Azure Portal](https://portal.azure.com) の Azure Active Directory (Azure AD) レポートで入手できます。

Azure Active Directory のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン アクティビティ** – マネージ アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報
    - **監査ログ** - ユーザーとグループの管理、マネージ アプリケーション、およびディレクトリのアクティビティに関するシステム アクティビティ情報です。
- **セキュリティ** 
    - **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 詳細については、「Risky sign-ins (リスクの高いサインイン)」を参照してください。
    - **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 詳細については、「Users flagged for risk (リスクのフラグ付きユーザー)」を参照してください。

このトピックでは、サインイン アクティビティの概要を説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者、セキュリティ閲覧者、またはレポート閲覧者ロールのユーザー
* グローバル管理者
* 任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。
* すべてのサインイン アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。


## <a name="sign-in-activities"></a>サインイン アクティビティ

ユーザー サインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがサインインを行ったか。
* これらのサインインはどのような状態か。

すべてのサインイン アクティビティ データへの最初のエントリ ポイントは、**[Azure Active Directory]** の [アクティビティ] セクションの **[サインイン]** です


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/61.png "サインイン アクティビティ")


サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態 

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/01.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/19.png "サインイン アクティビティ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/02.png "サインイン アクティビティ")

リスト ビュー内の項目をクリックすると、その項目に関する確認可能な詳細が横表示で表示されます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/03.png "サインイン アクティビティ")


## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用してサインイン データをフィルター処理できます。

- User
- アプリケーション
- サインインの状態
- リスク検出の状態
- 日付


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/04.png "サインイン アクティビティ")

**[ユーザー]** フィルターでは、確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) を指定できます。 

**[アプリケーション]** フィルターでは、確認したいアプリケーションの名前を指定できます。  

**[サインイン状態]** フィルターでは、次のいずれかを選択できます。

- すべて 
- 成功
- 失敗

**[リスクが検出されました]** フィルターでは、次のいずれかを選択できます。

- すべて
- [はい]
- いいえ  


**[日付]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

サインイン ビューにフィールドを追加すると、これらのフィールドがフィルターの一覧に自動的に追加されます。 たとえば、**[クライアント アプリ]** フィールドを一覧に追加した場合、次のフィルターを設定できるもう 1 つのフィルター オプションが表示されます。

- [ブラウザー] ボタンを      
- Exchange ActiveSync (supported) (Exchange ActiveSync (サポート対象))               
- Exchange ActiveSync (unsupported) (Exchange ActiveSync (サポート外))
- その他のクライアント               
    - IMAP
    - MAPI
    - 以前のバージョンの Office クライアント
    - POP
    - SMTP


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/12.png "サインイン アクティビティ")


> [!TIP] 
> 既定のフィルターの他に、サインイン ビューに追加するすべてのフィールドが、フィルター フィールドになります。


## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

サインイン アクティビティ データを Azure portal の外部で操作する場合は、ダウンロードすることができます。 Azure portal では、ダウンロード ボタンの他に、データをダウンロードするためのスクリプトを生成するオプションも提供されています。  

![ダウンロード](./media/active-directory-reporting-activity-sign-ins/71.png "ダウンロード")

**[ダウンロード]** をクリックすると、最新の 5K レコードの CSV ファイルが作成されます。 さらに柔軟性が必要である場合は、スクリプト ソリューションを使用することができます。 **[スクリプト]** をクリックすると、設定したすべてのフィルターを含むスクリプトが作成されます。 ダウンロードできるレコードの数は、技術的な実装だけでなく、[Azure Active Directory レポートの保持ポリシー](active-directory-reporting-retention.md)によっても制限されます。  



## <a name="sign-in-activities-shortcuts"></a>サインイン アクティビティのショートカット

[Azure Active Directory] の他にも、Azure Portal にはサインイン アクティビティ データに対するエントリ ポイントが 2 つ用意されています。

- ID のセキュリティ保護の概要
- ユーザー
- グループ
- エンタープライズ アプリケーション


### <a name="users-sign-ins-activities"></a>ユーザーのサインイン アクティビティ

ユーザー サインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

- ユーザーのサインインにどのようなパターンがあるか。
- 1 週間で何人のユーザーがユーザー サインインを行ったか。
- これらのサインインはどのような状態か。



このデータへのエントリ ポイントは、**ID のセキュリティ保護**の概要ページにあるユーザー サインイン グラフです。 ユーザー サインイン グラフは、特定期間内のすべてのユーザーのサインインについて、週単位の集計を示します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/06.png "サインイン アクティビティ")

サインイン グラフ内の日付をクリックすると、その日のサインイン アクティビティの概要が表示されます。


サインイン アクティビティ リストの各行には、次の情報が表示されます。

* サインインしたのはだれか。
* サインインの対象となったのはどのアプリケーションか。
* サインインの状態はどのような状態か。
* サインインの MFA 状態はどのような状態か。

項目をクリックすると、サインイン操作の詳細が表示されます。

- ユーザー ID
- User
- ユーザー名
- アプリケーション ID
- アプリケーション
- クライアント
- 場所
- IP アドレス
- 日付
- MFA が必要
- サインインの状態

 
**[ユーザー]** ページの **[アクティビティ]** セクションの **[サインイン]** をクリックすると、すべてのユーザー サインインの完全な概要が表示されます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/08.png "サインイン アクティビティ")




## <a name="usage-of-managed-applications"></a>マネージ アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最近ロールアウトしたアプリケーションは、 どのような状況か。

このデータへのエントリ ポイントは、**[エンタープライズ アプリケーション]** ページの **[概要]** セクションにある、"*過去 30 日間における組織内の上位 3 つのアプリケーション*" レポートです。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリへのサインインを週ごとに集計します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/47.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。


![レポート](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "レポート")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。




**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/11.png "サインイン アクティビティ")



## <a name="next-steps"></a>次の手順

サインイン アクティビティのエラー コードの詳細については、「[Azure Active Directory ポータルのサインイン アクティビティ レポートのエラー コード](active-directory-reporting-activity-sign-ins-errors.md)」を参照してください。

