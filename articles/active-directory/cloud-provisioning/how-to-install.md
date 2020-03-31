---
title: Azure AD Connect クラウド プロビジョニング エージェントのインストール
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230815"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect クラウド プロビジョニング エージェントのインストール
このドキュメントでは、Azure Active Directory (Azure AD) Connect プロビジョニング エージェントのインストール プロセスと、それを Azure portal で最初に構成する方法について説明します。

>[!IMPORTANT]
>次のインストール手順は、[前提条件](how-to-prerequisites.md)がすべて満たされていることを前提としています。

Azure AD Connect プロビジョニングのインストールと構成は、次の手順で行います。
    
- [エージェントをインストールする](#install-the-agent)
- [エージェントのインストールを検証する](#verify-agent-installation)


## <a name="install-the-agent"></a>エージェントをインストールする
エージェントをインストールするには、次の手順に従います。

1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。
1. Azure Portal にアクセスします。 左側の **[Azure Active Directory]** を選択します。
1. **[プロビジョニングの管理 (プレビュー)]**  >  **[すべてのエージェントの確認]** を選択します。
1. Azure portal から Azure AD Connect プロビジョニング エージェントをダウンロードします。

   ![オンプレミスのエージェントをダウンロードします](media/how-to-install/install9.png)</br>
1. Azure AD Connect プロビジョニングのインストーラ (AADConnectProvisioningAgent.Installer) を実行します。
1. **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面で、ライセンス条項に同意し、 **[インストール]** を選択します。

   ![[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ] 画面](media/how-to-install/install1.png)</br>

1. この操作が完了すると、構成ウィザードが起動します。 Azure AD 全体管理者アカウントでサインインします。
1. **[Active Directory の接続]** 画面で、 **[ディレクトリの追加]** を選択します。 次に、Active Directory 管理者アカウントでサインインします。 この操作によってオンプレミス ディレクトリが追加されます。 **[次へ]** を選択します。

   ![[Active Directory の接続] 画面](media/how-to-install/install3.png)</br>

1. **[構成の完了]** 画面で、 **[確認]** を選択します。 この操作によって、エージェントが登録されて再起動されます。

   ![[構成の完了] 画面](media/how-to-install/install4.png)</br>

1. この操作が終了すると、「**エージェントの構成が正常に検証されました。** 」という通知が表示されます。 **[終了]** を選択します。

   ![[終了] ボタン](media/how-to-install/install5.png)</br>
1. まだ最初の **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面が表示されている場合は、 **[閉じる]** を選択します。

## <a name="verify-agent-installation"></a>エエージェントのインストールを確認する
エージェントの確認は、Azure portal のほか、エージェントが実行されているローカル サーバーで行います。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認
エージェントが Azure で表示されていることを確認するには、次の手順に従います。

1. Azure portal にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。 中央の **[プロビジョニングの管理 (プレビュー)]** を選択します。

   ![Azure portal](media/how-to-install/install6.png)</br>

1.  **[Azure AD のプロビジョニング (プレビュー)]** 画面で **[すべてのエージェントの確認]** を選択します。

    ![[すべてのエージェントの確認] オプション](media/how-to-install/install7.png)</br>
 
1. **[オンプレミスのプロビジョニング エージェント]** 画面に、インストールしたエージェントが表示されます。 該当するエージェントが存在し、 *[アクティブ]* としてマークされていることを確認します。

   ![[オンプレミスのプロビジョニング エージェント] 画面](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>ポートを確認する
Azure がポート 443 でリッスンしていて、エージェントがそれと通信できることを確認するには、次の手順に従います。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

このテストでは、エージェントがポート 443 を介して Azure と通信できることを確認します。 ブラウザーを開き、エージェントがインストールされているサーバーから前の URL に移動します。

![ポートの到達可能性の確認](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>ローカル サーバーの場合
エージェントが実行されていることを確認するには、次の手順に従います。

1.  管理者アカウントでサーバーにサインインします。
1.  **[サービス]** を開きます。これには、そこに直接移動するか、**スタート** >  **[ファイル名を指定して実行]**  > 「**Services.msc**」に移動します。
1.  **[サービス]** に **[Microsoft Azure AD Connect エージェント アップデーター]** と **[Microsoft Azure AD Connect プロビジョニング エージェント]** が存在することと、その状態が *[実行中]* になっていることを確認します。

    ![[サービス] 画面](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>エージェントはインストールされていますが、それはユーザーの同期を開始する前に構成され、有効になっている必要があります。 新しいエージェントを構成するには、「[Azure AD Connect クラウドベース プロビジョニングの新しい構成を作成する](how-to-configure.md)」を参照してください。



## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
 
