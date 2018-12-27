---
title: Azure Site Recovery を使用した VMware VM の Azure へのディザスター リカバリーのためのソース環境と構成サーバーを設定する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にディザスター リカバリーするためのオンプレミスの環境と構成サーバーを設定する方法について説明します。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566314"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>ソース環境と構成サーバーを設定する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM を Azure にディザスター リカバリーするためのオンプレミスのソース環境を設定する方法について説明します。 これには、オンプレミスのマシンを Site Recovery の構成サーバーとして設定するための手順、およびオンプレミスの VM を自動的に検出するための手順が含まれます。 

## <a name="prerequisites"></a>前提条件

始める前に、以下の操作を実行してください。

- [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) を使用してディザスター リカバリーのデプロイのプランを作成します。 Planner を使用すると、ディザスター リカバリーと組織の復旧ポイントの目標 (RPO) にとって十分なリソースと帯域幅があることを確認できます。
- [Azure portal](http://portal.azure.com) で [Azure リソースを設定](tutorial-prepare-azure.md)します。
- [オンプレミスの VMware リソースを設定](vmware-azure-tutorial-prepare-on-premises.md)します。これには、オンプレミスの VMware VM の自動検出のための専用アカウントも含まれます。
- 構成サーバーの展開と管理に関する[一般的な質問を確認](vmware-azure-common-questions.md#configuration-server)できます。


## <a name="choose-protection-goals"></a>保護の目標を選択する

1. **[Recovery Services コンテナー]** で、コンテナー名を選択します。 
2. **[作業の開始]** で、[Site Recovery] を選択します。 次に、**[インフラストラクチャの準備]** を選択します。
3. **[保護の目標]** > **[マシンのある場所]** で、**[オンプレミス]** を選びます。
4. **[マシンをどこにレプリケートしますか]** で、**[To Azure]\(Azure\)** を選びます。
5. **[マシンは仮想化されていますか]** で、**[はい (VMware vSphere ハイパーバイザー の場合)]** を選びます。 **[OK]** をクリックします。

## <a name="about-the-configuration-server"></a>構成サーバーについて

VMware VM のディザスター リカバリーを Azure に設定するには、オンプレミスの構成サーバーを展開します。

- 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。
- VMware VM として構成サーバーを展開します。
- Site Recovery に用意されている OVA テンプレートを Azure portal からダウンロードして vCenter Server にインポートし、構成サーバー VM を設定します。
- 構成サーバーのコンポーネントとプロセスの[詳細をご確認ください](vmware-azure-architecture.md)。


>[!NOTE]
オンプレミスの物理マシンのディザスター リカバリーの構成サーバーを Azure にデプロイする場合には、これらの手順を使用しないでください。 このシナリオの場合には、[こちらの記事](physical-azure-set-up-source.md)に従ってください。


## <a name="before-you-deploy-the-configuration-server"></a>構成サーバーを展開する前に

OVA テンプレートを使用して VMware VM として構成サーバーをインストールする場合、前提条件をすべて満たした状態で VM がインストールされます。 前提条件を確認するには、次の記事を参照してください。

- 構成サーバーのハードウェア、ソフトウェア、容量に関する要件についての[詳細](vmware-azure-configuration-server-requirements.md)をご確認ください。
- 複数の VMware VM をレプリケートする場合、VMware レプリケーションに関する[容量プラン作成の考慮事項](site-recovery-plan-capacity-vmware.md)を確認し、[Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) ツールを実行する必要があります。
- OVA テンプレートの Windows ライセンスは 180 日間有効な評価版ライセンスです。 その後は、有効なライセンスを使用して Windows をアクティブ化する必要があります。 
- OVA テンプレートを使用すると、構成サーバーを VMware VM として簡単に設定できます。 何らかの理由によりテンプレートを使用しないで VMware VM を設定する必要がある場合には、前提条件を確認し、[こちらの記事](physical-azure-set-up-source.md)の手動による手順に従ってください。
- Azure アカウントには、Azure AD アプリを作成するためのアクセス許可が必要です。


>[!IMPORTANT]
この記事で説明されているとおりに構成サーバーを展開しなければなりません。 既存の構成サーバーのコピーまたは複製はサポートされていません。

### <a name="set-up-azure-account-permissions"></a>Azure アカウントのアクセス許可を設定する

テナント/グローバル管理者は、Azure AD アプリを作成するためのアクセス許可をアカウントに割り当てることか、(そのアクセス許可を持つ) アプリケーション開発者の役割をアカウントに割り当てることのいずれかができます。


テナント/グローバル管理者は、次のようにアカウントにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]** > **[ユーザー]** > **[ユーザー設定]** の順に移動します。
2. 管理者は、**[アプリの登録]** を **[はい]** に設定する必要があります。

 > [!NOTE]
 > これは、重要ではない既定の設定です。 [詳細情報](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

またはテナント/グローバル管理者には、アカウントに役割を割り当てるアクセス許可があります。 [詳細情報](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

 


## <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

1. コンテナーで、**[インフラストラクチャの準備]** > **[ソース]** の順に移動します。
2. **[ソースの準備]** で **[+ 構成サーバー]** を選びます。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[VMware の構成サーバー]** が表示されていることを確認します。
4. 構成サーバー用の Open Virtualization Application (OVA) テンプレートをダウンロードします。

  > [!TIP]
>構成サーバー テンプレートの最新バージョンは、[Microsoft ダウンロード センター](https://aka.ms/asrconfigurationserver)からダウンロードすることもできます。


## <a name="import-the-ova-template-in-vmware"></a>VMware に OVA テンプレートをインポートする

1. VMWare vSphere Client を使用して、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選び、Deploy OVF Template (OVF テンプレートのデプロイ) ウィザードを起動します。

     ![OVF テンプレート](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、**[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. **[Select storage]\(ストレージの選択\)** で、パフォーマンスを最大にするために、**[Select virtual disk format]\(仮想ディスクの形式の選択\)** の **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選びます。
7. ウィザードの残りのページでは、既定の設定をそのまま使います。
8. **[Ready to complete]\(完了の準備完了\)** で、既定の設定で VM をセットアップするには、**[Power on after deployment]\(デプロイ後に電源をオンにする\)** > **[Finish]\(完了\)** の順に選択します。
9. 既定では、VM は 1 つの NIC で展開されます。 さらに NIC を追加する場合は、**[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにして、**[Finish]\(完了\)** をクリックします。 その後、次の手順に従います。

## <a name="add-an-adapter-to-the-configuration-server"></a>構成サーバーにアダプターを追加する

構成サーバーにさらに NIC を追加する場合は、サーバーをコンテナーに登録する前に追加します。 登録後のアダプターの追加はサポートされていません。

1. vSphere Client インベントリで VM を右クリックし、**[Edit Settings]\(設定の編集\)** を選びます。
2. **[Hardware]\(ハードウェア\)** で、**[Add]\(追加\)** > **[Ethernet Adapter]\(イーサネット アダプター\)** の順に選択します。 次に、**[次へ]** を選択します。
3. アダプターの種類およびネットワークを選びます。
4. VM がオンになったときに仮想 NIC を接続するには、**[Connect at power- on]\(電源をオンにしたときに接続する\)** を選択します。 次に、**[Next]\(次へ\)** > **[Finish]\(完了\)** > **OK** の順に選びます。

## <a name="register-the-configuration-server"></a>構成サーバーを登録する 
VM をオンにして、Site Recovery コンテナーに構成サーバーを登録します。

1. VMWare vSphere Client のコンソールで、VM をオンにします。
2. VM が Windows Server 2016 のインストール エクスペリエンスで起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後に、管理者として VM にサインインします。



## <a name="set-up-the-configuration-server"></a>構成サーバーを設定する

展開の一環として、構成サーバー VM に MySQL をインストールする必要があります。 これは、次のような何とおりかの方法で実行できます。

- Site Recovery に MySQL のダウンロードとインストールを行わせる。 このオプションを使用する場合、構成サーバーの設定を開始する前に何も行う必要はありません。
- MySQL を手動でダウンロードおよびインストールする: 構成サーバーを設定する前に、MySQL インストーラーをダウンロードし、**C:\Temp\ASRSetup** フォルダーに配置します。 次に MySQL をインストールします。 
- Site Recovery を手動でダウンロードし、インストールする。 構成サーバーを設定する前に、MySQL インストーラーをダウンロードし、**C:\Temp\ASRSetup** フォルダーに配置します。


1. 初めて VM にサインインすると、Azure Site Recovery 構成ツールが起動されます。
2. 構成サーバーを Site Recovery コンテナーに登録するために使う名前を指定します。 次に、**[次へ]** を選択します。
3. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、**[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。 このアカウントは、構成サーバーを登録するコンテナーにアクセスできる必要があります。
4. ツールがいくつかの構成タスクを実行した後、再起動されます。
5. 再度マシンにサインインします。 数秒以内に、構成サーバーの管理ウィザードが自動的に起動します。
6. ウィザードで、**[接続の設定]** を選択します。
7. (既定で構成サーバーで実行されている) プロセス サーバーが VM からのレプリケーション トラフィックを受信するために使用する NIC を選択します。
8. 次に、**[保存]** を選択します。 構成サーバーの登録後にはコンテナーの設定は変更できないことにご注意ください。 
9. **[Recovery Services コンテナーを選択する]** で、Microsoft Azure にサインインし、Azure サブスクリプションと、関連するリソース グループおよびコンテナーを選びます。 
10. **[サードパーティ製ソフトウェアのインストール]** で、MySQL を次のようにインストールします。
     - Site Recovery によって MySQL ダウンロードとインストールを処理している場合、**[ダウンロードしてインストール]** をクリックします。 インストールが完了するのを待ってから、ウィザードを進めます。
     - MySQL のインストールが済んでいて、Site Recovery でこれをインストールする場合には、**[ダウンロードしてインストール]** をクリックします。 インストールが終了するのを待ってから、ウィザードを進めます。
     - 手動で MySQL のダウンロードとインストールが済んだ場合は、**[ダウンロードしてインストール]** をクリックします。 アプリが、**[既にインストールされています]** と示されます。 ウィザードを進めます。
11. 続行する前に、**[アプライアンス構成の検証]** で前提条件が検証されます。 
12. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンしているポートを入力し、コンテナーでの VMware サーバーのフレンドリ名を指定します。
13. 構成サーバーが VMware サーバーに接続したり、レプリケーションに使用できる VMware VM を自動的に検出したりするために使用する資格情報を入力します。 **[追加]** >  **[続行]** を選択します。 資格情報はローカルに保存されます。
14. VM のレプリケーションが有効になったときに Site Recovery がモビリティ サービスを自動的にインストールするために使用する資格情報を、**[仮想マシンの資格情報の構成]** に指定します。
    - Windows マシンの場合、このアカウントは、レプリケートするマシンに対するローカル管理者特権を持っている必要があります。
    - Linux の場合は、ルート アカウントの詳細を指定します。
15. **[構成の確定]** を選択して、登録を完了します。
16. 登録が終了したら、Azure portal を開き、構成サーバーと VMware サーバーが、**[Recovery Services コンテナー]** > **[管理]** > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** に表示されていることを確認します。


## <a name="exclude-antivirus-on-the-configuration-server"></a>構成サーバーのウイルス対策を除外します。

構成サーバー VM でウイルス対策ソフトウェアが実行されている場合、ウイルス対策操作で以下のフォルダーが除外されていることを確認します。 これにより、レプリケーションと接続が期待どおりに動作します。 

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Site Recovery のインストール ディレクトリ。 例: E:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>次の手順
- 問題が生じる場合、構成サーバーに関する[一般的な質問](vmware-azure-common-questions.md#configuration server)と[トラブルシューティング](vmware-azure-troubleshoot-configuration-server.md)をご確認ください。
- 次に、Azure で[ターゲット環境を設定](./vmware-azure-set-up-target.md)します。
