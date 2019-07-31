---
title: Azure Migrate に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Migrate サービスの既知の問題についての概要を説明し、一般的なエラーのトラブルシューティングのヒントを提供します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302322"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate のトラブルシューティング

[Azure Migrate](migrate-services-overview.md) には、評価と移行のための Microsoft ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが集約されています。 このドキュメントでは、Azure Migrate、Azure Migrate: Server Assessment、Azure Migrate: Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。

## <a name="azure-migrate-project-issues"></a>Azure Migrate プロジェクトの問題

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>既存の Azure Migrate プロジェクトが見つかりません。

Azure Migrate には [2 つのバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)があります。 プロジェクトを作成したバージョンに応じて、次の手順に従ってプロジェクトを検索してください。

1. Azure Migrate の以前のバージョン (以前のエクスペリエンス) で作成されたプロジェクトを探している場合は、次の手順に従ってプロジェクトを見つけます。

    1. [Azure portal](https://portal.azure.com) に移動して **Azure Migrate** を検索します。
    2. Azure Migrate ダッシュボードに、以前のプロジェクトへのアクセスについて説明するバナーが表示されます。 このバナーが表示されるのは、以前のエクスペリエンスでプロジェクトを作成した場合だけです。 このバナーをクリックします。

    ![既存のプロジェクトにアクセスする](./media/troubleshooting-general/access-existing-projects.png)

    3. 以前のバージョンの Azure Migrate で作成された既存のプロジェクトの一覧が表示されます。

2. 現在のバージョン (新しいエクスペリエンス) で作成されたプロジェクトを探している場合は、次の手順に従ってプロジェクトを見つけます。

    1. [Azure portal](https://portal.azure.com) に移動して **Azure Migrate** を検索します。
    2. Azure Migrate ダッシュボードの左側のウィンドウにある **[サーバー]** ページに移動し、右上隅の **[変更]** を選択します。

    ![既存の Azure Migrate プロジェクトに切り替える](./media/troubleshooting-general/switch-project.png)

    3. 適切な**サブスクリプション**と **Migrate プロジェクト**を選択します。

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>2 つ目の Azure Migrate プロジェクトを作成できません。

新しい Azure Migrate プロジェクトを作成するには、以下の手順に従ってください。

1. [Azure portal](https://portal.azure.com) に移動して **Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの左側のウィンドウにある **[サーバー]** ページに移動し、右上隅の **[変更]** を選択します。

   ![Azure Migrate プロジェクトを変更する](./media/troubleshooting-general/switch-project.png)

3. 新しいプロジェクトを作成するには、次のスクリーンショットに示すように **[ここをクリック]** を選択します。

   ![2 つ目の Azure Migrate プロジェクトを作成する](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Azure Migrate プロジェクトと関連 Log Analytics ワークスペースの削除

Azure Migrate プロジェクトを削除すると、移行プロジェクトと共に、検出されたマシンに関するメタデータが削除されます。 ただし、Log Analytics ワークスペースを Server Assessment ツールに関連付けている場合、Log Analytics ワークスペースが自動的に削除されることはありません。 これは、同じ Log Analytics ワークスペースが複数のユース ケースで使用される可能性があるためです。 Log Analytics ワークスペースも削除する場合、それは手動で行う必要があります。

1. プロジェクトに関連付けられている Log Analytics ワークスペースを参照します。
     1. 移行プロジェクトをまだ削除していない場合は、[基本] セクションに Server Assessment の概要ページからワークスペースへのリンクが見つかります。

     ![LA ワークスペース](./media/troubleshooting-general/loganalytics-workspace.png)

     2. 既に移行プロジェクトを削除した場合は、Azure portal の左側のウィンドウの **[リソース グループ]** を選択します。 ワークスペースが作成されたリソース グループに移動し、それを参照します。
2. [こちらの記事](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)にある指示に従い、ワークスペースを削除します。

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>"*要求にはユーザー ID ヘッダーが含まれていなければなりません*" というエラーが表示されて、移行プロジェクトの作成が失敗しました

この問題は、組織の Azure Active Directory (Azure AD) テナントへのアクセス権を持たないユーザーに発生することがあります。 Azure AD テナントに初めて追加されたユーザーは、テナントへの参加を求める招待メールを受信します。 ユーザーは、テナントに正しく追加されるために、メールを開いて招待を承諾する必要があります。 メールを表示することができない場合は、既にテナントへのアクセス権を持つユーザーに連絡して、[こちら](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)の手順を使用してご自身への招待を再送信するように依頼してもらいます。

招待メールを受信したら開き、メール内のリンクをクリックして招待を承諾する必要があります。 これが完了したら、Azure portal からサインアウトし、再度サインインする必要があります。ブラウザーの更新は機能しません。 これで、移行プロジェクトの作成を試行できます。

## <a name="appliance-issues"></a>アプライアンスの問題

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>VMware 用 Azure Migrate アプライアンスのデプロイが次のエラーで失敗しました: 指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェストのエントリ。

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされているかどうかを、そのハッシュ値をチェックして確認します。 ハッシュ値の確認については、こちらの[記事](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)を参照してください。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでも失敗する場合、および、OVF をデプロイするのに VMware vSphere クライアントを使用している場合は、vSphere Web クライアントでデプロイしてみてください。 それでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、次の手順に従って ESXi ホストに直接 OVA をデプロイしてみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して (vCenter Server ではなく) ESXi ホストに直接接続する。
   - **[ホーム]**  >  **[インベントリ]** に移動する。
   - **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートのデプロイ\)** をクリックする。 OVA を参照して、デプロイを完了する。
4. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>アプライアンスからインターネットに接続できない

これは、使用しているマシンがプロキシの内側にある場合に発生することがあります。 プロキシに承認資格情報が必要な場合は、これを提供してください。
URL ベースのファイアウォール プロキシを使用してアウトバウンド接続を制御している場合は、次の必須の URL を必ず許可リストに追加してください。

シナリオ | URL リスト
--- | ---
VMware のサーバー評価 | [こちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Hyper-V のサーバー評価 | [こちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
VMware のサーバー移行 (エージェントレス) | [こちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
VMware のサーバー移行 (エージェントベース) | [こちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Hyper-V のサーバー移行 | [こちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

インターネットへの接続にインターセプト プロキシを使用している場合、プロキシの証明書をアプライアンス VM にインポートする必要があります。 プロキシの証明書は、[こちら](https://docs.microsoft.com/azure/migrate/concepts-collector)に説明されている手順でインポートできます。

### <a name="error-802-date-and-time-synchronization-error"></a>エラー 802: 日付と時刻の同期エラー

サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があります。 コレクター VM のクロックの時刻を変更して現在の時刻と一致させるには、次の手順に従います。

1. VM で管理者用のコマンド プロンプトを開きます。
2. タイム ゾーンを確認するには、w32tm /tz を実行します。
3. 時刻を同期するには、w32tm /resync を実行します。


### <a name="error-unabletoconnecttoserver"></a>エラー UnableToConnectToServer

vCenter Server "Servername.com:9443" に接続できません。原因となったエラー: メッセージを受信できる https://Servername.com:9443/sdk でリッスンしているエンドポイントがありませんでした。

コレクター アプライアンスの最新バージョンが実行されているかどうかを確認します。そうでない場合は、アプライアンスを[最新バージョン](https://docs.microsoft.com/azure/migrate/concepts-collector)にアップグレードしてください。

最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をコレクター マシンで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターは既定でポート番号 443 に接続を試みます。

1. コレクター マシンから Servername.com に ping を実行してみてください。
2. 手順 1. が失敗した場合は、IP アドレスで vCenter サーバーへの接続を再試行してください。
3. 正しいポート番号を識別して vCenter に接続します。
4. 最後に、vCenter サーバーが起動されていて実行中かどうかを確認します。

## <a name="discovery-issues"></a>検出の問題

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>検出を開始しましたが、検出された VM が Azure portal に表示されません。 [Server Assessment] および [Server Migrate] タイルに "検出中" という状態が表示されます。
アプライアンスから検出を開始した後、検出されたマシンが Azure portal に表示されるまでにしばらく時間がかかります。 VMware の検出では約 15 分、Hyper-V の検出では追加されたホストごとに約 2 分かかります。 この時間が経過した後も引き続き "検出中" と表示される場合は、 **[サーバー]** タブの **[更新]** をクリックします。これで、検出されたサーバーの数が [Server Assessment] および [Server Migration] タイルに表示されるはずです。


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>オンプレミスの環境を継続的に検出するアプライアンスを使用していますが、オンプレミスの環境で削除された VM がまだポータルに表示されます。

アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。 30 分後になっても最新の情報が表示されない場合は、次の手順を使用してデータを最新の情報に更新してください。

1. [サーバー]、[Azure Migrate: Server Assessment] で、 **[概要]** をクリックします。
2. **[管理]** にある **[Agent Health]** をクリックします。
3. **[エージェントを更新する]** オプションをクリックします。 このオプションは、エージェントの一覧の下に表示されます。
4. 更新操作が完了するまで待ちます。 ご利用の VM に関する最新情報が表示されることを確認します。

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>オンプレミスの VM に関する最新情報がポータルに表示されません

アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。 30 分後になっても最新の情報が表示されない場合は、次の手順を使用してデータを最新の情報に更新してください。

1. [サーバー]、[Azure Migrate: Server Assessment] で、 **[概要]** をクリックします。
2. **[管理]** にある **[Agent Health]** をクリックします。
3. **[エージェントを更新する]** オプションをクリックします。 このオプションは、エージェントの一覧の下に表示されます。
4. 更新操作が完了するまで待ちます。 これで、ご利用の VM に関する最新情報が表示されます。

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>サーバー名を解決できないため、ホストまたはクラスターに接続できません。 WinRM エラー コード: 0x803381B9 (エラー ID: 50004)
このエラーは、アプライアンスの要求を処理する DNS が、指定されたクラスターまたはホストの名前を解決できない場合に発生します。 クラスターでこのエラーが表示された場合は、そのクラスターの完全修飾ドメイン名を指定してみてください。 

クラスター内のホストについても、このエラーが表示されることがあります。 その場合、アプライアンスからクラスターに接続することはできます。 ただし、クラスターから返されたホスト名が完全修飾ドメイン名ではありません。 

このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
1. 管理者ユーザーとしてメモ帳を開きます。 C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
2. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
3. hosts ファイルを保存して閉じます。
4. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、それらのホストの最新情報を Azure portal で確認できるようになります。 


## <a name="assessment-issues"></a>評価の問題

### <a name="azure-readiness-issues"></a>Azure 対応性の問題

問題 | Remediation
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用すると、移行中に VM のブートの種類が BIOS に変換されるので、そのような VM の移行を実行できます。
条件付きでサポートされる Windows OS | この OS はサポート期間が終了しており、Azure のサポートのためには[カスタム サポート契約 (CSA)](https://aka.ms/WSosstatement) が必要です。Azure に移行する前に OS をアップグレードすることを検討してください。
サポートされていない Windows OS | Azure は[一部の Windows OS バージョン](https://aka.ms/WSosstatement)のみをサポートしています。Azure に移行する前にマシンの OS をアップグレードすることを検討してください。
条件付きで動作が保証されている Linux OS | Azure は[一部の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみの動作を保証しています。Azure に移行する前にマシンの OS をアップグレードすることを検討してください。
動作が保証されていない Linux OS | マシンは Azure で起動する可能性はありますが、Azure でサポートされている OS はありません。Azure に移行する前に OS を[動作保証済みの Linux バージョン](../virtual-machines/linux/endorsed-distros.md)にアップグレードすることを検討してください
オペレーティング システムが不明です | vCenter Server で VM のオペレーティング システムが [その他] に指定されました。そのため、Azure Migrate は VM の Azure 対応性を識別できません。 マシンを移行する前に、マシン内で実行されている OS が Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット数版の OS | 32 ビット版 OS の VM は Azure で起動する可能性はありますが、Azure に移行する前に、VM の OS を 32 ビット版から 64 ビット版にアップグレードすることをお勧めします。
Visual Studio サブスクリプションが必要です。 | マシン内で実行されている Windows クライアント OS は、Visual Studio サブスクリプションでのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりませんでした。 | そのマシンに必要なストレージ パフォーマンス (IOPS/スループット) が Azure VM のサポート範囲を超えています。 移行前に、そのマシンのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりませんでした。 | そのマシンに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのマシンのネットワーク要件を緩和します。
指定した場所で VM が見つかりませんでした。 | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります。 | 仮想マシンに接続されている 1 つ以上のディスクが Azure の要件を満たしていません。 仮想マシンに接続されている各ディスクのサイズが 4 TB を超えていないことを確認し、超えている場合は Azure に移行する前にディスク サイズを縮小してください。 各ディスクで必要とされるパフォーマンス (IOPS/スループット) が Azure の[仮想マシンの管理ディスク](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)でサポートされていることを確認してください。   
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、マシンから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、マシンから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure は最大 4 TB のディスクをサポートしています。 移行前に、ディスクを 4 TB 未満に縮小します。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのマシンのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした。 | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした。 | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした。 | そのグループの評価を新しく作成してみます。

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>評価のプロパティで Azure プランとしてマイクロソフト エンタープライズ契約 (EA) を指定できません
Azure Migrate: Server Assessment では現在、マイクロソフト エンタープライズ契約 (EA) ベースの価格はサポートされていません。 代替策としては、Azure プランとして "従量課金制" を使い、"割引" プロパティを使って、受け取るカスタム割引を指定します。 [評価をカスタマイズする方法の詳細を確認してください](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Server Assessment によって Linux VM が "条件付き対応" とマークされるのはなぜですか。 これを解決するために何か行う必要がありますか。
Server Assessment には、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できない既知のギャップがあります (たとえば、RHEL 6.10 の場合、Server Assessment では現在、OS のバージョンが RHEL 6 としてのみ検出されます)。 特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Server Assessment において条件付き対応とマークされます。 オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート ドキュメント](https://aka.ms/migrate/selfhost/azureendorseddistros)を参照して手動で確認できます。 ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Server Assessment によって推奨される VM SKU のコアの数とメモリ サイズは、オンプレミスで割り当てられているものより大きい値です。 なぜですか?
Server Assessment での VM SKU の推奨事項は、評価のプロパティに依存します。 Server Assessment では、"パフォーマンスベース" の評価と "オンプレミス" の評価の 2 種類を作成できます。 パフォーマンスベースの評価では、Server Assessment によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 さらに、パフォーマンスベースのサイズ設定では、快適性係数が考慮されて効果的な使用率が特定されます。 オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスでの割り当てに基づいてターゲット SKU が推奨されます。

たとえば、4 つのコアと 8 GB のメモリを搭載した、CPU 使用率が 50%、メモリ使用率が 50% のオンプレミスの VM があり、評価で快適性係数 1.3 が指定されているとします。 評価のサイズ変更の設定基準が "オンプレミス" の場合は、4 つのコアと 8 GB のメモリを搭載した Azure VM SKU が推奨されます。一方、サイズ変更の設定基準がパフォーマンスベースの場合は、有効な CPU とメモリの使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づいて、最も低コストの VM SKU である 4 コア (サポートされている最も近いコア数) と 8 GB メモリ サイズ (サポートされている最も近いメモリ サイズ) が推奨されます。 [Server Assessment によってどのようにサイズ変更が行われるかを確認してください](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)。

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Server Assessment によって推奨されるディスク SKU が、オンプレミスでの割り当てより大きいサイズです。 なぜですか?
Server Assessment でのディスクのサイズ変更は、サイズ変更の設定基準とストレージの種類という 2 つの評価プロパティに依存します。 サイズ変更の設定条件が "パフォーマンスベース" で、ストレージの種類が "自動" である場合、ディスクの IOPS とスループットの値を考慮して、ターゲット ディスクの種類 (Standard HDD、Standard SSD、または Premium ディスク) が特定されます。 その後、オンプレミスのディスクのサイズ要件を考慮して、そのディスクの種類内のディスク SKU が推奨されます。 サイズ変更の設定基準が "パフォーマンスベース" で、ストレージの種類が "Premium" である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ変更の設定基準が "オンプレミス" で、ストレージの種類が "Standard HDD"、"Standard SSD"、または "Premium" である場合は、同じロジックを使ってディスクのサイズ変更が実行されます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Server Assessment では Premium ディスクの種類 (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 したがって、オンプレミスのディスクで必要なサイズは 32 GB でしたが、Server Assessment では、オンプレミスのディスクの高い IOPS 要件により、さらに大きいサイズのディスクが推奨されました。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Server Assessment が見積もったコンピューティング コストには、VM の OS ライセンス コストは含まれますか?
現在、Server Assessment では Windows マシンの OS のコストのみが考慮され、Linux マシンの OS ライセンス コストは考慮されません。 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>パフォーマンス履歴と百分位数での使用率は、サイズの推奨にどのような影響を与えますか?
これらのプロパティが適用されるのは、"パフォーマンスベース" のサイズ設定の場合のみです。 Server Assessment では、オンプレミスのマシンのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 次に示すのは、Server Assessment によるパフォーマンス データの収集方法です。
- Azure Migrate アプライアンスでは、オンプレミスの環境が継続的にプロファイリングされて、VMware VM の場合は 20 秒ごと、Hyper-V VM の場合は 30 秒ごとに、使用状況データがリアルタイムで収集されます。
- このアプライアンスでは、20 秒または 30 秒でサンプルがロールアップされて、10 分ごとに 1 つのデータ ポイントが作成されます。 1 つのデータ ポイントを作成するために、アプライアンスでは 20 秒または 30 秒のすべてのサンプルからピーク値が選択されて、Azure に送信されます。
- Server Assessment で評価を作成するときは、パフォーマンス期間とパフォーマンス履歴のパーセンタイル値に基づいて、代表的な使用率の値が特定されます。 たとえば、パフォーマンス履歴が 1 週間で、パーセンタイル使用率が 95 番目の場合、Azure Migrate では過去 1 週間のすべての 10 分サンプル ポイントが昇順で並べ替えられて、代表的な値として 95 番目のパーセンタイルが選択されます。
95 番目のパーセンタイル値を設定することにより、99 番目のパーセンタイルを選択した場合に含まれる可能性がある外れ値はすべて無視されるようになります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、パーセンタイル使用率として 99 番目のパーセンタイルを選択する必要があります。

## <a name="dependency-visualization-issues"></a>依存関係の視覚化の問題

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government プロジェクトの依存関係の視覚化機能が見つかりません。

Azure Migrate は依存関係の視覚化機能を Service Map に依存していますが、Service Map は Azure Government で現在使用できないため、この機能は Azure Government で使用できません。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>オンプレミスのVM へMicrosoft Monitoring Agent（MMA） と依存関係エージェントをインストールしましたが、依存関係が Azure Migrate ポータルに表示されています。

エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 ~ 30 分かかります。 30分以上待っている場合、下記の要領でMMA エージェントが OMS ワークスペースとコミュニケーションできるかを確認します。

Windows VM：
1. **[コントロール パネル]** に移動して **[Microsoft Monitoring Agent]** を起動します。
2. MMA のプロパティ ポップアップにある **[Azure Log Analytics (OMS)]** タブに移動します。
3. ワークスペースの**ステータス**が緑色であることを確認します。
4. ステータスが緑色でない場合、ワークスペースを削除して、再度MMAへ追加してみてください。
        ![MMA のステータス](./media/troubleshooting-general/mma-status.png)

Linux VM の場合、 MMA と依存関係エージェントのインストールに関するすべてのコマンドに成功したことを確認します。

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA でサポートされるオペレーティング システムは何ですか？

MMA でサポートされる Windows オペレーティング システムの一覧は[ここ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)にあります。
MMA でサポートされる Linux オペレーティング システムの一覧は[ここ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)にあります。

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>依存関係エージェントでサポートされるオペレーティング システムは何ですか？

依存関係エージェントでサポートされる Windows オペレーティング システムの一覧は[ここ](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)にあります。
依存関係エージェントでサポートされる Linux オペレーティング システムの一覧は[ここ](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)にあります。

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>1 時間以上経っても、Azure Migrate で依存関係を視覚化することができません。
Azure Migrate で依存関係を視覚化できる期間は、最大 1 時間です。 Azure Migrate を使用すると、過去 1 か月までの特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。 たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間ウィンドウの表示のみできます。 ただし、Azure Monitor ログを使用すれば、長期間にわたって[依存関係データのクエリ](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)を実行できます。

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>10 個を超える VM を持つグループの依存関係の視覚化ができません。
最大 10 個 の VM を含むグループについて[依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。グループに含まれる VM が 10 個 を超える場合は、そのグループを小さなグループに分割して、依存関係を視覚化することをお勧めします。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>エージェントをインストールし、依存関係の視覚エフェクトを使用してグループを作成しました。 現在、フェールオーバー後にマシンで [依存関係の表示] ではなく [エージェントのインストール] アクションが表示されます
* 計画的なフェールオーバー、または計画外のフェールオーバーの後、オンプレミスのマシンはオフになり、同等のマシンが Azure にスピン アップされます。 これらのマシンは、別々の MAC アドレスを取得します。 ユーザーがオンプレミスの IP アドレスを保持するか保持しないかによって、異なる IP アドレスを取得する場合があります。 MAC と IP アドレスの両方が異なる場合、Azure Migrate はオンプレミスのマシンと Service Map 依存関係データの関連付けを行わず、依存関係を表示する代わりにエージェントをインストールするようユーザーに要求します。
* テスト フェールオーバー後、オンプレミスのマシンは想定どおりにオンの状態を維持します。 Azure にスピン アップされる同等のマシンは、異なる MAC アドレスを取得して、異なる IP アドレスを取得することがあります。 これらのマシンから送信される Azure Monitor ログ トラフィックをユーザーがブロックしない限り、Azure Migrate はオンプレミスのマシンと Service Map 依存関係データの関連付けを行わず、依存関係を表示する代わりにエージェントをインストールするようユーザーに要求します。

## <a name="collect-azure-portal-logs"></a>Azure portal のログを収集する

**Azure portal のネットワーク トラフィック ログを収集するにはどうすればよいですか?**

1. ブラウザーを開き、[ポータル](https://portal.azure.com)に移動してサインインします。
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、 **[ナビゲーション時にエントリをクリア]** の設定をクリアします。
3. **[ネットワーク]** タブをクリックし、ネットワーク トラフィックのキャプチャを開始します。
   - Chrome では、 **[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 表示されない場合は、黒い円をクリックして開始します。
   - Microsoft Edge または IE では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンをクリックします。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
   - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** をクリックします。 これにより、ログが .har ファイルとして圧縮されエクスポートされます。
   - Microsoft Edge または IE では、 **[キャプチャしたトラフィックのエクスポート]** アイコンをクリックします。 これにより、ログが圧縮されてエクスポートされます。
6. **[コンソール]** タブに移動して、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
   - Chrome では、コンソール ログのどこかを右クリックします。 **[名前を付けて保存]** を選択し、ログをエクスポートして zip 圧縮します。
   - Microsoft Edge または IE では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。
