---
title: Azure AD Connect プロビジョニング エージェントをインストールする
description: Azure AD Connect プロビジョニング エージェントをインストールする方法や、Azure portal でこれを構成する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 294e66b8b7cef3cec9931b5369562a51b138ab28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448673"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect プロビジョニング エージェントをインストールする
この記事では、Azure Active Directory (Azure AD) Connect プロビジョニング エージェントのインストール プロセスと、それを Azure portal で初期構成する方法について説明します。

>[!IMPORTANT]
>次のインストール手順は、[前提条件](how-to-prerequisites.md)がすべて満たされていることを前提としています。

>[!NOTE]
>この記事では、ウィザードを使用したプロビジョニング エージェントのインストールについて説明します。 コマンド ライン インターフェイス (CLI) を使用した Azure AD Connect プロビジョニング エージェントのインストールの詳細については、「[コマンド ライン インターフェイス (CLI) と PowerShell を使用して Azure AD Connect プロビジョニング エージェントをインストールする](how-to-install-pshell.md)」を参照してください。

詳細と例については、次のビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mR]

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
グループ管理サービス アカウント (gMSA) は、パスワードの自動管理、簡略化されたサービス プリンシパル名 (SPN) の管理、および管理を他の管理者に委任する機能を提供する、マネージド ドメイン アカウントです。 また、この機能は複数のサーバーにも拡張されます。 Azure AD Connect Cloud Sync では、エージェントの実行にグループ管理サービス アカウントを使用することがサポートされ、またこれが推奨されています。 グループの管理されたサービス アカウントの詳細については、「[グループの管理されたサービス アカウント](how-to-prerequisites.md#group-managed-service-accounts)」を参照してください。


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>gMSA を使用するよう既存のエージェントをアップグレードする
インストール中に作成された、管理されたサービス アカウントを使用するように既存のエージェントをアップグレードするには、AADConnectProvisioningAgent.msi を実行して、エージェント サービスを最新バージョンに更新します。 ここで、インストール ウィザードを再度実行し、メッセージが表示されたらアカウントを作成するための資格情報を指定します。

## <a name="install-the-agent"></a>エージェントをインストールする

エージェントをインストールするには、次のようにします。

 1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。
 1. Azure portal にサインインし、 **[Azure Active Directory]** に移動します。
 1. 左側のメニューで **[Azure AD Connect]** を選択します。
 1. **[Manage cloud sync]\(クラウド同期の管理\)**  >  **[すべてのエージェントの確認]** を選択します。
 1. Azure portal から Azure AD Connect プロビジョニング エージェントをダウンロードします。
 
    ![[オンプレミス エージェントのダウンロード] を示すスクリーンショット。](media/how-to-install/install-9.png)</br>
 1. ご契約条件に同意して、 **[ダウンロード]** を選択します。
 1. Azure AD Connect プロビジョニングのインストーラ (AADConnectProvisioningAgentSetup.msi) を実行します。
 1. **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面で、ライセンス条項に同意し、 **[インストール]** を選択します。
 
    ![Microsoft Azure AD Connect のプロビジョニング エージェント パッケージの画面を示すスクリーンショット。](media/how-to-install/install-1.png)</br>
 1. この操作が完了すると、構成ウィザードが起動します。 Azure AD 全体管理者アカウントでサインインします。
 1. **[サービス アカウントの構成]** 画面で、 **[gMSA の作成 (Create gMSA)]** または **[Use custom gMSA (カスタム gMSA を使用する)]** を選択します。 アカウントの作成をエージェントに許可した場合、provAgentgMSA$ という名前が付けられます。 **[Use custom gMSA (カスタム gMSA を使用する)]** を指定した場合、このアカウントを指定するように求められます。
 1. ドメイン管理者の資格情報を入力して、エージェント サービスを実行するために使用されるグループ管理サービス アカウントを作成します。 **[次へ]** を選択します。
  
    ![[gMSA の作成 (Create gMSA)] オプションを示すスクリーンショット。](media/how-to-install/install-12.png)</br>
 1. **[Active Directory の接続]** 画面で、 **[ディレクトリの追加]** を選択します。 次に、Active Directory 管理者アカウントでサインインします。 この操作によってオンプレミス ディレクトリが追加されます。 
 1. 必要に応じて、 **[Select domain controller priority (ドメイン コントローラーの優先順位を選択する)]** を選択し、ドメイン コントローラーの一覧を並べ替えることによって、エージェントによって使用されるドメイン コントローラーの優先順位を管理できます。 **[OK]** を選択します。
 
    ![ドメイン コントローラーの順序を示すスクリーンショット。](media/how-to-install/install-2a.png)</br>
 1. **[次へ]** を選択します。
 
    ![[Active Directory の接続] 画面のスクリーンショット。](media/how-to-install/install-3a.png)</br>
 1. **[エージェントのインストール]** 画面で設定と、作成されるアカウントを確認し、 **[確認]** を選択します。
 
    ![設定の確認を示すスクリーンショット。](media/how-to-install/install-11.png)</br>
 1. この操作が終了すると、「**エージェントの構成が正常に検証されました。** 」と表示されます。 **[終了]** を選択します。
 
    ![[構成が完了しました] という画面を示すスクリーンショット。](media/how-to-install/install-4a.png)</br>
 1. まだ最初の **[Microsoft Azure AD Connect プロビジョニング エージェント パッケージ]** 画面が表示されている場合は、 **[閉じる]** を選択します。

## <a name="verify-agent-installation"></a>エエージェントのインストールを確認する
エージェントの確認は、Azure portal のほか、エージェントが実行されているローカル サーバーで行います。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認
エージェントが Azure で表示されていることを確認するには、次の手順に従います。

 1. Azure portal にサインインします。
 1. 左側の **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。 中央にある **Manage cloud sync］\(クラウド同期の管理\)** を選択します。

    ![Azure portal を示すスクリーンショット。](media/how-to-install/install-6.png)</br>

 1. **[Azure AD Connect cloud sync]\(Azure AD Connect クラウド同期\)** 画面で、 **[すべてのエージェントの確認]** を選択します。

    ![[すべてのエージェントを確認する] オプションを示すスクリーンショット。](media/how-to-install/install-7.png)</br>
 
 1. **[オンプレミスのプロビジョニング エージェント]** 画面に、インストールしたエージェントが表示されます。 該当するエージェントが存在し、 *[アクティブ]* としてマークされていることを確認します。

    ![[オンプレミスのプロビジョニング エージェント] 画面を示すスクリーンショット。](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>ローカル サーバーの場合
エージェントが実行されていることを確認するには、次のようにします。

1. 管理者アカウントでサーバーにサインインします。
1. **[サービス]** を開きます。これを行うには、[サービス] に直接移動するか、 **[スタート]**  >  **[実行]**  >  **[Services.msc]** を選択します。
1. **[サービス]** に **[Microsoft Azure AD Connect エージェント アップデーター]** と **[Microsoft Azure AD Connect プロビジョニング エージェント]** が存在することと、その状態が *[実行中]* になっていることを確認します。

    ![[サービス] 画面を示すスクリーンショット。](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>エージェントはインストールされていますが、ユーザーの同期を開始するには、事前に構成され、有効化されている必要があります。 新しいエージェントを構成するには、「[Azure AD Connect クラウド同期の新しい構成を作成する](how-to-configure.md)」を参照してください。

### <a name="enable-password-writeback-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期でパスワード ライトバックを有効にする 

パスワード ライトバックを使って、SSPR サービスでクラウド同期エージェントを検出するには、`Set-AADCloudSyncPasswordWritebackConfiguration` コマンドレットとテナントのグローバル管理者資格情報を使う必要があります。 

  ```   
   Import-Module "C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll" 
   Set-AADCloudSyncPasswordWritebackConfiguration -Enable $true -Credential $(Get-Credential)
  ```

Azure AD Connect クラウド同期でパスワード ライトバックを使う方法については、次を参照してください。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [Azure AD Connect クラウド同期の新しい構成を作成する](how-to-configure.md)

