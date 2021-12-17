---
title: Azure Migrate アプライアンスのトラブルシューティング
description: Azure Migrate アプライアンスで発生する可能性がある問題をトラブルシューティングするためのヘルプを取得します。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: d6d87d7503a68091e72f1aa84703966ab28f32b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654520"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスのトラブルシューティング

この記事は、[Azure Migrate](migrate-services-overview.md) アプライアンスをデプロイし、そのアプライアンスを使用してオンプレミスのサーバーを検出する際の問題のトラブルシューティングに役立ちます。

## <a name="whats-supported"></a>サポート対象

アプライアンスのサポート要件を[確認](migrate-appliance.md)します。

## <a name="invalid-ovf-manifest-entry-error-occurs-during-appliance-setup"></a>アプライアンス設定時に "無効な OVF マニフェスト エントリ" エラーが発生する

OVA テンプレートを使用してアプライアンスを設定しているときに、"指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェストのエントリ" というエラーが表示されます。

### <a name="remediation"></a>Remediation

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされていることを、そのハッシュ値をチェックして確認します。 [詳細については、こちらを参照してください](./tutorial-discover-vmware.md)。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
1. それでもデプロイが失敗する場合で、VMware vSphere クライアントを使って OVF ファイルをデプロイしている場合は、vSphere Web クライアントでデプロイしてみてください。 展開がそれでも失敗する場合は、別の Web ブラウザーをお試しください。
1. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、ESXi ホストに直接 OVA をデプロイしてみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して (vCenter Server ではなく) ESXi ホストに直接接続します。
   - **[ホーム]**  >  **[インベントリ]** で、 **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートのデプロイ\)** を選択します。 OVA を参照して、デプロイを完了する。
1. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。

## <a name="connectivity-check-fails-during-the-prerequisites-setup"></a>前提条件の設定中に接続チェックが失敗する

アプライアンスの接続チェックでエラーが発生します。

### <a name="remediation"></a>Remediation

