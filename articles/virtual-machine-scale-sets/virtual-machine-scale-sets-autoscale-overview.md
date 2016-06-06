<properties
	pageTitle="自動スケールと仮想マシン スケール セット | Microsoft Azure"
	description="診断機能と自動スケール リソースを使用してスケール セット内の仮想マシンを自動スケールする方法を説明します。"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 自動スケールと仮想マシン スケール セット

スケール セット内の仮想マシンの自動スケールとは、アプリケーションでパフォーマンス要件とサービス レベル アグリーメント (SLA) を達成するために、必要に応じてセット内の仮想マシンを作成または削除することを意味します。作業の量が多くなると、アプリケーションで効率よく作業を実行できるようにするために、追加リソースが必要になる場合があります。

自動スケールは、システム パフォーマンスを継続的に監視し、リソースの追加または不要リソースの削除に関する決定を行うことで管理オーバーヘッドを軽減する、自動化されたプロセスです。スケールはエラスティック プロセスです。システムの負荷が増加したときにはより多くのリソースをプロビジョニングし、リソースの需要が減ったときにはリソースの割り当てを解除してコストを軽減しながらも、適切なパフォーマンスを維持し て SLA を達成することができます。

スケール セットに対する自動スケールは、Azure Resource Manager テンプレート、Azure PowerShell、または Azure CLI を使用して設定します。

## Resource Manager テンプレートを使用したスケール設定

