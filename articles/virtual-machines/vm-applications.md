---
title: Azure Compute Gallery の VM アプリケーションの概要 (プレビュー)
description: Azure Compute Gallery の VM アプリケーション パッケージについて学習します。
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/02/2021
ms.author: cynthn
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b2d5c3c1d204771b8380a06ac9c103d4473e0d8f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708990"
---
# <a name="vm-applications-overview-preview"></a>VM アプリケーションの概要 (プレビュー)

VM アプリケーションは、仮想マシンのアプリケーションの管理、共有、およびグローバル配布を簡素化する Azure Compute Gallery (旧称 Shared Image Gallery) のリソースの種類です。

> [!IMPORTANT]
> **Azure Compute Gallery の VM アプリケーション** は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


アプリが事前にインストールされている VM のイメージを作成することはできますが、その場合はアプリケーションを変更するたびにイメージを更新する必要があります。 VM イメージからアプリケーションのインストールを分離することは、コードを変更するたびに新しいイメージを公開する必要がないことを意味します。

アプリケーション パッケージには、他のデプロイおよびパッケージ化方法よりも利点があります。

- パッケージのグループ化とバージョン管理

- VM アプリケーションは対象のインフラストラクチャに近い位置にグローバルにレプリケートできます。そのため、AzCopy またはその他のストレージ コピー メカニズムを使用して Azure リージョン間でそれらをコピーする必要はありません。

- Azure のロール ベースのアクセス制御 (RBAC) を使用した他のユーザーとの共有

- 仮想マシンのサポート、および柔軟で均一なスケール セット

- VM またはスケール セットにネットワーク セキュリティ グループ (NSG) ルールが適用されている場合、インターネット リポジトリからパッケージをダウンロードできないことがあります。 また、ストレージ アカウントでは、ロックダウンされた VM にパッケージをダウンロードするには、プライベート リンクを設定する必要があります。
- VM アプリケーションは、[DeployIfNotExists](../governance/policy/concepts/effects.md) ポリシーで使用できます。


## <a name="what-are-vm-app-packages"></a>VM アプリ パッケージとは

VM アプリケーション パッケージでは、複数のリソースの種類が使用されます。

| リソース | 説明|
|----------|------------|
| **Azure Compute Gallery** | ギャラリーは、アプリケーション パッケージを管理および共有するためのリポジトリです。 ユーザーは、ギャラリー リソースを共有でき、すべての子リソースは自動的に共有されます。 ギャラリー名は、サブスクリプションごとに一意である必要があります。 たとえば、1 つのギャラリーにすべての OS イメージを格納し、別のギャラリーにすべての VM アプリケーションを格納することができます。|
| **VM アプリケーション** | これは、VM アプリケーションの定義です。 これは、その下にあるすべてのバージョンの共通メタデータを格納する "*論理*" リソースです。 たとえば、Apache Tomcat 用の 1 つのアプリケーション定義内に複数のバージョンを含めることができます。 |
| **VM アプリケーションのバージョン** | これは、デプロイ可能なリソースです。 VM アプリケーションのバージョンを、対象の VM インフラストラクチャに近いターゲット リージョンにグローバルにレプリケートできます。 VM アプリケーションのバージョンをリージョン内の VM にデプロイするには、最初にリージョンにレプリケートする必要があります。 |


## <a name="limitations"></a>制限事項

- **リージョンごとに 3 つ以下のレプリカ**: VM アプリケーション バージョンを作成する場合、リージョンごとのレプリカの最大数は 3 つです。 

- **失敗したインストールの再試行**: 現在、失敗したインストールを再試行する唯一の方法は、プロファイルからアプリケーションを削除してから再度追加することです。

- **VM ごとに 5 つのアプリケーションのみ**: いつの時点でも VM にデプロイできるアプリケーションは 5 つまでです。

- **1 GB のアプリケーション サイズ**: アプリケーション バージョンの最大ファイル サイズは 1 GB です。 

- **スクリプトでの再起動は保証されない**: スクリプトで再起動が必要な場合は、そのアプリケーションを最後にデプロイすることをお勧めします。 コードで再起動の処理が試みられたときに、コードが失敗する可能性があります。

- **VM エージェントが必要**: VM エージェントが VM 上に存在し、目標の状態を受け取ることができる必要があります。

