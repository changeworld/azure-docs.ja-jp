---
title: Azure AD ECMA Connector Host の構成
description: この記事では、Azure AD ECMA Connector Host を構成する方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449694"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Azure AD ECMA Connector Host とプロビジョニング エージェントを構成する

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューです。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

この記事では、Azure Active Directory (Azure AD) ECMA Connector Host とプロビジョニング エージェントを正常にインストールした後、それらを構成する方法に関するガイダンスを提供します。

次のフローは、Azure AD ECMA Connector Host をインストールして構成するプロセスを示しています。 

 ![インストールのフローを示す図。](./media/on-premises-ecma-configure/flow-1.png)

インストールと構成の詳細については、以下を参照してください。
   - [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
   - [Azure AD ECMA Connector Host の汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA Connector Host を構成する
Azure AD ECMA Connector Host の構成は、次の 2 つの部分に分けられます。
    
   - **設定を構成する**: Azure AD ECMA Connector Host で使用するポートと証明書を構成します。 この手順は、ECMA Connector Host が初めて起動されたときにのみ実行されます。
   - **コネクタを作成する**: Azure AD ECMA Connector Host でデータ ソースにデータをエクスポートまたはインポートできるようにコネクタ (SQL や LDAP など) を作成します。

### <a name="configure-the-settings"></a>設定を構成する
Azure AD ECMA Connector Host を初めて起動すると、既定の **8585** が入力されたポート番号が表示されます。 

 ![設定の構成を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-1.png)

プレビューでは、新しい自己署名証明書を生成する必要があります。

 >[!NOTE]
 >このプレビューでは、時間に依存する証明書を使用します。 自動生成された証明書は自己署名されます。 信頼されたルートの一部と SAN がホスト名と一致します。


### <a name="create-a-connector"></a>コネクタを作成する
次に、Azure AD ECMA Connector Host で使用するコネクタを作成する必要があります。 このコネクタを使用すると、ECMA Connector Host で、作成したコネクタのデータ ソースにデータをエクスポートできます。 また、必要に応じてデータをインポートすることもできます。

個々の各コネクタの構成手順はより長く、独自のドキュメントに記載されています。

コネクタを作成して構成するには、[汎用 SQL コネクタ](on-premises-sql-connector-configure.md)を使用します。 このコネクタは、Azure SQL Database や Azure Database for MySQL などの Microsoft SQL データベースで動作します。

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Azure AD と Azure AD ECMA Connector Host の間の接続を確立する
以降のセクションでは、オンプレミスの Azure AD ECMA Connector Host と Azure AD との接続の確立について説明します。

### <a name="ensure-the-ecma2host-service-is-running"></a>ECMA2Host サービスが実行されていることを確認する
1. Azure AD ECMA Connector Host を実行しているサーバーで、 **[開始]** を選択します。
1. 「**run**」と入力し、ボックスに「**services.msc**」と入力します。
1. **[サービス]** の一覧で、 **[Microsoft ECMA2Host]** が存在し、実行されていることを確認します。 そうでない場合は、 **[開始]** を選択します。

   ![サービスが実行されていることを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>エンタープライズ アプリケーションの追加
1. アプリケーション管理者として Azure portal にサインインします。
1. ポータルで、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。
1. **[新しいアプリケーション]** を選択します。

   ![新しいアプリケーションの追加を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-4.png)
1. ギャラリーから **[オンプレミス プロビジョニング]** アプリケーションを見つけ、 **[作成]** を選択します。

### <a name="configure-the-application-and-test"></a>アプリケーションを構成してテストする
  1. アプリケーションが作成されたら、 **[プロビジョニング]** ページを選択します。
  1. **[開始する]** を選択します。
  
     ![作業の開始を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-6.png)
  1. **[プロビジョニング]** ページで、 **[プロビジョニング モード]** を **[自動]** に変更します。

      ![モードの変更を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-7.png)
  1. **[オンプレミス接続]** セクションで、デプロイしたエージェントを選択し、 **[エージェントの割り当て]** を選択します。
  
      ![エージェントの割り当てを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >エージェントを追加した後、登録が完了するまで 10 ～ 20 分待ちます。 登録が完了するまで、接続テストは機能しません。
      >
      >または、サーバーでプロビジョニング エージェントを再起動して、エージェントの登録を強制的に完了させることもできます。 サーバーに移動し、Windows 検索バーで **サービス** を検索し、**Azure AD Connect プロビジョニング エージェント サービス** を特定し、サービスを右クリックして再起動します。

  1. 10 分経ったら、 **[管理者資格情報]** セクションで、次の URL を入力します。 `"connectorName"` の部分を ECMA ホスト上のコネクタの名前に置き換えます。

      |プロパティ|値|
      |-----|-----|
      |テナントの URL|https://localhost:8585/ecma2host_connectorName/scim|

  1. コネクタを作成したときに定義したシークレット トークンの値を入力します。
  1. **[接続のテスト]** を選択して 1 分待ちます。
  
     ![接続のテストを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >エージェントを割り当てた後に必ず 10 ～ 20 分待ってから接続をテストしてください。 登録が完了していない場合、接続は失敗します。

  1. 接続テストが成功したら、 **[保存]** を選択します。</br>
  
     ![テストの成功を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>プロビジョニングのスコープに含めるユーザーを構成する
これで Azure AD ECMA Connector Host が Azure AD と通信できるようになったので、プロビジョニングのスコープに含めるユーザーの構成に進むことができます。 以降のセクションでは、ユーザーのスコープを指定する方法に関する情報を提供します。

### <a name="assign-users-to-your-application"></a>アプリケーションにユーザーを割り当てる
Azure AD を使用すると、アプリケーションへの割り当てに基づいて、または特定の属性でフィルター処理することによって、プロビジョニングのスコープに含めるユーザーを構成できます。 プロビジョニングのスコープに含める必要があるユーザーを決定し、必要に応じて、スコープ規則を定義します。 詳細については、「[Azure Active Directory のアプリに対するユーザー割り当ての管理](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)」を参照してください。

### <a name="configure-your-attribute-mappings"></a>属性マッピングを構成する
ここで、Azure AD のユーザー属性をターゲット アプリケーション内の属性にマップします。 Azure AD プロビジョニング サービスは、プロビジョニングの SCIM 標準に依存しています。 その結果、表示される属性には SCIM 名前空間があります。 次の例は、Azure AD 内の **mail** および **objectId** 属性をアプリケーション内の **Email** および **InternalGUID** 属性にマップする方法を示しています。

>[!NOTE]
>既定のマッピングでは、**userPrincipalName** が属性名 *PLACEHOLDER* に接続されます。 この *PLACEHOLDER* 属性をアプリケーションにある属性に変更する必要があります。 詳細については、「[ソース システムとターゲット システムで一致するユーザー](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)」を参照してください。

|Azure AD の属性名|SCIM の属性名|ターゲット アプリケーションの属性名|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|電子メール|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>属性マッピングを構成する
 1. Azure AD ポータルの **[エンタープライズ アプリケーション]** で、 **[プロビジョニング]** ページを選択します。
 2. **[開始する]** を選択します。
 3. **[マッピング]** を展開し、 **[Azure Active Directory ユーザーをプロビジョニングする]** を選択します。
 
    ![Azure Active Directory ユーザーのプロビジョニングを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-10.png)
 1. **[新しいマッピングの追加]** を選択します。
 
    ![新しいマッピングの追加を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-11.png)
 1. ソースとターゲットの属性を指定し、 **[OK]** を選択します。</br>
 
    ![[属性の編集] ウィンドウを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-12.png)


ユーザー属性をアプリケーションから Azure AD にマッピングする方法の詳細については、「[チュートリアル - Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングをカスタマイズする](customize-application-attributes.md)」を参照してください。

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>オンデマンドでユーザーをプロビジョニングすることで構成をテストする
構成をテストするには、ユーザーのオンデマンド プロビジョニングを使用できます。 ユーザーのオンデマンド プロビジョニングについては、[オンデマンド プロビジョニング](provision-on-demand.md)に関するページを参照してください。

 1. シングル サインオン ウィンドウに移動してから、プロビジョニング ウィンドウに戻ります。 新しいプロビジョニングの概要ウィンドウで、 **[オンデマンド]** を選択します。
 1. 「[Azure Active Directory でのオンデマンド プロビジョニング](provision-on-demand.md)」の説明に従って、複数のユーザーのオンデマンド プロビジョニングをテストします。
 
    ![プロビジョニングのテストを示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>ユーザーのプロビジョニングを開始する
 1. オンデマンド プロビジョニングが成功したら、プロビジョニングの構成ページに戻ります。 スコープが割り当て済みのユーザーとグループだけに確実に設定されているようにし、プロビジョニングの状態を **[オン]** に変更して **[保存]** を選択します。
 
    ![プロビジョニングの開始を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-14.png)

1. プロビジョニングが開始されるまで数分待ちます。 最大 40 分かかることがあります。 プロビジョニング ジョブが完了したら (次のセクションで説明されています)、プロビジョニングの状態を **[オフ]** に変更し、 **[保存]** を選択できます。 この手順により、プロビジョニング サービスは今後実行されなくなります。

### <a name="verify-users-were-successfully-provisioned"></a>ユーザーが正常にプロビジョニングされたことを確認する
待機後、データ ソースを調べ、新しいユーザーがプロビジョニングされているかどうかを確認します。

 ![ユーザーがプロビジョニングされていることの確認を示すスクリーンショット。](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>デプロイを監視する

1. プロビジョニング ログを使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
1. Azure Monitor の統合を使用して、カスタム アラート、ダッシュボード、クエリを作成します。
1. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1)をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md)
