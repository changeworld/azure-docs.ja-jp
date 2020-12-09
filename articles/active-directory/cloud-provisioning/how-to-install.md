---
title: Azure AD Connect クラウド プロビジョニング エージェントのインストール
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4ba11dabc8e805f3604afc2fc4388840ac67cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348536"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect クラウド プロビジョニング エージェントのインストール
このドキュメントでは、Azure Active Directory (Azure AD) Connect プロビジョニング エージェントのインストール プロセスと、それを Azure portal で最初に構成する方法について説明します。

>[!IMPORTANT]
>次のインストール手順は、[前提条件](how-to-prerequisites.md)がすべて満たされていることを前提としています。

Azure AD Connect プロビジョニングのインストールと構成は、次の手順で行います。

- [グループ管理サービス アカウント](#group-managed-service-accounts) 
- [エージェントをインストールする](#install-the-agent)
- [エージェントのインストールを検証する](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
グループ管理サービス アカウントは、パスワードの自動管理、簡略化されたサービス プリンシパル名 (SPN) の管理、および管理を他の管理者に委任する機能を提供し、またこの機能を複数のサーバーに拡張する、マネージド ドメイン アカウントです。  Azure AD Connect Cloud Sync では、エージェントの実行にグループ管理サービス アカウントを使用することがサポートされ、またこれが推奨されています。  gMSA の詳細については、[グループ管理サービス アカウント](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)に関するページを参照してください 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>gMSA アカウントを使用するよう既存のエージェントをアップグレードする
インストール中に作成された gMSA アカウントを使用するように既存のエージェントをアップグレードするには、AADConnectProvisioningAgent.msi を実行して、エージェント サービスを最新バージョンに更新するだけです。  これにより、サービスが最新バージョンにアップグレードされます。  ここで、インストール ウィザードを再度実行し、メッセージが表示されたらアカウントを作成するための資格情報を指定します。



## <a name="install-the-agent"></a>エージェントをインストールする
エージェントをインストールするには、次の手順に従います。

 1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。
 2. Azure portal にサインインし、 **[Azure Active Directory]** に移動します。
 3. 左側のメニューで、 **[Azure AD Connect]** を選択します。
 4. **[プロビジョニングの管理 (プレビュー)]**  >  **[すべてのエージェントの確認]** を選択します。
 5. Azure portal から Azure AD Connect プロビジョニング エージェントをダウンロードします。
   ![オンプレミスのエージェントをダウンロードします](media/how-to-install/install-9.png)</br>
 6. Azure AD Connect プロビジョニングのインストーラ (AADConnectProvisioningAgent.msi) を実行します。
 7. **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面で、ライセンス条項に同意し、 **[インストール]** を選択します。
   ![[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ] 画面](media/how-to-install/install-1.png)</br>
 8. この操作が完了すると、構成ウィザードが起動します。 Azure AD 全体管理者アカウントでサインインします。
 9. **サービス アカウントの構成画面** で、 **[create gMSA]\(gMSA の作成\)** または **[Use custom gMSA]\(カスタム gMSA を使用する\)** を選択します。  アカウントの作成をエージェントに許可した場合、provAgentgMSA$ という名前が付けられます。 **[Use custom gMSA]\(カスタム gMSA を使用する\)** を指定した場合、このアカウントを指定するように求められます。
 10. ドメイン管理者の資格情報を入力して、エージェント サービスを実行するために使用されるグループ管理サービス アカウントを作成します。 **[次へ]** をクリックします。  
   ![gMSA の作成](media/how-to-install/install-12.png)</br>
 11. **[Active Directory の接続]** 画面で、 **[ディレクトリの追加]** を選択します。 次に、Active Directory 管理者アカウントでサインインします。 この操作によってオンプレミス ディレクトリが追加されます。 
 12. 必要に応じて、 **[Select domain controller priority]\(ドメイン コントローラーの優先順位を選択する\)** を選択し、ドメイン コントローラーの一覧を並べ替えることによって、エージェントによって使用されるドメイン コントローラーの優先順位を管理できます。   **[OK]** をクリックします。
  ![ドメイン コントローラーの並び替え](media/how-to-install/install-2a.png)</br>
 13. **[次へ]** を選択します。
  ![[Active Directory の接続] 画面](media/how-to-install/install-3a.png)</br>
 14.  **[エージェントのインストール]** 画面で設定を確認し、作成されるアカウントをクリックして、 **[確認]** をクリックします。
  ![設定の確認](media/how-to-install/install-11.png)</br>
 15. この操作が終了すると、「**エージェントの構成が正常に検証されました。** 」と表示されます。 **[終了]** を選択します。
  ![[構成の完了] 画面](media/how-to-install/install-4a.png)</br>
1. まだ最初の **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面が表示されている場合は、 **[閉じる]** を選択します。

## <a name="verify-agent-installation"></a>エエージェントのインストールを確認する
エージェントの確認は、Azure portal のほか、エージェントが実行されているローカル サーバーで行います。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認
エージェントが Azure で表示されていることを確認するには、次の手順に従います。

1. Azure portal にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。 中央の **[プロビジョニングの管理 (プレビュー)]** を選択します。

   ![Azure portal](media/how-to-install/install-6.png)</br>

1.  **[Azure AD のプロビジョニング (プレビュー)]** 画面で **[すべてのエージェントの確認]** を選択します。

    ![[すべてのエージェントの確認] オプション](media/how-to-install/install-7.png)</br>
 
1. **[オンプレミスのプロビジョニング エージェント]** 画面に、インストールしたエージェントが表示されます。 該当するエージェントが存在し、 *[アクティブ]* としてマークされていることを確認します。

   ![[オンプレミスのプロビジョニング エージェント] 画面](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>ローカル サーバーの場合
エージェントが実行されていることを確認するには、次の手順に従います。

1.  管理者アカウントでサーバーにサインインします。
1.  **[サービス]** を開きます。これには、そこに直接移動するか、**スタート** >  **[ファイル名を指定して実行]**  > 「**Services.msc**」に移動します。
1.  **[サービス]** に **[Microsoft Azure AD Connect エージェント アップデーター]** と **[Microsoft Azure AD Connect プロビジョニング エージェント]** が存在することと、その状態が *[実行中]* になっていることを確認します。

    ![[サービス] 画面](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>エージェントはインストールされていますが、それはユーザーの同期を開始する前に構成され、有効になっている必要があります。 新しいエージェントを構成するには、「[Azure AD Connect クラウドベース プロビジョニングの新しい構成を作成する](how-to-configure.md)」を参照してください。




## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
