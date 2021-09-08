---
title: Azure Automation State Configuration からゲスト構成への移行計画
description: この記事では、Azure Automation の DSC version 2 から Azure Policy の version 3 に移行したい方のためにその手順を説明し、技術的なガイドを提供します。
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868621"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>ゲスト構成への移行計画のための Azure Automation State Configuration

ゲスト構成は、Azure Automation State Configuration (別名 Azure Automation Desired State Configuration (AADSC)) によってこれまで提供されてきた機能の、最新の実装です。
可能であれば、この新サービスにコンテンツとマシンを移行する計画を立ててください。
この記事では、Azure Automation からゲスト構成に移行する計画の立て方を説明します。

ゲスト構成では、顧客からの問い合わせが多い課題に対応するための、新たな機能を導入しています。

- 構成のサイズ上限の引き上げ (100 MB)
- Azure Resource Graph を利用した、リソース の ID と情報を含む詳細なレポート
- 同一のマシンに対する複数の構成の管理
- マシンの状態に問題が生じた場合に修復を行うタイミングの制御
- Linux と Windows の両方における、PowerShell DSC リソースの使用

作業を始める前に、[Azure Policy のゲスト構成](../concepts/guest-configuration.md)に関するページで、おおまかな説明を読んでおくといいでしょう。

## <a name="understand-migration"></a>移行について理解する

コンテンツを先に再デプロイし、その後でマシンを移行するのが、最もいい移行方法です。 通常のおおまかな移行手順は次のとおりです。

- Azure Automation から構成をエクスポートする
- 必要なモジュールを確認して環境にロードする
- 構成をコンパイルする
- ゲスト構成パッケージを作成、発行する
- ゲスト構成パッケージをテストする
- ハイブリッド マシンを Azure Arc にオンボードする
- Azure Automation State Configuration からサーバーの登録を削除する
- ゲスト構成を使用してサーバーに構成を割り当てる

ゲスト構成では、PowerShell version 7 で DSC version 3 を使用します。 DSC version 3 は、[Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) および [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) で、古いバージョンの DSC と共存できます。
実装は別々になります。 ですが競合は検出されません。

ゲスト構成では、サービスに対するモジュールや構成の発行、サービス上でのコンパイルは必要ありません。 専用のツールを使用してコンテンツを開発、テストし、HTTPS でマシンからアクセスできるあらゆる場所 (通常は Azure Blob Storage) にそれを発行できます。

移行する際にしばらくの間両方のサービスにマシンを置いておきたい場合は、管理がややこしくなるものの、そうすることに技術的な障害はありません。 それら 2 つのサービスは独立しています。

## <a name="export-content-from-azure-automation"></a>Azure Automation からコンテンツをエクスポートする

最初に、Azure Automation State Configuration にあるコンテンツを確認し、ゲスト構成のコンテンツ パッケージを作成、テスト、発行する開発環境にそれをエクスポートします。

### <a name="configurations"></a>構成

Azure Automation からエクスポートできるのは構成スクリプトだけです。 Node の構成やコンパイル済み MOF ファイルはエクスポートできません。
MOF ファイルを直接 Automation Account に発行して元のファイルにアクセスできなくなった場合、個人所有する構成スクリプトから再コンパイルするか、あるいは、オリジナルが見つからずに構成を再作成しなければならない場合があります。

Azure Automation から構成スクリプトをエクスポートするには、まず、目的の構成を含む Azure Automation アカウントと、Automation Account をデプロイしている Resource Group の名前を調べます。

PowerShell の Az.Automation モジュールをインストールします。

```powershell
Install-Module Az.Automation
```

Get-AzAutomationAccount コマンドで、Automation Account と、それをデプロイしている Resource Group を調べます。
次のステップでは ResourceGroupName と AutomationAccountName プロパティが重要です。

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

Automation Account の構成を見つけます。 1 つの構成につき 1 つの項目が出力されます。 たくさんある場合は、扱いやすいように、その情報を変数に保存します。

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

最後に、Export-AzAutomationDscConfiguration コマンドで、各構成をスクリプト ファイルとしてローカルにエクスポートします。 生成されるファイルの名前は `\ConfigurationName.ps1` の形式になります。

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>PowerShell パイプラインで構成をエクスポートする

アカウントと構成の数を把握したら、次に、すべての構成をマシンのローカル フォルダーにエクスポートしたいという場合があります。
この作業を自動化するには、上の各コマンドの出力を、パイプで次のコマンドに渡します。

例では 5 つの構成をエクスポートします。 成功したかどうかは、出力結果を見て判断します。

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>複雑な構成ファイルの分割を検討する

ゲスト構成では、マシン 1 台に対して複数の構成を管理できます。
Azure Automation State Configuration 用に書かれた構成の多くは、マシン 1 台につき構成 1 つという制約があります。 ゲスト構成の有するこの利点を活かして、大きな構成ファイルを多くの小さな構成に分割し、構成 1 つにつき特定のシナリオを 1 つずつカバーできます。

