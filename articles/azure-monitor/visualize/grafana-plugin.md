---
title: Grafana を使用して Azure のサービスとアプリケーションを監視する
description: Grafana で表示できるように Azure Monitor および Application Insights データをルーティングします。
ms.topic: conceptual
ms.date: 10/10/2021
ms.openlocfilehash: 612a65af3cfe83b96e2604d85348be03ff2b703a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252784"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana での Azure サービスの監視
[Grafana](https://grafana.com/) とそれに含まれる [Azure Monitor データ ソース プラグイン](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/)を使用して、Azure のサービスとアプリケーションを監視できます。 このプラグインにより、3 つの Azure サービスからデータが取得されます。
- Azure Monitor メトリック: Azure リソースで収集されたデータからの数値の時系列データ。 
- Azure Monitor ログ: Kusto クエリ言語 (KQL) を使用してクエリを実行できるようにする Azure リソースからのログおよびパフォーマンス データ。
- Azure Resource Graph: サブスクリプション全体で Azure リソースのクエリをすばやく実行して識別します。

その後、このパフォーマンスと可用性のデータを Grafana ダッシュボードに表示できます。

Grafana サーバーを設定し、設定不要の Azure Monitor ダッシュボードを使用して、Azure Monitor からのメトリックとログでカスタム ダッシュボードを作成するには、次の手順のようにします。

## <a name="set-up-a-grafana-server"></a>Grafana サーバーをセットアップする

### <a name="set-up-grafana-locally"></a>Grafana をローカルにセットアップする
Grafana サーバーをローカルにセットアップするには、[Grafana をダウンロードし、ご自分のローカル環境にインストール](https://grafana.com/grafana/download)します。

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Azure Marketplace を使用して Azure 上に Grafana をセットアップする
1. Azure Marketplace に移動し、Grafana Labs のそばの Grafana を選択します。

2. 名前と詳細を入力します。 新しいリソース グループを作成します。 VM ユーザー名、VM パスワード、および Grafana サーバー管理者のパスワードとして選択した値を記録してください。  

3. VM サイズとストレージ アカウントを選択します。

4. ネットワーク構成設定を構成します。

5. 概要を表示し、使用条件を受け入れた後、 **[作成]** を選択します。

6. デプロイが完了したら、 **[Go to Resource Group] \(リソース グループに移動する)** を選択します。 新しく作成されたリソースの一覧が表示されます。

    ![Grafana リソース グループ オブジェクト](media/grafana-plugin/grafana-resource-group.png)

    ネットワーク セキュリティ グループ (この場合は *grafana-nsg*) を選択すると、Grafana サーバーへのアクセスのためにポート 3000 が使用されることを確認できます。

7. Grafana サーバーのパブリック IP アドレスを取得します。リソースの一覧に戻り、 **[パブリック IP アドレス]** を選択します。

## <a name="sign-in-to-grafana"></a>Grafana にサインインする

> [!IMPORTANT]
> Internet Explorer と古いバージョンの Microsoft Edge は、Grafana と互換性がありません。最新バージョンの Microsoft Edge など、Chromium ベースのブラウザーを使用する必要があります。 [Grafana でサポートしているブラウザー](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)に関するページをご覧ください。

1. ブラウザーで、サーバーの IP アドレスを使用して [ログイン] ページを開きます (*http://\<IP address\>:3000* または *\<DNSName>\:3000*)。 3000 は既定のポートです。セットアップ中にご自身で別のポートを選択している場合があることに注意してください。 構築した Grafana サーバーのログイン ページが表示されます。

    ![Grafana ログイン画面](./media/grafana-plugin/login-screen.png)

2. ユーザー名 *admin* と、作成済みの Grafana サーバー管理者のパスワードを使用してサインインします。 ローカル セットアップを使用している場合、既定のパスワードは *admin* であり、初回のログイン時に変更することを要求されます。

## <a name="configure-data-source-plugin"></a>データ ソース プラグインを構成する

正常にログインすると、最初のデータ ソースを追加するためのオプションが表示されます。

![データ ソースを追加する](./media/grafana-plugin/add-data-source.png)

1. **[データソースの追加]** を選択し、*Azure* という名前でフィルター処理して、**Azure Monitor** データ ソースを選択します。

![Azure Monitor データ ソース](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. データ ソースの名前を選択し、認証をマネージド ID またはアプリ登録のいずれかから選択します。

Grafana インスタンスが、マネージド ID を有効にして Azure VM でホストされている場合は、この方法を使用して認証を行うことができます。 一方、Grafana インスタンスが Azure でホストされていない場合、またはマネージド ID が有効になっていない場合は、アプリ登録と Azure サービス プリンシパルを使用して、認証を設定する必要があります。

### <a name="use-managed-identity"></a>マネージド ID の使用

1. VM でマネージド ID を有効にし、Grafana サーバーでのマネージド ID のサポートの設定を true に変更します。
    * ホスティング VM のマネージド ID には、Grafana で視覚化するサブスクリプション、リソース グループ、またはリソースに対する[監視閲覧者ロール](../roles-permissions-security.md)を割り当てる必要があります。
    * また、Grafana サーバーの構成で "managed_identity_enabled = true" の設定を更新する必要があります。詳細については、[Grafana の構成](https://grafana.com/docs/grafana/latest/administration/configuration/)に関する記事を参照してください。 両方の手順が完了したら、保存してアクセスをテストできます。

2. **[保存 & テスト]** を選択すると、Grafana によって資格情報がテストされます。 次のようなメッセージが表示されます。  
    
   ![Grafana でデータ ソースのマネージド ID の構成が承認されました](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>またはアプリの登録を使用する

1. サービス プリンシパルを作成します。Grafana では、Azure Active Directory サービス プリンシパルを使用して Azure Monitor API に接続してメトリック データを収集します。 Azure リソースへのアクセスを管理するには、サービス プリンシパルを作成するか、既存のものを使用する必要があります。
    * サービス プリンシパルを作成するには、[この手順](../../active-directory/develop/howto-create-service-principal-portal.md)を参照してください。 ご自分のテナント ID (ディレクトリ ID)、クライアント ID (アプリケーション ID)、およびクライアント シークレット (アプリケーション キー値) をコピーして保存します。
    * [アプリケーションへのロールの割り当て](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)に関する記事を参照して、監視するサブスクリプション内の Azure Active Directory アプリケーション、リソース グループ、またはリソースに[監視閲覧者ロール](../roles-permissions-security.md)を割り当てます。
  
2. 使用する接続の詳細を指定します。
    * プラグインを構成するときに、このプラグインで監視する Azure クラウド (パブリック、Azure US Government、Azure Germany、または Azure China) を指定できます。
        > [!NOTE]
        > 一部のデータ ソース フィールドの名前は、相関関係にある Azure の設定の名前とは異なっています。
        > * [Tenant ID]\(テナント ID\) は Azure ディレクトリ ID です。
        > * [Client ID]\(クライアント ID\) は Azure Active Directory のアプリケーション ID です。
        > * [Client Secret]\(クライアント シークレット\) は Azure Active Directory のアプリケーション キー値です。

3. **[保存 & テスト]** を選択すると、Grafana によって資格情報がテストされます。 次のようなメッセージが表示されます。  
    
   ![Grafana でデータ ソースのアプリ登録の構成が承認されました](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>Azure Monitor データ ソース ダッシュボードを使用する

Azure Monitor プラグインに含まれる複数のダッシュボードをインポートして、そのまますぐに使用できます。

1. Azure Monitor プラグインの **[ダッシュボード]** タブをクリックして、使用できるダッシュボードの一覧を表示します。

   ![Azure Monitor のデータ ソース ダッシュボード](./media/grafana-plugin/azure-data-source-dashboards.png)

2. **[インポート]** をクリックして、ダッシュボードをダウンロードします。

3. インポートしたダッシュボードの名前をクリックして開きます。

4. ダッシュボードの上部にあるドロップダウン セレクターを使用して、目的のサブスクリプション、リソース グループ、およびリソースを選択します。

   ![ストレージ分析情報ダッシュボード](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>Grafana ダッシュボードを構築する

1. Grafana のホーム ページに移動し、 **[Create your first dashboard]\(初めてのダッシュボードの作成\)** を選択します。

2. 新しいダッシュボードで、 **[Add an empty panel]\(空のパネルの追加\)** を選択します。

3. ダッシュボードに空の "*時系列*" パネルが表示され、下にクエリ エディターが表示されます。 構成した Azure Monitor データ ソースを選択します。
   * Azure Monitor のメトリックの収集 - サービスのドロップダウンで **[Metrics]\(メトリック\)** を選択します。 セレクターの一覧が表示されます。この一覧から、このグラフで監視するリソースとメトリックを選択できます。 VM からメトリックを収集するには、**Microsoft.Compute/VirtualMachines** 名前空間を使用します。 VM とメトリックを選択したら、ダッシュボードでのそれらのデータの表示を開始できます。
     ![Grafana での Azure Monitor メトリック用のグラフの構成](./media/grafana-plugin/metrics-config.png)
   * Azure Monitor ログ データの収集 - サービスのドロップダウンから **[Logs]\(ログ\)** を選択します。 クエリを実行するリソースまたは Log Analytics ワークスペースを選択し、クエリ テキストを設定します。 Azure Monitor プラグインを使用すると、特定のリソースまたは Log Analytics ワークスペースのログのクエリを実行できることに注意してください。 下のクエリ エディターでは、既にある任意のログ クエリをコピーすることも、新しいログ クエリを作成することもできます。 クエリの入力中は、IntelliSense が起動し、オートコンプリートによる選択肢が表示されます。 最後に、視覚化の種類と **時系列** を選択して、クエリを実行します。
    
     > [!NOTE]
     >
     > プラグインに用意されている既定のクエリでは、2 つのマクロ ($__timeFilter() と $__interval) が使用されます。 
     > Grafana では、これらのマクロによって、グラフの一部を拡大するときに、時間範囲と時間グレインを動的に計算できます。 これらのマクロを削除し、*TimeGenerated > ago(1h)* などの標準的な時間フィルターを使用できますが、それを行うと、グラフで拡大機能はサポートされなくなります。
    
     次の例は、Application Insights リソースで実行されているすべての要求の平均応答時間についてのクエリを示したものです。

     ![Grafana での Azure Log Analytics 用のグラフの構成](./media/grafana-plugin/logs-config.png)

    * 上に示したメトリックとログのクエリに加えて、Azure Monitor プラグインでは、[Azure Resource Graph](../../governance/resource-graph/concepts/explore-resources.md) のクエリがサポートされています。

## <a name="advanced-grafana-features"></a>Grafana の高度な機能

### <a name="variables"></a>変数
一部のリソースとクエリの値は、ダッシュボード ユーザーが UI ドロップダウンから選択でき、リソースまたはクエリで更新されます。
たとえば、次のクエリでは Log Analytics ワークスペースの使用状況が示されています。
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

使用可能なすべての **ワークスペース** を一覧表示する変数を構成し、ユーザーの選択に基づいてクエリ対象のリソースを更新できます。
新しい変数を作成するには、ダッシュボードの右上の領域にある [Settings]\(設定\) をクリックし、 **[Variables]\(変数\)** 、 **[New]\(新規\)** の順に選択します。
[Variable]\(変数\) ページで、データ ソースと、値の一覧を取得するために実行するクエリを定義します。

![Grafana での変数の定義](./media/grafana-plugin/define-variable.png)

作成した後、選択した値を使用するようにクエリのリソースを変更すると、グラフがそれに従って応答します。

![変数を使用したクエリ](./media/grafana-plugin/query-with-variable.png)

Azure Monitor プラグインで使用できる[テンプレート変数](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/)の完全な一覧を参照してください。

### <a name="create-dashboard-playlists"></a>ダッシュボード再生リストを作成する

Grafana の多くの有効な機能の 1 つに、ダッシュボード再生リストがあります。 複数のダッシュボードを作成し、それらを再生リストに追加して、表示するダッシュボードごとに間隔を構成できます。 [Dashboards]\(ダッシュボード\) メニュー項目に移動し、 **[Playlists]\(プレイリスト\)** を選択して、循環する既存のダッシュボードのプレイリストを作成します。 それらを大きな壁のモニタに表示して、グループの状態ボードを提供できます。

![Grafana の再生リストの例](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure 上に Grafana 環境をセットアップした場合、VM を使用しているかどうかにかかわらず、VM が実行されたときに課金されます。 追加料金が発生しないようにするには、この記事で作成されたリソース グループをクリーンアップします。

1. Azure Portal の左側のメニューから、 **[リソース グループ]** 、 **[Grafana]** の順にクリックします。
2. リソース グループのページで、 **[削除]** をクリックし、テキスト ボックスに「**Grafana**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [Azure Monitor のメトリックとログを比較する](../data-platform.md)