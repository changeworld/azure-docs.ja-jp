---
title: Azure Files で DFS-N を使用する方法
description: Azure Files での DFS-N の一般的なユース ケース
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0676639523a0b1ebd23ff0e5082e6cccbd641f4a
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117281"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Azure Files で DFS 名前空間を使用する方法
[分散ファイル システム名前空間](/windows-server/storage/dfs-namespaces/dfs-overview) (一般的に DFS 名前空間や DFS-N と呼ばれます) は、運用環境における SMB ファイル共有のデプロイとメンテナンスを単純化する目的で広く使用されている Windows Server サーバー ロールです。 DFS 名前空間は、ストレージの名前空間仮想化技術です。つまり、これを使用すると、ファイル共有の UNC パスと実際のファイル共有自体との間に間接的なレイヤーを設けることができます。 DFS 名前空間は SMB ファイル共有と連動します。その働きは、ファイル共有のホストを選びません。たとえば、Azure File Sync の有無に関係なく、オンプレミスの Windows ファイル サーバーでホストされている SMB 共有で使用できるほか、Azure ファイル共有で直接使用することもできます。また、Azure NetApp Files などサードパーティのオファリングでホストされている SMB ファイル共有や、他のクラウドでホストされているファイル共有で使用することも可能です。 

根本として、SMB 共有のユーザー フレンドリな UNC パス (`\\contoso\shares\ProjectX` など) とその基になる UNC パス (`\\Server01-Prod\ProjectX` や `\\storageaccount.file.core.windows.net\projectx` など) との間のマッピングが、DFS 名前空間によって提供されます。 エンド ユーザーは自分のファイル共有に移動したい場合、ユーザー フレンドリな UNC パスを入力しますが、その SMB クライアントがアクセスするのは、マッピングの基になる SMB パスです。 この基本的な概念を、既存のファイル サーバー名 (`\\MyServer\ProjectX` など) の引き継ぎに応用することもできます。 この機能を使用して次のシナリオを実現できます。

- 論理データ セットに、移行の影響を受けない名前を提供する。 この例では、`\\OldServer\Engineering` に対応する `\\contoso\shares\Engineering` というマッピングがあるとします。 Azure Files への移行が完了した時点で、ユーザー フレンドリな UNC パスが `\\storageaccount.file.core.windows.net\engineering` を指すように、そのマッピングを変更することができます。 エンド ユーザーは、ユーザー フレンドリな UNC パスにアクセスすると、Azure ファイル共有のパスにシームレスにリダイレクトされるようになります。

- Azure File Sync を使用するなどして、物理的に異なるサイトにある複数のサーバーに分散されている論理データ セットに共通名を定義する。この例では、1 つの名前 (`\\contoso\shares\FileSyncExample` など) が複数の UNC パス (`\\FileSyncServer1\ExampleShare`、`\\FileSyncServer2\DifferentShareName`、`\\FileSyncServer3\ExampleShare` など) にマッピングされます。 ユーザーは、ユーザー フレンドリな UNC にアクセスすると、一連の UNC パスの候補が提示され、その中から、Windows Server Active Directory (AD) サイトの定義に基づいて自分に最も近いものを選択することになります。

- サイズや IO などのスケールしきい値を超えて論理データ セットを拡張する。 その一般的な形態としては、共有上で各ユーザーに各自のフォルダーを提供するユーザー ディレクトリや、一時的なデータ ニーズに対応する任意の領域をユーザーに提供する一時的な共有が考えられます。 DFS 名前空間を使用すると、複数のフォルダーを集めて 1 つの名前空間にまとめることができます。 たとえば、`\\contoso\shares\UserShares\user1` は `\\storageaccount.file.core.windows.net\user1` に、`\\contoso\shares\UserShares\user2` は `\\storageaccount.file.core.windows.net\user2` に対応するようにマッピングされます。  

Azure Files のデプロイに DFS 名前空間を使用する例については、次の概要動画でご覧いただけます。

