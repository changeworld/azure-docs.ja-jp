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
ms.openlocfilehash: e12ce0d979b323f10104d345f86b77618e094ba6
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570378"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA Connector ホストのインストール

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

AZURE AD ECMA Connector ホストが含まれており、Azure AD Connect プロビジョニング エージェント パッケージの一部です。  プロビジョニング エージェントと AZURE AD ECMA Connector ホストは、1 つのインストーラーを使用してインストールされ、同じコンピューターにデプロイされる 2 つの個別の Windows サービスです。 

Azure AD ECMA コネクタ ホストのインストールと構成はプロセスです。  プロセスを進めるには、以下のフローを使用します。

 ![インストールのフロー](./media/on-premises-ecma-install/flow-1.png)  

インストールと構成の詳細については、以下を参照してください。
   - [Azure AD ECMA Connector ホストの前提条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA コネクタ ホストとプロビジョニング エージェントの構成](on-premises-ecma-configure.md)
    - [Azure AD ECMA コネクタ ホストの汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)


## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Azure AD Connect プロビジョニング エージェント パッケージをダウンロードしてインストールする

 1. Azure portal にサインインする
 2. [エンタープライズ アプリケーション] > [新しいアプリケーションを追加] に移動します。
 3. "オンプレミス プロビジョニング" アプリケーションを検索し、テナント イメージに追加します
 4. プロビジョニング ブレードに異動する
 5. オンプレミスの接続をクリックする
 6.  エージェント インストーラーをダウンロードする
 7. Azure AD Connect プロビジョニングのインストーラ (AADConnectProvisioningAgentSetup.msi) を実行します。
 8. **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面で、ライセンス条項に同意し、 **[インストール]** を選択します。
   ![[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ] 画面](media/on-premises-ecma-install/install-1.png)</br>
 9. この操作が完了すると、構成ウィザードが起動します。 **[次へ]** をクリックします。
   ![[ようこそ] 画面](media/on-premises-ecma-install/install-2.png)</br>
 10. **[拡張機能 の選択]** 画面 で、 **[オンプレミス アプリケーションのプロビジョニング] (Azure AD)** を選択し、 **[次へ]** を クリックします。 
   ![拡張機能 の選択](media/on-premises-ecma-install/install-3.png)</br>
 12. グローバル管理者アカウントを使用し、Azure AD にサインインします。
     ![Azure サインイン](media/on-premises-ecma-install/install-4.png)</br>
 13.  **[エージェントの構成]** 画面で、 **[確認]** を クリックします。
     ![インストールの確認](media/on-premises-ecma-install/install-5.png)</br>
 14.  インストールが完了すると、ウィザードの下部にメッセージが表示されます。  **[完了]** をクリックします。
     ![[完了] をクリックする](media/on-premises-ecma-install/install-6.png)</br>
 15. **[閉じる]** をクリックします。

エージェント パッケージが正常にインストールされたので、Azure AD ECMA Connector ホストを構成し、コネクタを作成またはインポートする必要があります。  
## <a name="next-steps"></a>次の手順


- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA コネクタ ホスト の構成](on-premises-ecma-configure.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)