アプリケーションの各リソースを個別にデプロイして管理するのではなく、1 回の連携した操作ですべてのリソースをデプロイしてプロビジョニングするテンプレートを使用します。このテンプレートでは、アプリケーションのリソースを定義し、さまざまな環境に対応するデプロイ パラメーターを指定します。テンプレートは、JSON、およびデプロイの値を構築する際の式で構成されます。詳しくは、「[Azure Resource Manager のテンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。

テンプレートでは、容量 (capacity 要素) を指定します。

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

容量によって、セット内の仮想マシンの数を指定します。容量を手動で変更するには、別の値を指定したテンプレートをデプロイします。容量を変更するためだけにテンプレートをデプロイする場合、新しい容量を指定した SKU 要素のみを含めます。

スケール セットの容量を自動的に変更するには、Azure Resource Manager によって指定される autoscaleSettings リソースと、スケール セット内の仮想マシンにインストールされている Azure 診断拡張機能を組み合わせて使用します。

### Azure 診断拡張機能の構成

自動スケールは、スケール セット内の各仮想マシンでメトリックが正常に収集されている場合にのみ実行できます。Azure 診断拡張機能は、自動スケール リソースにおけるメトリック収集のニーズを満たす監視機能と診断機能を備えています。この拡張機能は、Resource Manager テンプレートの一部としてインストールできます。この拡張機能の使用方法について詳しくは、「[Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)」をご覧ください。

次の例では、テンプレートで診断拡張機能を構成するために使用される変数を示しています。

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="ja-JP"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

データが格納されるストレージ アカウントの名前や、データが収集されるスケール セットの名前などの値については、テンプレートがデプロイされたときにパラメーターが指定されます。また、この Windows Server の例では Thread Count パフォーマンス カウンターのみが収集されますが、Windows または Linux で使用可能なすべてのパフォーマンス カウンターを使用して診断情報を収集でき、拡張機能の構成に含めることができます。

次の例では、テンプレートにおける拡張機能の定義を示しています。

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

診断拡張機能を実行すると、指定したストレージ アカウントに置かれたテーブルにデータが収集されます。収集されたデータは、WADPerformanceCounters テーブルで見ることができます。たとえば、これはスケール セットに仮想マシンが最初に作成されたときに収集されたスレッド数です。

![Screenshot of the Windows Server performance counter data before added load](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### autoScaleSettings リソースの構成

autoscaleSettings リソースは、スケール セット内の仮想マシン数の増減に関する判断を下すために、診断拡張機能によって収集された情報を利用します。

次の例は、テンプレートにおけるリソースの構成を示しています。

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

この例では、自動スケール アクションを定義するために 2 つの規則を作成しています。最初の規則でスケールアウト アクションを定義し、2 番目の規則でスケールイン アクションを定義します。この 2 つの規則では次の値が指定されています。

- **metricName** - これは、診断拡張機能の wadperfcounter 変数で定義したパフォーマンス カウンターと同じです。この例では、Thread Count カウンターが使用されます。  
- **metricResourceUri** - 仮想マシン スケール セットのリソース識別子です。この識別子には、リソース グループの名前、リソースプロバイダーの名前、スケールするスケール セットの名前が含まれます。
- **timeGrain** – 収集するメトリックの粒度です。この例では、1 分間隔でデータを収集します。この値は、timeWindow と組み合わせて使用されます。
- **statistic** – 自動スケーリング処理に対応するためのメトリックの集計方法を指定します。指定できる値は Average、Min、Max です。
- **timeWindow** – インスタンス データを収集する時間の範囲です。5 分～ 12 時間の範囲で指定する必要があります。
- **timeAggregation** – 時間経過に沿って収集されたデータの集計方法を指定します。既定値は Average です。指定できる値は Average、Minimum、Maximum、Last、Total、Count です。
- **operator** – メトリック データとしきい値を比較するときに用いる演算子です。指定できる値は、Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual です。
- **threshold** – スケール処理の開始点となる値です。スケールアウト アクションに設定するしきい値と、スケールイン アクションに設定するしきい値の間には、十分な差を持たせてください。2 つのしきい値を同じ値に設定した場合 (たとえば、この例で両方のしきい値を 600 スレッドに設定した場合)、セットのサイズが絶え間なく増減されることを避けるために、スケール アクションが実行されなくなります。
- **direction** – しきい値に達したときに実行するアクションを指定します。指定できる値は Increase または Decrease です。
- **type** – 発生させるアクションの種類です。これは ChangeCount に設定する必要があります。
- **value** – スケール セットに追加する仮想マシンの数またはスケール セットから削除する仮想マシンの数です。1 以上の値を設定する必要があります。
- **cooldown** – 前回のスケーリング処理から次回のスケーリング処理までの待機時間です。これは 1 分から 1 週間の範囲で指定する必要があります。

使用するパフォーマンス カウンターによっては、このテンプレート構成の一部の要素を別の方法で使用します。次に示す例では、パフォーマンス カウンターに Thread Count を使用し、スケールアウト アクションのしきい値を 650、スケールイン アクションのしきい値を 550 に設定しています。%Processor Time などのカウンターを使用する場合は、スケール アクションを決定する CPU 使用率をしきい値として設定することになります。

セット内の仮想マシンに負荷が発生し、スケール アクションがトリガーされると、テンプレート内の value 要素に応じてセット内の仮想マシン数が増やされます。たとえば、容量が 3、スケール アクション値が 1 に設定されているスケール セットは次のようになります。

![Screenshot of the number of machines in a set before autoscaling](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

負荷が発生し、平均スレッド数がしきい値 650 を超えたときには、次のようになります。

![Screenshot of the Windows Server performance counter data after added load](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

スケールアウト アクションがトリガーされ、セットの容量が 1 ずつ増加します。

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

さらに、スケール セットに仮想マシンが追加されます。

![Screenshot of the number of machines in a set after autoscaling](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

5 分間のクールダウン期間の後で、仮想マシンの平均スレッド数がまだ 600 を超えている場合には、セットに仮想マシンがもう 1 つ追加されます。平均スレッド数が 550 を下回ったままになると、スケール セットの容量が 1 削減され、セットから 1 つの仮想マシンが削除されます。

## スケール操作に関する情報

- [Azure ポータル](https://portal.azure.com/) - 現在、ポータルを使用して入手できる情報の量は限られています。
- [Azure リソース エクスプローラー](https://resources.azure.com/) - スケール セットの現在の状態を調査するうえで最適なツールです。
- [Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) - **Get-AzureRmResource** や **Get-Autoscalesetting** などのコマンドレットを使用して、スケール セットに関する情報を入手できます。
- [Azure CLI](../xplat-cli-azure-resource-manager.md) - **azure resource show** コマンドを使用して、セットに関する情報を入手できます。
- 通常の仮想マシンに接続するときと同じように Jumpbox 仮想マシンに接続し、スケール セット内の仮想マシンにリモートからアクセスして個々のプロセスを監視することができます。

## 次のステップ

1. 初めてスケール セットを作成するときには、「[仮想マシン スケール セットを作成する](virtual-machine-scale-sets-windows-create.md)」をご覧ください。
2. 自動スケールを構成したスケール セットの作成方法の例については、[Windows 仮想マシン スケール セットの自動スケール](virtual-machine-scale-sets-windows-autoscale.md)に関する記事または[Linux 仮想マシン スケール セットの自動スケール](virtual-machine-scale-sets-linux-autoscale.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0525_2016-->