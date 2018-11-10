---
title: Azure Site Recovery を使用した VMware VM の Azure へのディザスター リカバリーのためのソース環境を設定する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にディザスター リカバリーするためのオンプレミスの環境を設定する方法について説明します。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 44b1cac461fa49b4bea19b7cf98f6038eb264492
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230728"
---
# <a name="set-up-the-source-environment-for-disaster-recovery-of-vmware-vms-to-azure"></a>VMware VM の Azure へのディザスター リカバリーのためのソース環境を設定する

この記事では、VMware VM を Azure にレプリケートするためのオンプレミスのソース環境を設定する方法について説明します。 これには、レプリケーション シナリオを選択するための手順、オンプレミス コンピューターを Site Recovery の構成サーバーとして設定するための手順、およびオンプレミスのVM を自動的に検出するための手順が含まれます。 

## <a name="prerequisites"></a>前提条件

この記事は、既に以下の操作を行っていることを前提としています。

- [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) を使用して、デプロイを計画した。 これにより、毎日のデータ変化率に基づいて十分な帯域幅を割り当て、必要な復旧ポイント目標 (RPO) を満たすことができます。
- [Azure Portal](http://portal.azure.com) で[リソースを設定する](tutorial-prepare-azure.md)。
- [オンプレミスの VMware 設定する](vmware-azure-tutorial-prepare-on-premises.md) (自動検出のための専用アカウントを含む)。

## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. **[Recovery Services コンテナー]** で、コンテナー名を選択します。 このシナリオでは、**ContosoVMVault** を使います。
2. **[作業の開始]** で、[Site Recovery] を選択します。 次に、**[インフラストラクチャの準備]** を選択します。
3. **[保護の目標]** > **[マシンのある場所]** で、**[オンプレミス]** を選びます。
4. **[マシンをどこにレプリケートしますか]** で、**[To Azure]\(Azure\)** を選びます。
5. **[マシンは仮想化されていますか]** で、**[はい (VMware vSphere ハイパーバイザー の場合)]** を選びます。 **[OK]** をクリックします。

## <a name="set-up-the-configuration-server"></a>構成サーバーを設定する

Open Virtualization Application (OVA) テンプレートを使用し、構成サーバーをオンプレミスの VMware VM として設定できます。 VMware VM にインストールされるコンポーネントについては、[こちら](concepts-vmware-to-azure-architecture.md)をご覧ください。

1. 構成サーバー デプロイの[前提条件](vmware-azure-deploy-configuration-server.md#prerequisites)を確認します。
2. デプロイに必要な[容量を確認](vmware-azure-deploy-configuration-server.md#capacity-planning)します。
3. OVA テンプレートを[ダウンロード](vmware-azure-deploy-configuration-server.md#download-the-template)して[インポート](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware)し、構成サーバーを実行するオンプレミス VMware VM を設定します。 テンプレートに付属するライセンスは評価版ライセンスとなり、180 日間有効です。 この期間が経過した後は、入手済みのライセンスを使用して Windows のライセンス認証をユーザーが行う必要があります。
4. VMware VM を有効にし、Recovery Services コンテナーに[登録](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)します。

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外

### <a name="if-antivirus-software-is-active-on-source-machine"></a>ウイルス対策ソフトウェアがソース マシンでアクティブな場合

ソース マシンにアクティブなウイルス対策ソフトウェアがある場合、インストール フォルダーを除外する必要があります。 そのため、スムーズなレプリケーションのために、フォルダー *C:\ProgramData\ASR\agent* を除外します。

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>ウイルス対策ソフトウェアが構成サーバーでアクティブな場合

スムーズなレプリケーションと接続の問題を回避するために、ウイルス対策ソフトウェアから次のフォルダーを除外します

 1. C:\Program Files\Microsoft Azure Recovery Services Agent
 2. C:\Program Files\Microsoft Azure Site Recovery Provider
 3. C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
 4. C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
 5. C:\thirdparty
 6. C:\Temp
 7. C:\strawberry
 8. C:\ProgramData\MySQL
 9. C:\Program Files (x86)\MySQL
 10. C:\ProgramData\ASR
 11. C:\ProgramData\Microsoft Azure Site Recovery
 12. C:\ProgramData\ASRLogs
 13. C:\ProgramData\ASRSetupLogs
 14. C:\ProgramData\LogUploadServiceLogs
 15. C:\inetpub
 16. ASR サーバー インストール ディレクトリ。 例: E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>ウイルス対策ソフトウェアがスケールアウト プロセス サーバー/マスター ターゲットでアクティブな場合

ウイルス対策ソフトウェアから次のフォルダーを除外します

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. ASR 負荷分散プロセス サーバー インストール ディレクトリ。例: C:\Program Files (x86)\Microsoft Azure Site Recovery

## <a name="common-issues"></a>一般的な問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>次の手順
Azure で[ターゲット環境を設定](./vmware-azure-set-up-target.md)します。
