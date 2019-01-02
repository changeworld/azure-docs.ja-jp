---
title: チュートリアル:Workday を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Workday に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849855"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>チュートリアル:Workday を構成し、自動ユーザー プロビジョニングに対応させる (プレビュー)

このチュートリアルでは、Workday から Active Directory と Azure Active Directory の両方に社員プロファイルをインポートするために必要な手順と、Workday にメール アドレスを書き戻すオプションについて説明します。

## <a name="overview"></a>概要

ユーザー アカウントをプロビジョニングするために、[Azure Active Directory ユーザー プロビジョニング サービス](../manage-apps/user-provisioning.md)を [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) と統合します。 Azure AD はこの接続を使用して、次のユーザー プロビジョニング ワークフローを有効にします。

* **Active Directory へのユーザーのプロビジョニング** - Workday から選択したユーザーのセットを、1 つ以上の Active Directory ドメインに同期します。

* **Azure Active Directory へのクラウドのみのユーザーのプロビジョニング** - オンプレミスの Active Directory を使用しないシナリオでは、Azure AD ユーザー プロビジョニング サービスを使用して、ユーザーを Workday から Azure Active Directory に直接プロビジョニングできます。 

* **Workday へのメール アドレスの書き戻し** - Azure AD ユーザー プロビジョニング サービスでは、Azure AD ユーザーのメール アドレスを Workday に書き戻すことができます。 

### <a name="what-human-resources-scenarios-does-it-cover"></a>対象になる人事管理シナリオ

Azure AD のユーザー プロビジョニング サービスでサポートされている Workday ユーザー プロビジョニング ワークフローは、次の人事管理および ID ライフサイクル管理シナリオを自動化します。

* **新しい従業員の雇用** - Workday に新しい従業員が追加されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に作成され、メール アドレスが Workday に書き戻されます。

* **従業員の属性とプロファイルの更新** - Workday で従業員レコード (名前、職名、マネージャなど) が更新されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に更新されます。

* **従業員の退職** - Workday で従業員が退職状態になると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に無効になります。

* **従業員の再雇用** - Workday で従業員が再雇用されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)に以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この Workday ユーザー プロビジョニング ソリューションは、現在パブリック プレビュー段階です。このソリューションは次のお客様に最適です。

* Workday ユーザー プロビジョニング用に事前に構築されたクラウドベースのソリューションを求めている組織

* Workday から Active Directory、または Azure Active Directory への直接ユーザー プロビジョニングを必要とする組織

* Workday HCM モジュールから取得したデータを使用してユーザーをプロビジョニングする必要がある組織 ([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) を参照してください)

* 1 つ以上の Active Directory フォレスト、ドメイン、および OU と同期するために、Workday HCM モジュールで検出された変更情報のみに基づいてユーザーの登録、移動、削除を行う必要がある組織 ([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) を参照してください)

* 電子メールに Office 365 を使用している組織

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このセクションでは、一般的なハイブリッド環境に向けた、エンド ツー エンドのユーザー プロビジョニング ソリューションのアーキテクチャについて説明します。 2 つの関連するフローがあります。

* **権限がある人事データのフロー – Workday からオンプレミスの Active Directory へ:** このフローでは、最初に社員イベント (新規雇用、異動、退職など) がクラウドの Workday HR テナントで発生し、イベント データはその後、Azure AD ディレクトリとプロビジョニング エージェントを通ってオンプレミスの Active Directory に移動します。 イベントによっては、AD での作成/更新/有効化/無効化の操作に至る可能性があります。
* **メール書き戻しのフロー – オンプレミスの Active Directory から Workday へ:** Active Directory でアカウントの作成が完了すると、Azure AD Connect を介して Azure AD と同期され、Active Directory がソースであるメール属性を Workday に書き戻すことができます。

