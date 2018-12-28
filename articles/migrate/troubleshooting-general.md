---
title: Azure Migrate に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Migrate サービスの既知の問題についての概要を説明し、一般的なエラーのトラブルシューティングのヒントを提供します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 4ebd6eb860a6b102d1a3b12642510c429c18baa7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259156"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate のトラブルシューティング

## <a name="troubleshoot-common-errors"></a>一般的なエラーのトラブルシューティング

[Azure Migrate](migrate-overview.md) は、オンプレミスのワークロードを評価することによって、Azure への移行を支援します。 この記事では、Azure Migrate をデプロイして使用する際に発生する問題のトラブルシューティングを説明します。

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>オンプレミスの環境を継続的に検出する OVA を使用していますが、オンプレミスの環境で削除された VM がまだポータルに表示されます。

継続的検出のアプライアンスでは、パフォーマンス データのみが継続的に収集され、オンプレミス環境での構成の変更 (VM の追加、削除、ディスクの追加など) は検出されません。 オンプレミス環境で構成の変更がある場合は、次の操作を行って、変更をポータルに反映することができます。

- 項目 (VM、ディスク、コアなど) の追加:これらの変更を Azure portal に反映するには、アプライアンスで検出を停止してから、再開します。 これにより、Azure Migrate プロジェクトで変更が確実に更新されます。

   ![検出の停止](./media/troubleshooting-general/stop-discovery.png)

- VM の削除:アプライアンスの設計方法のため、検出を停止して開始しても VM の削除は反映されません。 これは、後続の検出のデータが古い検出に追加され、上書きされないためです。 この場合、VM をグループから削除し、評価を再計算して、ポータルの VM は単に無視することができます。

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>"*要求にはユーザー ID ヘッダーが含まれていなければなりません*" というエラーが表示されて、移行プロジェクトの作成が失敗しました

この問題は、組織の Azure Active Directory (Azure AD) テナントへのアクセス権を持たないユーザーに発生することがあります。 Azure AD テナントに初めて追加されたユーザーは、テナントへの参加を求める招待メールを受信します。 ユーザーは、テナントに正しく追加されるために、メールを開いて招待を承諾する必要があります。 メールを表示することができない場合は、既にテナントへのアクセス権を持つユーザーに連絡して、[こちら](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)の手順を使用してご自身への招待を再送信するように依頼してもらいます。

招待メールを受信したら開き、メール内のリンクをクリックして招待を承諾する必要があります。 これを行ったら、Azure portal からサインアウトし、再度サインインする必要があります。ブラウザーの更新は機能しません。 これで、移行プロジェクトの作成を試行できます。

### <a name="i-am-unable-to-export-the-assessment-report"></a>評価レポートをエクスポートできません

評価レポートをポータルからエクスポートできない場合は、下の REST API を使用して評価レポートのダウンロード URL を取得してみてください。

1. コンピューターに *armclient* をインストールします (まだインストールしていない場合)。

  a. 管理者のコマンド プロンプト ウィンドウで、```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"``` のコマンドを実行します。

  b. 管理者の Windows PowerShell ウィンドウで、```choco install armclient``` のコマンドを実行します。

2.  Azure Migrate REST API を使用して評価レポートのダウンロード URL を取得します。

  a.    管理者の Windows PowerShell ウィンドウで、```armclient login``` のコマンドを実行します。

  これにより Azure ポップアップが表示されます。ここでは、Azure にログオンする必要があります。

  b.    同じ PowerShell ウィンドウで、次のコマンドを実行して、評価レポートのダウンロード URL を取得します (下のサンプル API 要求で URI パラメーターを適切な値に置き換えます)。

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

       サンプルの要求と出力:

       ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
