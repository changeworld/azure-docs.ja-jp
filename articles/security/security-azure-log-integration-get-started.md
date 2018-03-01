---
title: "Azure ログ統合の使用 | Microsoft Docs"
description: "Azure ログ統合サービスをインストールし、Azure ストレージのログ、Azure 監査ログ、および Azure Security Center の警告を統合する方法について説明します。"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 4555216950811960ccb42241bcade5ec892a77ce
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure 診断ログおよび Windows イベント転送による Azure ログ統合

Azure ログ統合 (AzLog) は、SIEM ベンダーから [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) コネクタを入手できない顧客にその代替手段を提供します。 Azure ログ統合は Azure ログを SIEM で使用可能にし、ユーザーがすべての資産のための統合されたセキュリティ ダッシュボードを作成できるようにします。

>[!NOTE]
>Azure Monitor の詳細については、[Azure Monitor の概要](../monitoring-and-diagnostics/monitoring-get-started.md)に関するページを確認できます。Azure monitor コネクタの状態の詳細については、SIEM ベンダーに問い合わせてください。

>[!IMPORTANT]
>主な関心が仮想マシン ログの収集である場合、ほとんどの SIEM ベンダーがソリューションにこれを含めています。 SIEM ベンダーのコネクタの使用は常に、推奨される代替手段になります。

この記事は、AzLog サービスのインストールに重点を置き、しかもこのサービスを Azure 診断と統合することによって、ユーザーが Azure ログ統合を使用開始する場合に役立ちます。 Azure ログ統合サービスにより、Azure IaaS に展開された仮想マシンの Windows セキュリティ イベント チャネルから、Windows イベント ログ情報を収集することができます。 これは、ユーザーがオンプレミスで使用した可能性のある "イベント転送" と同様です。

>[!NOTE]
>Azure ログ統合の出力を SIEM に転送する機能は、SIEM 自体によって提供されます。 詳細については、[Azure ログ統合とオンプレミスの SIEM の統合](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)に関する記事を参照してください。

Azure ログ統合サービスは、Windows Server 2008 R2 以降のオペレーティング システム (Windows Server 2012 R2 または Windows Server 2016 を推奨) を使用している物理または仮想コンピューター上で実行されます。

物理コンピューターは、オンプレミス (またはホストしているサイト上) で実行できます。 仮想マシンで Azure ログ統合サービスを実行する場合、その仮想マシンをオンプレミスまたは Microsoft Azure などのパブリック クラウドに設置することができます。

物理または仮想マシンが Azure ログ統合サービスを実行するには、Azure パブリック クラウドへのネットワーク接続が必要です。 この記事では、構成に関する手順を詳しく説明します。

## <a name="prerequisites"></a>前提条件

AzLog のインストールには、少なくとも次が必要です。

* **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。
* Windows Azure 診断ログに使用可能な**ストレージ アカウント** (事前構成されたストレージ アカウントを使用するか、新規に作成できます。この記事の後半で、ストレージ アカウントを構成する方法を紹介します)。

  >[!NOTE]
  シナリオによっては、ストレージ アカウントが必要ない場合があります。 この記事で説明する Azure 診断シナリオの場合は必要です。

* **2 つのシステム**: Azure ログ統合サービスを実行するマシンと、AzLog サービス マシンに送信されるログ情報が存在する監視対象のマシン。
   * 監視対象のマシン - [Azure 仮想マシン](../virtual-machines/virtual-machines-windows-overview.md)として実行する VM
   * Azure ログ統合サービスを実行するマシン。このマシンは、後で SIEM にインポートされるすべてのログ情報を収集します。
    * このシステムは、オンプレミスまたは Microsoft Azure に設置可能です。  
    * Windows server 2008 R2 SP1 の x64 バージョンまたはそれ以上を実行し、.NET 4.5.1 がインストールされている必要があります。 [方法 : インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/library/hh925568)の記事に従って、インストールされている .NET のバージョンを確認することができます。  
    Azure 診断ログに使用する Azure Storage アカウントへの接続が必要です。 この接続を確認する方法については、この記事の後の方で説明します。

