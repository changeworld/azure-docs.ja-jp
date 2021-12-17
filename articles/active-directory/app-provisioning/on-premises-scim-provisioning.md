---
title: SCIM 対応アプリへの Azure AD オンプレミス アプリケーションのプロビジョニング
description: この記事では、Azure AD プロビジョニング サービスを使用して、SCIM が有効になっているオンプレミス アプリにユーザーをプロビジョニングする方法について説明します。
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 232735af49cd0f38ff169c224a48b76b0707c97d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261141"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>SCIM 対応アプリへの Azure AD オンプレミス アプリケーションのプロビジョニング

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

Azure Active Directory (Azure AD) プロビジョニング サービスでは、クラウドまたはオンプレミスのアプリケーションにユーザーを自動的にプロビジョニングするために使用できる [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) クライアントがサポートされています。 この記事では、Azure AD プロビジョニング サービスを使用して、SCIM 対応のオンプレミス アプリケーションにユーザーをプロビジョニングする方法について説明します。 SQL をデータ ストアとして使用する非 SCIM のオンプレミス アプリケーションにユーザーをプロビジョニングする場合は、「[Azure AD ECMA Connector Host 汎用 SQL コネクタのチュートリアル](tutorial-ecma-sql-connector.md)」を参照してください。 Dropbox、Atlassian などのクラウド アプリにユーザーをプロビジョニングする場合は、アプリ固有の[チュートリアル](../../active-directory/saas-apps/tutorial-list.md)を確認してください。

![SCIM アーキテクチャを示す図。](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>前提条件
- Azure AD Premium P1 または Premium P2 (または EMS E3 または E5) を持つ Azure AD テナント。 [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- エージェントをインストールするための管理者ロール。 このタスクは 1 回の作業であり、ハイブリッド管理者またはグローバル管理者である Azure アカウントで行う必要があります。 
- クラウドでアプリケーションを構成するための管理者ロール (アプリケーション管理者、クラウド アプリケーション管理者、グローバル管理者、アクセス許可を持つカスタム ロール)。

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>SCIM 対応アプリへのオンプレミス アプリのプロビジョニング
SCIM 対応アプリにユーザーをプロビジョニングするには、次の手順に従います。

 1. [ギャラリー](../../active-directory/manage-apps/add-application-portal.md)から **オンプレミスの SCIM アプリ** を追加します。
 1. 使用するアプリに移動し、 **[プロビジョニング]**  >  **[プロビジョニング エージェントのダウンロード]** の順に選択します。
 1. **[オンプレミスの接続]** を選択し、プロビジョニング エージェントをダウンロードします。
 1. SCIM エンドポイントがホストされている仮想マシンまたはサーバーにエージェントをコピーします。
 1. プロビジョニング エージェント インストーラーを開き、サービス使用条件に同意して、 **[インストール]** を選択します。
 1. プロビジョニング エージェント ウィザードを開き、有効にする拡張機能の入力を求めるメッセージが表示されたら、 **[オンプレミスのプロビジョニング]** を選択します。
 1. 承認を求められたら、Azure AD 管理者の資格情報を入力します。 ハイブリッド管理者またはグローバル管理者が必要です。
 1. **[確認]** を選択して、インストールが成功したことを確認します。
 1. 使用するアプリケーションに戻って、 **[オンプレミスの接続]** を選択します。
 1. インストールしたエージェントをドロップダウン リストから選択し、 **[エージェントの割り当て]** を選択します。
 1. 10 分待つか、ご使用のサーバーまたは VM の Azure AD Connect プロビジョニング エージェント サービスを再起動します。
 1. **[テナントの URL]** ボックスに SCIM エンドポイントの URL を指定します。 たとえば https://localhost:8585/scim です。
 
     ![エージェントの割り当てを示すスクリーンショット。](./media/on-premises-scim-provisioning/scim-2.png)
 1. **[テスト接続]** を選択し、資格情報を保存します。
 1. アプリケーションに必要な[属性マッピング](customize-application-attributes.md)または[スコープ](define-conditional-rules-for-provisioning-user-accounts.md)規則を構成します。
 1. [ユーザーとグループ](../../active-directory/manage-apps/add-application-portal-assign-users.md)をアプリケーションに割り当て、スコープにユーザー を追加します。
 1. [オンデマンド](provision-on-demand.md)で少数のユーザーのプロビジョニングをテストします。
 1. さらに多くのユーザーをアプリケーションに割り当てて、スコープに追加します。
 1. **[プロビジョニング]** ペインに移動し、 **[プロビジョニングの開始]** を選択します。
 1. [プロビジョニング ログ](../../active-directory/reports-monitoring/concept-provisioning-logs.md)を使用して監視します。

## <a name="additional-requirements"></a>その他の要件
* [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) の実装が、[Azure AD の SCIM 要件](use-scim-to-provision-users-and-groups.md)を満たしていることを確認します。
  
  Azure AD では、開発者が SCIM の実装をブートストラップするために使用できるオープンソースの[参照コード](https://github.com/AzureAD/SCIMReferenceCode/wiki)が提供されています。 コードは現状有姿です。
* /schemas エンドポイントをサポートして、Azure portal で必要な構成を減らします。 

## <a name="next-steps"></a>次のステップ

- [アプリ プロビジョニング](user-provisioning.md)
- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md)
- [チュートリアル: ECMA Connector Host の汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)
