---
title: Azure Active Directory ポータルのサインイン アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのサインイン アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 5607dd0f3c682915f1a64edcc5e946491bab5279
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653358"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **監査ログ** - [監査ログ](concept-audit-logs.md)は、ユーザーとグループの管理や、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提供します。
- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

このトピックでは、サインイン レポートの概要を説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者、セキュリティ閲覧者、レポート閲覧者ロールのユーザー
* グローバル管理者
* さらに、任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。
* すべてのサインイン アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="sign-ins-report"></a>サインイン レポート

ユーザーのサインイン レポートは、次の質問に対する回答を提示します。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがサインインを行ったか。
* これらのサインインはどのような状態か。

[Azure portal](https://portal.azure.com) の **[Azure Active Directory]** ブレードの **[アクティビティ]** セクションで **[サインイン]** を選択して、サインイン レポートにアクセスできます。

![サインイン アクティビティ](./media/concept-sign-ins/61.png "サインイン アクティビティ")

> [!IMPORTANT]
> サインイン レポートには、**対話型**のサインイン、つまりユーザーがユーザー名とパスワードを使用して手動で行うサインインのみが表示されます。 サービス間の認証のような対話型ではないサインインは、サインイン レポートに表示されません。 

サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態

![サインイン アクティビティ](./media/concept-sign-ins/01.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![サインイン アクティビティ](./media/concept-sign-ins/19.png "サインイン アクティビティ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![サインイン アクティビティ](./media/concept-sign-ins/02.png "サインイン アクティビティ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![サインイン アクティビティ](./media/concept-sign-ins/03.png "サインイン アクティビティ")

> [!NOTE]
> すべてのサインイン レポートで、条件付きアクセス ポリシーのトラブルシューティングを実行できるようになりました。 サインイン レコードの **[条件付きアクセス]** タブをクリックして、条件付きアクセスの状態を確認し、サインインに適用されたポリシーの詳細と各ポリシーの結果を調べることができます。
> 詳細については、「[Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access)」 (すべてのサインインの CA 情報に関してよく寄せられる質問) を参照してください。

![サインイン アクティビティ](./media/concept-sign-ins/ConditionalAccess.png "サインイン アクティビティ")


## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用してサインイン データをフィルター処理できます。

- User
- アプリケーション
- サインインの状態
- 条件付きアクセス
- 日付

![サインイン アクティビティ](./media/concept-sign-ins/04.png "サインイン アクティビティ")

**[ユーザー]** フィルターでは、確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) を指定できます。

**[アプリケーション]** フィルターでは、確認したいアプリケーションの名前を指定できます。

**[サインイン状態]** フィルターでは、次のいずれかを選択できます。

- All
- Success
- 失敗

**条件付きアクセス** フィルターでは、次に示すサインインの CA ポリシーの状態を選択できます。

- All
- 未適用
- Success
- 失敗

**[日付]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

サインイン ビューにフィールドを追加すると、これらのフィールドがフィルターの一覧に自動的に追加されます。 たとえば、**[クライアント アプリ]** フィールドを一覧に追加した場合、次のフィルターを設定できるもう 1 つのフィルター オプションが表示されます。

- ブラウザー      
- Exchange ActiveSync (supported) (Exchange ActiveSync (サポート対象))               
- Exchange ActiveSync (unsupported) (Exchange ActiveSync (サポート外))
- その他のクライアント               
    - IMAP
    - MAPI
    - 以前のバージョンの Office クライアント
    - POP
    - SMTP


![サインイン アクティビティ](./media/concept-sign-ins/12.png "サインイン アクティビティ")


## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

Azure portal の外部で操作する場合は、[サインイン データをダウンロード](quickstart-download-sign-in-report.md)できます。 **[ダウンロード]** をクリックすると、最新の 5K レコードの CSV ファイルが作成されます。  Azure portal では、ダウンロード ボタンの他に、[データをダウンロードするためのスクリプトを生成する](tutorial-signin-logs-download-script.md)オプションも提供されています。  

![ダウンロード](./media/concept-sign-ins/71.png "ダウンロード")

さらに柔軟性が必要である場合は、スクリプト ソリューションを使用することができます。 **[スクリプト]** をクリックすると、設定したすべてのフィルターを含む PowerShell が作成されます。 **管理者モード**でこのスクリプトをダウンロードして実行し、CSV ファイルを生成します。 

> [!IMPORTANT]
> ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。  

### <a name="running-the-script-on-a-windows-10-machine"></a>Windows 10 マシン上でスクリプトを実行する

**Windows 10** マシン上でスクリプトを実行する場合は、最初にいくつかの手順を追加で実行する必要があります。 

1. [AzureRM モジュール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l)をインストールします。
2. PowerShell プロンプトを開き、コマンド **Import-Module AzureRM** を実行して、モジュールをインポートします。
3. **Set-ExecutionPolicy unrestricted** を実行し、**[Yes to All]\(すべてはい\)** を選択します。 
4. 以上で、ダウンロードした PowerShell スクリプトを管理者モードで実行して、CSV ファイルを生成できます。

## <a name="sign-ins-data-shortcuts"></a>サインイン データのショートカット

Azure AD の他にも、Azure portal にはサインイン データに対する追加のエントリ ポイントが用意されています。

- ID のセキュリティ保護の概要
- ユーザー
- グループ
- エンタープライズ アプリケーション

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID セキュリティ保護のユーザーのサインイン データ

**ID セキュリティ保護**のユーザー サインイン グラフは、特定期間内のすべてのユーザーのサインインについて、週単位の集計を示します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/06.png "サインイン アクティビティ")

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

![サインイン アクティビティ](./media/concept-sign-ins/08.png "サインイン アクティビティ")

## <a name="usage-of-managed-applications"></a>マネージド アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最近ロールアウトしたアプリケーションは、 どのような状況か。

このデータへのエントリ ポイントは、**[エンタープライズ アプリケーション]** の **[概要]** セクションにある過去 30 日間のレポートに示される、組織内の上位 3 つのアプリケーションです。

![サインイン アクティビティ](./media/concept-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリへのサインインを週ごとに集計します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/47.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。

![レポート](./media/concept-sign-ins/single_spp_usage_graph.png "レポート")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。

**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

![サインイン アクティビティ](./media/concept-sign-ins/11.png "サインイン アクティビティ")

## <a name="next-steps"></a>次の手順

* [サインイン アクティビティ レポートのエラー コード](reference-sign-ins-error-codes.md)
* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)