- **同じ VM 上の同じアプリケーションの複数のバージョン**: VM 上に同じアプリケーションの複数のバージョンを配置することはできません。


## <a name="cost"></a>[コスト]

VM アプリケーション パッケージの使用に追加料金はかかりませんが、次のリソースに対しては課金されます。

- 各パッケージとレプリカを格納するためのストレージ コスト。 
- ソース リージョンからレプリケート先のリージョンに最初のイメージ バージョンをレプリケートするための、ネットワーク エグレスの料金。 後続のレプリカはリージョン内で処理されるので、追加料金は発生しません。 

ネットワーク エグレスの詳細については、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」を参照してください。


## <a name="vm-applications"></a>VM アプリケーション

VM アプリケーション リソースでは、VM アプリケーションについて次のことを定義します。

- VM アプリケーションが格納されている Azure Compute Gallery
- アプリケーションの名前
- サポートされている OS の種類 (Linux、Windows など)
- VM アプリケーションの説明

## <a name="vm-application-versions"></a>VM アプリケーションのバージョン

VM アプリケーションのバージョンは、デプロイ可能なリソースです。 バージョンは、次のプロパティで定義されます。
- バージョン番号
- ストレージ アカウントのアプリケーション パッケージ ファイルへのリンク
- アプリケーションをインストールするためのインストール文字列
- アプリを適切に削除する方法を示すための削除文字列
- VM にダウンロードするときに使用するパッケージ ファイル名
- VM 上のアプリを構成するために使用される構成ファイルの名前
- VM アプリケーションの構成ファイルへのリンク
- VM アプリケーションを新しいバージョンに更新する方法を示すための更新文字列
- 有効期限の終了日。 有効期間の終了日は、情報提供のためのものです。有効期限が切れた後も、VM アプリケーションのバージョンをデプロイできます。
- 最新から除外。 あるバージョンをアプリケーションの最新バージョンとして使用しないようにできます。 
- レプリケーションのターゲット リージョン
- リージョンあたりのレプリカ数

## <a name="download-directory"></a>ダウンロード ディレクトリ 
 
アプリケーション パッケージと構成ファイルのダウンロード場所は次のとおりです。
  
- Linux: `/var/lib/waagent/Microsoft.CPlat.Core.VMApplicationManagerLinux/<appname>/<app version> `
- Windows: `C:\Packages\Plugins\Microsoft.CPlat.Core.VMApplicationManagerWindows\1.0.4\Downloads\<appname>\<app version> `


インストール コマンド、更新コマンド、削除コマンドは、アプリケーション パッケージと構成ファイルが現在のディレクトリにあると想定して記述する必要があります。

## <a name="file-naming"></a>ファイルの名前付け

プレビュー期間中は、アプリケーション ファイルを VM にダウンロードしたときのファイル名は VM アプリケーションの作成時に使用した名前と同じになります。 たとえば、VM アプリケーションの名前が `myApp` である場合、ストレージ アカウントで使用されているファイル名に関係なく、VM にダウンロードされるファイルにも `myApp` という名前が付けられます。 VM アプリケーションに構成ファイルもある場合、そのファイル名は、アプリケーションの名前の後に `_config` が追加された名前になります。 `myApp` に構成ファイルがある場合、その名前は `myApp_config` になります。

たとえば、ギャラリーで作成するときに `myApp` という名前を付けた VM アプリケーションがストレージ アカウントに `myApplication.exe` として格納されているとします。これが VM にダウンロードされると、ファイル名は `myApp` になります。 インストール文字列は、ファイルの名前を VM で実行するために必要な名前 (myApp.exe など) に変更することから開始する必要があります。

インストール、更新、および削除の各コマンドは、この点を考慮して記述する必要があります。 
 
## <a name="command-interpreter"></a>コマンド インタープリター  

既定のコマンド インタープリターは次のとおりです。
- Linux: `/bin/sh` 
- Windows: `cmd.exe`

別のインタープリターがマシンにインストールされている場合は、それを使用することができます。そのためには、実行可能ファイルを呼び出してコマンドを渡します。 たとえば、コマンドを cmd ではなく Windows の PowerShell で実行するには、`powershell.exe -Command '<powershell commmand>'` を渡します。
  