[![Azure Files で DFS-N をセットアップする方法のデモ (クリックすると再生されます)](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> DFS 名前空間の設定方法を確認するには、この動画の 10:10 にスキップしてください。

既に DFS 名前空間が設定されている場合は、それを Azure Files と File Sync で使用するために必要な特別な手順はありません。Azure ファイル共有にオンプレミスからアクセスする場合は、標準的なネットワークの考慮事項が適用されます。詳細については、「[Azure Files のネットワークに関する考慮事項](./storage-files-networking-overview.md)」を参照してください。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="namespace-types"></a>名前空間の種類
DFS 名前空間には、主要な名前空間として次の 2 種類があります。

- **ドメインベースの名前空間**: Windows Server AD ドメインの一部としてホストされる名前空間。 AD の一部としてホストされる名前空間には、ドメイン名を含む UNC パスが割り当てられます。たとえば、ドメインが `contoso.com` の場合は、`\\contoso.com\shares\myshare` となります。 ドメインベースの名前空間でサポートされるスケールの上限の方が大きく、また、AD による冗長性が組み込まれています。 ドメインベースの名前空間を、フェールオーバー クラスター上のクラスター リソースにすることはできません。 
- **スタンドアロンの名前空間**: Windows Server AD の一部としてホストされるのではなく、個々のサーバー上でホストされる名前空間。 スタンドアロンの名前空間には、スタンドアロン サーバーの名前に基づく名前、たとえば `\\MyStandaloneServer\shares\myshare` が割り当てられます。この場合、スタンドアロン サーバーの名前は `MyStandaloneServer` です。 スタンドアロンの名前空間は、ドメインベースの名前空間に比べてサポートされるスケール ターゲットは低いものの、フェールオーバー クラスター上のクラスター リソースとしてホストすることができます。

## <a name="requirements"></a>要件
Azure Files および File Sync で DFS 名前空間を使用するには、次のリソースが必要となります。

- Active Directory ドメイン。 オンプレミスや Azure 仮想マシン (VM)、さらに別のクラウドなど、これはどこにホストされていてもかまいません。
- 名前空間をホストできる Windows Server。 DFS 名前空間のデプロイ パターンとしては、Active Directory ドメイン コントローラーを使用して名前空間をホストするのが一般的ですが、DFS 名前空間サーバー ロールがインストールされていれば、どのサーバーからでも名前空間を設定できます。 サポート対象のすべての Windows Server バージョンで、DFS 名前空間を利用できます。
- ドメイン参加済みの環境でホストされている SMB ファイル共有 (ドメイン参加済みのストレージ アカウント内でホストされている Azure ファイル共有、ドメイン参加済みの Windows ファイル サーバーで Azure File Sync を使用してホストされているファイル共有など)。ストレージ アカウントのドメイン参加の詳細については、[ID ベースの認証](storage-files-active-directory-overview.md)に関するページを参照してください。 Azure File Sync を使用するかどうかに関係なく、Windows ファイル サーバーは、同じ方法でドメインに参加しています。
- DFS 名前空間で使用する SMB ファイル共有にオンプレミスのネットワークから到達可能であること。 これは主に Azure ファイル共有で考慮すべき事柄ですが、厳密には、Azure やその他のクラウドでホストされるファイル共有すべてに当てはまります。 ネットワークの詳細については、[直接アクセスに関するネットワークの考慮事項](storage-files-networking-overview.md)に関するページを参照してください。

## <a name="install-the-dfs-namespaces-server-role"></a>DFS 名前空間サーバー ロールをインストールする
既に DFS 名前空間を使用している場合や、ドメイン コントローラー上に DFS 名前空間を設定したい場合は、これらの手順をスキップしてかまいません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
DFS 名前空間サーバー ロールをインストールするには、サーバーでサーバー マネージャーを開きます。 **[管理]** を選択し、 **[役割と機能の追加]** を選択します。 表示されるウィザードで、画面の指示に従って、DFS 名前空間の実行と管理に必要な Windows コンポーネントをインストールします。 

インストール ウィザードの **[インストールの種類]** セクションで、 **[役割ベースまたは機能ベースのインストール]** を選択し、 **[次へ]** を選択します。 **[サーバーの選択]** セクションで、DFS 名前空間サーバー ロールのインストール先となるサーバーを選択し、 **[次へ]** を選択します。 

**[サーバー ロール]** セクションにあるロールの一覧から **[DFS 名前空間]** ロールを選択してチェック ボックスをオンにします。 これは、 **[ファイルおよび記憶域サービス]**  >  **[ファイル サービスおよび iSCSI サービス]** にあります。 DFS 名前空間サーバー ロールを選択すると、まだインストールしていない、サポート対象のサーバー ロールや機能が必要となり、追加される場合があります。

![**[DFS 名前空間]** ロールが選択されている**役割と機能の追加**ウィザードのスクリーンショット。](./media/files-manage-namespaces/dfs-namespaces-install.png)

**[DFS 名前空間]** ロールのチェック ボックスをオンにしたら、後続のすべての画面で **[次へ]** を選択してかまいません。ウィザードの **[インストール]** ボタンが有効になったらそのボタンを選択してください。 インストールが完了したら、名前空間を構成できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
管理者特権の PowerShell セッションから (または PowerShell リモート処理を使用して)、次のコマンドを実行します。

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>ルート統合によって既存のサーバー名を引き継ぐ
DFS 名前空間の重要な用途として、ファイル共有の物理的なレイアウトをリファクタリングする際に、既存のサーバー名を引き継ぐことがあります。 たとえば、複数の古いファイル サーバーにあるファイル共有を、モダン化に伴う移行の過程で 1 台のファイル サーバーにまとめたい場合があります。 従来、エンド ユーザーの慣れやドキュメント リンクの機能上、異なるファイル サーバーのファイル共有を 1 つのホストに統合するのには限界がありましたが、DFS 名前空間のルート統合機能を使用すると、複数のサーバー名に対して 1 台のサーバーを立て、適切な共有名にルーティングすることができます。

ルート統合はさまざまなデータセンター移行シナリオで有効に活用できますが、その利便性が特に発揮されるのは、クラウドネイティブな Azure ファイル共有の導入時です。理由は次のとおりです。

- Azure ファイル共有では、既存のオンプレミス サーバー名を維持できません。
- Azure ファイル共有には、ストレージ アカウントの完全修飾ドメイン名 (FQDN) でアクセスする必要があります。 たとえば、ストレージ アカウント `storageaccount` にある `share` という Azure ファイル共有は常に、`\\storageaccount.file.core.windows.net\share` という UNC パスでアクセスされます。 短い名前 (例: `\\MyServer\share`) や組織のドメイン名のサブドメインとなる名前を想定しているエンド ユーザーの混乱を招くかもしれません。 `\\MyServer.contoso.com\share`).

ルート統合は、スタンドアロンの名前空間でのみ使用することができます。 ドメインベースの既存の名前空間があって、既にファイル共有に使用している場合、ルート統合された名前空間を作成する必要はありません。

### <a name="enabling-root-consolidation"></a>ルート統合を有効にする
ルート統合を有効にするには、管理者特権の PowerShell セッションから (または PowerShell リモート処理を使用して) 次のレジストリ キーを設定します。

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>既存のファイルサーバー名の DNS エントリを作成する
DFS 名前空間を既存のファイル サーバー名に対応させるには、DFS 名前空間のサーバー名を指す、既存のファイル サーバーのエイリアス (CNAME) レコードを作成します。 DNS レコードを更新する厳密な手順は、社内で使用されているサーバーや、DNS の管理を自動化する目的で使用されているカスタム ツールによって異なります。 以下の手順は、Windows Server に付属していて自動的に Windows AD で使用される DNS サーバーを想定しています。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Windows DNS サーバーで、DNS 管理コンソールを開きます。 **[スタート]** ボタンを選択し、「**DNS**」と入力すると見つかります。 ドメインの前方参照ゾーンに移動します。 たとえば `contoso.com` ドメインの場合、前方参照ゾーンは、管理コンソールの **[前方参照ゾーン]**  >  **[`contoso.com`]** にあります。 このダイアログに表示される厳密な階層は、実際のネットワークの DNS 構成によって異なります。

前方参照ゾーンを右クリックし、 **[新しいエイリアス (CNAME)]** を選択します。 表示されたダイアログで、置き換えるファイル サーバーの短い名前を入力します (完全修飾ドメイン名は、 **[完全修飾ドメイン名]** というテキストボックスに自動的に入力されます)。 **[Fully qualified domain name (FQDN) for the target host]\(ターゲット ホストの完全修飾ドメイン名 (FQDN)\)** ボックスに、セットアップした DFS-N サーバーの名前を入力します。 必要に応じて **[参照]** ボタンを使用してサーバーを選択できます。 **[OK]** を選択すると、サーバーの CNAME レコードが作成されます。

![CNAME DNS エントリ用の **[新しいリソース レコード]** を表すスクリーンショット。](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Windows DNS サーバーで PowerShell セッションを開き (または PowerShell リモート処理を使用して)、以下のコマンドを実行します。`$oldServer` と `$dfsnServer` には、実際の環境の適切な値を設定してください (`$domain` には、ドメイン名が自動的に事前設定されますが、これを手動で入力することもできます)。

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>名前空間の作成
DFS 名前空間の基本的な管理単位は名前空間です 名前空間のルート (または名前) は、名前空間の起点です。`\\contoso.com\Public\` という UNC パスの中で、`Public` が名前空間のルートとなります。 

DFS 名前空間のルート統合によって既存のサーバー名を引き継ぐ場合、名前空間の名前は、引き継ぐサーバー名の前に `#` 文字を付けた名前にする必要があります。 たとえば、`MyServer` という名前の既存のサーバーを引き継ぐ場合、`#MyServer` という DFS-N 名前空間を作成することになります。 以下の PowerShell セクションでは、先頭の `#` が自動的に追加されますが、DFS 管理コンソールで名前空間を作成する場合は、適宜手動で追加する必要があります。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
新しい名前空間を作成するには、**DFS 管理** コンソールを開きます。 **[スタート]** ボタンを選択し、「**DFS Management**」と入力すると見つかります。 表示される管理コンソールには、 **[名前空間]** と **[レプリケーション]** の 2 つのセクションがあります。それぞれ DFS 名前空間と DFS レプリケーション (DFS-R) を表します。 Azure File Sync には、レプリケーションと同期の先進的なメカニズムが備わっており、レプリケーションも必要であれば、DFS-R の代わりにそれらのメカニズムを使用することもできます。

**[名前空間]** セクションで、 **[新しい名前空間]** ボタンを選択します ( **[名前空間]** セクションで右クリックする方法もあります)。 **新しい名前空間ウィザード** が起動するので、画面の指示に従って名前空間を作成します。 

ウィザードの最初のセクションでは、名前空間のホストとなる DFS 名前空間サーバーを選択する必要があります。 名前空間は複数のサーバーでホストできますが、DFS 名前空間の設定は、一度に 1 台のサーバーに対して行う必要があります。 必要な DFS 名前空間サーバーの名前を入力し、 **[次へ]** を選択します。 **[名前空間の名前と設定]** セクションで、目的の名前空間の名前を入力し、 **[次へ]** を選択します。 

**[名前空間の種類]** セクションでは、 **[ドメインベースの名前空間]** か **[スタンドアロンの名前空間]** かを選択できます。 既存のファイル サーバー名または NAS デバイス名を維持することを目的に DFS 名前空間を使用する場合、[スタンドアロンの名前空間] オプションを選択する必要があります。 それ以外のシナリオでは、ドメインベースの名前空間を選択します。 名前空間の種類の選択について詳しくは、前出の「[名前空間の種類](#namespace-types)」を参照してください。

![**新しい名前空間ウィザード**で、ドメインベースの名前空間かスタンドアロンの名前空間かを選択する画面のスクリーンショット。](./media/files-manage-namespaces/dfs-namespace-type.png)

実際の環境に必要な名前空間の種類を選択し、 **[次へ]** を選択します。 その後、ウィザードで作成される名前空間のサマリーが表示されます。 **[作成]** をクリックして名前空間を作成し、ダイアログが完了したら **[閉じる]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
DFS 名前空間サーバーの PowerShell セッションから、次の PowerShell コマンドを実行します。`$namespace`、`$type`、`$takeOverName` には、実際の環境の適切な値を設定してください。

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>フォルダーとフォルダー ターゲットを構成する
名前空間を有効活用するためには、名前空間にフォルダーとフォルダー ターゲットが必要です。 各フォルダーには、1 つまたは複数のフォルダー ターゲットを割り当てることができます。フォルダー ターゲットは、コンテンツのホストとなる SMB ファイル共有へのポインターです。 フォルダー ターゲットが割り当てられているフォルダーをユーザーが参照すると、クライアント コンピューターに参照が渡され、その参照によって、いずれかのフォルダー ターゲットにクライアント コンピューターが透過的にリダイレクトされます。 フォルダー ターゲットのないフォルダーを使用して、名前空間に構造と階層を追加することもできます。

DFS 名前空間のフォルダーは、ファイル共有と同様のものと考えることができます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
DFS 管理コンソールで、先ほど作成した名前空間を選択し、 **[新しいフォルダー]** を選択します。 表示された **[新しいフォルダー]** ダイアログで、フォルダーとフォルダー ターゲットの両方を作成できます。

![**[新しいフォルダー]** ダイアログのスクリーンショット。](./media/files-manage-namespaces/dfs-folder-targets.png)

**[名前]** ボックスに、フォルダーの名前を入力します。 このフォルダーのフォルダー ターゲットを追加するには、 **[追加]** を選択します。 これによって表示される **[フォルダー ターゲットを追加]** ダイアログには、 **[フォルダー ターゲットへのパス]** というテキストボックスが備わっていて、そこに目的のフォルダーの UNC パスを入力することができます。 **[フォルダー ターゲットを追加]** ダイアログで **[OK]** を選択します。 Azure ファイル共有の UNC パスを追加しようとすると、サーバー `storageaccount.file.core.windows.net` に接続できないというメッセージが表示される場合があります。 これは想定された動作です。 **[はい]** を選択して続行してください。 最後に、 **[新しいフォルダー]** ダイアログの **[OK]** を選択して、フォルダーとフォルダー ターゲットを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

名前空間、フォルダー、フォルダー ターゲットを作成したら、DFS 名前空間を通じてファイル共有をマウントすることができます。 ドメインベースの名前空間を使用している場合、共有の完全なパスは `\\<domain-name>\<namespace>\<share>` となります。 スタンドアロンの名前空間を使用している場合、共有の完全なパスは `\\<DFS-server>\<namespace>\<share>` となります。 スタンドアロンの名前空間と併せてルート統合を使用している場合は、以前のサーバー名 (`\\<old-server>\<share>` など) で直接アクセスすることができます。

## <a name="see-also"></a>関連項目
- Azure ファイル共有のデプロイ: [Azure Files のデプロイの計画](storage-files-planning.md)と[ファイル共有の作成方法](storage-how-to-create-file-share.md)。
- ファイル共有のアクセスの構成: [ID ベースの認証](storage-files-active-directory-overview.md)と[直接アクセスに関するネットワークの考慮事項](storage-files-networking-overview.md)。
- [Windows Server 分散ファイル システムの名前空間](/windows-server/storage/dfs-namespaces/dfs-overview)