ゲスト構成には、構成の順序を調整するオーケストレーションの機能がないため、構成を順に処理する必要がある場合は、複数のステップを 1 つのパッケージにまとめます。

### <a name="modules"></a>モジュール

Azure Automation からモジュールをエクスポートすること、どの構成にどのモジュールやバージョンが必要かを自動的に判断することはできません。 新しいゲスト構成パッケージを作成するには、ローカル環境にモジュールが存在する必要があります。 移行に必要なモジュールのリストを作成するには、PowerShell で Azure Automation へのクエリを実行して、モジュールの名前とバージョンを取得します。

カスタム作成したモジュールを使用していて、それが非公開の開発環境にしかない場合は、それを Azure Automation からエクスポートすることはできません。

アカウントに存在する構成に必要なカスタム モジュールが環境にない場合は、構成をコンパイルできないので、その構成は移行できません。

#### <a name="list-modules-imported-in-azure-automation"></a>Azure Automation にインポートしたモジュールのリストを取得する

オートメーション アカウントにインストールしたすべてのモジュールのリストを取得するには `Get-AzAutomationModule` コマンドを使用します。 IsGlobal プロパティを見れば、モジュールが常に Azure Automation に組み込まれているかどうか、つまり、モジュールがアカウントに対し発行されたかどうかが分かります。

たとえば、自分のアカウントのいずれかに発行したすべてのモジュールのリストを作成するには、次のようにします。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

PowerShell ギャラリーを補助的に使用して、公開しているモジュールの詳細を確認することもできます。 たとえば次の例では、新しい Automation Account に組み込まれた、DSC リソースを含むモジュールのリストを作成します。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>PowerShell ギャラリーまたは PowerShellGet リポジトリからモジュールをダウンロードする

PowerShell ギャラリーからモジュールをインポートした場合は、`Find-Module` から `Install-Module` にパイプで直接出力を渡すことができます。 パイプを使って複数のコマンド間で出力を受け渡しすれば、その時点で Automation Account に存在するモジュールのうち、PowerShell ギャラリーに公開されているものすべてを、開発環境にロードできます。

カスタム NuGet フィードを [PowerShellGet リポジトリ](/powershell/scripting/gallery/how-to/working-with-local-psrepositories)としてローカル環境に登録しておけば、同じ方法で、その Nuget フィードからモジュールをプルできます。

例の `Find-Module` コマンドはエラーを抑制しません。つまり、ギャラリーに存在しないすべてのモジュールに対してエラー メッセージが出ます。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>構成スクリプトを見て必要なモジュールを確認する

Azure Automation から構成スクリプトをエクスポートした場合は、その内容を見ることで、各構成を MOF ファイルにコンパイルするのに必要なモジュールを確認できます。 この作業が必要なのは、モジュールを削除した Automation Account に構成が存在する場合だけです。
それらの構成にはもうマシンに対する効力がありませんが、引き続きアカウントに残る場合があります。

各ファイルの内容の上の方にある、Import-DscResource を含む行を探します。
このコマンドは構成の内部でのみ使用でき、コンパイル時にモジュールをロードします。

たとえば、PowerShell ギャラリーの WindowsIISServerConfig 構成には、この例の行が含まれています。

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

この構成では、xWebAdministration モジュールの version 1.19.0.0 と PSDesiredStateConfiguration モジュールが必要です。

### <a name="test-content-in-azure-guest-configuration"></a>Azure ゲスト構成でコンテンツをテストする

Azure Automation State Configuration から移行するコンテンツをゲスト構成で使用できるかどうかを調べるには、「[Windows 用のゲスト構成ポリシーを作成する方法](./guest-configuration-create.md)」に詳しいチュートリアルがあるので、その説明に従います。

