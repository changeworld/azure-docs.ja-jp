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
ms.openlocfilehash: cad38ff8c0dc18d363773783e95e544d67f55193
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570450"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Azure AD ECMA Connector Host とプロビジョニング エージェントを構成する

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューです。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

この記事では、Azure AD ECMA Connector Host とプロビジョニング エージェントを正常にインストールしてから構成する方法に関するガイダンスを提供します。

Azure AD ECMA Connector Host のインストールと構成は 1 つのプロセスです。  このプロセスを進めるには、以下のフローを使用します。

 ![インストールのフロー](./media/on-premises-ecma-configure/flow-1.png)  

インストールと構成の詳細については、以下を参照してください。
   - [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
    - [Azure AD ECMA Connector Host の汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA Connector Host を構成する
Azure AD ECMA Connector Host の構成は、2 つの部分で行われます。
    
   - **設定を構成する** - 使用する Azure AD ECMA Connector Host のポートと証明書を構成します。  これは、ECMA Connector Host が初めて起動されたときにのみ行われます。
   - **コネクタを作成する** - コネクタ (SQL や LDAP など) を作成し、Azure AD ECMA Connector Host でデータ ソースに対してデータをエクスポートまたはインポートできるようにします。

### <a name="configure-the-settings"></a>設定を構成する
Azure AD ECMA Connector Host を初めて起動すると、既定の 8585 を使用して既に入力されているポート番号が表示されます。  

 ![設定を構成する](.\media\on-premises-ecma-configure\configure-1.png)

プレビューでは、新しい自己署名証明書を生成する必要があります。

 >[!NOTE]
 >このプレビューでは、時間に依存する証明書を使用します。 自動生成された証明書は自己署名され、信頼されたルートの一部になり、SAN がホスト名と一致します。


### <a name="create-a-connector"></a>コネクタを作成する
次に、Azure AD ECMA Connector Host で使用するコネクタを作成する必要があります。  このコネクタを使用すると、ECMA Connector Host で、作成したコネクタのデータ ソースにデータをエクスポート (および必要に応じてインポート) できるようになります。  

個々の各コネクタの構成手順はより長く、独自のドキュメントに記載されています。

コネクタを作成して構成するには、以下のいずれかのリンクを使用します。

- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md) - Microsoft SQL や MySQL などの SQL データベースを操作するコネクタ。


## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Azure AD と Azure AD ECMA Connector Host の間の接続を確立する
次のセクションでは、オンプレミスの Azure AD ECMA Connector Host と Azure AD との接続を確立する手順について説明します。

#### <a name="ensure-ecma2host-service-is-running"></a>ECMA2Host サービスが実行されていることを確かめる
1.  Azure AD ECMA Connector Host を実行しているサーバーで、[開始] をクリックします。
2. 「run」と入力し、ボックスに「services.msc」と入力します
3. サービスで、**Microsoft ECMA2Host** が確実に存在し、実行中であるようにします。  そうでない場合は、 **[開始]** をクリックします。
 ![サービス実行中](.\media\on-premises-ecma-configure\configure-2.png)

#### <a name="add-enterprise-application"></a>エンタープライズ アプリケーションを追加する
1.  アプリケーション管理者として、Azure portal にサインインします
2. ポータルで、Azure Active Directory、 **[エンタープライズ アプリケーション]** の順に移動します。
3. **[新しいアプリケーション]** をクリックします。
 ![新しいアプリケーションを追加する](.\media\on-premises-ecma-configure\configure-4.png)
4. ギャラリーから "オンプレミス プロビジョニング" アプリケーションを見つけ、 **[作成]** をクリックします。

### <a name="configure-the-application-and-test"></a>アプリケーションを構成してテストする
  1. 作成されたら、 **[プロビジョニング] ページ** をクリックします。
  2. **[作業の開始]** をクリックします。
 ![[作業の開始]](.\media\on-premises-ecma-configure\configure-6.png)
  3. **[プロビジョニング] ページ** で、モードを **[自動]** に変更します
   ![モードを変更する](.\media\on-premises-ecma-configure\configure-7.png)
  4. [オンプレミス接続] セクションで、デプロイしたばかりのエージェントを選択し、[エージェントの割り当て] をクリックします。
     ![エージェントを割り当てる](.\media\on-premises-ecma-configure\configure-8.png)</br>

  >[!NOTE]
  >エージェントの追加後、登録が完了するまで 10 分から 20 分待つ必要があります。  登録が完了するまで、接続テストは機能しません。
  >
  >または、サーバーでプロビジョニング エージェントを再起動して、エージェントの登録を強制的に完了させることもできます。 サーバーに移動し、Windows 検索バーでサービスを検索し、Azure AD Connect プロビジョニング エージェント サービスを特定し、サービスを右クリックして再起動します。
  

  5.  10 分後に、 **[管理者の資格情報]** セクションに次の URL を入力します。"connectorName" の部分は、ECMA Host 上のコネクタの名前に置き換えてください。

      |プロパティ|値|
      |-----|-----|
      |テナントの URL|https://localhost:8585/ecma2host_connectorName/scim|

  6. コネクタの作成時に定義したシークレット トークンの値を入力します。
  7. [接続のテスト] をクリックし、1 分待ちます。
     ![接続をテストする](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >エージェントの割り当て後、必ず 10 分から 20 分待ってから接続をテストしてください。  登録が完了していない場合、接続は失敗します。
  8. 接続のテストが成功したら、 **[保存]** をクリックします。</br>
   ![成功したテスト](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-who-is-in-scope-for-provisioning"></a>プロビジョニングの対象ユーザーを構成する
これで、Azure AD ECMA Connector Host が Azure AD と通信できるようになったので、プロビジョニングの対象ユーザーの構成に進むことができます。  以下のセクションでは、ユーザーのスコープを指定する方法について説明します。

### <a name="assign-users-to-your-application"></a>アプリケーションにユーザーを割り当てる
Azure AD を使用すると、アプリケーションへの割り当てに基づいて、または特定の属性に基づくフィルター処理を行って、プロビジョニングする必要があるユーザーのスコープを指定できます。 プロビジョニングの対象にする必要があるユーザーを決定し、必要に応じてスコープ ルールを定義します。  詳細については、「[Azure Active Directory のアプリに対するユーザー割り当ての管理](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)」を参照してください。

### <a name="configure-your-attribute-mappings"></a>属性マッピングを構成する
Azure AD のユーザー属性をターゲット アプリケーションの属性にマップする必要があります。 Azure AD プロビジョニング サービスはプロビジョニングの SCIM 標準に依存しているため、表示される属性には SCIM 名前空間があります。 以下の例は、Azure AD の mail と objectId の属性を、アプリケーションの Email と InternalGUID の属性にマップする方法を示しています。 

>[!NOTE]
>既定のマッピングには、属性名のプレースホルダーに対する userPrincipalName が含まれています。 プレースホルダー属性は、アプリケーションで見つかったものに変更する必要があります。 詳細については、「[ソース システムとターゲット システムで一致するユーザー](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)」を参照してください。

|Azure AD の属性名|SCIM の属性名|ターゲット アプリケーションの属性名|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|電子メール|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

#### <a name="configure-attribute-mapping"></a>属性マッピングを構成する
 1. Azure AD ポータルの **[エンタープライズ アプリケーション]** で、 **[プロビジョニング] ページ** をクリックします。
 2. **[作業の開始]** をクリックします。
 3. **[マッピング]** を展開し、 **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** をクリックします
   ![ユーザーをプロビジョニングする](.\media\on-premises-ecma-configure\configure-10.png)
 4. **[新しいマッピングの追加]** をクリックします
   ![マッピングを追加する](.\media\on-premises-ecma-configure\configure-11.png)
 5. ソースとターゲットの属性を指定し、 **[OK]** をクリックします。</br>
   ![属性の編集](.\media\on-premises-ecma-configure\configure-12.png)


ユーザー属性をアプリケーションから Azure AD にマッピングする方法の詳細については、「[チュートリアル - Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングをカスタマイズする](customize-application-attributes.md)」を参照してください。

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>オンデマンドでユーザーをプロビジョニングすることで構成をテストする
構成をテストするために、ユーザーのオンデマンド プロビジョニングを使用できます。  オンデマンドでのユーザーのプロビジョニングについては、[オンデマンド プロビジョニング](provision-on-demand.md)に関するページを参照してください。

 1. シングル サインオン ブレードに移動し、プロビジョニング ブレードに戻ります。 新しいプロビジョニングの概要ブレードで、オンデマンドをクリックします。
 2. [ここで](provision-on-demand.md)説明されているように、オンデマンドで何人かのユーザーのプロビジョニングをテストします。
   ![プロビジョニングをテストする](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>ユーザーのプロビジョニングを開始する
 1. オンデマンド プロビジョニングが成功したら、プロビジョニングの構成ページに戻ります。 スコープが割り当てられたユーザーとグループのみに確実に設定されているようにし、**プロビジョニングを [オン]** にして、 **[保存]** をクリックします。
   ![プロビジョニングの開始](.\media\on-premises-ecma-configure\configure-14.png)
  2.  プロビジョニングが開始されるまで数分待機します (最大 40 分かかる場合があります)。 ここでプロビジョニング サービスのパフォーマンスについてさらに詳しく学習できます。 プロビジョニング ジョブが完了した後、次のセクションで説明されているように、プロビジョニングの状態を [オフ] に変更し、[保存] をクリックできます。 これにより、プロビジョニング サービスは今後実行されなくなります。

### <a name="verify-users-have-been-successfully-provisioned"></a>ユーザーが正常にプロビジョニングされたことを確認する
待機後、データ ソースを調べ、新しいユーザーがプロビジョニングされているかどうかを確認します。
 ![ユーザーがプロビジョニングされていることを確認する](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>デプロイを監視する

1. プロビジョニング ログを使用して、プロビジョニングに成功したユーザーと失敗したユーザーを特定します。
2. Azure Monitor 統合を使用して、カスタム アラート、ダッシュボード、クエリをビルドします。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、こちらを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)