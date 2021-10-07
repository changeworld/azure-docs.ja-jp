---
title: Azure Automation Update Management の概要
description: この記事では、Windows および Linux マシンの更新プログラムを実装する Update Management 機能について概要を説明します。
services: automation
ms.subservice: update-management
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: fed1ce7f236b568458f1eb1b25ce5420c2ad5501
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092162"
---
# <a name="update-management-overview"></a>Update Management の概要

Azure Automation の Update Management を使用して、Azure での Windows および Linux 仮想マシン、オンプレミス環境やその他のクラウド環境での物理マシンまたは VM に対するオペレーティング システムの更新プログラムを管理できます。 利用可能な更新プログラムの状態をすばやく評価し、Update Management に報告しているマシンに必要な更新プログラムをインストールするプロセスを管理できます。 

サービス プロバイダーは、[Azure Lighthouse](../../lighthouse/overview.md) に複数の顧客テナントをオンボードしている場合があります。 Update Management を使用して、同じ Azure Active Directory (Azure AD) テナントの複数のサブスクリプションのマシンまたは Azure Lighthouse を使用している複数のテナントに対する更新プログラムのデプロイの評価とスケジュールを実行できます。

Microsoft では、全体的な更新管理戦略の一部として考慮する必要がある、Azure VM または Azure 仮想マシン スケール セットに対する更新プログラムの管理に役立つその他の機能を提供しています。 

- 毎月リリースされる "*重要な*" 更新プログラムと *セキュリティ* 更新プログラムによってセキュリティ コンプライアンスを維持するために Azure 仮想マシンの自動的な評価と更新に関心がある場合は、「[VM ゲストの自動パッチ適用](../../virtual-machines/automatic-vm-guest-patching.md)」(プレビュー) を参照してください。 これは、お使いの Azure VM を (可用性セット内の VM を含めて) オフピーク時間中に自動更新するための代替更新管理ソリューションであり、これらの VM に対する更新プログラムのデプロイを Azure Automation の Update Management から管理するものとは別のソリューションです。 

- Azure 仮想マシン スケール セットを管理する場合は、スケール セット内のすべてのインスタンスの OS ディスクを安全かつ自動的にアップグレードするために、[OS イメージの自動アップグレード](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)を実行する方法を参照してください。 

Update Management をデプロイしてマシンを管理できるようにする前に、次のセクションの情報を理解しておいてださい。

## <a name="about-update-management"></a>Update Management について

次の図に、接続されたすべての Windows Server と Linux サーバーに対する、Update Management によるセキュリティ更新プログラムの評価と適用の実行方法を示します。

![Update Management ワークフロー](./media/overview/update-mgmt-workflow.png)

Update Management では Azure Monitor ログと連携して、割り当てられている Azure と Azure 以外のマシンから収集された更新プログラムの評価と更新プログラムのデプロイの結果が、ログ データとして保存されます。 このデータを収集するために、Automation アカウントと Log Analytics ワークスペースがリンクされ、マシン上にこのワークスペースに報告するように構成された Windows および Linux 用の Log Analytics エージェントが必要です。 

Update Management では、ワークスペースに接続されている System Center Operations Manager 管理グループのエージェントからのシステムの更新に関する情報の収集がサポートされています。 1 つのマシンを複数の Log Analytics ワークスペースに Update Management 用に登録すること (マルチホームとも言われます) は、サポートされていません。

次の表に、Update Management でサポートされている接続先ソースの概要を示します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows |はい |Update Management によって、Log Analytics エージェントを使用して Windows マシンからシステムの更新プログラムと、必要な更新プログラムのインストールに関する情報が収集されます。<br> マシンは、Microsoft Update または Windows Server Update Services (WSUS) に報告する必要があります。 |
| Linux |はい |Update Management によって、Log Analytics エージェントを使用して Linux マシンからシステムの更新プログラムと、サポートされているディストリビューションに関する必要な更新プログラムのインストールに関する情報が収集されます。<br> マシンは、ローカルまたはリモートのリポジトリに報告する必要があります。 |
| Operations Manager 管理グループ |はい |Update Management によって、接続されている管理グループ内のエージェントからソフトウェアの更新に関する情報が収集されます。<br/><br/>Operations Manager エージェントから Azure Monitor ログへの直接接続は必要ありません。 ログ データは、管理グループから Log Analytics ワークスペースに転送されます。 |

