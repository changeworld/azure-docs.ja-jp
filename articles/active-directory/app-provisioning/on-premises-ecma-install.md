---
title: Azure AD ECMA Connector ホストのインストール
description: この記事では、Azure AD ECMA Connector ホストにインストールする方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449592"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA Connector ホストのインストール

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

Azure Active Directory (Azure AD) ECMA Connector Host は、Azure AD Connect Provisioning Agent Package の一部として含まれています。 プロビジョニング エージェントと Azure AD ECMA Connector Host は、2 つの個別の Windows サービスです。 これらは同じコンピューターにデプロイされている 1 つのインストーラーを使用してインストールされます。

このフローでは、Azure AD ECMA Connector Host をインストールして構成するプロセスについて説明します。

 ![インストール フローを示す図。](./media/on-premises-ecma-install/flow-1.png)

インストールと構成の詳細については、以下を参照してください。

   - [Azure AD ECMA Connector ホストの前提条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA コネクタ ホストとプロビジョニング エージェントの構成](on-premises-ecma-configure.md)
   - [Azure AD ECMA コネクタ ホストの汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Azure AD Connect プロビジョニング エージェント パッケージをダウンロードしてインストールする

 1. Azure ポータルにサインインします。
 1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーションを追加]** に移動します。
 1. **オンプレミス プロビジョニング** アプリケーションを検索し、テナント イメージに追加します。
 1. **[プロビジョニング]** ペインに移動します。
 1. **[オンプレミス接続]** を選択します。
 1. エージェント インストーラーをダウンロードします。
 1. Azure AD Connect プロビジョニングのインストーラ (**AADConnectProvisioningAgentSetup.msi**) を実行します。
 1. **[Microsoft Azure AD Connect Provisioning Agent Package]** 画面で、ライセンス条項に同意し、 **[インストール]** を選択します。
 
    ![[Microsoft Azure AD Connect Provisioning Agent Package] 画面。](media/on-premises-ecma-install/install-1.png)</br>
 1. この操作が完了すると、構成ウィザードが起動します。 **[次へ]** を選択します。
 
    ![ようこそ画面のスクリーンショット。](media/on-premises-ecma-install/install-2.png)</br>

 1. **[拡張機能の選択]** 画面で、 **[オンプレミス アプリケーションのプロビジョニング] (Azure AD からアプリへ)** を選択します。 **[次へ]** を選択します。 
 
    ![[拡張機能の選択] を示すスクリーンショット。](media/on-premises-ecma-install/install-3.png)</br>
 1. グローバル管理者アカウントを使用し、Azure AD にサインインします。
 
     ![Azure サインインを示すスクリーンショット。](media/on-premises-ecma-install/install-4.png)</br>
 1. **[エージェントの構成]** 画面で、 **[確認]** を選択します。
 
     ![インストールの確認を示すスクリーンショット。](media/on-premises-ecma-install/install-5.png)</br>
 1. インストールが完了すると、ウィザードの下部にメッセージが表示されます。 **[終了]** を選択します。
 
     ![完了を示すスクリーンショット。](media/on-premises-ecma-install/install-6.png)</br>
 

エージェント パッケージが正常にインストールされたので、Azure AD ECMA Connector Host を構成し、コネクタを作成またはインポートする必要があります。
 
## <a name="next-steps"></a>次のステップ

- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA コネクタ ホスト の構成](on-premises-ecma-configure.md)
- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md)
