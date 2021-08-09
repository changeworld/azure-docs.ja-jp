---
title: SCIM 対応アプリへの Azure AD オンプレミス アプリケーションのプロビジョニング
description: この記事では、SCIM 対応アプリにオンプレミス アプリをプロビジョニングする方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: bf0b13bf1e2de31d15599bff2aa8750396f021ac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570040"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>SCIM 対応アプリへの Azure AD オンプレミス アプリケーションのプロビジョニング

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

Azure AD プロビジョニング サービスでは、クラウドまたはオンプレミスのアプリケーションにユーザーを自動的にプロビジョニングするために使用できる [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) クライアントがサポートされています。 このドキュメントでは、Azure AD プロビジョニング サービスを使用して、SCIM が有効になっているオンプレミス アプリケーションにユーザーをプロビジョニングする方法について説明します。 非 AD LDAP ディレクトリや SQL DB など、SCIM 以外のオンプレミス アプリケーションにユーザーをプロビジョニングする場合は、こちらを参照してください (作成する必要がある新しいドキュメントへのリンク)。 DropBox、Atlassian などのクラウド アプリにユーザーをプロビジョニングする場合は、アプリ固有の[チュートリアル](../../active-directory/saas-apps/tutorial-list.md)を確認してください。 

![アーキテクチャ](./media/on-premises-scim-provisioning/scim-4.png)


## <a name="pre-requisites"></a>前提条件
- Azure AD Premium P1 または Premium P2 (または EMS E3 または E5) を持つ Azure AD テナント。 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- エージェントをインストールするための管理者ロール。  これは 1 回の作業であり、ハイブリッド管理者またはグローバル管理者である Azure アカウントである必要があります。 
- クラウドでアプリケーションを構成するための管理者ロール (アプリケーション管理者、クラウド アプリケーション管理者、グローバル管理者、アクセス許可を持つカスタム ロール)

## <a name="steps-for-on-premises-app-provisioning-to-scim-enabled-apps"></a>SCIM 対応アプリへのオンプレミス アプリケーションのプロビジョニングの手順
SCIM 対応アプリにプロビジョニングするには、次の手順に従います。 

 1. [ギャラリー](../../active-directory/manage-apps/add-application-portal.md)から "エージェントベースの SCIM プロビジョニング" アプリを追加します。
 2. [アプリ]、[プロビジョニング] の順に移動し、プロビジョニング エージェントをダウンロードします。
 3. [オンプレミス接続] をクリックし、プロビジョニング エージェントをダウンロードします。
 4. SCIM エンドポイントがホストされている仮想マシンまたはサーバーにエージェントをコピーします。
 5. プロビジョニング エージェント インストーラーを開き、サービス利用規約に同意して、[インストール] をクリックします。
 6. プロビジョニング エージェント ウィザードを開き、有効にする拡張機能の入力を求めるメッセージが表示されたら、オンプレミスのプロビジョニングを選択します。
 7. 認可を求めるメッセージが表示されたら、Azure AD 管理者の資格情報を入力します (ハイブリッド管理者またはグローバル管理者が必要)。
 8. [確認] をクリックして、インストールが成功したことを確認します。
 9. [アプリケーション]、[オンプレミス接続] の順に戻ります。
 10. インストールしたエージェントをドロップダウン リストから選択し、[エージェントの割り当て] をクリックします。
 11. 10 分待つか、サーバーまたは VM の Azure AD Connect プロビジョニング エージェント サービスを再起動します。
 12. [テナント URL] フィールドに SCIM エンドポイントの URL を指定します (例: Https://localhost:8585/scim) )
     ![エージェントの割り当て](./media/on-premises-scim-provisioning/scim-2.png)
 13. [テスト接続] をクリックし、資格情報を保存します。
 14. アプリケーションに必要な[属性マッピング](customize-application-attributes.md)または[スコープ](define-conditional-rules-for-provisioning-user-accounts.md)規則を構成します。  
 15. [ユーザーとグループ](../../active-directory/manage-apps/add-application-portal-assign-users.md)をアプリケーションに割り当て、スコープにユーザー を追加します。
 16. [オンデマンド](provision-on-demand.md)で少数のユーザーのプロビジョニングをテストします。
 17. ユーザーをアプリケーションに割り当て、スコープにユーザーを追加します。
 18. [プロビジョニング] ブレードに移動し、[プロビジョニングの開始] をクリックします。
 19. [プロビジョニング ログ](../../active-directory/reports-monitoring/concept-provisioning-logs.md)を使用して監視します。
 

## <a name="things-to-be-aware-of"></a>注意事項
* [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) の実装が、[Azure AD の SCIM 要件](use-scim-to-provision-users-and-groups.md)を満たしていることを確認します。
  * Azure AD から、開発者が SCIM の実装をブートストラップするために使用できるオープンソースの[参照コード](https://github.com/AzureAD/SCIMReferenceCode/wiki)が提供されます (コードはそのままです)
* /schemaDiscovery エンドポイントをサポートして、Azure portal で必要な構成を減らします。 

## <a name="next-steps"></a>次の手順

- [アプリ プロビジョニング](user-provisioning.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)
- [チュートリアル: ECMA Connector Host 汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)
