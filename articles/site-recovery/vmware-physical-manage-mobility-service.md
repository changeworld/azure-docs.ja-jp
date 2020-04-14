---
title: Azure Site Recovery を使用して VMware または物理サーバーのモビリティ エージェントを管理する
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするために、モビリティ サービス エージェントを管理します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228851"
---
# <a name="manage-the-mobility-agent"></a>モビリティ エージェントを管理する 

Azure への VMware VM と物理サーバーのディザスター リカバリーに Azure Site Recovery を使うときは、ご自身のサーバーでモビリティ エージェントを設定します。 モビリティ エージェントは、保護されたマシン、構成サーバー/スケールアウト プロセス サーバーの間の通信を調整し、データのレプリケーションを管理します。 この記事は、デプロイ後にモビリティ エージェントを管理するための一般的なタスクをまとめたものです。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Azure portal からモビリティ サービスを更新する

1. 保護されたマシン上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、マスター ターゲット サーバーを必ず更新します。
2. ポータルでコンテナーを開き、 **[レプリケートされたアイテム]** を開きます。
3. 構成サーバーが最新版の場合、"Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。 クリックしてインストールしてください" という通知が表示されます。

     ![[レプリケートされたアイテム] ウィンドウ](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 通知をクリックし、 **[エージェントの更新]** でモビリティ サービスをアップグレードするマシンを選択します。 次に、 **[OK]** をクリックします

     ![[レプリケートされたアイテム] VM リスト](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 選択したマシンごとに、モビリティ サービスの更新ジョブが開始されます。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows サーバー上の PowerShell スクリプトを介してモビリティ サービスを更新する

保護されたマシン上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、マスター ターゲット サーバーを必ず更新します。

次のスクリプトを使用して、PowerShell コマンドレットを介してサーバー上のモビリティ サービスをアップグレードします

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>保護されるサーバーのそれぞれでモビリティ サービスを手動で更新する

1. 保護されたマシン上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、マスター ターゲット サーバーを必ず更新します。

2. サーバーのオペレーティング システムに基づいて[エージェントを見つけます](vmware-physical-mobility-service-overview.md#locate-installer-files)。

>[!IMPORTANT]
> Azure IaaS VM を Azure リージョン間でレプリケートしている場合は、この方法を使用しないでください。 使用可能なすべてのオプションの詳細については、[ガイダンス](azure-to-azure-autoupdate.md)を参照してください。

3. 保護されたマシンにインストール ファイルをコピーして実行することにより、モビリティ エージェントを更新します。

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>モビリティ サービスのプッシュ インストールに使用されるアカウントを更新する

Site Recovery をデプロイしたとき、モビリティ サービスのプッシュ インストールを有効にする目的で、アカウントを指定しました。このアカウントは、マシンで複製が有効になっているとき、マシンにアクセスし、サービスをインストールするために Site Recovery プロセス サーバーによって使用されます。 このアカウントの資格情報を更新する場合、[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従ってください。

## <a name="uninstall-mobility-service"></a>モビリティ サービスをアンインストールする

### <a name="on-a-windows-machine"></a>Windows マシンの場合

UI またはコマンド プロンプトからアンインストールします。

- **UI から**:マシンの [コントロール パネル] で **[プログラム]** を選択します。 **[Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー]**  > 、 **[アンインストール]** の順に選択します。
- **コマンド プロンプトから**:マシンの管理者としてコマンド プロンプト ウィンドウを開きます。 次のコマンドを実行します。 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux マシンの場合
1. Linux マシンで**ルート** ユーザーとしてサインインします。
2. ターミナルで /user/local/ASR に移動します。
3. 次のコマンドを実行します。
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>ソース マシンに Site Recovery VSS プロバイダーをインストールする

アプリケーション整合性ポイントを生成するには、ソース マシンに Azure Site Recovery VSS プロバイダーが必要です。 プロバイダーのインストールがプッシュ インストールで成功しなかった場合は、以下のガイドラインに従って手動でインストールしてください。

1. 管理者の cmd ウィンドウを開きます。
2. モビリティ サービスのインストール場所に移動します。 (例 - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. InMageVSSProvider_Uninstall.cmd スクリプトを実行します。 これにより、サービスが既に存在する場合はアンインストールされます。
4. InMageVSSProvider_Install.cmd スクリプトを実行して、VSS プロバイダーを手動でインストールします。

## <a name="next-steps"></a>次のステップ

- [VMware VM のディザスター リカバリーを設定する](vmware-azure-tutorial.md)
- [物理サーバーのディザスター リカバリーを設定する](physical-azure-disaster-recovery.md)