1. アプライアンスから必要な [URL](./migrate-appliance.md#url-access) に接続できることを確認します。
1. これらの URL へのアクセスをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 許可リストを作成する必要がある場合は、すべての URL を含めるようにします。
1. オンプレミスでプロキシ サーバーが構成されている場合は、同じ手順で **[プロキシの設定]** を選択して、プロキシの詳細を正しく入力します。 プロキシ認可資格情報が必要な場合は、それを入力します。
1. サーバーがこれまでに[レプリケーション アプライアンス](./migrate-replication-appliance.md)の設定に使用されていないこと、またはモビリティ サービス エージェントがサーバーにインストールされていることを確認します。

## <a name="connectivity-check-fails-for-the-akams-url-during-the-prerequisites-setup"></a>前提条件の設定中に aka.ms URL の接続チェックが失敗する

アプライアンスで aka.ms URL の接続チェック時にエラーが発生します。

### <a name="remediation"></a>Remediation

1. インターネットに接続でき、URL-aka.ms/* を許可リストに追加していることを確認して、最新バージョンのサービスをダウンロードします。
1. この URL へのアクセスをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 構成マネージャーの前提条件の手順でプロキシの詳細を正しく指定していることを確認します。
1. アプライアンス構成マネージャーに戻り、前提条件を再実行して自動更新を開始します。
1. 再試行しても問題が解決しない場合は、*latestcomponents.json* ファイルを [この Web サイト](https://aka.ms/latestapplianceservices)からダウンロードして、障害が発生しているサービスの最新バージョンを確認します。 それらをファイル内のダウンロード リンクから手動で更新します。

 アプライアンスの **プライベート エンドポイント接続** を有効にしてあり、この URL へのインターネット経由でのアクセスを許可したくない場合は、[自動更新を無効](./migrate-appliance.md#turn-off-auto-update)にします (aka.ms リンクはこのサービスに必要であるため)。

 >[!Note]
 >自動更新サービスを無効にすると、アプライアンスで実行されているサービスに最新の更新プログラムが自動で適用されなくなります。 この状況を回避するには、[アプライアンス サービスを手動で更新します](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="auto-update-check-fails-during-the-prerequisites-setup"></a>自動更新チェックが前提条件の設定中に失敗する

アプライアンスでの自動更新チェックでエラーが発生します。

### <a name="remediation"></a>Remediation

1. [必要な URL](./migrate-appliance.md#url-access) の許可リストを作成し、プロキシやファイアウォール設定によってこれらがブロックされていないことを確認します。
1. アプライアンス コンポーネントの更新が失敗する場合は、前提条件を再実行するか、[アプライアンス サービスを手動で更新します](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="time-sync-check-fails-during-the-prerequisites-setup"></a>時刻同期チェックが前提条件の設定中に失敗する

時刻の同期に関するエラーは、サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があることを示します。

### <a name="remediation"></a>Remediation

- コントロール パネルから日付と時刻の設定をチェックして、アプライアンス サーバーの時刻がインターネット時刻と同期していることを確認します。
- 次の手順に従って、アプライアンス サーバーのクロック時刻を現在の時刻に合わせて変更することもできます。
     1. サーバーで管理者用のコマンド プロンプトを開きます。
     1. タイム ゾーンを確認するには、**w32tm /tz** を実行します。
     1. 時刻を同期するには、**w32tm /resync** を実行します。

## <a name="vddk-check-fails-during-the-prerequisites-setup-on-the-vmware-appliance"></a>VMware アプライアンスで前提条件の設定中に VDDK チェックが失敗する

アプライアンスにインストールされている必要な VDDK キットをアプライアンスが見つけることができなかったため、Virtual Disk Development Kit (VDDK) チェックは失敗しました。 この問題によって、進行中のレプリケーションでエラーが発生する可能性があります。

### <a name="remediation"></a>Remediation

1. VDDK 6.7 をダウンロードしてあり、そのファイルをアプライアンス サーバー上の **C:\Program Files\VMware\VMware Virtual Disk Development Kit** にコピーしてあることを確認します。
1. アプライアンスで他のバージョンの VDDK を使用しているソフトウェアやアプリケーションが他にないか確認します。

## <a name="project-key-related-error-occurs-during-appliance-registration"></a>アプライアンスの登録中にプロジェクト キー関連のエラーが発生する

 プロジェクトからコピーされた Azure Migrate プロジェクト キーを使用して、アプライアンスを登録しようとすると問題が発生します。

### <a name="remediation"></a>Remediation

1. プロジェクトから正しいキーをコピーしたことを確認します。 プロジェクトの **[Azure Migrate: Discovery and assessment]** カードで、 **[検出]** を選択します。 次に、手順 1 で **[既存のアプライアンスの管理]** を選択します。 ドロップダウン メニューから、以前にキーを生成したアプライアンス名を選択します。 対応するキーをコピーします。
1. 適切な **クラウドの種類** (パブリック、US Gov) および **アプライアンスの種類** (VMware、Hyper-V、物理、またはその他) のアプライアンスにキーを貼り付けていることを確認します。 アプライアンス構成マネージャーの上部で、クラウドとシナリオの種類を確認します。

## <a name="failed-to-connect-to-the-azure-migrate-project-error-occurs-during-appliance-registration"></a>アプライアンス登録時に "Azure Migrate プロジェクトに接続できませんでした" エラーが発生する

Azure ユーザー アカウントを使用して正常にサインインした後、アプライアンスの登録手順が失敗し、"Azure Migrate プロジェクトに接続できませんでした。 エラーの詳細を確認し、[再試行] をクリックして修復手順に従ってください" というメッセージが表示されます。

この問題は、アプライアンス構成マネージャーからのサインインに使用された Azure ユーザー アカウントが、ポータルで Azure Migrate プロジェクト キーの生成に使用されたユーザー アカウントと異なる場合に発生します。

### <a name="remediation"></a>Remediation

2 つのオプションがあります。

- アプライアンスの登録を完了するには、ポータルで Azure Migrate プロジェクト キーを生成したのと同じ Azure ユーザー アカウントを使用します。
- アプライアンスの登録に使用されている他の Azure ユーザー アカウントに、必要なロールと[アクセス許可](./tutorial-discover-vmware.md#prepare-an-azure-user-account)を割り当てることもできます。

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-error-occurs-during-appliance-registration"></a>アプライアンスの登録中に "Azure Active Directory (AAD) の操作が失敗しました。状態は Forbidden です" というエラーが発生する

Azure Active Directory の特権が不足しているために登録を完了できず、"Azure Active Directory (AAD) の操作が失敗しました。状態は Forbidden です" というエラーが表示されます。

### <a name="remediation"></a>Remediation

Azure で Azure Active Directory アプリケーションを作成および管理するために[必要なアクセス許可](./tutorial-discover-vmware.md#prepare-an-azure-user-account)があることを確認します。 **アプリケーション開発者** ロール、*または***ユーザーがアプリケーションを登録できる** ことがテナント レベルで許可されているユーザー ロールが必要です。

## <a name="forbidden-to-access-key-vault-error-occurs-during-appliance-registration"></a>アプライアンスの登録中に "Key Vault へのアクセスが許可されていません" エラーが発生する

"{KeyVaultName}" に対する Azure Key Vault の作成または更新操作が、エラー "{KeyVaultErrorMessage}" のため失敗しました。

この問題は通常、アプライアンスの登録に使用された Azure ユーザー アカウントが、ポータル上で Azure Migrate プロジェクト キーを生成するために (つまり、キー コンテナーが作成されたときに) 使用されたアカウントと異なる場合に発生します。

### <a name="remediation"></a>Remediation

1. アプライアンス上で現在サインインしているユーザー アカウントに、エラー メッセージに記載されているキー コンテナーに対する必要なアクセス許可があることを確認します。 ユーザー アカウントには、[この Web サイト](./tutorial-discover-vmware.md#prepare-an-azure-user-account)に記載されているアクセス許可が必要です。
1. キー コンテナーに移動し、ユーザー アカウントに、**キー コンテナーのアクセス ポリシー** で割り当てられているすべての **キー**、**シークレット**、**証明書** のアクセス許可を持つアクセス ポリシーがあることを確認します。 [詳細については、こちらを参照してください](../key-vault/general/assign-access-policy-portal.md)。
1. アプライアンスの **プライベート エンドポイント接続** を有効にした場合は、アプライアンスがキー コンテナーを作成したのと同じ仮想ネットワークでホストされていること、またはプライベート リンクを経由でキー コンテナーが作成された Azure 仮想ネットワークに接続されていることを確認します。 キー コンテナー プライベート リンクがアプライアンスから解決可能であることを確認してください。 **[Azure Migrate: Discovery and assessment]**  >  **[プロパティ]** にアクセスして、Azure Migrate キー作成中に作成したキー コンテナーなどのリソースのプライベート エンドポイントの詳細を確認します。 [詳細については、こちらを参照してください](./troubleshoot-network-connectivity.md)。
1. 必要なアクセス許可と接続がある場合は、しばらくしてからアプライアンスで登録を再試行してください。

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>検証中に vCenter Server と接続できない

この接続エラーを受け取る場合、vCenter Server <*サーバー名*>.com:9443 に接続できない可能性があります。 エラーの詳細では、`https://\*servername*.com:9443/sdk` でリッスンしているエンドポイントで、メッセージを受け入れることができるものがないことが示されています。

### <a name="remediation"></a>Remediation

- 最新バージョンのアプライアンスを実行しているかどうかを確認します。 そうでない場合は、アプライアンスを[最新バージョン](./migrate-appliance.md)にアップグレードします。
- 最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をアプライアンスで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターでは既定でポート番号 443 への接続が試みられます。

    1. アプライアンスから <*サーバー名*>.com に対して ping を実行します。
    1. ステップ 1 が失敗する場合は、IP アドレスを使用して vCenter サーバーへの接続を試みます。
    1. 正しいポート番号を識別して vCenter サーバーに接続します。
    1. vCenter サーバーが起動されて実行中であることを確認します。

## <a name="server-credentials-domain-fails-validation-on-the-vmware-appliance"></a>サーバー資格情報 (ドメイン) が VMware アプライアンスで検証に失敗する

ソフトウェア インベントリとエージェントレス依存関係分析を実行するために VMware アプライアンスで追加されたドメイン資格情報で "検証に失敗しました" が表示されます。

### <a name="remediation"></a>Remediation

1. 正しいドメイン名と資格情報を指定したことを確認します。
1. 資格情報を検証するために、アプライアンスからドメインに到達可能であることを確認します。 アプライアンスに通信経路の問題があるか、アプライアンス サーバーからドメイン名を解決できない可能性があります。
1. **[編集]** を選択して、ドメイン名または資格情報を更新します。 **[資格情報の再検証]** を選択して、しばらくしてから資格情報を再度検証します。

## <a name="access-is-denied-error-occurs-when-you-connect-to-hyper-v-hosts-or-clusters-during-validation"></a>検証中に Hyper-V ホストまたはクラスターに接続すると "アクセスが拒否されました" エラーが発生する

"アクセスが拒否されました" エラーのため、追加された Hyper-V ホストまたはクラスターを検証できません。

### <a name="remediation"></a>Remediation

1. [Hyper-V ホストの前提条件](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)がすべて満たされていることを確認します。 
1. 手動で、またはプロビジョニング PowerShell スクリプトを使用して、Hyper-V ホストを準備する方法については、[この Web サイト](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts)の手順を確認してください。

## <a name="the-server-does-not-support-ws-management-identify-operations-error-occurs-during-validation"></a>検証中に "サーバーは WS-Management Identify 操作をサポートしていません" エラーが発生する

Hyper-V クラスターをアプライアンス上で検証できず、"The server does not support WS-Management Identify operations. Skip the TestConnection part of the request and try again" (サーバーは WS-Management Identify 操作をサポートしていません。要求の TestConnection の部分をスキップして、再試行してください) のエラーが表示されます。

### <a name="remediation"></a>Remediation

このエラーは、通常、アプライアンスでプロキシ構成を指定している場合に発生します。 ノードの FQDN を指定した場合でも、アプライアンスでは、クラスター ノードの短い名前を使用してクラスターに接続されます。 クラスターノードの短い名前をアプライアンスのバイパス プロキシの一覧に追加すると、この問題は解決されて、Hyper-V クラスターの検証が成功します。

## <a name="cant-connect-to-host-or-cluster-error-occurs-during-validation-on-a-hyper-v-appliance"></a>Hyper-V アプライアンスでの検証中に "ホストまたはクラスターに接続できない" エラーが発生する

エラー "サーバー名を解決できないため、ホストまたはクラスターに接続できません。 WinRM エラー コード: 0x803381B9" は、アプライアンスの要求を処理する Azure DNS で、指定されたクラスターまたはホストの名前を解決できない場合に発生する可能性があります。

この問題は通常、DNS で解決できないホストの IP アドレスを追加した場合に発生します。 クラスター内のホストについても、このエラーが表示されることがあります。 これは、アプライアンスはクラスターに接続できますが、クラスターから FQDN ではないホスト名が返されることを示します。

### <a name="remediation"></a>Remediation

このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
1. 管理者としてメモ帳を開きます。
1. C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
1. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
1. hosts ファイルを保存して閉じます。
1. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、これらのホストの最新情報が Azure portal に表示されるようになるはずです。

## <a name="unable-to-connect-to-server-error-occurs-during-validation-of-physical-servers"></a>物理サーバーの検証中に "サーバーに接続できない" エラーが発生する

### <a name="remediation"></a>Remediation

- アプライアンスからターゲット サーバーへの接続があることを確認します。
- Linux サーバーの場合は、次の手順に従ってパスワード ベースの認証が有効になっていることを確認します。
    1. Linux サーバーにサインインし、コマンド **vi /etc/ssh/sshd_config** を使用して SSH 構成ファイルを開きます。
    1. **PasswordAuthentication** オプションを yes に設定します。 ファイルを保存します。
    1. **service sshd restart** を実行して SSH サービスを再起動します。
- Windows サーバーの場合は、リモート WMI 呼び出しを許可するため、ポート 5985 が開かれていることを確認します。
- GCP Linux サーバーを検出中で、ルート ユーザーを使用している場合は、次のコマンドを使用して、ルート ログインの既定の設定を変更します。
    1. Linux サーバーにサインインし、コマンド **vi /etc/ssh/sshd_config** を使用して SSH 構成ファイルを開きます。
    1. **PermitRootLogin** オプションを yes に設定します。
    1. **service sshd restart** を実行して SSH サービスを再起動します。

## <a name="failed-to-fetch-bios-guid-error-occurs-for-the-server-during-validation"></a>検証中にサーバーに対して "BIOS GUID をフェッチできませんでした" エラーが発生する

アプライアンスで物理サーバーの検証が失敗し、"BIOS GUID をフェッチできませんでした" というエラー メッセージが表示されます。

### <a name="remediation"></a>Remediation

**Linux サーバー:**

検証が失敗しているターゲット サーバーに接続します。 次のコマンドを実行して、サーバーの BIOS GUID が返されるかどうかを確認します。

````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
このコマンドをアプライアンス サーバーのコマンド プロンプトから実行することもできます。次のコマンドを使用してターゲットの Linux サーバーとの SSH 接続を作成してください。
````
ssh <username>@<servername>
````

**Windows サーバー:**

検証に失敗しているターゲット サーバーに対して、アプライアンス サーバーから PowerShell で次のコードを実行して、サーバーの BIOS GUID が返されるかどうかを確認します。

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

前のコードを実行する場合は、ターゲット サーバーのホスト名を指定する必要があります。 それには、IP アドレス/FQDN/ホスト名を指定できます。 その後、サーバーに接続するための資格情報を入力するよう求められます。

## <a name="no-suitable-authentication-method-found-error-occurs-for-the-server-during-validation"></a>検証中のサーバーで "適切な認証方法が見つかりませんでした" エラーが発生する

物理アプライアンスを使用して Linux サーバーを検証しようとすると、"適切な認証方法が見つかりません" というエラーが表示されます。

### <a name="remediation"></a>Remediation

次の手順に従って、Linux サーバーでパスワードベースの認証が有効になっていることを確認します。

1. Linux サーバーにサインインします。 コマンド **vi /etc/ssh/sshd_config** を使用して SSH 構成ファイルを開きます。
1. **PasswordAuthentication** オプションを **yes** に設定します。 ファイルを保存します。
1. **service sshd restart** を実行して SSH サービスを再起動します。

## <a name="access-is-denied-error-occurs-when-you-connect-to-physical-servers-during-validation"></a>検証中に物理サーバーに接続すると "アクセスが拒否されました" エラーが発生する

物理アプライアンスを介して Windows サーバーを検証しようとすると、エラー "WS-Management サービスは要求を処理できません。 WMI サービスはアクセス拒否エラーを返しました" が表示されます。

### <a name="remediation"></a>Remediation

- このエラーが発生した場合は、アプライアンス構成マネージャーで指定されたユーザー アカウント (ドメイン/ローカル) が、リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザーの各グループに追加されていることを確認してください。
- リモート管理ユーザー グループが存在しない場合は、ユーザー アカウントをグループ WinRMRemoteWMIUsers_ に追加します。
- 目的のサーバーのコマンド プロンプトで次のコマンドを実行すれば、WS-Management プロトコルがそのサーバーで有効になっているかどうかを確認することもできます: `winrm qc`
- 問題が解決しない場合は、ユーザー アカウントに CIMV2 名前空間とサブ名前空間に対するアクセス許可があることを、WMI コントロールパネルで確認してください。 アクセスを設定するには、次の手順に従ってください。

    1. アプライアンスで検証に失敗しているサーバーにアクセスします。
    1. **スタート** メニューで **[ファイル名を指定して実行]** を検索して選択します。 **[ファイル名を指定して実行]** ダイアログで、 **[名前]** テキスト ボックスに「**wmimgmt.msc**」と入力し、**Enter** を選択します。
    1. wmimgmt コンソールが開き、左側のペインに **[WMI コントロール (ローカル)]** が表示されます。 それを右クリックして、メニューから **[プロパティ]** を選択します。
    1. **[WMI コントロール (ローカル) のプロパティ]** ダイアログで、 **[セキュリティ]** タブを選択します。
    1. **[セキュリティ]** タブで、名前空間ツリーの **[Root]** フォルダーを展開し、**cimv2** 名前空間を選択します。
    1. **[セキュリティ]** を選択して **[セキュリティ ROOT\CIMV2]** ダイアログを開きます。
    1. **[グループ名またはユーザー名]** セクションで、 **[追加]** をクリックして、 **[ユーザー、コンピューター、サービス アカウントまたはグループの選択]** ダイアログを開きます。
    1. ユーザー アカウントを検索して選択し、 **[OK]** を選択して **[セキュリティ ROOT\CIMV2]** ダイアログに戻ります。
    1. **[グループ名またはユーザー名]** セクションで、先ほど追加したユーザー アカウントを選択します。 次のアクセス許可が与えられていることを確認します。<br/>
       - アカウントの有効化 <br/>
       - リモートの有効化
    1. **[適用]** を選択して、ユーザー アカウントに設定されたアクセス許可を有効にします。

- ドメイン/ワークグループ以外のサーバーのローカル ユーザー アカウントにも同じ手順が適用されます。 場合によっては、コマンドが標準ユーザーとして実行される際に [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) フィルター処理によって一部の WMI プロパティがブロックされる可能性があります。そのため、ローカル管理者アカウントを使用するか UAC を無効にして、ローカル ユーザー アカウントがフィルター処理されず、代わりに完全な管理者になるようにすることができます。
- リモート UAC を制御するレジストリ エントリを変更してリモート UAC を無効にするのは推奨されませんが、ワークグループで必要になる場合があります。 レジストリ エントリは HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy です。 このエントリの値がゼロ (0) のとき、リモート UAC アクセス トークンのフィルター処理が有効です。 値が 1 のとき、リモート UAC は無効です。

## <a name="appliance-is-disconnected"></a>アプライアンスが切断されている

ポータルからいくつかの VMware サーバーでレプリケーションを有効にしようとすると、"アプライアンスが切断されている" というエラー メッセージが表示されます。

このエラーは、アプライアンスがシャットダウン状態にある場合、またはアプライアンスの DRA サービスが Azure と通信できない場合に発生する可能性があります。

### <a name="remediation"></a>Remediation

 1. アプライアンス構成マネージャーにアクセスし、前提条件を再実行すると、 **[アプライアンス サービスを表示]** に DRA サービスの状態が表示されます。 
 1. サービスが実行されていない場合は、コマンド プロンプトから、次のコマンドを使用してサービスを停止してから再起動します。

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>次のステップ

[VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md)、または[物理サーバー](how-to-set-up-appliance-physical.md)用にアプライアンスを設定します。
