---
title: Azure Migrate アプライアンスのトラブルシューティング
description: Azure Migrate アプライアンスで発生する可能性がある問題をトラブルシューティングするためのヘルプを取得します。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 405976fdf418c02172c3ad8129d206c3313380e2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183569"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスのトラブルシューティング

この記事は、[Azure Migrate](migrate-services-overview.md) アプライアンスをデプロイし、そのアプライアンスを使用してオンプレミスのサーバーを検出する際の問題のトラブルシューティングに役立ちます。

## <a name="whats-supported"></a>サポート対象

アプライアンスのサポート要件を[確認](migrate-appliance.md)します。

## <a name="invalid-ovf-manifest-entry-during-appliance-set-up"></a>アプライアンス設定時の"無効な OVF マニフェスト エントリ"

**エラー**

OVA テンプレートを使用してアプライアンスを設定しているとき、"指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェストのエントリ" というエラーが表示されます。

**修正**

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされていることを、そのハッシュ値をチェックして確認します。 [詳細については、こちらを参照してください](./tutorial-discover-vmware.md)。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでもデプロイが失敗する場合で、VMware vSphere クライアントを使って OVF ファイルをデプロイしている場合は、vSphere Web クライアントでデプロイしてみてください。 展開がそれでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、ESXi ホストに直接 OVA をデプロイしてみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して (vCenter Server ではなく) ESXi ホストに直接接続します。
   - **[ホーム]**  >  **[インベントリ]** で、 **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートのデプロイ\)** を選択します。 OVA を参照して、デプロイを完了する。
4. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。

## <a name="connectivity-check-failing-during-set-up-prerequisites"></a>"前提条件の設定" 中に接続チェックが失敗する

**エラー**

アプライアンスの接続チェックでエラーが発生しています。

**修正**

