---
title: グループ ポリシーからゲスト構成ポリシーを作成する方法
description: グループ ポリシーをポリシー定義に変換する方法について説明します。
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772296"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>グループ ポリシーからゲスト構成ポリシーを作成する方法

始める前に、[ゲスト構成](../concepts/guest-configuration.md)の概要を記したページと、ゲスト構成ポリシーの効果について詳しく記されている[ゲスト構成の修復オプションの構成方法](../concepts/guest-configuration-policy-effects.md)に関するページを読むことをお勧めします。

> [!IMPORTANT]
> グループ ポリシーからゲスト構成への変換は、**プレビュー段階** です。 グループ ポリシー設定のすべての種類について、PowerShell 7 で対応する DSC リソースを使用できるわけではありません。
>
> このページのすべてのコマンドは、**Windows PowerShell 5.1** で実行する必要があります。
> 結果の出力 MOF ファイルを、PowerShell 7.1.3 以降の `GuestConfiguration` モジュールを使用してパッケージ化する必要があります。
> 
> **AuditIfNotExists** を使用するカスタム ゲスト構成ポリシー定義は一般提供されていますが、ゲスト構成で **DeployIfNotExists** を使用する定義は **プレビュー段階** です。
> 
> Azure の仮想マシンには、ゲスト構成拡張機能が必要となります。 すべてのマシンに拡張機能を大規模にデプロイするためには、次のようなポリシー イニシアティブを割り当ててください: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
>
> カスタム コンテンツ パッケージでは、秘密や機密情報を使用しないでください。

オープンソース コミュニティから、エクスポートされた[グループ ポリシー](/support/windows-server/group-policy/group-policy-overview) テンプレートを PowerShell DSC 形式に変換するための [BaselineManagement](https://github.com/microsoft/BaselineManagement) モジュールが公開されています。 `GuestConfiguration` モジュールと共に、エクスポートされたグループ ポリシー オブジェクトから Windows 用のゲスト構成パッケージを作成できます。 その後、そのゲスト構成パッケージを使用すると、ドメインに参加していない場合でも、ローカル ポリシーを使用してサーバーを監査または構成できます。

このガイドでは、グループ ポリシー オブジェクト (GPO) から Azure Policy ゲスト構成パッケージを作成するプロセスについて説明します。

## <a name="download-required-powershell-modules"></a>必要な PowerShell モジュールをダウンロードする

PowerShell で必要なすべてのモジュールをインストールするには:

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

Active Directory 環境からグループ ポリシー オブジェクト (GPO) をバックアップするには、リモート サーバー管理ツールキット (RSAT) で PowerShell コマンドを使用できる必要があります。

Windows 10 のグループポリシー管理コンソールで RSAT を有効にするには:

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>グループ ポリシーをエクスポートしてゲスト構成に変換する

グループ ポリシー ファイルをエクスポートし、ゲスト構成で使用するために DSC に変換するには、3 つのオプションがあります。

- 1 つのグループ ポリシー オブジェクトをエクスポートします
- OU についてマージされたグループ ポリシー オブジェクトをエクスポートします
- マシン内からマージされたグループ ポリシー オブジェクトをエクスポートします

### <a name="single-group-policy-object"></a>1 つのグループ ポリシー オブジェクト

`Group Policy` モジュールのコマンドを使用して、エクスポートするグループ ポリシー オブジェクトの GUID を特定します。 大規模な環境では、出力を `where-object` にパイプして、名前でフィルター処理することを検討します。

**ドメイン参加済み** Windows マシン上の **Windows PowerShell 5.1** 環境で、次のものを実行します。

```powershell
# List all Group Policy Objects
Get-GPO -all
```

グループ ポリシーをファイルにバックアップします。 コマンドは "Name" パラメーターも受け取りますが、ポリシーの GUID を使用するとエラーが発生しにくくなります。

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

エクスポートした PowerShell スクリプトを調べて、すべての設定が指定されていることと、エラー メッセージが書き込まれていないことを確認します。 「[カスタム ゲスト構成パッケージの成果物の作成方法](./guest-configuration-create.md)」ページのガイダンスに従い、MOF ファイルを使用して新しい構成パッケージを作成します。
ゲスト構成パッケージを作成してテストする手順は、PowerShell 7 環境で実行する必要があります。

### <a name="merged-group-policy-objects-for-an-ou"></a>OU についてマージされたグループ ポリシー オブジェクト

指定した組織単位でマージしたグループ ポリシー オブジェクトの組み合わせ (ポリシーの結果セットに似たもの) をエクスポートします。 マージ操作では、リンクの状態、強制、アクセスが考慮されますが、WMI フィルターは考慮されません。

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

コマンドの出力からは、ファイルの詳細が返されます。

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>マシン内からマージされたグループ ポリシー オブジェクト

Windows PowerShell から `Merge-GPOs` コマンドを実行して、特定のマシンに適用されているポリシーをマージすることもできます。 WMI フィルターは、マシン内からマージする場合にのみ評価されます。

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

コマンドの出力では、ファイルの詳細が返されます。

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>省略可能: テスト用にサンプル グループ ポリシー ファイルをダウンロードする

Active Directory 環境からグループ ポリシー ファイルをエクスポートする準備ができていない場合は、Windows セキュリティ コンプライアンス ツールキットから Windows Server セキュリティ ベースラインをダウンロードできます。

Windows Security Compliance ツールキットから Windows Server 2019 セキュリティ ベースラインのディレクトリを作成してダウンロードします。

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

ダウンロードした Server 2019 ベースラインのブロックを解除して展開します。

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

**MapGuidsToGpoNames.ps1** を使用して、Server 2019 ベースラインの内容を検証します。

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>次の手順

- ゲスト構成用の[パッケージ成果物を作成する](./guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](./guest-configuration-create-test.md)。
- マシンからアクセス可能になるように、[パッケージ成果物を発行](./guest-configuration-create-publish.md)する。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