* Windows Azure 診断ログに使用できる**ストレージ アカウント**。 事前に構成されたストレージ アカウントを使用するか、または新しいアカウントを作成できます。 ストレージ アカウントは、この記事の後の方で構成します。
  >[!NOTE]
  シナリオによっては、ストレージ アカウントが必要ない場合があります。 この記事で説明する Azure 診断シナリオの場合は必要です。
* **2 つのシステム**: Azure ログ統合サービスを実行するマシンと、Azlog サービス マシンに送信されるログ情報が存在する監視対象のマシン。
   * 監視対象のマシン - [Azure 仮想マシン](../virtual-machines/virtual-machines-windows-overview.md)として実行する VM
   * Azure ログ統合サービスを実行するマシン。このマシンは、後で SIEM にインポートされるすべてのログ情報を収集します。
    * このシステムは、オンプレミスまたは Microsoft Azure に設置可能です。  
    * Windows server 2008 R2 SP1 の x64 バージョンまたはそれ以上を実行し、.NET 4.5.1 がインストールされている必要があります。 [方法 : インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/library/hh925568)の記事に従って、インストールされている .NET のバージョンを確認することができます。  
    Azure 診断ログに使用する Azure Storage アカウントへの接続が必要です。 この接続を確認する方法については、この記事の後の方で説明します。

Azure Portal を使用して仮想マシンを作成するプロセスの簡単なデモについては、下のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

テスト中、オペレーティング システムの最小要件を満たす任意のシステムを使用できます。 運用環境の場合は、負荷のスケールアップまたはスケールアウトの計画が必要になることがあります。

Azure ログ統合サービスの複数のインスタンスを実行できます。 これは、物理または仮想マシンあたり 1 つのインスタンスに制限されます。 また、Windows 用 Azure 診断 (WAD) ストレージ アカウントの負荷分散が可能です。インスタンスに提供するサブスクリプションの数は、容量をベースにする必要があります。

>[!NOTE]
>この時点では、Azure ログ統合マシン (つまり、Azure ログ統合サービスを実行しているマシン) のインスタンスをいつスケールアウトするかや、ストレージ アカウントまたはサブスクリプションに関する具体的な推奨事項はありません。 拡大/縮小の決定は、これらの各領域におけるパフォーマンスの観測値に基づく必要があります。

イベント量が多い場合は、Azure ログ統合サービスの複数のインスタンスを実行できます (物理または仮想マシンあたり 1 つのインスタンス)。 さらに、Windows 用の Azure 診断ストレージ アカウント (WAD) を負荷分散できます。

またパフォーマンス向上のために、Azure ログ統合サービスをスケールアップすることもできます。 次のパフォーマンス メトリックは、Azure ログ統合サービスを実行するマシンのサイズ変更に役立ちます。

* 8 プロセッサ (コア) マシンでは、Azlog インテグレーターの 1 つのインスタンスで 1 日あたり約 2,400 万イベント (最大 100 万/時間) を処理できます。
* 4 プロセッサ (コア) マシンでは、Azlog インテグレーターの 1 つのインスタンスで 1 日あたり約 150 万イベント (最大 62,500/時間) を処理できます。

## <a name="install-azure-log-integration"></a>Azure ログ統合のインストール

Azure ログ統合のインストールには、[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)インストール ファイルをダウンロードする必要があります。 セットアップ ルーチンを実行し、利用統計情報をマイクロソフトに提供するかを決定します。  

![インストール画面で利用統計情報チェック ボックスをオンに](./media/security-azure-log-integration-get-started/telemetry.png)

> [!NOTE]
> マイクロソフトによる利用統計情報の収集を許可することをお勧めします。 このオプションをオフにして、利用統計情報の収集を無効にすることができます。
>


Azure ログ統合サービスは、インストール先のマシンから利用統計情報を収集します。  

収集される利用統計情報を以下に示します。

* Azure ログ統合の実行中に発生する例外
* 処理されたクエリおよびイベントの数に関するメトリック
* 使用されている Azlog.exe コマンド ライン オプションについての統計情報

インストール プロセスについては、次のビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>ポスト インストールおよび検証の手順

基本的なセットアップ ルーチンを完了すると、ポスト インストールおよび検証手順が実行できます。