018_12_16_21/downloadUrl?api-version=2018-02-02
{
  "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
  "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. 応答から URL をコピーし、それをブラウザーで開いて評価レポートをダウンロードします。

4. レポートをダウンロードしたら、Excel を使用して、ダウンロードしたフォルダーを参照し、Excel でファイルを開いて表示します。

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>ディスクとネットワーク アダプターのパフォーマンス データが 0 と表示されています

これは、vCenter サーバーの統計設定レベルを 3 よりも低いレベルに設定した場合に発生することがあります。 レベルが 3 以上の場合、vCenter はコンピューティング、ストレージ、ネットワークの VM パフォーマンス履歴を保存します。 レベルが 3 よりも小さい場合、vCenter はストレージとネットワーク データは保存せずに、CPU とメモリのデータのみを保存します。 このシナリオでは、Azure Migrate でパフォーマンス データが 0 と表示され、Azure Migrate はオンプレミスのマシンから収集したメタデータに基づいて、ディスクとネットワークの推奨サイズを提供します。

ディスクおよびネットワーク パフォーマンス データの収集を有効にするには、統計レベルの設定を 3 に変更します。 その後、ご利用の環境が検出され評価されるまで、少なくとも 1 日待ちます。

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>移行プロジェクトの作成時に Azure の地理を指定しましたが、検出されたメタデータが格納される Azure リージョンをどうやって探せばいいでしょうか？

メタデータが格納されている場所を識別するには、プロジェクトの**概要**ページにある**Essentials**セクションをご覧ください。 場所が Azure Migrate によって地理内で任意に選択され、変更することはできません。 特定のリージョンだけでプロジェクトを作成するには、移行プロジェクトを作成し、目的のリージョンを通過する REST API を使用できます。

   ![プロジェクトの場所](./media/troubleshooting-general/geography-location.png)

## <a name="collector-errors"></a>コレクターのエラー

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Azure Migrate Collector のデプロイが次のエラーで失敗しました: 指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェストのエントリ。

1. ハッシュ値をチェックして、Azure Migrate Collector の OVA ファイルが正常にダウンロードされているかどうかを確認します。 ハッシュ値の確認については、こちらの[記事](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)を参照してください。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでも失敗する場合、および、OVF をデプロイするのに VMware vSphere クライアントを使用している場合は、vSphere Web クライアントでデプロイしてみてください。 それでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 にそれをデプロイしようとしている場合は、次の手順に従って ESXi ホストに直接 OVA をデプロイしてみてください。
  - Web クライアント (https:// <*ホスト IP アドレス*>/ui) を使用して、(vCenter Server ではなく) ESXi ホストに直接接続する
  - [ホーム] > [インベントリ] に移動する
  - [ファイル] > [OVF テンプレートのデプロイ] > [OVA を参照] をクリックして、デプロイを完了する
4. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。


### <a name="collector-is-not-able-to-connect-to-the-internet"></a>コレクターがインターネットに接続できない

これは、使用しているマシンがプロキシの内側にある場合に発生することがあります。 プロキシに承認資格情報が必要な場合は、これを提供してください。
URL ベースのファイアウォール プロキシを使用して送信接続を制御している場合は、次の必須の URL を必ずホワイトリストに登録してください。

**URL** | **目的**  
--- | ---
*.portal.azure.com | Azure サービスとの接続性を確認し、時刻の同期の問題を検証するために必要です。
*.oneget.org | powershell ベースの vCenter PowerCLI モジュールをダウンロードするために必要です。

**証明書の検証に失敗したためコレクターがインターネットに接続できない**

この問題は、インターネットへの接続にインターセプト プロキシを使用していて、かつプロキシの証明書をまだコレクター VM にインポートしていない場合に発生することがあります。 プロキシの証明書は、[こちら](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity)に説明されている手順でインポートできます。

**ポータルからコピーしたプロジェクト ID とキーを使用しても、コレクターがプロジェクトに接続できません。**

情報を正しくコピーして貼り付けたかどうかを確認してください。 この問題を解決するには、Microsoft Monitoring Agent (MMA) をインストールし、次のように MMA がプロジェクトに接続できるかどうかを確認します。

1. コレクター VM 上に、[MMA](https://go.microsoft.com/fwlink/?LinkId=828603) をダウンロードします。
2. インストールを開始するには、ダウンロードしたファイルをダブルクリックします。
3. 設定の **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
4. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、**[次へ]** をクリックします。
5. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics]** > **[次へ]** の順にクリックします。
6. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 コピーしたプロジェクト ID とキーを貼り付けます。 その後、 **[次へ]** をクリックします。
7. エージェントがプロジェクトに接続できることを確認します。 できない場合は、設定を確認します。 エージェントは接続できるがコレクターは接続できないという場合は、サポートにお問い合わせください。


### <a name="error-802-date-and-time-synchronization-error"></a>エラー 802: 日付と時刻の同期エラー

サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があります。 コレクター VM のクロックの時刻を変更して現在の時刻と一致させるには、次の手順に従います。

1. VM で管理者用のコマンド プロンプトを開きます。
2. タイム ゾーンを確認するには、w32tm /tz を実行します。
3. 時刻を同期するには、w32tm /resync を実行します。

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI をインストールできませんでした

PowerCLI は、Azure Migrate Collector によってダウンロードされ、アプライアンスにインストールされます。 PowerCLI のインストールが失敗するのは、PowerCLI リポジトリのエンドポイントに到達できないことが原因である可能性があります。 トラブルシューティングするには、次の手順を使用して、手動で PowerCLI をコレクター VM にインストールしてみてください。

1. 管理者モードで Windows PowerShell を開きます
2. C:\ProgramFiles\ProfilerService\VMWare\Scripts\ ディレクトリに移動します
3. InstallPowerCLI.ps1 スクリプトを実行します

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>エラー UnhandledException 内部エラーが発生しました: System.IO.FileNotFoundException

VMware PowerCLI のインストールに関する問題が原因で発生する可能性があります。 この問題を解決するには、以下の手順に従ってください。

1. コレクターアプライアンスのバージョンが最新でない場合は、コレクターを[最新バージョン](https://aka.ms/migrate/col/checkforupdates)にアップグレードして、問題が解決されるかどうかを確認します。
2. 既にコレクターの最新バージョンを使用している場合は、[VMware PowerCLI 6.5.2](https://www.powershellgallery.com/packages/VMware.PowerCLI/6.5.2.6268016) を手動でインストールして、問題が解決されるかどうかを確認します。
3. 上記によって問題が解決されなければ、C:\Program Files\ProfilerService フォルダーに移動し、フォルダーにある VMware.dll  と VimService65.dll ファイルを削除し、Windows Services Manager で 「Azure Migrate Collector」サービスを再起動します ( Windows Services Manager を開くには、「実行」を開き、 「services.msc」を入力する)。

### <a name="error-unabletoconnecttoserver"></a>エラー UnableToConnectToServer

vCenter Server "Servername.com:9443" に接続できません。原因となったエラー: メッセージを受信できる https://Servername.com:9443/sdk でリッスンしているエンドポイントがありませんでした。

コレクター アプライアンスの最新バージョンが実行されているかどうかを確認します。そうでない場合は、アプライアンスを[最新バージョン](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector)にアップグレードしてください。

最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をコレクター マシンで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターは既定でポート番号 443 に接続を試みます。

1. コレクター マシンから Servername.com に ping を実行してみてください。
2. 手順 1. が失敗した場合は、IP アドレスで vCenter サーバーへの接続を再試行してください。
3. 正しいポート番号を識別して vCenter に接続します。
4. 最後に、vCenter サーバーが起動されていて実行中かどうかを確認します。

## <a name="dependency-visualization-issues"></a>依存関係の視覚化の問題

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government プロジェクトの依存関係の視覚化機能が見つかりません。

Azure Migrate は依存関係の視覚化機能を Service Map に依存していますが、Service Map は Azure Government で現在使用できないため、この機能は Azure Government で使用できません。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>オンプレミスのVM へMicrosoft Monitoring Agent（MMA） と依存関係エージェントをインストールしましたが、依存関係が Azure Migrate ポータルに表示されています。

エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 ~ 30 分かかります。 30分以上待っている場合、下記の要領でMMA エージェントが OMS ワークスペースとコミュニケーションできるかを確認します。

Windows VM：
1. **[コントロール パネル]** から **[Microsoft Monitoring Agent]** を起動します。
2. MMA プロパティポップアップ にある**Azure Log Analytics (OMS)** タブに移動します。
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
Azure Migrate で依存関係を視覚化できる期間は、最大 1 時間です。 Azure Migrate を使用すると、過去 1 か月までの特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。 たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間ウィンドウの表示のみできます。

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>10 個を超える VM を持つグループの依存関係の視覚化ができません。
最大 10 個 の VM を含むグループについて[依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。グループに含まれる VM が 10 個 を超える場合は、そのグループを小さなグループに分割して、依存関係を視覚化することをお勧めします。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>エージェントをインストールし、依存関係の視覚エフェクトを使用してグループを作成しました。 現在、フェールオーバー後にマシンで [依存関係の表示] ではなく [エージェントのインストール] アクションが表示されます
* 計画的なフェールオーバー、または計画外のフェールオーバーの後、オンプレミスのマシンはオフになり、同等のマシンが Azure にスピン アップされます。 これらのマシンは、別々の MAC アドレスを取得します。 ユーザーがオンプレミスの IP アドレスを保持するか保持しないかによって、異なる IP アドレスを取得する場合があります。 MAC と IP アドレスの両方が異なる場合、Azure Migrate はオンプレミスのマシンと Service Map 依存関係データの関連付けを行わず、依存関係を表示する代わりにエージェントをインストールするようユーザーに要求します。
* テスト フェールオーバー後、オンプレミスのマシンは想定どおりにオンの状態を維持します。 Azure にスピン アップされる同等のマシンは、異なる MAC アドレスを取得して、異なる IP アドレスを取得することがあります。 これらのマシンから送信される Log Analytics トラフィックをユーザーがブロックしない限り、Azure Migrate はオンプレミスのマシンと Service Map 依存関係データの関連付けを行わず、依存関係を表示する代わりにエージェントをインストールするようユーザーに要求します。

## <a name="troubleshoot-azure-readiness-issues"></a>Azure の対応性に関する問題のトラブルシューティング

**問題** | **解決策**
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) を使用すると、移行中に VM のブートの種類が BIOS に変換される、このような VM の移行を行うことができます。
条件付きでサポートされる Windows OS | この OS はサポート期間が終了しており、Azure のサポートのためには[カスタム サポート契約 (CSA)](https://aka.ms/WSosstatement) が必要です。Azure に移行する前に OS をアップグレードすることを検討してください。
サポートされていない Windows OS | Azure は[一部の Windows OS バージョン](https://aka.ms/WSosstatement)のみをサポートしています。Azure に移行する前にマシンの OS をアップグレードすることを検討してください。
条件付きで動作が保証されている Linux OS | Azure は[一部の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみの動作を保証しています。Azure に移行する前にマシンの OS をアップグレードすることを検討してください。
動作が保証されていない Linux OS | マシンは Azure で起動する可能性はありますが、Azure でサポートされている OS はありません。Azure に移行する前に OS を[動作保証済みの Linux バージョン](../virtual-machines/linux/endorsed-distros.md)にアップグレードすることを検討してください
オペレーティング システムが不明です | vCenter Server で VM のオペレーティング システムが [その他] に指定されました。そのため、Azure Migrate は VM の Azure 対応性を識別できません。 マシンを移行する前に、マシン内で実行されている OS が Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット数版の OS | 32 ビット版 OS の VM は Azure で起動する可能性はありますが、Azure に移行する前に、VM の OS を 32 ビット版から 64 ビット版にアップグレードすることをお勧めします。
Visual Studio サブスクリプションが必要です。 | マシン内で、Visual Studio サブスクリプションでのみサポートされている Windows クライアント OS が実行されています。
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


## <a name="collect-logs"></a>ログの収集

**コレクター VM でのログの収集方法**

ログ記録は既定で有効になっています。 ログは次の場所にあります。

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Windows イベント トレーシング を収集するには、次の操作を行います。

1. コレクター VM で、PowerShell コマンド ウィンドウを開きます。
2. **Get-EventLog -LogName Application | export-csv eventlog.csv** を実行します。

**ポータルのネットワーク トラフィック ログの収集方法**

1. ブラウザーを開き、[ポータル](https://portal.azure.com)に移動してログインします。
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、**[ナビゲーション時にエントリをクリア]** の設定をクリアします。
3. **[ネットワーク]** タブをクリックし、ネットワーク トラフィックのキャプチャを開始します。
 - Chrome では、**[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 表示されない場合は、黒い円をクリックして開始します。
 - Edge/IE では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンをクリックします。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
 - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** をクリックします。 これにより、ログが .har ファイルとして圧縮されエクスポートされます。
 - Edge/IE では、**[キャプチャしたトラフィックのエクスポート]** アイコンをクリックします。 これにより、ログが圧縮されエクスポートされます。
6. **[コンソール]** タブに移動して、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
 - Chrome では、コンソール ログのどこかを右クリックします。 **[Save as]\(名前を付けて保存\)** を選択して、ログをエクスポートおよび圧縮します。
 - Edge/IE では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。

## <a name="collector-error-codes-and-recommended-actions"></a>コレクターのエラー コードと推奨されるアクション

| エラー コード | エラー名   | Message   | 考えられる原因 | 推奨される操作  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | コレクターの有効期限が切れています。                                                        | コレクターの有効期限が切れました。                                                                                    | 新しいバージョンのコレクターをダウンロードし、再試行してください。                                                                                      |
| 751       | UnableToConnectToServer        | vCenter Server '%Name;' に接続できません。原因となったエラー: %ErrorMessage;     | 詳細については、エラー メッセージを確認してください。                                                             | 問題を解決してから、操作をやり直してください。                                                                                                           |
| 752       | InvalidvCenterEndpoint         | サーバー '%Name;' は vCenter Server ではありません。                                  | vCenter Server の詳細を指定してください。                                                                       | 正しい vCenter Server の詳細を指定してから、操作を再試行してください。                                                                                   |
| 753       | InvalidLoginCredentials        | 次のエラーが原因で vCenter Server '%Name;' に接続できません: %ErrorMessage; | ログイン資格情報が無効であるため、vCenter Server への接続に失敗しました。                             | 指定されたログイン資格情報が正しいことを確認してください。                                                                                    |
| 754       | NoPerfDataAvaialable           | パフォーマンス データは利用できません。                                               | vCenter Server で統計情報のレベルを確認してください。 パフォーマンス データを利用するには 3 に設定されている必要があります。 | 統計情報のレベルを 3 (5 分、30 分、2 時間間隔) に変更し、少なくとも 1 日待ってからやり直してください。                   |
| 756       | NullInstanceUUID               | InstanceUUID が null のマシンを検出しました                                  | vCenter Server に不適切なオブジェクトがある可能性があります。                                                      | 問題を解決してから、操作をやり直してください。                                                                                                           |
| 757       | VMNotFound                     | 仮想マシンが見つかりません                                                  | 仮想マシンが削除されている可能性があります: %VMID;                                                                | 検出中に、vCenter のインベントリのスコーピングで選択した仮想マシンが存在することを確認してください                                      |
| 758       | GetPerfDataTimeout             | VCenter の要求がタイムアウトしました。メッセージ %Message;                                  | vCenter Server の資格情報が正しくありません                                                              | VCenter Server の資格情報をチェックし、その vCenter Server が到達可能であることを確認して、 操作をやり直してください。 問題が解決しない場合は、サポートにお問い合わせください。 |
| 759       | VmwareDllNotFound              | VMWare.Vim DLL が見つかりません。                                                     | PowerCLI が正しくインストールされていません。                                                                   | PowerCLI が正しくインストールされていることを確認して、 操作をやり直してください。 問題が解決しない場合は、サポートにお問い合わせください。                               |
| 800       | ServiceError                   | Azure Migrate Collector サービスが実行されていません。                               | Azure Migrate Collector サービスが実行されていません。                                                       | services.msc を使用してサービスを開始し、操作をやり直してください。                                                                             |
| 801       | PowerCLIError                  | VMware PowerCLI をインストールできませんでした。                                          | VMware PowerCLI をインストールできませんでした。                                                                  | 操作をやり直してください。 問題が解決しない場合は、手動でインストールしてやり直してください。                                                   |
| 802       | TimeSyncError                  | 時刻がインターネット時刻サーバーと同期していません。                            | 時刻がインターネット時刻サーバーと同期していません。                                                    | マシンの時間がマシンのタイム ゾーンに合わせて正しく設定されていることを確認し、操作をやり直してください。                                 |
| 702       | OMSInvalidProjectKey           | 無効なプロジェクト キーが指定されました。                                                | 無効なプロジェクト キーが指定されました。                                                                        | 正しいプロジェクト キーを指定してから、操作をやり直してください。                                                                                              |
| 703       | OMSHttpRequestException        | 要求の送信中にエラーが発生しました。 メッセージ %Message;                                | プロジェクト ID とキーをチェックして、エンドポイントが到達可能であることを確認してください。                                       | 操作をやり直してください。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP 要求がタイムアウトしました。メッセージ %Message;                                     | プロジェクト ID とキーをチェックして、エンドポイントが到達可能であることを確認してください。                                       | 操作をやり直してください。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。                                                                     |