[構成を作成する](./guest-configuration-create.md#author-a-configuration)の手順では、MOF ファイルを生成する構成スクリプトに、Azure Automation State Configuration からエクスポートしたスクリプトの 1 つを使用します。 構成を MOF ファイルにコンパイルしてゲスト構成パッケージを作成するには、必要な PowerShell モジュールを環境にインストールしておく必要があります。

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>ゲスト構成でモジュールが機能しない場合はどうすればいいですか。

モジュールによっては、ゲスト構成で互換性の問題が起こる場合があります。 よくあるのは .NET Framework と .NET Core の間の問題です。 詳しい技術的な情報は、[Windows PowerShell 5.1 と PowerShell (core) 7.x の違い](/powershell/scripting/whats-new/differences-from-windows-powershell)に関するページに記載しています

互換性の問題を解決する 1 つの方法は、`powershell.exe` を実行することにより、PowerShell 7 にインポートしたモジュールから Windows PowerShell でコマンドを実行することです。
Azure-Policy リポジトリにこの方法を実行するサンプル モジュールがあります。このモジュールでは [Windows DSC Configuration](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97) の状態を検査します。

この例は、簡単な概念実証の例にもなっています。

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>移行するすべてのモジュールの Get-TargetResource に Reasons プロパティを追加する必要がありますか。

[Reasons プロパティ](../concepts/guest-configuration-custom.md#special-requirements-for-get)を設定すると、Azure Portal で構成の割り当ての結果が見やすくなります。 モジュールの `Get` メソッドで Reasons を指定しない場合は、`Get` メソッドで取得できるプロパティの詳細を含む標準の出力が表示されます。 よって、移行時にこれを行うかどうかは任意です。

## <a name="machines"></a>マシン

ゲスト構成で Azure Automation State Configuration のコンテンツのテストを終えたら、マシンを移行する計画を立てます。

Azure Automation State Configuration は、Azure 上の仮想マシンと Azure 外のハイブリッド マシンのどちらでも利用できます。 これらの各シナリオに対し、異なる手順による計画を立てる必要があります。

### <a name="azure-vms"></a>Azure VM

Azure 上には既に Azure 仮想マシン用の [リソース](../../../azure-resource-manager/management/overview.md#terminology) があります。つまり、ゲスト構成割り当てを行って、その仮想マシンを構成に関連付ける準備ができています。 Azure Automation State Configuration から仮想マシンを削除してから、ゲスト構成を使用して構成を割り当てるのが、Azure 仮想マシン移行の全体的な手順です。

Azure Automation State Configuration からマシンを削除するには「[Automation State Configuration から構成とノードを削除する方法](../../../automation/state-configuration/remove-node-and-configuration-package.md)」の手順に従います。

ゲスト構成を使用して構成を割り当てるには「[準拠していないリソースを識別するためのポリシー割り当てを作成する」を参照してください。](../assign-policy-portal.md)」などの、Azure Policy クイックスタートの手順に従います。
ステップ 6 でポリシー定義を選ぶときは、Azure Automation State Configuration から移行した、構成に対して適用可能な定義を選びます。

### <a name="hybrid-machines"></a>ハイブリッド マシン

Azure の外にあるマシンも [Azure Automation State Configuration に登録できます](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)が、Azure にはそのマシンのためのマシン リソースがありません。 Azure Automation への接続は、 Local Configuration Manager サービスによってマシン内部で処理します。ノードの記録は、Azure Automaion プロバイダー内のリソースとして管理します。

Azure Automation State Configuration からマシンを削除する前に、[Azure Arc 対応サーバー](../../../azure-arc/servers/overview.md)として各ノードをオンボードします。
Azure Arc にオンボードすると Azure にマシン リソースが作成され、マシンを Azure Policy で管理できるようになります。 マシンはいつでも Azure Arc にオンボードできますが、Azure Automation State Configuration を使用すればこの作業を自動化できます。

マシンは、PowerShell DSC を使用して Azure Arc 対応サーバーに登録できます。
詳しくは「[Windows PowerShell DSC を使用して Connected Machine エージェントをインストールする方法](../../../azure-arc/servers/onboard-dsc.md)」をご覧ください。
ただし、Azure Automation State Configuration では、Automation Account 1 つにつき、マシン 1 台と、それに対応する構成 1 つしか管理できないことに留意してください。 ですが、ゲスト構成のコンテンツをエクスポート、テストまたは作成してから、Azure Automation でノードの構成を “切り替えて”、Azure Arc にオンボードできます。ノードの登録を Azure Automation State Configuration から削除する最後のステップを実行した後は、常にゲスト構成でマシンの状態を管理します。

## <a name="troubleshooting-issues-when-exporting-content"></a>コンテンツのエクスポートに関するトラブルシューティング

既知の問題の詳細を記載します

### <a name="exporting-configurations-results-in--character-in-file-name"></a>構成をエクスポートすると、ファイル名に “\\” 記号が挿入されます

MacOS または Linux で PowerShell を使用すると、`Export-AzAutomationDSCConfiguration` によるファイル名の出力で問題が発生します。

回避策として、PowerShell ギャラリーで公開している [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/) というモジュールがあります。
このモジュールにはコマンドが 1 つだけ含まれていて、そのコマンドでは、Azure Automation に保存してある構成のコンテンツをエクスポートするよう、サービスに REST 要求を行います。

## <a name="next-steps"></a>次のステップ

- ゲスト構成の[パッケージ アーティファクトを作成する](./guest-configuration-create.md)。
- 開発環境から[パッケージ アーティファクトをテストする](./guest-configuration-create-test.md)。
- 自分のマシンからアクセスできるように、[パッケージ アーティファクトを公開する](./guest-configuration-create-publish.md)。
- `GuestConfiguration` モジュールを使用して環境の大規模管理を行うための [Azure Policy 定義](./guest-configuration-create-definition.md) を作成する。
- Azure portal で[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成のポリシー割り当てのコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を確認する方法を学ぶ。