## <a name="how-updates-are-handled"></a>更新の処理方法

アプリケーションのバージョンを更新するとき、デプロイ時に指定した更新コマンドが使用されます。 更新されたバージョンに更新コマンドがない場合は、現在のバージョンが削除され、新しいバージョンがインストールされます。 

更新コマンドは、VM アプリケーションの古いバージョンから更新される可能性があることを想定して記述する必要があります。


## <a name="tips-for-creating-vm-applications-on-linux"></a>Linux 上で VM アプリケーションを作成するためのヒント 

Linux 用サード パーティ製アプリケーションは、いくつかの方法でパッケージ化できます。 最も一般的ないくつかのインストール コマンドの作成を処理する方法について説明します。

### <a name="tar-and-gz-files"></a>.tar および .gz ファイル 

これらは圧縮されたアーカイブであり、目的の場所に簡単に展開できます。 特定の場所に展開する必要がある場合に備えて、元のパッケージのインストール手順を確認します。 .tar.gz ファイルにソース コードが含まれている場合は、ソースからインストールする方法について、パッケージの手順を参照してください。 

Linux マシンに `golang` をインストールするインストール コマンドの例:

```bash
tar -C /usr/local -xzf go_linux
```

削除コマンドの例:

```bash
rm -rf /usr/local/go
```

### <a name="deb-rpm-and-other-platform-specific-packages"></a>.deb、.rpm、およびその他のプラットフォーム固有のパッケージ 
プラットフォーム固有のパッケージ マネージャーごとに個別のパッケージをダウンロードできますが、通常、すべての依存関係が含まれているわけではありません。 これらのファイルについては、アプリケーション パッケージにすべての依存関係を含めるか、VM で使用可能なリポジトリを介してシステム パッケージ マネージャーが依存関係をダウンロードするように設定する必要があります。 インターネット アクセスが制限されている VM を使用している場合は、すべての依存関係を自分でパッケージ化する必要があります。


依存関係を把握するのは少し難しい作業です。 依存関係ツリー全体を表示できるサード パーティ製のツールがあります。 

Ubuntu では、`apt-get install <name> --simulate` を実行して、`apt-get install <name>` コマンド用にインストールされるすべてのパッケージを表示できます。 次に、その出力を使用してすべての .deb ファイルをダウンロードし、アプリケーション パッケージとして使用できるアーカイブを作成できます。 この方法の欠点は、VM に既にインストールされている依存関係が表示されないことです。
 
たとえば、Ubuntu 用の PowerShell をインストールする VM アプリケーション パッケージを作成するには、新しい Ubuntu VM でコマンド `apt-get install powershell --simulate` を実行します。 **[The following NEW packages will be installed]\(次の新しいパッケージがインストールされます\)** 行の出力を確認します。ここには、次のパッケージが一覧表示されています。
- `liblttng-ust-ctl4` 
- `liblttng-ust0` 
- `liburcu6` 
- `powershell`. 

`apt-get download` を使用してこれらのファイルをダウンロードし、ルート レベルのすべてのファイルを含む tar アーカイブを作成します。 この tar アーカイブがアプリケーション パッケージ ファイルになります。 この場合のインストール コマンドは次のとおりです。

```bash
tar -xf powershell && dpkg -i ./liblttng-ust-ctl4_2.10.1-1_amd64.deb ./liburcu6_0.10.1-1ubuntu1_amd64.deb ./liblttng-ust0_2.10.1-1_amd64.deb ./powershell_7.1.2-1.ubuntu.18.04_amd64.deb
```

削除コマンドは次のとおりです。

```bash
dpkg -r powershell && apt autoremove
```

すべての依存関係を明示的に削除するのではなく、`apt autoremove` を使用してください。 依存関係が重複する他のアプリケーションがインストールされている可能性があります。その場合、明示的な削除コマンドは失敗します。


依存関係を自分で解決したくない場合に apt または rpm でリポジトリに接続できるときは、1 つの .deb または .rpm ファイルを使用してアプリケーションをインストールし、依存関係については apt または rpm に処理させることができます。

インストール コマンドの例:

```bash
dpkg -i <appname> || apt --fix-broken install -y
```
 