Update Management に割り当てられたマシンによって、同期するように構成されているソースに基づいて、各自の最新の状態が報告されます。 Windows マシンは、[Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) または [Microsoft Update](https://www.update.microsoft.com) に報告するように構成する必要があり、Linux マシンは、ローカルまたはパブリックのリポジトリに報告するように構成する必要があります。 Update Management と Microsoft Endpoint Configuration Manager を使用することもできます。詳細については、「[Microsoft Endpoint Configuration Manager と Update Management を統合する](mecmintegration.md)」を参照してください。 

Windows マシン上の Windows Update Agent (WUA) が WSUS に報告するように構成されている場合は、WSUS の Microsoft Update との最後の同期のタイミングによっては、Microsoft Update で示されるものと結果が一致しないことがあります。 この動作は、パブリック リポジトリではなくローカル リポジトリに報告するように構成されている Linux マシンにも当てはまります。 Windows マシンでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。 Linux マシンでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 Log Analytics エージェントを再起動した場合、コンプライアンス スキャンは 15 分以内に開始されます。 マシンで更新プログラムのコンプライアンスのためのスキャンが完了すると、エージェントによって情報が Azure Monitor ログに一括転送されます。 

スケジュールされたデプロイを作成することで、更新が必要なマシンへのソフトウェア更新プログラムのデプロイとインストールを実行できます。 Windows マシンの場合、"*オプション*" に分類されている更新プログラムはデプロイの範囲に含まれません。 デプロイの範囲には、必須の更新プログラムのみが含まれています。

スケジュールされたデプロイでは、適用可能な更新プログラムを受け取るターゲット マシンが定義されます。 これを行うには、特定のマシンを明示的に指定するか、特定のマシン セットのログ検索 (または指定された条件に基づいて動的に Azure VM を選択する [Azure クエリ](query-logs.md)) に基づく[コンピューター グループ](../../azure-monitor/logs/computer-groups.md)を選択します。 これらのグループは、Update Management を有効にするために構成を受け取るコンピューターのターゲット設定を制御するために使用される[スコープ構成](../../azure-monitor/insights/solution-targeting.md)とは異なります。 これにより、更新プログラムのコンプライアンスを実行および報告したり、承認された必要な更新プログラムをインストールしたりできなくなります。

デプロイを定義するときに、更新プログラムをインストールできる期間を承認および設定するスケジュールも指定します。 この期間は、メンテナンス期間と呼ばれます。 再起動が必要な場合、適切な再起動オプションを選択していれば、再起動のために 20 分間のメンテナンス期間が予約されます。 パッチ適用に予想よりも時間がかかり、メンテナンス期間の残りが 20 分を切った場合、再起動は行われません。

更新プログラム パッケージのデプロイがスケジュールされた後、Linux マシンで更新プログラムが評価用に表示されるまでに 2 時間から 3 時間かかります。 Windows マシンの場合、更新プログラムがリリースされてから評価用に表示されるまで 12 時間から 15 時間かかります。 更新プログラムのインストールの前後に、更新プログラムのコンプライアスのためのスキャンが実行され、ログ データの結果がワークスペースに転送されます。

更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 更新プログラムのデプロイを作成すると、対象に含めたマシンに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 マスター Runbook によって、Windows 上で Windows Update エージェントを使用して必要な更新プログラムのインストールを開始する子 Runbook が各エージェントで開始されます。サポートされている Linux ディストリビューションでは該当するコマンドが実行されます。

更新プログラムのデプロイで指定された日時に、ターゲット マシンでデプロイが並列で実行されます。 まず、スキャンが実行され、その更新プログラムが依然として必須であることが確認されてからインストールされます。 WSUS クライアント マシンの場合、更新プログラムが WSUS で承認されていないと、更新プログラムのデプロイは失敗します。

## <a name="limits"></a>制限

Update Management に適用される制限については、「[Azure Automation サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)」を参照してください。

## <a name="permissions"></a>アクセス許可

更新プログラムのデプロイを作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[Update Management へのロールベースのアクセス](../automation-role-based-access-control.md#update-management-permissions)に関するページをご覧ください。

## <a name="update-management-components"></a>Update Management コンポーネント

Update Management では、このセクションで説明されているリソースが使用されます。 Update Management を有効にすると、これらのリソースが Automation アカウントに自動的に追加されます。

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ

Update Management を有効にすると、Log Analytics ワークスペースに直接接続されているすべての Windows マシンは、Update Management をサポートする runbook をサポートするために、自動的にシステム Hybrid Runbook Worker として構成されます。

Update Management で管理されている各 Windows マシンは、Automation アカウントの [システム ハイブリッド worker グループ] として、[ハイブリッド worker グループ] ペインに表示されます。 グループでは、`Hostname FQDN_GUID` の名前付け規則が使用されます。 アカウントの Runbook でこれらのグループを対象として指定することはできません。 指定しようとすると、失敗します。 これらのグループは、Update Management のみをサポートすることを目的としています。 Hybrid Runbook Worker として構成されている Windows マシンの一覧を表示する方法の詳細については、「[Hybrid Runbook Workers の表示](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)」を参照してください。

Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すると、Windows マシンを Automation アカウント内のユーザー Hybrid Runbook Worker グループに追加して Automation Runbook をサポートできます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 で追加されました。

### <a name="external-dependencies"></a>外部依存関係

Azure Automation Update Management は、ソフトウェア更新プログラムを配信するために、以下の外部依存関係に依存しています。

* ソフトウェア更新プログラム パッケージと、Windows ベースのマシンでのソフトウェア更新プログラムの適用性スキャンのために、Windows Server Update Services (WSUS) または Microsoft Update が必要です。
* マシンが WSUS サーバーまたは Microsoft Update に接続できるように、Windows ベースのマシン上に Windows Update エージェント (WUA) クライアントが必要です。
* Linux ベースのマシンで OS 更新プログラムを取得してインストールするための、ローカルまたはリモートのリポジトリ。

### <a name="management-packs"></a>管理パック

次の管理パックが、Update Management によって管理されるマシンにインストールされます。 Operations Manager 管理グループが [Log Analytics ワークスペースに接続されている](../../azure-monitor/agents/om-agents.md)場合、管理パックは Operations Manager 管理グループにインストールされます。 これらの管理パックを構成または管理する必要はありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

> [!NOTE]
> Log Analytics ワークスペースに接続される Operations Manager 1807 または 2019 管理グループに、ログ データを収集するように構成されたエージェントがある場合は、**Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 規則内で `IsAutoRegistrationEnabled` パラメーターをオーバーライドして `True` に設定する必要があります。

管理パックの更新プログラムの詳細については、[Azure Monitor ログへの Operations Manager の接続](../../azure-monitor/agents/om-agents.md)に関する記事を参照してください。

> [!NOTE]
> Log Analytics エージェントを使用して Update Management でマシンを完全に管理するには、Windows 用の Log Analytics エージェント、または Linux 用 Log Analytics エージェントに更新する必要があります。 エージェントを更新する方法については、[Operations Manager エージェントのアップグレード方法](/system-center/scom/deploy-upgrade-agents)に関する記事を参照してください。 Operations Manager を使用する環境では、System Center Operations Manager 2012 R2 UR 14 以降を実行している必要があります。

## <a name="data-collection-frequency"></a>データ収集の頻度

Update Management では、次のルールを使用して、管理対象のマシンのデータのスキャンが行われます。 管理対象のマシンの更新されたデータがダッシュボードに表示されるまでに、30 分から 6 時間かかる可能性があります。

* 各 Windows マシン - Update Management では、各マシンで 1 日に 2 回スキャンが行われます。

* 各 Linux マシン - Update Management では 1 時間ごとにスキャンが行われます。

Update Management を使用しているマシンでの Azure Monitor ログによる平均データ使用量は、1 か月あたり約 25 MB です。 この値は概数にすぎず、環境によって異なることがあります。 正確な使用量を把握するために、環境を監視することをお勧めします。 Azure Monitor ログのデータ使用量を分析する方法の詳細については、[使用量とコストの管理](../../azure-monitor/logs/manage-cost-storage.md)に関するページを参照してください。

## <a name="update-classifications"></a>更新プログラムの分類

次の表では、Update Management が Windows 更新プログラムでサポートされる分類が定義されています。

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムの累積セットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムの累積セットです。        |
|定義の更新     | ウイルスまたは他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新プログラムです。        |

次の表では、Linux 更新プログラムでサポートされる分類が定義されています。

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

> [!NOTE]
> Linux マシン用の更新プログラムの分類は、サポートされている Azure パブリック クラウド リージョンで使用される場合にのみ利用できます。 次の国内クラウド リージョンで Update Management を使用する場合、Linux の更新プログラムの分類はありません。
>
>* Azure US Government
>* 中国の 21Vianet
>
> 更新プログラムは、分類される代わりに、 **[他の更新プログラム]** カテゴリ内で報告されます。
>
> Update Management では、サポートされているディストリビューションによって発行されたデータが使用されます。具体的には、リリースされた [OVAL](https://oval.mitre.org/) (Open Vulnerability and Assessment Language) ファイルです。 インターネット アクセスはこれらの国内クラウドから制限されているため、Update Management によるファイルへのアクセスはできません。

Linux の場合、クラウドでのデータ エンリッチメントにより、Update Management は、評価データを表示しながら、 **[セキュリティ]** と **[その他]** の分類の下、クラウド内で重要な更新プログラムとセキュリティ更新プログラムを識別できます。 修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり、RTM バージョンの CentOS ではこの情報は使用できません。 CentOS マシンで、次のコマンドに対してセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 現時点では、お客様がこの機能をご自身で使用可能にした場合、与えられるサポートに限度があります。

Red Hat Enterprise バージョン 6 の更新プログラムを分類するには、yum-security プラグインをインストールする必要があります。 Red Hat Enterprise Linux 7 では、プラグインは既に yum 自体の一部であるため、何もインストールする必要はありません。 詳細については、次の Red Hat の[ナレッジ記事](https://access.redhat.com/solutions/10021)を参照してください。

たとえば、**セキュリティ** の分類と一致する更新プログラムのみをインストールするように構成されている Linux マシン上で更新プログラムを実行するようにスケジュールした場合、インストールされる更新プログラムが、この分類と一致する更新プログラムと一致しないことがあったり、そのサブセットであったりします。 お使いの Linux マシンに対して保留になっている OS 更新プログラムの評価が実行されると、Linux ディストリビューション ベンダーが提供する [Open Vulnerability and Assessment Language](https://oval.mitre.org/) (OVAL) ファイルが分類のために Update Management によって使用されます。

セキュリティの問題または脆弱性に対処する更新プログラムを含む OVAL ファイルに基づき、Linux 更新プログラムが **[セキュリティ]** か **[その他]** として分類されます。 ただし、更新スケジュールが実行される場合、Linux マシンでは、それらは YUM、APT、ZYPPER などの適切なパッケージ マネージャーを使用してインストールされます。 Linux ディストリビューションのパッケージ マネージャーには、更新プログラムを分類する別のメカニズムが備わっていることがあり、その場合の結果は、Update Management によって OVAL ファイルから取得されるものと異なる可能性があります。 マシンを手動で確認し、パッケージ マネージャーによってセキュリティ関連の更新プログラムを把握するには、[Linux 更新プログラムのデプロイのトラブルシューティング](../troubleshoot/update-management.md#updates-linux-installed-different)に関する記事を参照してください。

>[!NOTE]
> Update Management でサポートされる Linux ディストリビューションでは、更新プログラムの分類に基づく更新プログラムのデプロイが正しく機能しない場合があります。 これは、OVAL ファイルの名前付けスキーマに関して見つかっている問題によるものです。この問題が妨げとなって、Update Management が、フィルタリング規則に基づいて適切に分類を照合することができません。 セキュリティ更新プログラムの評価に別のロジックが使用されていることが原因で、デプロイ中に適用されるセキュリティ更新プログラムとは結果が異なる場合があります。 分類が **緊急** および **セキュリティ** として設定されている場合、更新プログラムのデプロイは正しく機能します。 影響を受けるのは、評価中の "*更新プログラムの分類*" のみです。
>
> Windows Server マシンの Update Management には影響せず、更新プログラムの分類とデプロイに変更は生じません。

## <a name="integrate-update-management-with-configuration-manager"></a>Configuration Manager と Azure Update Management を統合する

PC、サーバー、モバイル デバイスを管理するために Microsoft Endpoint Configuration Manager に投資してきたお客様は、Configuration Manager の強みと成熟度を活用してソフトウェア更新プログラムを管理できます。 Update Management を Configuration Manager と統合する方法については、「[Configuration Manager と Azure Update Management を統合する](mecmintegration.md)」を参照してください。

## <a name="third-party-updates-on-windows"></a>Windows でのサードパーティの更新プログラム

Update Management では、ローカルで構成された更新リポジトリに依存して、サポートされている Windows システム (WSUS または Windows Update) を更新します。 [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) などのツールを使用すると、WSUS を使用してカスタム更新プログラムをインポートして発行することができます。 このシナリオでは、サードパーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で更新プログラムを適用できます。 Updates Publisher を構成する方法については、「[Install Updates Publisher](/configmgr/sum/tools/install-updates-publisher)」(Updates Publisher のインストール) を参照してください。

## <a name="next-steps"></a>次のステップ

* Update Management を有効にして使用する前に、「[Update Management のデプロイを計画する](plan-deployment.md)」を確認します。

* Update Management についてよく寄せられる質問については、「[Azure Automation についてよく寄せられる質問](../automation-faq.md)」でご確認ください。