1. アプライアンスから必要な [URL](./migrate-appliance.md#url-access) に接続できることを確認します。
1. これらの URL へのアクセスをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 許可リストを作成する必要がある場合は、すべての URL を含めるようにします。
1. オンプレミスでプロキシ サーバーが構成されている場合は、同じ手順で **[プロキシの設定]** を選択して、プロキシの詳細を正しく指定していることを確認します。 プロキシで承認資格情報が必要とされる場合には、それを指定していることを確認してください。
1. サーバーがこれまでに[レプリケーション アプライアンス](./migrate-replication-appliance.md)の設定に使用されていないこと、またはモビリティ サービス エージェントがサーバーにインストールされていることを確認します。

## <a name="connectivity-check-failing-for-akams-url-during-set-up-prerequisites"></a>"前提条件の設定" 中に aka.ms URL の接続チェックが失敗する

**エラー**

アプライアンスで aka.ms URL の接続チェック時にエラーが発生しています。

**修正**

1. インターネットに接続できることを確認し、URL-aka.ms/* を許可リストに登録して最新バージョンのサービスをダウンロードします。
2. これらの URL へのアクセスをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 構成マネージャーの前提条件の手順でプロキシの詳細を正しく指定していることを確認します。
3. アプライアンス構成マネージャーに戻り、前提条件を再実行して自動更新を開始します。
3. 再試行しても問題が解決しない場合は、*latestcomponents.json* ファイルを[ここ](https://aka.ms/latestapplianceservices)からダウンロードして、失敗しているサービスの最新バージョンを確認し、これらをファイル内のダウンロード リンクから手動で更新することができます。

 アプライアンスの **プライベート エンドポイント接続** を有効にしてあり、この URL へのインターネット経由でのアクセスを許可したくない場合、[自動更新を無効](./migrate-appliance.md#turn-off-auto-update)にすることができます (aka.ms リンクはこのサービスに必要であるため)。

 >[!Note]
 >自動更新サービスを無効にすると、アプライアンスで実行されているサービスに最新の更新プログラムが自動で適用されなくなります。 この問題を回避するには、[アプライアンス サービスを手動で更新します](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="auto-update-check-failing-during-set-up-prerequisites"></a>自動更新チェックが "前提条件の設定" 中に失敗する

**エラー**

アプライアンスでの自動更新チェックでエラーが発生します。

**修正**

1. [必要な URL](./migrate-appliance.md#url-access) の許可リストを作成し、プロキシやファイアウォール設定によってこれらがブロックされていないことを確認します。
1. アプライアンス コンポーネントの更新が失敗する場合は、前提条件を再実行するか、[アプライアンス サービスを手動で更新します](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="time-sync-check-failing-during-set-up-prerequisites"></a>時刻同期チェックが "前提条件の設定" 中に失敗する

**エラー**

時刻の同期に関するエラーは、サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があることを示します。

**修正**

- コントロール パネルから日付と時刻の設定を確認して、アプライアンス サーバーの時刻がインターネット時刻と同期していることを確認します。
- 次の手順に従って、アプライアンス サーバーのクロック時刻を現在の時刻に合わせて変更することもできます。
     1. サーバーで管理者用のコマンド プロンプトを開きます。
     2. タイム ゾーンを確認するには、**w32tm /tz** を実行します。
     3. 時刻を同期するには、**w32tm /resync** を実行します。

## <a name="vddk-check-failing-during-set-up-prerequisites-on-vmware-appliance"></a>VMware アプライアンスで "前提条件の設定" 中に VDDK チェックが失敗する

**エラー**

アプライアンスにインストールされている必要な VDDK キットをアプライアンスが見つけることができなかったので、VDDK チェックは失敗しました。 この結果、進行中のレプリケーションでエラーが発生する可能性があります。

**修正**

1. VDDK キット 6.7 をダウンロードしてあり、そのファイルをアプライアンス サーバー上の **C:\Program Files\VMware\VMware Virtual Disk Development Kit** にコピーしてあることを確認します。
2. アプライアンスで他のバージョンの VDDK キットを使用しているソフトウェアやアプリケーションが他にないか確認します。

## <a name="getting-project-key-related-error-during-appliance-registration"></a>アプライアンスの登録中にプロジェクト キー関連のエラーが発生する

**エラー** 

プロジェクトからコピーされた Azure Migrate プロジェクト キーを使用して、アプライアンスを登録しようとすると問題が発生します。

**修正**

1. プロジェクトから正しいキーをコピーしたことを確認します。プロジェクトの **[Azure Migrate: Discovery and Assessment]** カードで、 **[検出]** 、手順 1 の **[Manage Existing appliance]\(既存のアプライアンスの管理\)** の順に選択します。 ドロップダウン メニューからアプライアンス名 (以前にキーを生成したもの) を選択し、対応するキーをコピーします。
2. 適切な **クラウドの種類** (パブリック、US Gov) および **アプライアンスの種類** (VMware、Hyper-V、物理、またはその他) のアプライアンスにキーを貼り付けていることを確認します。 アプライアンス構成マネージャーの上部で、クラウドとシナリオの種類を確認します。

## <a name="failed-to-connect-to-the-azure-migrate-project-during-appliance-registration"></a>アプライアンス登録中の "Failed to connect to the Azure Migrate project"(Azure Migrate プロジェクトに接続できませんでした)

**エラー**

Azure ユーザー アカウントを使用して正常にログインした後、アプライアンスの登録手順が失敗し、 **"Failed to connect to the Azure Migrate project. Check the error detail and follow the remediation steps by clicking Retry" (Azure Migrate プロジェクトへの接続に失敗しました。エラーの詳細を確認し、[再試行] をクリックして修復手順に従ってください)** というメッセージが表示されます。

この問題は、アプライアンス構成マネージャーからのログインに使用された Azure ユーザー アカウントが、ポータルで Azure Migrate プロジェクト キーの生成に使用されたユーザー アカウントと異なる場合に発生します。

**修正**
1. アプライアンスの登録を完了するには、ポータルで Azure Migrate プロジェクト キーを生成したのと同じ Azure ユーザー アカウントを使用します。または
1. アプライアンスの登録に使用された他の Azure ユーザー アカウントに、必要なロールと[アクセス許可](./tutorial-discover-vmware.md#prepare-an-azure-user-account)を割り当てます

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-during-appliance-registration"></a>アプライアンスの登録中に "Azure Active Directory (AAD) operation failed with status Forbidden" (Azure Active Directory (AAD) の操作が失敗しました。状態は Forbidden です)

**エラー**

AAD の特権が不足しているために登録を完了できず、"Azure Active Directory (AAD) operation failed with status Forbidden" (Azure Active Directory (AAD) の操作が失敗しました。状態は Forbidden です) というエラーが発生します。

**修正**

Azure の AAD アプリケーションを作成および管理するために必要な[アクセス許可](./tutorial-discover-vmware.md#prepare-an-azure-user-account)があることを確認します。 **アプリケーション開発者** ロール、または **ユーザーがアプリケーションを登録できること** がテナント レベルで許可されているユーザー ロールが必要です。

## <a name="forbidden-to-access-key-vault-during-appliance-registration"></a>アプライアンスの登録中に "Forbidden to access Key Vault" (Key Vault へのアクセスが許可されていません)

**エラー**

"{KeyVaultName}" に対する Azure Key Vault の作成または更新操作が、次のエラーのため失敗しました: "{KeyVaultErrorMessage}"。

これは通常、アプライアンスの登録に使用された Azure ユーザー アカウントが、ポータル上で Azure Migrate プロジェクト キーを生成するために (つまり、キー コンテナーが作成されたときに) 使用されたアカウントと異なる場合に発生します。

**修正**

1. アプライアンス上で現在ログインしているユーザー アカウントに Key Vault に対する必要なアクセス許可があることを確認します (エラー メッセージに記載)。 ユーザー アカウントには、[こちら](./tutorial-discover-vmware.md#prepare-an-azure-user-account)に記載されているアクセス許可が必要です。
2. Key Vault に移動し、ユーザー アカウントに、キー コンテナーのアクセス ポリシーで割り当てられているすべての _キー、シークレット、証明書_ のアクセス許可を持つアクセス ポリシーがあることを確認します。 [詳細情報](../key-vault/general/assign-access-policy-portal.md)
3. アプライアンスを **プライベート エンドポイント接続** で有効にした場合は、アプライアンスが Key Vault を作成したのと同じ VNet でホストされていること、あるいはプライベート リンクを使用して Azure VNet (Key Vault が作成された場所) に接続されていることを確認します。 Key Vault プライベート リンクはアプライアンスから解決可能であることを確認してください。 **[Azure Migrate**: **Discovery** and **assessment]** >  **[プロパティ]** にアクセスして、Azure Migrate キー作成中に作成した Key Vault などのリソースのプライベート エンドポイントの詳細を確認します。 [詳細情報](./troubleshoot-network-connectivity.md)
4. 必要なアクセス許可と接続がある場合は、しばらくしてからアプライアンスで登録を再試行してください。

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>検証中に vCenter Server と接続できない

**エラー**

この接続エラーを受け取る場合、vCenter Server <*サーバー名*>.com:9443 に接続できない可能性があります。 エラーの詳細では、`https://\*servername*.com:9443/sdk` でリッスンしているエンドポイントで、メッセージを受け入れることができるものがないことが示されています。

**修正**

- 最新バージョンのアプライアンスを実行しているかどうかを確認します。 そうでない場合は、アプライアンスを[最新バージョン](./migrate-appliance.md)にアップグレードします。
- 最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をアプライアンスで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターでは既定でポート番号 443 への接続が試みられます。

    1. アプライアンスから <*サーバー名*>.com に対して ping を実行します。
    2. ステップ 1 が失敗する場合は、IP アドレスを使用して vCenter サーバーへの接続を試みます。
    3. vCenter Server に接続するための正しいポート番号を確認します。
    4. vCenter Server が起動されて実行中であることを確認します。

## <a name="server-credentials-domain-failing-validation-on-vmware-appliance"></a>サーバー資格情報 (ドメイン) が VMware アプライアンスで検証できない

**エラー**

ソフトウェア インベントリ、エージェントレス依存関係分析を実行するために VMware アプライアンスで追加されたドメイン資格情報で "検証に失敗しました" が表示されます。

**修正**

1. 正しいドメイン名と資格情報を指定したことを確認します
1. 資格情報を検証するために、アプライアンスからドメインに到達可能であることを確認します。 アプライアンスに通信経路の問題があるか、アプライアンス サーバーからドメイン名を解決できない可能性があります。
1. **[編集]** を選択してドメイン名または資格情報を更新し **[資格情報の再検証]** を選択して、しばらくしてから資格情報を再度検証することができます

## <a name="access-is-denied-when-connecting-to-hyper-v-hosts-or-clusters-during-validation"></a>検証中に Hyper-V ホストまたはクラスターへ接続するとき "アクセスが拒否されました"

**エラー**

"アクセスが拒否されました" というエラーが発生するため、追加された Hyper-V ホスト/クラスターを検証できません。

**修正**

1. [Hyper-V ホストの前提条件](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)がすべて満たされていることを確認します。 
1. Hyper-V ホストを手動で準備する方法や、プロビジョニング PowerShell スクリプトの使用方法については、[**こちら**](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts)の手順を参照してください。

## <a name="the-server-does-not-support-ws-management-identify-operations-during-validation"></a>検証中の "The server does not support WS-Management Identify operations"(サーバーは WS-Management Identify 操作をサポートしていません)

**エラー**

Hyper-V クラスターをアプライアンス上で検証できず、"The server does not support WS-Management Identify operations. Skip the TestConnection part of the request and try again" (サーバーは WS-Management Identify 操作をサポートしていません。要求の TestConnection の部分をスキップして、再試行してください) のエラーが表示されます。

**修正**

これは通常、アプライアンスでプロキシ構成を指定したときに発生します。 ノードの FQDN を指定した場合でも、アプライアンスはクラスター ノードの短い名前を使用してクラスターに接続します。 クラスターノードの短い名前をアプライアンスのバイパス プロキシの一覧に追加すると、この問題は解決されて、Hyper-V クラスターの検証が成功します。

## <a name="cant-connect-to-host-or-cluster-during-validation-on-hyper-v-appliance"></a>Hyper-V アプライアンスでの検証中に "ホストまたはクラスターに接続できない"

**エラー**

"Can't connect to a host or cluster because the server name can't be resolved. (サーバー名を解決できないため、ホストまたはクラスターに接続できません。) WinRM エラー コード: 0x803381B9" は、アプライアンスの要求を処理する Azure DNS で、指定されたクラスターまたはホストの名前を解決できない場合に発生する可能性があります。

これは通常、DNS で解決できないホストの IP アドレスを追加した場合に発生します。 クラスター内のホストについても、このエラーが表示されることがあります。 これは、アプライアンスをクラスターに接続することはできるものの、クラスターから FQDN ではないホスト名が返されることを示します。

**修正**

このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
1. 管理者としてメモ帳を開きます。
1. C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
1. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
1. hosts ファイルを保存して閉じます。
1. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、これらのホストの最新情報が Azure portal に表示されるようになるはずです。

## <a name="unable-to-connect-to-server-during-validation-of-physical-servers"></a>物理サーバーの検証中に "サーバーに接続できない"

**修正**

- アプライアンスからターゲット サーバーへの接続があることを確認します。
- Linux サーバーの場合は、次の手順に従ってパスワード ベースの認証が有効になっていることを確認します。
    1. Linux サーバーにログインし、コマンド 'vi /etc/ssh/sshd_config' を使用して SSH 構成ファイルを開きます
    2. "PasswordAuthentication" オプションを [はい] に設定します。 ファイルを保存します。
    3. "service sshd restart" を実行して SSH サービスを再起動します
- Windows サーバーの場合は、リモート WMI 呼び出しを許可するため、ポート 5985 が開かれていることを確認します。
- GCP Linux サーバーを検出中で、ルート ユーザーを使用している場合は、次のコマンドを使用して、ルート ログインの既定の設定を変更します
    1. Linux サーバーにログインし、コマンド 'vi /etc/ssh/sshd_config' を使用して SSH 構成ファイルを開きます
    2. "PermitRootLogin" オプションを [はい] に設定します。
    3. "service sshd restart" を実行して SSH サービスを再起動します

## <a name="failed-to-fetch-bios-guid-for-server-during-validation"></a>検証中のサーバーで "Failed to fetch BIOS GUID"(BIOS GUID をフェッチできませんでした)

**エラー**

アプライアンスで物理サーバーの検証が失敗し、"BIOS GUID をフェッチできませんでした" というエラー メッセージが表示されます。

**修正**

**Linux サーバー:** 検証に失敗している対象サーバーに Connect し、次のコマンドを実行してサーバーの BIOS GUID を返すかどうかを確認します。
````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
このコマンドをアプライアンス サーバーのコマンド プロンプトから実行することもできます。次のコマンドを使用してターゲットの Linux サーバーとの SSH 接続を作成してください。
````
ssh <username>@<servername>
````

**Windows サーバー:** 検証に失敗している対象サーバーのアプライアンスサーバーから次のコードを実行して、サーバーの BIOS GUID を返すかどうかを確認します。
````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

上のコードを実行するとき、ターゲット サーバーのホスト名 (IP アドレス/FQDN/ホスト名) を指定する必要があります。 その後で、サーバーに接続するための資格情報を入力するよう求められます。

## <a name="no-suitable-authentication-method-found-for-server-during-validation"></a>検証中のサーバーで "No suitable authentication method found"(適切な認証方法が見つかりませんでした)

**エラー**

Linux サーバーを物理アプライアンスを通じて検証しようとしているときに、"No suitable authentication method found"(適切な認証方法が見つかりませんでした) というメッセージが表示されます。

**修正**

次の手順に従って、Linux サーバーでパスワード ベースの認証が有効になっていることを確認します。

1. Linux サーバーにログインし、コマンド 'vi /etc/ssh/sshd_config' を使用して SSH 構成ファイルを開きます
2. "PasswordAuthentication" オプションを [はい] に設定します。 ファイルを保存します。
3. "service sshd restart" を実行して SSH サービスを再起動します

## <a name="access-is-denied-when-connecting-to-physical-servers-during-validation"></a>検証中に物理サーバーへ接続しているときの "アクセスが拒否されました"

**エラー**

Windows サーバーを物理アプライアンス経由で検証しようとしているとき、“WS-Management service cannot process the request. The WMI service returned an access denied error”(WS-Management サービスが要求を処理できません。WMI サービスはアクセス拒否エラーを返しました) のエラーが発生します。

**修正**

- このエラーが発生した場合は、アプライアンス構成マネージャーで指定されたユーザーアカウント (ドメイン/ローカル) が、リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザーの各グループに追加されていることを確認してください。
- リモート管理ユーザー グループが存在しない場合は、ユーザー アカウントを次のグループに追加します: WinRMRemoteWMIUsers_。
- また、ターゲット サーバーのコマンド プロンプトで次のコマンドを実行して、WS-Management プロトコルがサーバーで有効になっているか調べることもできます。
    
    ```` winrm qc ````
- 問題が解決しない場合は、ユーザー アカウントに CIMV2 名前空間とサブ名前空間に対するアクセス許可があることを、WMI コントロールパネルで確認してください。 アクセスを設定するには、次の手順に従ってください。
    1.  アプライアンスで検証に失敗しているサーバーにアクセスします
    2.  [実行] を [スタート] メニューで探して選択します。 [実行] ダイアログ ボックスで、‘Open:’\(オープン:\) テキスト フィールドに「wmimgmt .msc」と入力し、Enter キーを押します。
    3.  wmimgmt console コンソールが開き、"WMI コントロール (ローカル)" が左側のパネルに表示されます。 右クリックして、メニューから [プロパティ] を選択します。
    4.  [WMI Control (Local) Properties\(WMI コントロール (ローカル) のプロパティ\)] ダイアログ ボックスで、[セキュリティ] タブをクリックします。
    5.  [セキュリティ] タブで、名前空間ツリーの "ルート" フォルダーを展開し、"cimv2" 名前空間を選択します。
    6.  [セキュリティ] ボタンをクリックすると、[Security for ROOT\cimv2]\(ROOT\cimv2 のセキュリティ\) ダイアログ ボックスが開きます。
    7.  [Group or users names]\(グループ名またはユーザー名\)セクションで、[追加] ボタンをクリックして、[ユーザー、コンピューター、サービス アカウントまたはグループの選択] ダイアログ ボックスを開きます。
    8.  ユーザー アカウントを検索して選択し、[OK] ボタンをクリックして [Security for ROOT\cimv2]\(ROOT\cimv2 のセキュリティ\) ダイアログ ボックスに戻ります。
    9.  [Group or users names]\(グループ名またはユーザー名\) セクションで、追加したユーザー アカウントを選択し、次のアクセス許可が許可されているかを確認します。<br/>
    アカウントの有効化 <br/>
    リモートの有効化
    10. [適用] をクリックして、ユーザー アカウントに設定されたアクセス許可を有効にします。

- ドメイン/ワークグループ以外のサーバーのローカル ユーザー アカウントにも同じ手順が適用されますが、場合によっては、コマンドが標準ユーザーとして実行される際に [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) フィルター処理によって一部の WMI プロパティがブロックされる可能性があります。そのため、ローカル管理者アカウントを使用するか UAC を無効にして、ローカル ユーザー アカウントがフィルター処理され、代わりに完全な管理者になります。
- リモート UAC を制御するレジストリ エントリを変更してリモート UAC を無効にするのはお勧めできませんが、ワークグループで必要になる場合があります。 レジストリ エントリは HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy です。 このエントリの値がゼロ (0) のとき、リモート UAC アクセス トークンのフィルター処理が有効です。 値が 1 のとき、リモート UAC は無効です。

## <a name="appliance-is-disconnected"></a>アプライアンスが切断されている

**エラー**

ポータルからいくつかの VMware サーバーでレプリケーションを有効にしようとすると、"アプライアンスが切断されている" というエラー メッセージが表示されます。

これは、アプライアンスがシャットダウン状態にある場合、またはアプライアンスの DRA サービスが Azure と通信できない場合に発生する可能性があります。

**修正**

 1. アプライアンス構成マネージャーにアクセスし、前提条件を再実行すると、 **[アプライアンス サービスを表示]** に DRA サービスの状態が表示されます。 
 1. サービスが実行されていない場合は、コマンド プロンプトから、次のコマンドを使用してサービスを停止してから再起動します。

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>次のステップ

[VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md)、または[物理サーバー](how-to-set-up-appliance-physical.md)用にアプライアンスを設定します。