1. 管理者特権の PowerShell ウィンドウを開き、**c:\Program Files\Microsoft Azure Log Integration** に移動します。
2. まず初めに Azlog コマンドレットをインポートします。 スクリプト **LoadAzlogModule.ps1** (次のコマンドでは “.\” に注意してください) を実行することでインポートが可能です。 **.\LoadAzlogModule.ps1** と入力し、**ENTER** をクリックします。 次の図のように表示されます。 </br></br>
![テレメトリ ボックスにチェックが入ったインストール画面](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. 特定の Azure 環境を使用して AzLog を構成する必要があります。 「Azure 環境」とは、使用する Azure のクラウド データ センターの「種類」です。 この時点では複数の Azure 環境が存在しますが、現在対象となるオプションは **AzureCloud** または**AzureUSGovernment** のいずれかです。   管理者特権の PowerShell 環境では、**c:\program files \Microsoft Azure ログ統合\**内であることを確認します。 </br></br>
    確認したらコマンドを実行します。 </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (Azure 商用)

      >[!NOTE]
      >コマンドの成功時に、フィードバックは提供されません。  米国政府の Azure クラウドを使用する場合、米国政府クラウド向け **AzureUSGovernment** (Name 変数) が使用されます。 その他の Azure クラウドは、現時点ではサポートされていません。  
4. システムを監視するには、Azure 診断に使用されているストレージ アカウントの名前がわかっている必要があります。  Azure Portal で **[仮想マシン]** に移動し、監視する仮想マシンを探します。 **[プロパティ]** セクションで、**[診断設定]** を選択します。  **[エージェント]** をクリックし、指定されたストレージ アカウント名をメモします。 このアカウント名は、後の手順で必要になります。

    ![Azure 診断設定](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

    ![Azure 診断設定](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

     >[!NOTE]
     >仮想マシンの作成中に監視が有効になっていなかった場合は、上に示すように、監視を有効にするオプションが提供されます。

5. ここで再び Azure ログ統合マシンについて説明します。 Azure ログ統合をインストールしたシステムと、ストレージ アカウント間の接続を確認する必要があります。 Azure ログ統合サービスを実行しているコンピューターは、監視対象の各システム上で構成されている Azure 診断によって記録された情報を取得するために、ストレージ アカウントへのアクセスが必要です。  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 Azure Storage エクスプローラーをダウンロードするには[ここ](http://storageexplorer.com/)をクリック。
   b. セットアップ ルーチンを実行する c. インストールが完了したら **[次へ]** をクリックします。**[Microsoft Azure Storage エクスプローラーを起動]** チェック ボックスはチェックを入れたままにします。  
    d. Azure にログインします。
   e. Azure 診断用に構成したストレージ アカウントが表示できることを確認します。 
        ![ストレージ アカウント](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
6. ストレージ アカウントには、オプションがいくつかありますのでご注意ください。 そのうちの 1 つは**テーブル**です。 **テーブル**には、**WADWindowsEventLogsTable** が表示されます。 </br></br>

 仮想マシンの作成中に監視が有効になっていなかった場合は、上に示すように、監視を有効にするオプションが提供されます。
7. ここで再び Azure ログ統合マシンについて説明します。 Azure ログ統合をインストールしたシステムと、ストレージ アカウント間の接続を確認する必要があります。 Azure ログ統合サービスを実行する物理コンピューターまたは仮想マシンは、ストレージ アカウントに接続し、各監視対象システムで構成されている Azure 診断が記録する情報を取得する必要があります。  
  1. Azure Storage エクスプローラーをダウンロードするには[ここ](http://storageexplorer.com/)をクリック。
  2. セットアップ ルーチンの実行
  3. インストールが完了したら **[次へ]** をクリックします。**[Microsoft Azure Storage エクスプローラーを起動]** チェック ボックスはチェックを入れたままにします。  
  4. Azure にログインします。
  5. Azure 診断用に構成したストレージ アカウントが表示できることを確認します。  
  6. ストレージ アカウントには、オプションがいくつかありますのでご注意ください。 そのうちの 1 つは**テーブル**です。 **テーブル**には、**WADWindowsEventLogsTable** が表示されます。 </br></br>
   ![ストレージ アカウント](./media/security-azure-log-integration-get-started/storage-explorer.png) 

## <a name="integrate-azure-diagnostic-logging"></a>Azure 診断ログの統合

この手順では、Azure ログ統合サービスを実行しているコンピューターを構成し、ログ ファイルを含むストレージ アカウントに接続します。
この手順を完了するには、事前にいくつかのことが必要です。  
* **FriendlyNameForSource:** Azure 診断から情報を格納するために仮想マシンを構成した、ストレージ アカウントに適用可能な表示名。
* **StorageAccountName:** これは、Azure 診断を構成するときに指定したストレージ アカウントの名前です。  
* **StorageKey:** 仮想マシン用に Azure 診断情報が格納されるストレージ アカウントに対するストレージ キー。  

ストレージ キーを取得するには、次の手順を実行します。
 1. [Azure ポータル](http://portal.azure.com)にアクセスします。
 2. Azure コンソールのナビゲーション ウィンドウで、**[すべてのサービス]** をクリックします。
  3. **[フィルター]** テキスト ボックスで、**ストレージ**を入力します。 **[ストレージ アカウント]**をクリックします。

       ![[すべてのサービス] でのストレージ アカウントを示すスクリーンショット](./media/security-azure-log-integration-get-started/filter.png)
 4. ストレージ アカウントの一覧が表示されます。ログ ストレージに割り当てたアカウントをダブルクリックします。

       ![ストレージ アカウント一覧](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. **[設定]** セクションで **[アクセス キー]** をクリックします。

      ![アクセス キー](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. **key1** をコピーし、次の手順でアクセスできるよう安全な場所に格納します。

       ![2 つのアクセス キー](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Azure ログ統合をインストールしたサーバーで、管理者特権のコマンド プロンプトを開きます (管理者特権の PowerShell コンソールではなく、管理者特権のコマンド プロンプト ウィンドウを必ず使用してください)。
 8. **c:\Program Files\Microsoft Azure Log Integration** に移動します。
 9. ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` を実行します。 </br> たとえば、``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``サブスクリプション ID をイベント XML に表示するには、サブスクリプション ID を表示名に追加します。``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>``または``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]
>60 分間待つと、ストレージ アカウントから取得されたイベントが表示されます。 表示するには、Azlog インテグレーターで **[イベント ビューアー] > [Windows ログ] > [転送されたイベント]** を開きます。

ここで説明した手順については、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>[転送されたイベント] フォルダーにデータが表示されない場合
1 時間経過しても、 **[転送されたイベント]** フォルダーにデータが表示されない場合は、次の操作を行います。

1. Azure ログ統合サービスを実行するコンピューターが、Azure にアクセスできることを確認します。 接続をテストするには、 [Azure ポータル](http://portal.azure.com) をブラウザーから開いてみます。
2. ユーザー アカウント **AzLog** にフォルダー **users\Azlog** に対する書き込みアクセス許可があることを確認します。
  <ol type="a">
   <li>**Windows エクスプローラー** を開く</li>
  <li> **c:\users** に移動</li>
  <li> **c:\users\Azlog**  を右クリック</li>
  <li> **セキュリティ**  をクリック</li>
  <li> **NT Service\Azlog** をクリックし、アカウントのアクセス許可を確認します。 アカウントがタブに表示されない場合や、適切なアクセス許可が表示されない場合は、このタブでアカウント権限を付与することができます。</li>
  </ol>
3. **Azlog source list** コマンドを実行して、**Azlog source add** コマンドに追加されたストレージ アカウントを確認します。
4. **[イベント ビューアー] > [Windows ログ] > [アプリケーション]** の順にアクセスして、Azure ログ統合からエラーが報告されていないかどうかを確認します。


インストールおよび構成中に問題が発生した場合、[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を作成し、サポートを要求するサービスとして **[ログ統合]** を選択します。

別のオプションとして、[Azure ログ統合の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)も利用可能です。 このコミュニティでは、Azure ログ統合を最大限に活用する方法に関する質問、回答、ヒント、およびコツなどによる、相互サポート機能を提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。

## <a name="next-steps"></a>次の手順
Azure ログの統合の詳細については、次のドキュメントを参照してください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md) – このドキュメントでは、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。 これは、SIEM コンポーネントを構成する方法に関する現在のガイダンスです。 詳細については、まず SIEM ベンダーに確認してください。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure アクティビティ ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。