## <a name="tips-for-creating-vm-applications-on-windows"></a>Windows 上で VM アプリケーションを作成するためのヒント 

Windows のサード パーティ製アプリケーションのほとんどは、.exe または .msi のインストーラーとして入手できます。 一部は、展開および実行 ZIP ファイルとしても入手できます。 それぞれのベスト プラクティスを見てみましょう。


### <a name="exe-installer"></a>.exe インストーラー 

インストーラー実行可能ファイルは、通常、ユーザーがクリックする必要があるユーザー インターフェイス (UI) を起動します。 インストーラーがサイレント モード パラメーターをサポートしている場合は、それをインストール文字列に含める必要があります。 

また Cmd.exe は、実行可能ファイルの拡張子が .exe であることを想定しているため、ファイルの名前を .exe 拡張子に変更する必要があります。  

実行可能ファイルとして出荷される myApp.exe 用の VM アプリケーション パッケージを作成するとします。VM アプリケーションの名前は "myApp" です。そこで、アプリケーション パッケージが現在のディレクトリにあると想定してコマンドを記述します。

```
"move .\\myApp .\\myApp.exe & myApp.exe /S -config myApp_config" 
```
 
インストーラーの実行可能ファイルでアンインストール パラメーターがサポートされていない場合は、テスト マシンのレジストリを調べてアンインストーラーが配置されている場所を見つけることができることもあります。 

レジストリで、アンインストール文字列は `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\<installed application name>\UninstallString` に格納されているため、その内容を削除コマンドとして使用します。

```
'\"C:\\Program Files\\myApp\\uninstall\\helper.exe\" /S'
```

### <a name="msi-installer"></a>.msi インストーラー 

`.msi` インストーラーをコマンド ラインで実行するには、アプリケーションをインストールまたは削除するコマンドで `msiexec` を使用する必要があります。 通常、`msiexec` は独自の個別のプロセスとして実行され、`cmd` はその完了を待たないため、複数の VM アプリケーションをインストールするときに問題が発生する可能性があります。  `start` コマンドを `msiexec` と一緒に使用すると、コマンドから制御が返される前にインストールが完了するようにすることができます。 次に例を示します。

```
start /wait %windir%\\system32\\msiexec.exe /i myapp /quiet /forcerestart /log myapp_install.log
```

削除コマンドの例:

```
start /wait %windir%\\system32\\msiexec.exe /x $appname /quiet /forcerestart /log ${appname}_uninstall.log
```

### <a name="zipped-files"></a>ZIP 形式のファイル 

.zip またはその他の ZIP 形式のファイルについては、アプリケーション パッケージの内容を目的の場所に解凍するだけです。 

インストール コマンドの例:

```
mkdir C:\\myapp && powershell.exe -Command \"Expand-Archive -Path myapp -DestinationPath C:\\myapp\" 
```

削除コマンドの例:

```
rmdir /S /Q C:\\myapp
```


## <a name="troubleshooting-during-preview"></a>プレビュー期間中のトラブルシューティング

プレビュー期間中、VM アプリケーション拡張機能は、VM アプリのインストール、更新、削除中にエラーが発生したかどうかに関係なく、常に成功を返します。 VM アプリケーション拡張機能は、拡張機能または基になるインフラストラクチャに問題がある場合にのみ、拡張機能の状態をエラーとして報告します。 特定の VM アプリケーションが VM インスタンスに正常に追加されたかどうかを確認するには、VMApplication 拡張機能のメッセージを確認してください。

