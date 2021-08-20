---
title: Azure AD ECMA Connector Host 汎用 SQL コネクタのチュートリアル
description: このチュートリアルでは、オンプレミスアプリケーションを使用して汎用 SQL アプリケーション コネクタをプロビジョニングする方法を説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/13/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ef75392676b2c766064eefb3c8113734bfa622
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437539"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-tutorial"></a>Azure AD ECMA Connector Host 汎用 SQL コネクタのチュートリアル

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューです。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

このチュートリアルでは、ユーザーを Azure Active Directory (Azure AD) から SQL データベースに自動的にプロビジョニングおよびプロビジョニング解除するために実行する必要がある手順について説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

このチュートリアルでは、汎用 SQL コネクタと Azure AD ECMA Connector Host を設定して使用する方法を説明します。 

## <a name="prepare-the-sample-database"></a>サンプル データベースの準備
SQL Server を実行しているサーバー上で、 [付録 A](#appendix-a) にある SQLスクリプトを実行します。このスクリプトでは、CONTOSO という名前のサンプル データベースを作成します。 これは、ユーザーをプロビジョニングするデータベースです。


## <a name="create-the-dsn-connection-file"></a>DSN 接続ファイルの作成
汎用 SQL コネクタは、SQL サーバーに接続するための DSN ファイルです。 最初に、ODBC 接続情報を含むファイルを作成する必要があります。

1. サーバー上で ODBC 管理ユーティリティを起動します。
  
     ![ODBC 管理を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/odbc.png)
1. **[ファイル DSN]** タブを選択し、 **[追加]** を選択します。 
 
     ![[ファイル DSN] タブを示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-2.png)
1. **SQL Server Native Client 11.0** を選択し、 **[次へ]** を選択します。 
 
     ![ネイティブ クライアントの選択を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-3.png)
1. ファイルに **GenericSQL** などの名前を付け、 **[次へ]** を選択します。 
 
     ![コネクタの名前を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-4.png)
1. **[完了]** を選択します。 
 
     ![終了を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-5.png)
1. ここで接続を構成します。 サーバー名に **「APP1」** を入力し、 **[次へ]** を選択します。

     ![サーバー名の入力を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-6.png)
1. Windows 認証を維持して **[次へ]** を選択します。

     ![Windows 認証を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-7.png)
1. サンプル データベース名の **CONTOSO** を入力します。

     ![データベース名の入力を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-8.png)
1. この画面はすべて既定値のままにし、 **[完了]** を選択します。

     ![完了の選択を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-9.png)
1. 問題なく動作することを確認するには、 **[データ ソースのテスト]** を選択します。 
 
     ![テスト データ ソースを示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-10.png)
1. テストの成功を確認します。

     ![成功を示すスクリーンショット。](./media/tutorial-ecma-sql-connector/dsn-11.png)
1. **[OK]** を 2 回選びます。 ODBC データ ソース アドミニストレーターを閉じます。

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Azure AD Connect プロビジョニング エージェント パッケージをダウンロードしてインストールする

 1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。
 1. Azure portal にサインインし、 **[Azure Active Directory]** に移動します。
 1. 左側のメニューで **[Azure AD Connect]** を選びます。
 1. **[Manage cloud sync]\(クラウド同期の管理\)**  >  **[すべてのエージェントの確認]** を選択します。
 1. Azure portal から Azure AD Connect をプロビジョニングするエージェント パッケージをダウンロードします。
 1. ご契約条件に同意して、 **[ダウンロード]** を選択します。
 1. Azure AD Connect プロビジョニングのインストーラ (AADConnectProvisioningAgentSetup.msi) を実行します。
 1. **[Microsoft Azure AD Connect のプロビジョニング エージェント パッケージ]** 画面で **[インストール]** を選択します。
 
    ![Microsoft Azure AD Connect のプロビジョニング エージェント パッケージの画面を示すスクリーンショット。](media/on-premises-ecma-install/install-1.png)</br>
 1. この操作が完了すると、構成ウィザードが起動します。 **[次へ]** を選びます。
 
    ![ようこそ画面のスクリーンショット。](media/on-premises-ecma-install/install-2.png)</br>
 1. **[拡張機能 の選択]** 画面 で、 **[オンプレミス アプリケーションのプロビジョニング] (Azure AD からアプリへ)** を選択し、 **[次へ]** を 選択します。 
 
    ![[拡張機能の選択] 画面を示すスクリーンショット。](media/on-premises-ecma-install/install-3.png)</br>
 1. グローバル管理者アカウントを使用し、Azure AD にサインインします。
 
     ![Azure サインインの画面を示すスクリーンショット。](media/on-premises-ecma-install/install-4.png)</br>
 1. **[エージェントの構成]** 画面で、 **[確認]** を選択します。
 
     ![インストールの確認を示すスクリーンショット。](media/on-premises-ecma-install/install-5.png)</br>
 1. インストールが完了すると、ウィザードの下部にメッセージが表示されます。 **[終了]** を選択します。
 
     ![[終了] ボタンが表示されているスクリーンショット。](media/on-premises-ecma-install/install-6.png)</br>
 
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA Connector Host を構成する
1. デスクトップにある ECMA ショートカットを選択します。
1. ECMA Connector Host の構成が開始したら、既定のポート **8585** のままにし、 **[生成]** を選択して証明書を生成します。 自動生成された証明書は、信頼ルートの一部として自己署名されます。 SAN はホスト名と一致します。

     ![設定の構成を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-1.png)
1. **[保存]** を選択します。

## <a name="create-a-generic-sql-connector"></a>汎用 SQL コネクタを作成する
 1. デスクトップで、ECMA Connector Host のショートカットを選択します。
 1. **[新しいコネクタ]** を選択します。
 
     ![新しいコネクタの選択を示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. **[プロパティ]** ページでは、画像の下にある表に指定される値をボックスに入力し、 **[次へ]** を選択します。
 
     ![プロパティの入力を示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-1.png)

     |プロパティ|値|
     |-----|-----|
     |名前|SQL|
     |AutoSync タイマー (分)|120|
     |シークレット トークン|ここに自身のキーを入力します。 12 文字以上である必要があります。|
       |拡張 DLL|汎用 SQL コネクタの場合、**Microsoft.IAM.Connector.GenericSql.dll** を選択します。|
 1. **[接続性]** ページでは、画像の下にある表に指定される値をボックスに入力し、 **[次へ]** を選択します。
 
     ![[接続性] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-2.png)

     |プロパティ|値|
     |-----|-----|
     |DSN ファイル|「DSN 接続ファイルを作成する」のチュートリアルの冒頭で作成したファイルに移動します。|
     |[ユーザー名]|contoso\administrator|
     |Password|管理者のパスワードを入力します。|
 1. **[スキーマ 1]** ページでは、画像の下にある表に指定される値をボックスに入力し、 **[次へ]** を選択します。
 
     ![[スキーマ 1] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-3.png)

     |プロパティ|値|
     |-----|-----|
     |オブジェクトの種類の検出方法|Fixed Value|
     |固定値のリスト/テーブル/ビュー/SP|ユーザー|
 1. **[スキーマ 2]** ページでは、画像の下にある表に指定される値をボックスに入力し、 **[次へ]** を選択します。
 
     ![[スキーマ 2] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-4.png)

     |プロパティ|値|
     |-----|-----|
     |ユーザー: 属性の検出|テーブル|
     |ユーザー: テーブル/ビュー/SP|Employees|
 1. **[スキーマ 3]** ページでは、画像の下にある表に指定される値をボックスに入力し、 **[次へ]** を選択します。
 
     ![[スキーマ 3] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-5.png)

     |プロパティ|説明|
     |-----|-----|
     |[:User] に[アンカー] を選択します|User:ContosoLogin|
     |ユーザーの DN 属性を選択してください|AzureID|
 1. **[スキーマ 4]** ページでは、既定値のままにし、 **[次へ]** を選択します。
 
     ![[スキーマ 4] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-6.png)
 1. **[グローバル]** ページで、ボックスに入力し、 **[次へ]** をクリックします。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
 
     ![[グローバル] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-7.png)

     |プロパティ|説明|
     |-----|-----|
     |データ ソースの日付と時刻の形式|yyyy-MM-dd HH:mm:ss|
 1. **[パーティション]** ページで **[次へ]** を選択します。
 
     ![[パーティション] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-8.png)

 1. **[プロファイルの実行]** ページで **[エクスポート]** チェックボックスを選択したままにします。 **[フル インポート]** チェックボックスを選択して、 **[次へ]** を選択します。
 
     ![[実行プロファイル] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-9.png)

 1. **[エクスポート]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。 
 
     ![[エクスポート] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-10.png)

     |プロパティ|説明|
     |-----|-----|
     |操作方法|テーブル|
     |テーブル/ビュー/SP|Employees|
 
 1. **[フル インポート]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。 
 
     ![[フル インポート] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-11.png)

     |プロパティ|説明|
     |-----|-----|
     |操作方法|テーブル|
     |テーブル/ビュー/SP|Employees|
 
 1. **[オブジェクトの種類]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。

      - **Anchor**: この属性は、ターゲット システム内で一意である必要があります。 Azure AD プロビジョニング サービスでは、初期サイクル後、この属性を使用して ECMA ホストに対してクエリを実行します。 このアンカー値は、スキーマ 3 のアンカー値と同じである必要があります。
      - **Query Attribute**: ECMA ホストでメモリ内キャッシュに対してクエリを実行するために使用されます。 この属性は一意である必要があります。
      - **DN**: ほとんどの場合、**Autogenerated** オプションを選択する必要があります。 選択解除されている場合、DN 属性が、CN = anchorValue, Object = objectType の形式で、DN を格納している Azure AD 内の属性に確実にマップされるようにします。

        ![[オブジェクトの種類] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-12.png)

      |プロパティ|説明|
      |-----|-----|
      |ターゲット オブジェクト|ユーザー|
      |アンカー|ContosoLogin|
      |クエリ属性|AzureID|
      |DN|AzureID|
      |自動生成|オン|
      

 1. **[属性の選択]** ページで、ドロップダウン リストのすべての属性を追加し **[次へ]** を選択します。 
 
     ![[属性の選択] ページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-13.png)

      **[属性]** ドロップダウン リストには、ターゲット システムで検出され、以前の **[属性の選択]** ページでは選択 *されなかった* 属性が示されます。 
 1. **[プロビジョニング解除]** ページの **[フローを無効化]** で **[削除]** を選択します。 **[完了]** を選択します。
 
     ![プロビジョニング解除のページを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\conn-14.png)

## <a name="ensure-ecma2host-service-is-running"></a>ECMA2Host サービスが実行されていることを確かめる
1. Azure AD ECMA Connector Host を実行しているサーバーで、 **[開始]** をクリックします。
1. **「run」** と入力し、ボックスに **「services.msc」** と入力します。
1. **サービス** リストで、**Microsoft ECMA2Host** が確実に存在し、実行中であるようにします。 層で内場合は、 **[開始]** を選択します。

   ![サービスが稼働中であることを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-2.png)

## <a name="add-an-enterprise-application"></a>エンタープライズ アプリケーションの追加
1. アプリケーション管理者として、Azure portal にサインインします
1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。
1. **[新しいアプリケーション]** を選択します。

   ![新しいアプリケーションの追加を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-4.png)
1. **オンプレミス ECMA アプリ** のギャラリーを検索し、 **[作成]** を選択します。

## <a name="configure-the-application-and-test"></a>アプリケーションを構成してテストする
1. 作成されたら、 **[プロビジョニング] ページ** をクリックします。
1. **[Get started]\(作業を開始する\)** を選択します。

     ![開始を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-1.png)
1. **[プロビジョニング]** ページで、モードを **[自動]** に変更します。

     ![モードを [自動] への変更を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-7.png)
1. **[オンプレミス接続]** セクションで、デプロイしたばかりのエージェントを選択し、 **[エージェントの割り当て]** をクリックします。
     >[!NOTE]
     >エージェントの追加後、登録が完了するまで 10 分間待機してください。 登録が完了するまで、接続テストは機能しません。
     >
     >または、サーバーでプロビジョニング エージェントを再起動して、エージェントの登録を強制的に完了させることもできます。 サーバーに移動し、Windows 検索バーで **サービス** を検索し、**Azure AD Connect プロビジョニング エージェント サービス** を特定し、サービスを右クリックして再起動します。
   
     ![エージェントの再起動を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-8.png)
1.  10 分後、 **[管理者資格情報]** セクションで、次の URL を入力します。 `connectorName` 部分を、ECMA ホスト上のコネクタの名前に置き換えます。 `localhost` はホスト名に置き換えることもできます。

     |プロパティ|値|
     |-----|-----|
     |テナントの URL|https://localhost:8585/ecma2host_connectorName/scim|

1. コネクタの作成時に定義した **シークレット トークン** の値を入力します。
1. **[接続のテスト]** をクリックし、1 分待ちます。

     ![エージェントの割り当てを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-5.png)
1. 接続のテストが成功したら、 **[保存]** をクリックします。</br>

     ![エージェントのテストを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="assign-users-to-an-application"></a>アプリケーションにユーザーを割り当てる
これで、Azure AD ECMA Connector Host が Azure AD と通信できるようになったので、プロビジョニングの対象ユーザーの構成に進むことができます。 

1. Azure portal で、 **[エンタープライズ アプリケーション]** を選択します。
1. **オンプレミスのプロビジョニング** アプリケーションを選択します。
1. 左側のウィンドウの **[管理]** で、 **[ユーザーとグループ]** を選択します。
1. **[Add user/group]\(ユーザーまたはグループの追加\)** を選択します。

     ![ユーザーの追加を示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\app-2.png)
1. **[ユーザー]** で **[選択なし]** を選択します。

     ![何も選択されていないことを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\app-3.png)
1. 右側からユーザーを選択し、 **[選択]** ボタンを選択します。</br>

     ![ユーザーの選択を示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\app-4.png)
1. **[割り当て]** を選択します。

     ![ユーザーの割り当てを示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\app-5.png)

## <a name="configure-attribute-mappings"></a>属性マッピングの構成
次に、オンプレミス アプリケーションと SQL サーバー間で属性をマップする必要があります。

#### <a name="configure-attribute-mapping"></a>属性マッピングを構成する
 1. Azure AD ポータルの **[エンタープライズ アプリケーション]** で、 **[プロビジョニング]** ページを選択します。
 1. **[Get started]\(作業を開始する\)** を選択します。
 1. **[マッピング]** を展開して **[Azure Active Directory ユーザーをプロビジョニングする]** を選択します。
 
    ![ユーザーのプロビジョニングを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-10.png)
 1. **[新しいマッピングの追加]** を選択します。
 
     ![新しいマッピングの追加を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-11.png)
 1. ソース属性とターゲット属性を指定し、次の表に示すすべてのマッピングを追加します。

      |マッピングの種類|ソース属性|ターゲット属性|
      |-----|-----|-----|
      |直接|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
      |直接|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
      |直接|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
      |直接|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
      |直接|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
      |直接|mailNickname|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|

 1. **[保存]** を選択します。
 
     ![マッピングの保存を示すスクリーンショット。](.\media\tutorial-ecma-sql-connector\app-6.png)

## <a name="test-provisioning"></a>プロビジョニングをテストする
属性がマップされたので、ユーザーの 1 人でオンデマンド プロビジョニングをテストできます。
 
 1. Azure portal で、 **[エンタープライズ アプリケーション]** を選択します。
 1. **オンプレミスのプロビジョニング** アプリケーションを選択します。
 1. 左側で **プロビジョニング** を選択します。
 1. **[Provision on demand] (オンデマンドでプロビジョニングする)** を選択します。
 1. テスト ユーザーのものを検索し、 **[プロビジョニング]** を選択します。
 
     ![プロビジョニングのテストを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-13.png)

## <a name="start-provisioning-users"></a>ユーザーのプロビジョニングを開始する
 1. オンデマンド プロビジョニングが成功したら、プロビジョニングの構成ページに戻ります。 スコープが割り当てられたユーザーとグループのみに確実に設定されているようにし、プロビジョニングを [**オン]** にして、 **[保存]** を選択します。
 
    ![プロビジョニングの開始を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-14.png)
 1. プロビジョニングが開始されるまで数分待機します。 最大 40 分かかる場合があります。 プロビジョニング ジョブが完了した後、次のセクションで説明されているように、プロビジョニングの状態を **[オフ]** に変更し、 **[保存]** をクリックできます。 これにより、プロビジョニング サービスは今後実行されなくなります。

## <a name="check-that-users-were-successfully-provisioned"></a>ユーザーが正常にプロビジョニングされたことを確認する
待機した後、SQL データベースを確認して、ユーザーがプロビジョニングされているのを確認します。

 ![ユーザーがプロビジョニングされていることの確認を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="appendix-a"></a>Azure BLOB ストレージ アカウントにデータを取得/アップロードする方法については、
次の SQL スクリプトを使用して、サンプル データベースを作成します。

```SQL
---Creating the Database---------
Create Database CONTOSO
Go
-------Using the Database-----------
Use [CONTOSO]
Go
-------------------------------------

/****** Object:  Table [dbo].[Employees]    Script Date: 1/6/2020 7:18:19 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Employees](
    [ContosoLogin] [nvarchar](128) NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [Email] [nvarchar](128) NULL,
    [InternalGUID] [uniqueidentifier] NULL,
    [AzureID] [uniqueidentifier] NULL,
    [textID] [nvarchar](128) NULL
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Employees] ADD  CONSTRAINT [DF_Employees_InternalGUID]  DEFAULT (newid()) FOR [InternalGUID]
GO

```




## <a name="next-steps"></a>次のステップ

- [オンプレミス アプリケーションのプロビジョニングのトラブルシューティング](on-premises-ecma-troubleshoot.md)
- [既知の制限事項のレビュー](known-issues.md)
- [オンプレミスプロビジョニングの前提条件](on-premises-ecma-prerequisites.md)
- [オンプレミス プロビジョニングの前提条件のレビュー](on-premises-ecma-prerequisites.md)
