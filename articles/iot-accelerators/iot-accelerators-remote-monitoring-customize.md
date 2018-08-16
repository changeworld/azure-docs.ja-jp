---
title: リモート監視ソリューション UI のカスタマイズ - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータ UI にアクセスし、いくつかのカスタマイズを行う方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: 9700a76284e2ee2a652ae0dbcbaa2885ab515f79
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596557"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータをカスタマイズする

この記事では、ソース コードにアクセスし、リモート監視ソリューション アクセラレータ UI をカスタマイズする方法について説明します。 この記事では、次の内容について説明します。

## <a name="prepare-a-local-development-environment-for-the-ui"></a>UI のローカル開発環境を準備する

リモート監視ソリューション アクセラレータ UI のコードは、React.js フレームワークを使用して実装されます。 [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub リポジトリでソース コードを見つけることができます。

UI を変更するために、そのコピーをローカルで実行できます。 ローカル コピーは、ソリューションのデプロイ済みのインスタンスに接続し、テレメトリの取得などのアクションを実行します。

次の手順に、UI 開発のためのローカル環境を設定するプロセスを説明します。

1. **pcs** CLI を使用して、ソリューション アクセラレータの**基本**インスタンスをデプロイします。 デプロイの名前と仮想マシンに提供した資格情報をメモしておきます。 詳しくは、[CLI を使用したデプロイ](iot-accelerators-remote-monitoring-deploy-cli.md)に関するページをご覧ください。

1. Azure Portal または [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、ソリューションでマイクロサービスをホストする仮想マシンへの SSH アクセスを有効にします。 例: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    テストと開発時にのみ、SSH アクセスを有効にします。 SSH を有効にした場合、[できるだけ早く無効にする必要があります](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-azure-virtual-machines)。

1. Azure Portal または [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、仮想マシンの名前とパブリック IP アドレスを検索します。 例: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH を使用して、前の手順で取得した IP アドレスと、ソリューションをデプロイするために、**pcs** を実行したときに提供した資格情報を使用して、仮想マシンに接続します。

1. ローカル UX で接続できるようにするには、仮想マシンの bash シェルで、次のコマンドを実行します。

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. コマンドが完了し、Web サイトが起動したことを確認したら、仮想マシンから切断できます。

1. [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) リポジトリのローカル コピーで、**.env** ファイルを編集し、デプロイ済みのソリューションの URL を追加します。

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. `azure-iot-pcs-remote-monitoring-webui` フォルダーのローカル コピーのコマンド プロンプトで、次のコマンドを実行して、必要なライブラリをインストールし、ローカルで UI を実行します。

    ```cmd/sh
    npm install
    npm start
    ```

1. 前のコマンドは、http://localhost:3000/dashboard のローカルで UI を実行します。 サイトの実行中にコードを編集し、動的に更新されることを確認できます。

## <a name="customize-the-layout"></a>レイアウトのカスタマイズ

リモート監視ソリューションの各ページは、ソース コードで*パネル*と呼ばれる一連のコントロールから構成されます。 たとえば、**ダッシュボード** ページは、概要、マップ、アラーム、テレメトリ、および KPI の 5 つのパネルのページから構成されます。 各ページとそのパネルを定義するソース コードを、[pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub リポジトリで見つけることができます。 たとえば、**Dashboard** ページ、そのレイアウト、およびページ上のパネルを定義するコードは、[src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) フォルダーにあります。

パネルがその独自のレイアウトとサイズを管理するため、ユーザーはページのレイアウトを簡単に変更できます。 たとえば、`src/components/pages/dashboard/dashboard.js` ファイル内の **PageContent** 要素への次の変更により、マップ パネルとテレメトリ パネルの位置が交換され、マップ パネルと KPI パネルの相対的な幅が変更されます。

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![パネル レイアウトの変更](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> マップは、ローカル デプロイでは構成されません。

[パネルを複製して、カスタマイズする](#duplicate-and-customize-an-existing-control)場合は、同じパネルの複数のインスタンスや複数のバージョンを追加することもできます。 次の例は、`src/components/pages/dashboard/dashboard.js` ファイルを編集して、telemetry パネルの 2 つのインスタンスを追加する方法を示しています。

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

各パネルでさまざまなテレメトリを表示できます。

![複数のテレメトリ パネル](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> マップは、ローカル デプロイでは構成されません。

## <a name="duplicate-and-customize-an-existing-control"></a>既存のコントロールの複製とカスタマイズ

次の手順では、既存のパネルを複製し、それを変更して、変更済みのバージョンを使用する方法の例として、**アラーム** パネルを使用する方法を説明します。

1. リポジトリのローカル コピーで、`src/components/pages/dashboard/panels` フォルダー内の **alarms** フォルダーのコピーを作成します。 新しいコピーに **cust_alarms** と名付けます。

1. **cust_alarms** フォルダー内の **AlarmsPanel.js** ファイルで、クラスの名前を **CustAlarmsPanel** に編集します。

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. 次の行を `src/components/pages/dashboard/panels/index.js` ファイルに追加します。

    ```nodejs
    export * from './cust_alarms';
    ```

1. `src/components/pages/dashboard/dashboard.js` ファイル内の `AlarmsPanel` を `CustAlarmsPanel` で置き換えます。

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

これで、元の **Alarms** パネルを **CustAlarms** というコピーで置き換えました。 このコピーは、元のパネルと同一です。 これでコピーを変更できます。 たとえば、**Alarms** パネルの列の順序を変更するには、次の手順を実行します。

1. `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` ファイルを開きます。

1. 列定義を次のコード スニペットに示すように変更します。

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

次のスクリーン ショットは、**Alarms** パネルの新しいバージョンを示しています。

![更新済みの Alarms パネル](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>テレメトリ グラフのカスタマイズ

**ダッシュ ボード** ページのテレメトリ グラフは、`src/components/pages/dashboard/panels/telemtry` フォルダー内のファイルによって定義されます。 UI は、`src/services/telemetryService.js` ファイル内のソリューション バックエンドから、テレメトリを取得します。 次の手順では、テレメトリ グラフに表示される期間を 15 分から 5 分に変更する方法を示します。

1. `src/services/telemetryService.js` ファイルで、**getTelemetryByDeviceIdP15M** という関数を見つけます。 この関数のコピーを作成し、コピーを次のように変更します。

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. この新しい関数を使用して、テレメトリ グラフに入力するには、`src/components/pages/dashboard/dashboard.js` ファイルを開きます。 テレメトリ ストリームを初期化する行を見つけて、それを次のように変更します。

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

テレメトリ グラフに、5 分間のテレメトリ データが表示されるようになりました。

![1 日を示すテレメトリ グラフ](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>新しい KPI の追加

**ダッシュボード** ページには、**システム KPI** パネルの KPI が表示されます。 これらの KPI は、`src/components/pages/dashboard/dashboard.js` ファイルで計算されます。 KPI は `src/components/pages/dashboard/panels/kpis/kpisPanel.js` ファイルによってレンダリングされます。 次の手順で、新しい KPI 値を計算して、**ダッシュ ボード** ページにレンダリングする方法について説明します。 示している例では、警告アラーム KPI に新しいパーセンテージの変更を追加しています。

1. `src/components/pages/dashboard/dashboard.js` ファイルを開きます。 **initialState** オブジェクトを次のように **warningAlarmsChange** プロパティを含めるように変更します。

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. **currentAlarmsStats** オブジェクトを **totalWarningCount** をプロパティとして含めるように変更します。

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. 新しい KPI を計算します。 重大なアラーム数の計算を見つけます。 コードをコピーし、次のようにコピーを変更します。

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. KPI ストリームに新しい **warningAlarmsChange** を含めます。

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. KPI パネルに渡されるデータを更新します。

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

これで、`src/components/pages/dashboard/dashboard.js` ファイルの変更が完了しました。 次の手順では、新しい KPI を表示するために、`src/components/pages/dashboard/panels/kpis/kpisPanel.js` ファイルに行う変更について説明します。

1. 次のように新しい KPI 値を取得するために、次のコードの行を変更します。

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. 次のように、新しい KPI 値を表示するために、マークアップを変更します。

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

これで、**ダッシュボード** ページに、新しい KPI 値が表示されるようになります。

![警告 KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>マップをカスタマイズする

ソリューションのマップ コンポーネントの詳細については、GitHub の[マップのカスタマイズ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)に関するページをご覧ください。

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>その他のカスタマイズのオプション

リモート監視ソリューションのプレゼンテーションおよび視覚化レイヤーをさらに変更するために、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [Azure IoT ソリューションの構成マイクロサービス (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Azure IoT ソリューションの構成マイクロサービス (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT PCS リモート監視 Web UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のカスタマイズに役立つように使用できるリソースについて説明します。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください

リモート監視ソリューションのカスタマイズの詳細については、「[マイクロサービスをカスタマイズして再展開する](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->」をご覧ください