![概要](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>エンド ツー エンドのユーザー データ フロー

1. 人事チームは、Workday HCM で社員のトランザクション (参加者/異動者/休暇者または新規雇用/移動/退職) を実行します
2. Azure AD プロビジョニング サービスは、Workday HR からの、スケジュールされた ID の同期を実行し、オンプレミスの Active Directory との同期のために処理する必要がある変更を識別します。
3. Azure AD プロビジョニング サービスは、AD アカウントの作成/更新/有効化/無効化の操作を含む要求ペイロードを使用して、オンプレミスの AAD Connect プロビジョニング エージェントを呼び出します。
4. Azure AD Connect プロビジョニング エージェントは、サービス アカウントを使用して AD アカウントのデータを追加/更新します。
5. Azure AD Connect (AD の同期エンジン) は、デルタ同期を実行して AD 内の更新をプルします。
6. Active Directory の更新は、Azure Active Directory と同期されます。
7. Workday Writeback コネクタが構成されている場合は、使用された一致する属性に基づいて、メール属性が Workday に書き戻されます。


## <a name="planning-your-deployment"></a>デプロイの計画

Workday の統合を開始する前に、以下の前提条件を確認し、現在の Active Directory のアーキテクチャおよびユーザー プロビジョニング要件を Azure Active Directory が提供するソリューションに適合させる方法について、次のガイダンスをお読みください。

### <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* グローバル管理者アクセス権を持つ有効な Azure AD Premium P1 サブスクリプション
* テストと統合のために Workday を実装したテナント
* システム統合ユーザーを作成し、テスト目的でテスト用従業員データを変更する Workday の管理者権限
* Active Directory へのユーザー プロビジョニングの場合、[オンプレミス プロビジョニング エージェント](https://go.microsoft.com/fwlink/?linkid=847801)をホストするために、.NET 4.7+ ランタイムがインストールされた Windows Server 2012 以上を実行しているサーバーが必要です
* Active Directory と Azure AD を同期する [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

### <a name="planning-considerations"></a>計画に関する考慮事項

Azure AD には、Workday から Active Directory、Azure AD、SaaS アプリなどへのプロビジョニングおよび ID ライフ サイクル管理を解決するための豊富なプロビジョニング コネクタが用意されています。 使用する機能とソリューションの設定方法は、組織の環境と要件によって異なります。 最初の手順として、次のうちどれが組織内に存在し、展開されているかを確認します。

* 使用中の Active Directory フォレスト数
* 使用中の Active Directory ドメイン数
* 使用中の Active Directory の組織単位 (OU) 数
* 使用中の Azure Active Directory のテナント数
* Active Directory と Azure Active Directory の両方にプロビジョニングする必要があるユーザー ("ハイブリッド" ユーザーなど) の有無
* Azure Active Directory にプロビジョニングする必要があるが、Active Directory にはプロビジョニングの必要がないユーザー ("クラウドのみ" ユーザーなど) の有無
* ユーザーのメール アドレスを Workday に書き戻す必要の有無

これらの質問に答えたあと、以下のガイダンスに従って、Workday プロビジョニングの展開を計画します。

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>AAD Connect プロビジョニング エージェントの展開の計画

Workday から AD へのユーザー プロビジョニング ソリューションを使用するには、少なくとも 4 GB の RAM と .NET 4.7+ ランタイムを備えた Windows 2012 R2 以上を実行するサーバーに、プロビジョニング エージェントを 1 つまたは複数展開する必要があります。 プロビジョニング エージェントをインストールする前に、以下の点を考慮する必要があります。

* プロビジョニング エージェントを実行しているホスト サーバーが、ターゲット AD ドメインへのネットワーク アクセスを持っていることを確認します
* プロビジョニング エージェントの構成ウィザードで、エージェントを Azure AD テナントに登録します。登録プロセスでは、ポート 8082 の *msappproxy.net にアクセスする必要があります。 この通信を可能にするファイアウォールのアウトバウンド規則が適用されていることを確認します。
* プロビジョニング エージェントは、サービス アカウントを使用してオンプレミスの AD ドメインと通信します。 エージェントをインストールする前に、ユーザー プロパティの読み取り/書き込みアクセス許可と無期限のパスワードを持つサービス アカウントを作成することをお勧めします。  
* プロビジョニング エージェントの構成時には、プロビジョニング要求を処理する必要のあるドメイン コント ローラーを選択できます。 地理的に分散されたドメイン コント ローラーが複数ある場合は、エンド ツー エンドのソリューションの信頼性とパフォーマンスを向上させるため、優先ドメイン コント ローラーと同じサイトにプロビジョニング エージェントをインストールします
* 高可用性については、複数のプロビジョニング エージェントを展開し、それが同じセットのオンプレミス AD ドメインを処理するように登録できます。

> [!IMPORTANT]
> 運用環境で高可用性が必要な場合は、Azure AD テナントで少なくとも 3 つのプロビジョニング エージェントが構成されているようにすることをお勧めします。

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>展開するプロビジョニング コネクタ アプリの選択

Workday と Active Directory を統合するときには、考慮に入れる必要があるソースとターゲットのシステムが複数あります。

| ソース システム | ターゲット システム | メモ |
| ---------- | ---------- | ---------- |
| Workday | Active Directory ドメイン | 各ドメインは、個別のターゲット システムとして扱われます |
| Workday | Azure AD テナント | クラウドのみユーザー用に必要 |
| Active Directory フォレスト | Azure AD テナント | 現在、このフローは AAD Connect によって処理されます |
| Azure AD テナント | Workday | メール アドレスの書き戻し用 |

Workday と Active Directory の間のワークフローのプロビジョニングを容易にするため、Azure AD には、Azure AD アプリ ギャラリーから追加できるプロビジョニング コネクタ アプリが複数用意されています。

![AAD アプリ ギャラリー](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory Provisioning** - このアプリは、Workday から単一の Active Directory ドメインへのユーザー アカウントのプロビジョニングを容易にします。 複数のドメインがある場合は、プロビジョニング先にする必要がある Active Directory ドメインごとに 1 つ、Azure AD アプリ ギャラリーからこのアプリのインスタンスを追加できます。

* **Workday to Azure AD Provisioning** - AAD Connect は、Active Directory ユーザーを Azure Active Directory に同期するのに必要なツールですが、このアプリはクラウドのみユーザーを Workday から単一の Azure Active Directory テナントへプロビジョニングするのを容易にします。

* **Workday Writeback** - このアプリは、ユーザーのメール アドレスを Azure Active Directory から Workday に書き戻すのを容易にします。

> [!TIP]
> 通常の "Workday" アプリケーションは、Workday と Azure Active Directory の間でシングル サインオンを設定するために使用されます。 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Workday から AD ユーザー属性へのマッピングと変換を決定する

Active Directory ドメインへのユーザー プロビジョニングを構成する前に、以下の事項について検討してください。 これらの事項に対する答えで、スコープ フィルターと属性マッピングをどのように設定する必要があるかが決まります。

* **Workday のユーザーをこの Active Directory フォレストにプロビジョニングする必要はありますか。**

   * *例:Workday の "Company" 属性に値 "Contoso" が含まれ、"Worker_Type" 属性に "Regular" が含まれているユーザー*

* **ユーザーはどのように組織単位 (OU) にルーティングされますか。**

   * *例:Workday の "Municipality" 属性と "Country_Region_Reference" 属性の定義に従って、オフィスの位置に対応する OU にルーティングされるユーザー*

* **次の属性は Active Directory にどのように埋め込む必要がありますか。**

   * 共通名 (cn)
      * *例:人事によって設定された Workday の User_ID 値を使用します*
      
   * 従業員 ID (employeeId)
      * *例:Workday の Worker_ID 値を使用します*
      
   * SAM アカウント名 (sAMAccountName)
      * *例:Azure AD プロビジョニング式でフィルター処理された Workday の User_ID 値を使用して、不正な文字を削除します*
      
   * ユーザー プリンシパル名 (userPrincipalName)
      * *例:Azure AD プロビジョニング式で Workday の User_ID 値を使用して、ドメイン名を付加します*

* **Workday と Active Directory の間でどのようにユーザーを対応付ける必要がありますか。**

  * *例:特定の Workday "Worker_ID" 値を持つユーザーは、"employeeID" が同じ値の Active Directory ユーザーと対応付けます。Active Directory で Worker_ID の値が見つからない場合は、新しいユーザーを作成します。*
  
* **Active Directory フォレストには、一致するロジックが動作するために必要なユーザー ID が既に含まれていますか。**

  * *例:Workday を新規にデプロイする場合は、一致するロジックをできるだけ単純にするために、Active Directory に正しい Workday の Worker_ID 値 (または選択した一意の ID 値) をあらかじめ入力することを強く推奨します。*



これらの特別なプロビジョニング コネクタ アプリをセットアップして構成する方法が、このチュートリアルの残りのセクションの主題です。 どのアプリを構成するかは、プロビジョニングが必要な対象システム、および環境内の Active Directory ドメインと Azure AD テナントの数によって異なります。



## <a name="configure-integration-system-user-in-workday"></a>Workday の統合システム ユーザーの構成

すべての Workday プロビジョニング コネクタに共通する要件は、Workday Human Resources API に接続するために、Workday システム統合アカウントの資格情報を必要とすることです。 このセクションでは、Workday で統合システムのユーザーを作成する方法について説明します。

> [!NOTE]
> この手順を省略し、代わりに Workday グローバル管理者アカウントをシステム統合アカウントとして使用することもできます。 これはデモでは問題なく動作するかもしれませんが、本番環境での展開にはお勧めできません。

### <a name="create-an-integration-system-user"></a>統合システム ユーザーの作成

**統合システム ユーザーを作成するには、次の手順に従います。**

1. 管理者アカウントを使用して、Workday テナントにサインインします。 **Workday アプリケーション** で、検索ボックスに「ユーザーの作成」と入力し、**[Create Integration System User (統合システム ユーザーの作成)]** をクリックします。

    ![ユーザーの作成](./media/workday-inbound-tutorial/wd_isu_01.png "ユーザーの作成")
2. 新しい統合システム ユーザーのユーザー名とパスワードを指定して、**統合システム ユーザーの作成**を完了します。  
 * このユーザーはプログラムを使用してログオンするため、**[次回のサインイン時に新しいパスワードを要求する]** オプションはオフのままにしておきます。
 * **[セッション タイムアウト (分)]** は既定値の 0 のままにしておきます。これにより、ユーザーのセッションが有効期限前にタイムアウトするのを防ぎます。
 * オプション **[Do Not Allow UI Sessions]\(UI セッションを許可しない)** を選択します。これは、統合システムのパスワードを持つユーザーが Workday にログインできないようにする追加のセキュリティ層を提供するためです。 

    ![統合システム ユーザーの作成](./media/workday-inbound-tutorial/wd_isu_02.png "統合システム ユーザーの作成")

### <a name="create-a-security-group"></a>セキュリティ グループの作成
この手順では、Workday 内に、制約のない統合システム セキュリティ グループを作成し、このグループに、前の手順で作成した統合システム ユーザーを割り当てます。

**セキュリティ グループを作成するには、次の手順に従います。**

1. 検索ボックスに「セキュリティ グループの作成」と入力し、 **[Create Security Group (セキュリティ グループの作成)]** をクリックします。

    ![CreateSecurity グループ](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity グループ")
2. **[セキュリティ グループの作成]** タスクを完了します。  
   * **[Type of Tenanted Security Group]\(テナント セキュリティ グループの種類\)** ドロップダウンから **[Integration System Security Group (Unconstrained)]\(統合システム セキュリティ グループ (制約なし)\)** を選択します。

    ![CreateSecurity グループ](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity グループ")

3. セキュリティ グループの作成が成功した後、セキュリティ グループにメンバーを割り当てることができるページが表示されます。 このセキュリティ グループに新しい統合システム ユーザーを追加し、適切な組織のスコープを選択します。
![セキュリティ グループの編集](./media/workday-inbound-tutorial/wd_isu_05.png "セキュリティ グループの編集")
 
### <a name="configure-domain-security-policy-permissions"></a>ドメイン セキュリティ ポリシーのアクセス許可の構成
この手順では、セキュリティ グループに、社員データについての "ドメイン セキュリティ" ポリシーのアクセス許可を付与します。

**ドメイン セキュリティ ポリシーのアクセス許可を構成するには:**

1. 検索ボックスに「**Domain Security Configuration**」と入力し、**[Domain Security Configuration Report]\(ドメイン セキュリティ構成レポート)** リンクをクリックします。  

    ![ドメイン セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_06.png "ドメイン セキュリティ ポリシー")  
2. **[Domain]\(ドメイン)** テキスト ボックスで以下のドメインを検索し、それらをフィルターに 1 つずつ追加します。  
   * *External Account Provisioning*
   * *Worker Data:Public Worker Reports*
   * *Person Data:Work Contact Information*
   * *Worker Data:All Positions*
   * *Worker Data:Current Staffing Information*
   * *Worker Data:Business Title on Worker Profile*
 
    ![ドメイン セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_07.png "ドメイン セキュリティ ポリシー")  

    ![ドメイン セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_08.png "ドメイン セキュリティ ポリシー") 

    Click **OK**.

3. 表示されるレポートで、**External Account Provisioning** の横に表示される省略記号 (...) を選択し、メニュー オプション **Domain(ドメイン) -> Edit Security Policy Permissions(セキュリティ ポリシー アクセス許可の編集** をクリックします

    ![ドメイン セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_09.png "ドメイン セキュリティ ポリシー")  

4. **[Edit Domain Security Policy Permissions]\(ドメイン セキュリティ ポリシー アクセス許可の編集)** ページで、**[Integration Permissions]\(統合アクセス許可)** セクションまで下へスクロールします。 [+] 記号をクリックし、**Get** と **Put** の統合アクセス許可を持つセキュリティ グループの一覧に統合システム グループを追加します。

    ![アクセス許可の編集](./media/workday-inbound-tutorial/wd_isu_10.png "アクセス許可の編集")  

5. [+] 記号をクリックし、**Get** と **Put** の統合アクセス許可を持つセキュリティ グループの一覧に統合システム グループを追加します。

    ![アクセス許可の編集](./media/workday-inbound-tutorial/wd_isu_11.png "アクセス許可の編集")  

6. 以下の残りのセキュリティ ポリシーごとに、上記の手順 3 ～ 5 を繰り返します。

   | Operation | ドメイン セキュリティ ポリシー |
   | ---------- | ---------- | 
   | Get と Put | Worker Data:Public Worker Reports |
   | Get と Put | Person Data:Work Contact Information |
   | 取得 | Worker Data:All Positions |
   | 取得 | Worker Data:Current Staffing Information |
   | 取得 | Worker Data:Business Title on Worker Profile |

### <a name="configure-business-process-security-policy-permissions"></a>ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成
この手順では、セキュリティ グループに、社員データについての "ビジネス プロセス セキュリティ" ポリシーのアクセス許可を付与します。 これは、Workday Writeback アプリのコネクタを設定するために必要です。 

**ビジネス プロセス セキュリティ ポリシーのアクセス許可を構成するには:**

1. 検索ボックスに「**Business Process Policy**」と入力し、**[Edit Business Process Security Policy]\(ビジネス プロセス セキュリティ ポリシーの編集)** タスクのリンクをクリックします。  

    ![ビジネス プロセス セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_12.png "ビジネス プロセス セキュリティ ポリシー")  

2. **[Business Process Type]\(ビジネス プロセスの種類)** テキストボックスで、*[Contact]* を検索し、**[Contact Change]** ビジネス プロセスを選択して **[OK]** をクリックします。

    ![ビジネス プロセス セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_13.png "ビジネス プロセス セキュリティ ポリシー")  

3. **Edit Business Process Security Policy\(ビジネス プロセス セキュリティ ポリシーの編集)** ページで、**Maintain Contact Information (Web Service)\(連絡先情報 (Web サービス) の管理)** セクションまで下へスクロールします。

    ![ビジネス プロセス セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_14.png "ビジネス プロセス セキュリティ ポリシー")  

4. 新しい統合システム セキュリティ グループを選択し、Web サービス要求を開始できるセキュリティ グループの一覧に追加します。 **[Done]\(終了)** をクリックします。 

    ![ビジネス プロセス セキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_15.png "ビジネス プロセス セキュリティ ポリシー")  

 
### <a name="activate-security-policy-changes"></a>セキュリティ ポリシーの変更のアクティブ化

**セキュリティ ポリシーの変更をアクティブにするには、次の手順に従います。**

1. 検索ボックスに「アクティブ化」と入力し、**[保留中のセキュリティ ポリシーの変更をアクティブ化]** リンクをクリックします。

    ![アクティブ化](./media/workday-inbound-tutorial/wd_isu_16.png "アクティブ化") 
2. 監査用のコメントを入力し、 **[OK]** をクリックして、[Activate Pending Security Policy Changes (保留中のセキュリティ ポリシーの変更のアクティブ化)] 作業を開始します。 

    ![保留中のセキュリティのアクティブ化](./media/workday-inbound-tutorial/wd_isu_17.png "保留中のセキュリティのアクティブ化")  
1. **[確認]** チェック ボックスをオンにして、**[OK]** をクリックし、次の画面で作業を完了します。

    ![保留中のセキュリティのアクティブ化](./media/workday-inbound-tutorial/wd_isu_18.png "保留中のセキュリティのアクティブ化")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday から Active Directory へのユーザー プロビジョニングの構成

次の手順に従って、Workday から、統合の範囲内にある各 Active Directory ドメインへのユーザー アカウントのプロビジョニングを構成します。

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>パート 1:オンプレミス プロビジョニング エージェントのインストールと構成

オンプレミスの Active Directory にプロビジョニングするには、.NET 4.7+ Framework と目的の Active Directory ドメインへのネットワーク アクセスを備えたサーバーに、エージェントがインストールされている必要があります。

> [!TIP]
> [ここ](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)に示した手順に従って、サーバーの .NET Framework のバージョンをチェックできます。
> サーバーに .NET 4.7 以降がインストールされていない場合は、[ここ](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)からダウンロードできます。  

.NET 4.7+ を展開したら、**[ここのオンプレミス プロビジョニング エージェント](https://go.microsoft.com/fwlink/?linkid=847801)** をダウンロードして、以下に示す手順に従ってエージェントの構成を完了できます。

1. 新しいエージェントをインストールする Windows Server にログインします。
2. プロビジョニング エージェントのインストーラーを起動し、条件に同意して **[インストール]** ボタンをクリックします。
![インストール画面](./media/workday-inbound-tutorial/pa_install_screen_1.png "インストール画面")

3. インストールが完了したらウィザードが起動され、**[Connect Azure AD]\(Azure AD の接続)** 画面が表示されます。 **[認証]** ボタンをクリックして、お使いの Azure AD インスタンスに接続します。
![Azure AD の接続](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD の接続")

4. グローバル管理者の資格情報を使用して、Azure AD インスタンスに対して認証します。 
![管理者の認証](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理者の認証")

5. Azure AD で認証が成功すると、**[Connect Active Directory]\(Active Directory の接続)** 画面が表示されます。 この手順では、お使いの AD ドメイン名を入力し、**[ディレクトリの追加]** ボタンをクリックしてください。
![ディレクトリの追加](./media/workday-inbound-tutorial/pa_install_screen_4.png "ディレクトリの追加")

6. ここで、AD ドメインに接続するために必要な資格情報の入力を求められます。 同じ画面で、**[Select domain controller priority]\(ドメイン コント ローラーの優先度を選択する)** を使用して、エージェントがプロビジョニング要求の送信に使用する必要があるドメイン コントローラーを指定できます。
![ドメインの資格情報](./media/workday-inbound-tutorial/pa_install_screen_5.png "ドメインの資格情報")

7. ドメインの構成後、インストーラーによって、構成されたドメインの一覧が表示されます。 この画面では、手順 5 と 6 を繰り返してより多くのドメインを追加するか、**[次へ]** をクリックしてエージェントの登録に進むことができます。 
![構成されたドメイン](./media/workday-inbound-tutorial/pa_install_screen_6.png "構成されたドメイン")

   > [!NOTE]
   > 複数の AD ドメイン (例: na.contoso.com、emea.contoso.com) がある場合は、各ドメインを個々に一覧に追加してください。 親ドメイン (例: contoso.com) を追加するだけでは不十分です。それぞれの子ドメインをエージェントに登録することをお勧めします。 

8. 構成の詳細を確認し、**[Confirm]\(確認)** をクリックしてエージェントを登録します。 
![確認画面](./media/workday-inbound-tutorial/pa_install_screen_7.png "確認画面")

9. 構成ウィザードに、エージェント登録の進行状況が表示されます。
![エージェントの登録](./media/workday-inbound-tutorial/pa_install_screen_8.png "エージェントの登録")

10. エージェントの登録が成功したら、**[終了]** をクリックしてウィザードを終了できます。 
![終了画面](./media/workday-inbound-tutorial/pa_install_screen_9.png "終了画面")

11. エージェントのインストールを確認し、実行中であることを確認します。それには、[サービス] スナップインを開き、"Microsoft Azure AD Connect Provisioning Agent" という名前のサービスを探します![サービス](./media/workday-inbound-tutorial/services.png)  


**エージェントのトラブルシューティング**

エージェントをホストする Windows Server マシンの [Windows イベント ログ](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)には、エージェントによって実行されるすべての操作のイベントが含まれています。 これらのイベントを表示するには:
    
1. **Eventvwr.msc** を開きます。
2. **[Windows ログ] > [アプリケーション]** の順に選択します。
3. ソース **AAD.Connect.ProvisioningAgent** のログに記録されたすべてのイベントを表示します。 
4. エラーと警告をを確認します。

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>パート 2:プロビジョニング コネクタ アプリの追加と Workday への接続の作成

**Workday を Active Directory プロビジョニングに構成するには、**

1.  <https://portal.azure.com> に移動します

2.  左のナビゲーション バーで、**[Azure Active Directory]** を選択します

3.  **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順に選択します。

4.  **[アプリケーションの追加]** を選択し、**[すべて]** のカテゴリを選択します。

5.  「**Workday Provisioning to Active Directory**」を検索し、ギャラリーからそのアプリを追加します。

6.  アプリが追加され、アプリの詳細画面が表示されたら、**[プロビジョニング]** を選択します

7.  **[プロビジョニング** **モード]** を **[自動]** に設定します

8.  以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理者ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 **次のように表示されます: username@contoso4**

   * **管理者パスワード –** Workday 統合システム アカウントのパスワードを入力します

   * **テナント URL –** テナントの Workday Web サービス エンドポイントへの URL を入力します。 これは https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources のようになります。contoso4 は適切テナント名に置き換え、wd3-impl は適切な環境文字列に置き換えます。

   * **Active Directory フォレスト -** エージェントに登録されている、Active Directory ドメインの "名前"。 これは通常、*contoso.com* のような文字列です。

   * **Active Directory コンテナー -** エージェントが既定でユーザー アカウントを作成する必要のあるコンテナー DN を入力します。 
        例:*OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> この設定が適用されるのは、属性のマッピングで *parentDistinguishedName* 属性が構成されていない場合のユーザー アカウント作成のみです。 この設定は、ユーザーの検索や更新の操作には使用されません。 ドメインのサブツリー全体が、検索操作の範囲内になります。
   * **メール通知 –** メール アドレスを入力し、[send email if failure occurs (失敗した場合にメールを送信する)] チェック ボックスをオンにします。
> [!NOTE]
> Azure AD プロビジョニング サービスは、プロビジョニング ジョブが[検査](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine)状態になった場合にメール通知を送信します。

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗する場合は、エージェントのセットアップで構成された Workday 資格情報と AD 資格情報が有効であることを再確認します。

![Azure ポータル](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>パート 2:属性マッピングの構成 

このセクションでは、ユーザー データが Workday から Active Directory に移動する方法を構成します。

1.  **[マッピング]** の [プロビジョニング] タブで、**[Synchronize Workday Workers to OnPremises (Workday の社員をオンプレミスに同期する)]** をクリックします。

2.  **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、AD へのプロビジョニングの対象にする Workday のユーザー セットを選択できます。 既定のスコープは、"Workday のすべてのユーザー" です。 フィルターの例:

   * 例:1000000 から 2000000 までの Worker ID を持つユーザーにスコープを設定

      * 属性:WorkerID

      * 演算子:REGEX Match

      * 値:(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 例:臨時社員ではなく、従業員のみ 

      * 属性:EmployeeID

      * 演算子:IS NOT NULL

3.  **[対象オブジェクトのアクション]** フィールドでは、Active Directory で実行可能なアクションをグローバルにフィルタリングできます。 **作成**と**更新**が最も一般的です。

4.  **[属性マッピング]** セクションでは、個別の Workday 属性を Active Directory の属性にマッピングする方法を定義できます。

5. 既存の属性マッピングをクリックして更新するか、または画面の下部にある **[新しいマッピングの追加]** をクリックして、新しいマッピングを追加します。 個々の属性マッピングは、次のプロパティをサポートしています。

      * **マッピングの種類**

         * **ダイレクト** – 変更なしで Workday 属性の値を AD 属性に書き込みます

         * **定数** - 静的な定数文字列の値を AD 属性に書き込みます

         * **式** – 1 つ以上の Workday 属性に基づいて、AD 属性にカスタム値を書き込むことができます。 [詳細については、式に関するこの記事を参照してください](../manage-apps/functions-for-customizing-application-data.md)。

      * **ソース属性** - Workday のユーザー属性。 探している属性が存在しない場合は、「[Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)」を参照してください。

      * **既定値** – 省略可能。 ソース属性に空の値がある場合、マッピングではこの値が代わりに書き込まれます。
            最も一般的な構成では、これを空白のままにします。

      * **ターゲット属性**: Active Directory のユーザー属性。

      * **この属性を使用してオブジェクトを照合する** –このマッピングを使用して、Workday と Active Directory 間でユーザーを一意に識別するかどうかを示します。 これは、通常、Workday の Worker ID フィールドで設定され、一般的に Active Directory の従業員 ID 属性のいずれかにマッピングされます。

      * **照合の優先順位** – 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

      * **このマッピングを適用する**
       
         * **常に** – このマッピングをユーザーの作成と更新の両方のアクションに適用します

         * **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します

6. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。

![Azure ポータル](./media/workday-inbound-tutorial/WD_2.PNG)

**以下にいくつか示すのは、Workday と Active Directory との間の属性マッピングの例と、一般的に使用される式です**

-   parentDistinguishedName 属性にマップされる式を使用すると、1 つ以上の Workday ソース属性に基づいて異なる OU にユーザーをプロビジョニングできます。 この例では、住所の市区町村に基づいて、異なる OU にユーザーを配置しています。

-   Active Directory の userPrincipalName 属性は、Workday のユーザー ID とドメイン サフィックスを連結して生成されます

-   [式の記述に関するドキュメントについては、こちらを参照してください](../manage-apps/functions-for-customizing-application-data.md)。 特殊文字を削除する方法の例についても紹介されています。

  
| WORKDAY 属性 | ACTIVE DIRECTORY 属性 |  ID 一致の有無 | 作成/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **はい** | 作成時のみ書き込まれる |
| **UserID**    |  cn    |   |   作成時のみ書き込まれる |
| **Join("@", [UserID], "contoso.com")**   | userPrincipalName     |     | 作成時のみ書き込まれる 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         作成時のみ書き込まれる |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 作成時 + 更新時 |
| **FirstName**   | givenName       |     |    作成時 + 更新時 |
| **LastName**   |   sn   |     |  作成時 + 更新時 |
| **PreferredNameData**  |  displayName |     |   作成時 + 更新時 |
| **Company**         | company   |     |  作成時 + 更新時 |
| **SupervisoryOrganization**  | department  |     |  作成時 + 更新時 |
| **ManagerReference**   | manager  |     |  作成時 + 更新時 |
| **BusinessTitle**   |  title     |     |  作成時 + 更新時 | 
| **AddressLineData**    |  streetAddress  |     |   作成時 + 更新時 |
| **Municipality**   |   l   |     | 作成時 + 更新時 |
| **CountryReferenceTwoLetter**      |   co |     |   作成時 + 更新時 |
| **CountryReferenceTwoLetter**    |  c  |     |         作成時 + 更新時 |
| **CountryRegionReference** |  st     |     | 作成時 + 更新時 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  作成時 + 更新時 |
| **PostalCode**  |   postalCode  |     | 作成時 + 更新時 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 作成時 + 更新時 |
| **Fax**      | facsimileTelephoneNumber     |     |    作成時 + 更新時 |
| **Mobile**  |    mobile       |     |       作成時 + 更新時 |
| **LocalReference** |  preferredLanguage  |     |  作成時 + 更新時 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  作成時 + 更新時 |
  


### <a name="part-4-start-the-service"></a>パート 4:サービスの開始
パート 1 ～ 3 が完了したら、Azure Portal に戻ってプロビジョニング サービスを開始できます。

1.  **[プロビジョニング]** タブで、**[プロビジョニングの状態]** を **[ON]** に設定します。

2. **[Save]** をクリックします。

3. これにより初期同期が開始されます。これに要する時間は Workday のユーザー数に応じて異なります。

4. 好きなときに、Azure Portal の **[監査ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 監査ログには、Workday から読み込まれたユーザーや、その後 Active Directory に追加または更新されたユーザーなど、プロビジョニング サービスによって実行された個々の同期イベントがすべて表示されます。 **[監査ログの読み方の詳細については、プロビジョニング レポート ガイドを参照してください](../manage-apps/check-status-user-account-provisioning.md)**

1.  エージェントをホストする Windows Server マシンの [Windows イベント ログ](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)で、新しいエラーや警告が発生していないかどうかを確認します。 こうしたイベントを表示するには、サーバーで **Eventvwr.msc** を起動し、**[Windows ログ] > [アプリケーション]** の順に選択します。 プロビジョニングに関連するすべてのメッセージが、ソース **AADSyncAgent** のログに記録されます。

6. プロビジョニングが完了すると、次に示すように、**[プロビジョニング]** タブに監査概要レポートが書き込まれます。

![Azure ポータル](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Azure Active Directory へのユーザー プロビジョニングの構成
Azure Active Directory へのプロビジョニングの構成方法は、以下の表に示すとおり、プロビジョニングの要件によって異なります。

| シナリオ | 解決策 |
| -------- | -------- |
| **ユーザーは Active Directory と Azure AD にプロビジョニングする必要があります** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** を使用する |
| **ユーザーは Active Directory にのみプロビジョニングする必要があります** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** を使用する |
| **ユーザーは Azure AD にのみ (クラウドのみ) プロビジョニングする必要があります** | アプリ ギャラリーの **Workday to Azure Active Directory provisioning** アプリを使用する |

Azure AD Connect の設定手順については、[Azure AD Connect に関するドキュメント](../hybrid/whatis-hybrid-identity.md)を参照してください。

次のセクションでは、Workday と Azure AD の間の接続を設定して、クラウドのみのユーザーをプロビジョニングする方法について説明します。

> [!IMPORTANT]
> Azure AD にプロビジョニングする必要があり、オンプレミスの Active Directory にはその必要がないクラウドのみのユーザーが存在する場合は、以下の手順にのみ従ってください。

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>パート 1:Azure AD プロビジョニング コネクタ アプリケーションの追加と Workday への接続の作成

**Workday を構成して、クラウドのみのユーザーを Azure Active Directory にプロビジョニングするには、**

1.  <https://portal.azure.com> にアクセスします。

2.  左のナビゲーション バーで、**[Azure Active Directory]** を選択します

3.  **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順に選択します。

4.  **[アプリケーションの追加]** を選択し、**[すべて]** のカテゴリを選択します。

5.  **Workday to Azure AD provisioning** を検索し、ギャラリーからそのアプリを追加します。

6.  アプリが追加され、アプリの詳細画面が表示されたら、**[プロビジョニング]** を選択します

7.  **[プロビジョニング** **モード]** を **[自動]** に設定します

8.  以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理者ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 次のように表示されます: username@contoso4

   * **管理者パスワード –** Workday 統合システム アカウントのパスワードを入力します

   * **テナント URL –** テナントの Workday Web サービス エンドポイントへの URL を入力します。 これは https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources のようになります。contoso4 は適切テナント名に置き換え、wd3-impl は適切な環境文字列に置き換えます。 この URL が不明の場合は、Workday 統合パートナーまたはサポート担当者に連絡して、使用する正しい URL を確認してください。

   * **メール通知 –** メール アドレスを入力し、[send email if failure occurs (失敗した場合にメールを送信する)] チェック ボックスをオンにします。

   * **[接続のテスト]** ボタンをクリックします。

   * 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、Workday URL と資格情報が Workday で有効であることを再度確認します。

### <a name="part-2-configure-attribute-mappings"></a>パート 2:属性マッピングの構成 

このセクションでは、クラウドのみのユーザー データが Workday から Azure Active Directory に移動する方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、**[Synchronize Workers to Azure AD]**(Workers を Azure AD に同期する) をクリックします。

2. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、Azure AD へのプロビジョニングの対象にするWorkday のユーザー セットを選択できます。 既定のスコープは、"Workday のすべてのユーザー" です。 フィルターの例:

   * 例:1000000 から 2000000 までの Worker ID を持つユーザーにスコープを設定

      * 属性:WorkerID

      * 演算子:REGEX Match

      * 値:(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 例:正規従業員ではなく、臨時社員のみ

      * 属性:ContingentID

      * 演算子:IS NOT NULL

3. **[対象オブジェクトのアクション]** フィールドでは、Azure AD で実行可能なアクションをグローバルにフィルタリングできます。 **作成**と**更新**が最も一般的です。

4. **[属性マッピング]** セクションでは、個別の Workday 属性を Active Directory の属性にマッピングする方法を定義できます。

5. 既存の属性マッピングをクリックして更新するか、または画面の下部にある **[新しいマッピングの追加]** をクリックして、新しいマッピングを追加します。 個々の属性マッピングは、次のプロパティをサポートしています。

   * **マッピングの種類**

      * **ダイレクト**– 変更なしで Workday 属性の値を AD 属性に書き込みます

      * **定数** - 静的な定数文字列の値を AD 属性に書き込みます

      * **式** – 1 つ以上の Workday 属性に基づいて、AD 属性にカスタム値を書き込むことができます。 [詳細については、式に関するこの記事を参照してください](../manage-apps/functions-for-customizing-application-data.md)。

   * **ソース属性** - Workday のユーザー属性。 探している属性が存在しない場合は、「[Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)」を参照してください。

   * **既定値** – 省略可能。 ソース属性に空の値がある場合、マッピングではこの値が代わりに書き込まれます。
            最も一般的な構成では、これを空白のままにします。

   * **ターゲット属性** – Azure AD のユーザーの属性です。

   * **この属性を使用してオブジェクトを照合する** – このマッピングを使用して、Workday と Azure AD 間でユーザーを一意に識別するかどうかを示します。 これは、通常、Workday の Worker ID フィールドで設定され、一般的に Azure AD の従業員 ID 属性 (新規) または拡張属性にマッピングされます。

   * **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

   * **このマッピングを適用する**

     * **常に** – このマッピングをユーザーの作成と更新の両方のアクションに適用します

     * **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します

6. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。

### <a name="part-3-start-the-service"></a>パート 3:サービスの開始
パート 1 ～ 2 の部分が完了したら、プロビジョニング サービスを開始できます。

1. **[プロビジョニング]** タブで、**[プロビジョニングの状態]** を **[ON]** に設定します。

2. **[Save]** をクリックします。

3. これにより初期同期が開始されます。これに要する時間は Workday のユーザー数に応じて異なります。

4. **[監査ログ]** タブで、個々の同期イベントを表示できます。**[監査ログの読み方の詳細については、プロビジョニング レポート ガイドを参照してください](../manage-apps/check-status-user-account-provisioning.md)**

5. プロビジョニングが完了すると、次に示すように、**[プロビジョニング]** タブに監査概要レポートが書き込まれます。

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>メール アドレスを Workday に書き戻す構成
ユーザーのメールアドレスを Azure Active Directory から Workday に書き戻すように構成するには、次の手順に従ってください。

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>パート 1:プロビジョニング コネクタ アプリの追加と Workday への接続の作成

**Workday Writeback コネクタを構成するには:**

1. <https://portal.azure.com> に移動します

2. 左のナビゲーション バーで、**[Azure Active Directory]** を選択します

3. **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、**[すべて]** のカテゴリを選択します。

5. **Workday Writeback** を検索し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、**[プロビジョニング]** を選択します

7. **[プロビジョニング** **モード]** を **[自動]** に設定します

8. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理者ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 次のように表示されます: username@contoso4

   * **管理者パスワード –** Workday 統合システム アカウントのパスワードを入力します

   * **テナント URL –** テナントの Workday Web サービス エンドポイントへの URL を入力します。 これは https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources のようになります。contoso4 は適切なテナント名に置き換え、wd3-impl は適切な環境文字列に置き換えます (必要な場合)。

   * **メール通知 –** メール アドレスを入力し、[send email if failure occurs (失敗した場合にメールを送信する)] チェック ボックスをオンにします。

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、Workday URL と資格情報が Workday で有効であることを再度確認します。

### <a name="part-2-configure-attribute-mappings"></a>パート 2:属性マッピングの構成 

このセクションでは、ユーザー データが Workday から Active Directory に移動する方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、**[Synchronize Azure AD Users to Workday]**(Azure AD ユーザーを Workday に同期する) をクリックします。

2. **ソース オブジェクト スコープ** フィールドでは、必要があれば、メール アドレスを Workday に書き戻す対象を Azure Active Directory のユーザー セットからフィルタリングできます。 既定のスコープは、"Azure AD のすべてのユーザー" です。 

3. **[属性マッピング]** セクションで、Workday の Worker ID または従業員 ID が格納されている Azure Active Directory の属性を示すように一致する ID を更新します。 一般的なマッチング メソッドは、Workday の Worker ID または従業員 ID を Azure AD の extensionAttribute1-15 に同期してから、この Azure AD の属性を使用して、Workday に戻ってユーザーを照合します。 

4. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。

### <a name="part-3-start-the-service"></a>パート 3:サービスの開始
パート 1 ～ 2 の部分が完了したら、プロビジョニング サービスを開始できます。

1. **[プロビジョニング]** タブで、**[プロビジョニングの状態]** を **[ON]** に設定します。

2. **[Save]** をクリックします。

3. これにより初期同期が開始されます。これに要する時間は Workday のユーザー数に応じて異なります。

4. **[監査ログ]** タブで、個々の同期イベントを表示できます。**[監査ログの読み方の詳細については、プロビジョニング レポート ガイドを参照してください](../manage-apps/check-status-user-account-provisioning.md)**

5. プロビジョニングが完了すると、次に示すように、**[プロビジョニング]** タブに監査概要レポートが書き込まれます。

## <a name="customizing-the-list-of-workday-user-attributes"></a>Workday のユーザー属性リストをカスタマイズする
Active Directory と Azure AD の Workday プロビジョニング アプリにはどちらも、Workday のユーザー属性の選択元となる既定のリストが含まれています。 ただしこれらのリストは、必要な情報をすべてカバーしているわけではありません。 Workday は、想定されるさまざまなユーザー属性を数多くサポートしていますが、それらのユーザー属性には、標準の属性と特定の Workday テナントに固有の属性とがあります。 

Azure AD プロビジョニング サービスは、このリスト (Workday 属性) をカスタマイズできるようになっており、Human Resources API の [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 操作に公開されている属性であれば、どのような属性でも含めることができます。

そのためには、使用する属性の XPath 式を [Workday Studio](https://community.workday.com/studio-download) を使って抽出し、Azure Portal の高度な属性エディターを使ってそれらをプロビジョニング構成に追加する必要があります。

**Workday のユーザー属性に使用する XPath 式を取得するには**

1. [Workday Studio](https://community.workday.com/studio-download) をダウンロードしてインストールします。 インストーラーにアクセスするには、Workday コミュニティ アカウントが必要です。

2. 次の URL から Workday Human_Resources WDSL ファイルをダウンロードします: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Workday Studio を起動します。

4. コマンド バーから **[Workday] > [Test Web Service in Tester]\(Tester で Web サービスをテストする\)** オプションを選択します。

5. **[External]\(外部\)** を選択し、手順 2. でダウンロードした Human_Resources WSDL ファイルを選択します。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. **[Location]\(場所\)** フィールドを `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` に設定します。ただし、"IMPL-CC" の部分は実際のインスタンス タイプに、"TENANT" の部分は実際のテナント名に置き換えてください。

7. **[Operation]\(操作\)** を **[Get_Workers]** に設定します。

8.  [Request/Response]\(要求/応答\) ウィンドウの下に小さく表示された **[configure]\(構成\)** リンクをクリックして、Workday の資格情報を設定します。 **[Authentication]\(認証\)** チェック ボックスをオンにし、Workday 統合システム アカウントのユーザー名とパスワードを入力します。 ユーザー名は必ず name@tenant の形式で指定し、**[WS-Security UsernameToken]** オプションはオンのままにしてください。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. **[OK]** を選択します。

10. **[Request]\(要求\)** ウィンドウに以下の XML を貼り付けます。**Employee_ID** には、ご使用の Workday テナントに存在する実際のユーザーの従業員 ID を設定してください。 抽出対象となる属性が設定されているユーザーを選択します。

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. **[Send Request]\(要求を送信する\)** (緑色の矢印) をクリックしてコマンドを実行します。 成功した場合、その応答が **[Response]\(応答\)** ウィンドウに表示されます。 エラーではなく、入力したユーザー ID のデータが応答に含まれていることを確認します。

12. 成功した場合は、**[Response]\(応答\)** ウィンドウから XML をコピーし、XML ファイルとして保存します。

13. Workday Studio のコマンド バーで **[File]\(ファイル\) > [Open File...]\(ファイルを開く...\)** を選択し、保存した XML ファイルを開きます。 Workday Studio の XML エディターにファイルが表示されます。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. ファイル ツリーで、**[/env:Envelope] > [env:Body] > [wd:Get_Workers_Response] > [wd:Response_Data] > [wd:Worker]** の順に移動して、該当するユーザーのデータを特定します。 

15. **[wd:Worker]** から、追加する属性を探して選択します。

16. 選択した属性の XPath 式を **[Document Path]\(ドキュメント パス\)** フィールドからコピーします。

1. コピーした式から **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** プレフィックスを削除します。

18. コピーした式の最後の項目がノード (例: "/wd:Birth_Date") である場合、式の末尾に **/text()** を付加します。 最後の項目が属性 (例: "/@wd: type") である場合、この手順は不要です。

19. 最終的には、`wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` のようになっている必要があります。 これを Azure Portal にコピーすることになります。


**プロビジョニング構成に Workday のカスタム ユーザー属性を追加するには**

1. このチュートリアルで前述した説明に従い、[Azure Portal](https://portal.azure.com) を起動して、対象の Workday プロビジョニング アプリケーションの [プロビジョニング] セクションに移動します。

2. **[プロビジョニングの状態]** を **[オフ]** に設定し、**[保存]** を選択します。 こうすることで、準備が整った段階でのみ、変更を反映することができます。

3. **[マッピング]** で **[Synchronize Workers to OnPremises]\(ワーカーをオンプレミスと同期\)** または **[Synchronize Workers to Azure AD]\(ワーカーを Azure AD と同期\)** を選択します。

4. 次の画面の一番下までスクロールして **[詳細オプションを表示する]** を選択します。

5. **[Edit attribute list for Workday]\(Workday の属性リストの編集\)** を選択します。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 属性リストの一番下にある入力フィールドまでスクロールします。

7. **[名前]** に、属性の表示名を入力します。

8. **[Type]\(型\)** には、対象となる属性にふさわしい型を選択します (通常は **String** が一般的)。

9. **[API Expression]\(API 式\)** には、Workday Studio からコピーした XPath 式を入力します。 例: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **[属性の追加]** を選択します。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 上の **[保存]** を選択し、ダイアログで **[はい]** を選択します。 [属性マッピング] 画面をまだ開いている場合は閉じてください。

12. メインの **[プロビジョニング]** タブに戻り、**[Synchronize Workers to OnPremises]\(ワーカーをオンプレミスと同期\)** または **[Synchronize Workers to Azure AD]\(ワーカーを Azure AD と同期\)** を再び選択します。

13. **[新しいマッピングの追加]** を選択します。

14. **[ソース属性]** リストに新しい属性が表示されます。

15. 必要に応じて新しい属性のマッピングを追加します。

16. 完了したら、忘れずに **[プロビジョニングの状態]** を **[オン]** に設定してから保存してください。

## <a name="known-issues"></a>既知の問題

* オンプレミスの Active Directory での thumbnailPhoto ユーザー属性へのデータの書き込みは、現在サポートされていません。

* "Workday から Azure AD" へのコネクタは、AAD Connect が有効になっている Azure AD テナントでは現在サポートされていません。  

## <a name="managing-personal-data"></a>個人データの管理

Active Directory のための Workday プロビジョニング ソリューションには、ドメインに参加しているサーバーに同期エージェントがインストールされている必要があります。このエージェントは、個人を特定できる情報が含まれている可能性のあるログを Windows イベント ログ内に作成します。

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
* [Workday と Azure Active Directory の間でシングル サインオンを構成する方法](workday-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)