VM 拡張機能の状態を取得する方法の詳細については、[Windows の仮想マシン拡張機能と機能](extensions/features-windows.md#view-extension-status)に関するページを参照してください。

VM 拡張機能の状態を取得するには、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用します。

```azurepowershell-interactive
Get-AzVM -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

VMSS 拡張機能の状態を取得するには、[Get-AzVMSS](/powershell/module/az.compute/get-azvmss) を使用します。

```azurepowershell-interactive
Get-AzVmss -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

## <a name="error-messages"></a>エラー メッセージ

| Message | 説明 |
|--|--|
| Current VM Application Version {name} was deprecated at {date}. (現在の VM アプリケーション バージョン {名前} は {日付} 付けで非推奨になりました。) | 既に非推奨になっている VM アプリケーション バージョンをデプロイしようとしました。 特定のバージョンを指定する代わりに、`latest` を使用してください。 |
| Current VM Application Version {name} supports OS {OS}, while current OSDisk's OS is {OS}. (現在の VM アプリケーション バージョン {名前} は OS {OS} をサポートしていますが、現在の OSDisk の OS は {OS} です。) | Linux アプリケーションを Windows インスタンスに (またはその逆に) デプロイしようとしました。 |
| The maximum number of VM applications (max=5, current={count}) has been exceeded. (VM アプリケーションの最大数 (最大 = 5、現在 = {数}) を超えました。) Use fewer applications and retry the request. (使用するアプリケーションの数を減らしてから要求を再試行してください。) | 現時点では、VM または VMSS ごとに 5 つの VM アプリケーションのみをサポートしています。 |
| More than one VMApplication was specified with the same packageReferenceId. (同じ packageReferenceId で複数の VMApplication が指定されました。) | 同じアプリケーションが複数回指定されました。 |
| Subscription not authorized to access this image. (サブスクリプションにこのイメージへのアクセスが許可されていません。) | このサブスクリプションには、このアプリケーション バージョンへのアクセス権がありません。 |
| Storage account in the arguments does not exist. (引数内のストレージ アカウントが存在しません。) | このサブスクリプションにはアプリケーションがありません。 |
| The platform image {image} is not available. (プラットフォーム イメージ { イメージ} は使用できません。) Verify that all fields in the storage profile are correct. (ストレージ プロファイルのすべてのフィールドが適切なことを確認してください。) For more details about storage profile information, please refer to https://aka.ms/storageprofile. (ストレージ プロファイル情報の詳細については、 https://aka.ms/storageprofile を参照してください。) | アプリケーションが存在しません。 |
| The gallery image {image} is not available in {region} region. ({リージョン} リージョンではギャラリー イメージ {イメージ} を使用できません。) Please contact image owner to replicate to this region, or change your requested region. (イメージ所有者に連絡して、このリージョンにレプリケートするか、要求されたリージョンを変更するようにしてください。) | ギャラリー アプリケーション バージョンは存在しますが、このリージョンにレプリケートされていません。 |
| The SAS is not valid for source uri {uri}. (SAS がソース uri {uri} に対して無効です。) | url (mediaLink または defaultConfigurationLink) に関する情報を取得しようとしたときに、ストレージから `Forbidden` エラーを受け取りました。 |
| The blob referenced by source uri {uri} does not exist. (ソース uri {uri} で参照されている BLOB が存在しません。) | The blob provided for the mediaLink or defaultConfigurationLink properties does not exist. (MediaLink または defaultConfigurationLink プロパティに指定された BLOB が存在しません。) |
| The gallery application version url {url} cannot be accessed due to the following error: remote name not found. (次のエラーにより、ギャラリー アプリケーション バージョン url {url} にアクセスできません: リモート名が見つかりません。) Ensure that the blob exists and that it's either publicly accessible or is a SAS url with read privileges. (BLOB が存在し、パブリックにアクセスできること、または読み取り権限のある SAS url であることを確認してください。) | 最も可能性の高いケースは、読み取り権限のある SAS uri が指定されていないことです。 |
| The gallery application version url {url} cannot be accessed due to the following error: {error description}. (次のエラーにより、ギャラリー アプリケーション バージョン url {url} にアクセスできません: {エラー説明}。) Ensure that the blob exists and that it's either publicly accessible or is a SAS url with read privileges. (BLOB が存在し、パブリックにアクセスできること、または読み取り権限のある SAS url であることを確認してください。) | 指定されたストレージ BLOB に問題がありました。 詳細については、エラーの説明を参照してください。 |
| Operation {operationName} is not allowed on {application} since it is marked for deletion. (操作 {operationName} は、削除対象としてマークされているため、{アプリケーション} では許可されていません。) 再試行できるのは削除操作のみです (または、進行中の作業が完了するまで待機してください)。 | 現在削除中のアプリケーションを更新しようとしています。 |
| The value {value} of parameter 'galleryApplicationVersion.properties.publishingProfile.replicaCount' is out of range. (パラメーター 'galleryApplicationVersion.properties.publishingProfile.replicaCount' の値 {値} が範囲外です。) 値には、1 から 3 までの値を指定する必要があります。 | VMApplication バージョンで許可されるレプリカは、1 つから 3 つだけです。 |
| Changing property 'galleryApplicationVersion.properties.publishingProfile.manageActions.install' is not allowed. (プロパティ 'galleryApplicationVersion.properties.publishingProfile.manageActions.install' の変更は許可されていません。) (or update, delete) ((または更新、削除)) | 既存の VmApplication の管理アクションを変更することはできません。 新しい VmApplication バージョンを作成する必要があります。 |
| Changing property ' galleryApplicationVersion.properties.publishingProfile.settings.packageFileName ' is not allowed. (プロパティ 'galleryApplicationVersion.properties.publishingProfile.settings.packageFileName' の変更は許可されていません。) (or configFileName) ((または configFileName)) | パッケージ ファイル名や構成ファイル名などの設定を変更することはできません。 新しい VmApplication バージョンを作成する必要があります。 |
| The blob referenced by source uri {uri} is too big: size = {size}. (ソース uri {uri} によって参照される BLOB が大きすぎます: サイズ = {サイズ}。) The maximum blob size allowed is '1 GB'. (許可される最大 BLOB サイズは '1 GB' です。) | mediaLink または defaultConfigurationLink によって参照される BLOB の最大サイズは、現在 1 GB です。 |
| The blob referenced by source uri {uri} is empty. (ソース uri {uri} で参照されている BLOB が空です。) | 空の BLOB が参照されました。 |
| {type} blob type is not supported for {operation} operation. ({type} BLOB の種類は、{操作} 操作ではサポートされていません。) Only page blobs and block blobs are supported. (サポートされているのは、ページ BLOB とブロック BLOB のみです。) | VmApplications では、ページ BLOB とブロック BLOB のみがサポートされます。 |
| The SAS is not valid for source uri {uri}. (SAS がソース uri {uri} に対して無効です。) | mediaLink または defaultConfigurationLink に指定された SAS uri が有効な SAS uri ではありません。 |
| Cannot specify {region} in target regions because the subscription is missing required feature {featureName}. (サブスクリプションに必要な機能 {featureName} がないため、ターゲット リージョンで {リージョン} を指定できません。) Either register your subscription with the required feature or remove the region from the target region list. (サブスクリプションを必要な機能に登録するか、ターゲット リージョンの一覧からリージョンを削除してください。) | 特定の制限されたリージョンで VmApplications を使用するには、そのサブスクリプションに対して機能フラグを登録する必要があります。 |
| Gallery image version publishing profile regions {regions} must contain the location of image version {location}. (ギャラリー イメージ バージョンの公開プロファイル リージョン {リージョン} には、イメージ バージョンの場所 {場所} が含まれている必要があります。) | レプリケーション用のリージョンの一覧には、アプリケーション バージョンがある場所が含まれている必要があります。 |
| "ターゲットの公開リージョンで重複するリージョンは許可されません。 " | 公開リージョンの重複は許可されません。 |
| Gallery application version resources currently do not support encryption. (ギャラリー アプリケーション バージョンのリソースでは、現在、暗号化はサポートされていません。) | ターゲット リージョンの暗号化プロパティは、VM アプリケーションではサポートされていません |
| Entity name does not match the name in the request URL. (エンティティ名が要求 URL 内の名前と一致しません。) | 要求 url に指定されたギャラリー アプリケーション バージョンが、要求本文で指定されたバージョンと一致しません。 |
| The gallery application version name is invalid. (ギャラリー アプリケーション バージョン名が無効です。) The application version name should follow Major(int32).Minor(int32).Patch(int32) format, where int is between 0 and 2,147,483,647 (inclusive). (アプリケーション バージョン名は、Major(int32).Minor(int32).Patch(int32) 形式に従う必要があります。ここで、int は 0 から 2,147,483,647 までの値です。) e.g. 1.0.0, 2018.12.1 etc. (たとえば、1.0.0、2018.12.1) | ギャラリー アプリケーション バージョンは、指定された形式に従う必要があります。 |



## <a name="next-steps"></a>次の手順

- [VM アプリケーション パッケージを作成してデプロイする](vm-applications-how-to.md)方法について学習します。