---
title: Azure Migrate の Collector アプライアンスについて | Microsoft Docs
description: Azure Migrate の Collector アプライアンスに関する情報を提供します。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: b79045e54b9c2ee4846f2216704a419e0ff85501
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434434"
---
# <a name="about-the-collector-appliance"></a>Collector アプライアンスについて

 この記事では、Azure Migrate Collector に関する情報を提供します。

Azure Migrate Collector は、Azure に移行する前の [Azure Migrate](migrate-overview.md) サービスによる評価を目的として、オンプレミス vCenter 環境を検出するために使用される軽量アプライアンスです。  


## <a name="deploying-the-collector"></a>Collector のデプロイ

OVF テンプレートを使用して、Collector アプライアンスをデプロイします。

- Azure portal で Azure Migrate プロジェクトから OVF テンプレートをダウンロードします。 Collector アプライアンスの仮想マシンを設定するには、ダウンロードしたファイルを vCenter Server にインポートします。
- VMware は、OVF から 4 コア、8 GB の RAM、80 GB の 1 ディスクを備えた仮想マシンを設定します。 オペレーティング システムは Windows Server 2012 R2 (64 ビット) です。
- Collector を実行すると、Collector が Azure Migrate に接続できることを確認するために、いくつかの前提条件の確認が実行されます。

- Collector の作成について[説明します](tutorial-assessment-vmware.md#create-the-collector-vm)。


## <a name="collector-prerequisites"></a>Collector の前提条件

Collector が Azure Migrate サービスに接続し、検出されたデータをアップロードするためには、いくつかの前提条件チェックに合格する必要があります。

- **インターネット接続の確認**: Collector はインターネットに直接、またはプロキシ経由で接続できます。
    - 前提条件の確認によって、[必須および省略可能な URL](#connect-to-urls) への接続が検証されます。
    - インターネットへの直接接続があれば、Collector が必須の URL にアクセスできることを確認する以外は、特別のアクションは不要です。
    - プロキシ経由で接続する場合には、[以下の要件](#connect-via-a-proxy)に注意してください。
- **時刻同期を検証する**: サービスに対する要求が認証されるには、Collector はインターネット時刻サーバーと同期されている必要があります。
    - 時刻を検証できるよう、portal.azure.com の URL にコレクターから接続できる必要があります。
    - マシンが同期していない場合は、Collector 仮想マシンのクロックの時刻を現在の時刻と一致するよう変更する必要があります。 これを実行するには、仮想マシン上で管理プロンプトを開き、**w32tm /tz** を実行してタイム ゾーンをチェックします。 時刻を同期するには、**w32tm /resync** を実行します。
- **Collector サービスが実行していることをチェックする**: Azure Migrate Collector サービスが、Collector 仮想マシンで実行している必要があります。
    - このサービスは、マシンが起動したときに自動的に開始されます。
    - サービスが実行していない場合は、コントロール パネルから開始します。
    - Collector サービスは、vCenter Server に接続し、仮想マシンのメタデータとパフォーマンス データを収集し、それを Azure Migrate サービスに送信します。
- **VMware PowerCLI 6.5 のインストールを確認する**: vCenter Server と通信できるように、Collector 仮想マシン上に VMware PowerCLI 6.5 PowerShell モジュールがインストールされている必要があります。
    - Collector がモジュールをインストールするために必要な URL にアクセスできる場合、インストールは Collector のデプロイ中に自動的に実行されます。
    - Collector がデプロイ中にモジュールをインストールできない場合は、[手動でインストール](#install-vwware-powercli-module-manually)する必要があります。
- **vCenter Server への接続を確認する**: Collector は、vCenter Server に接続して、仮想マシン、そのメタデータ、およびパフォーマンス カウンターのクエリを実行できなければなりません。 接続の[前提条件を確認](#connect-to-vcenter-server)します。


### <a name="connect-to-the-internet-via-a-proxy"></a>プロキシ経由でのインターネットへの接続

- プロキシ サーバーで認証が必要な場合は、Collector を設定するときにユーザー名とパスワードを指定できます。
- プロキシ サーバーの IP アドレスまたは FQDN は、*http://IPaddress* または *http://FQDN* と指定する必要があります。
- サポートされるのは HTTP プロキシのみです。 HTTPS ベースのプロキシ サーバーは、Collector ではサポートされていません。
- プロキシ サーバーがインターセプト プロキシである場合は、Collector 仮想マシンにプロキシ証明書をインポートする必要があります。
    1. Collector 仮想マシンで、**[スタート メニュー]** > **[コンピューター証明書の管理]** に移動します。
    2. 証明書ツールの **[証明書 - ローカル コンピューター]** で、**[信頼された発行元]** > **[証明書]** を探します。

        ![証明書ツール](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. Collector 仮想マシンにプロキシ証明書をコピーします。 証明書はネットワーク管理者から取得することが必要になる場合があります。
    4. 証明書をダブルクリックして開き、**[証明書のインストール]** をクリックします。
    5. 証明書インポート ウィザードで、[ストアの場所] に移動して、**[ローカル マシン]** を選択します。

    ![証明書ストアの場所](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. **[証明書をすべて次のストアに配置する]** > **[参照]** > **[信頼された発行元]** を選択します。 **[完了]** をクリックして、証明書をインポートします。

    ![証明書ストア](./media/concepts-intercepting-proxy/certificate-store.png)

    7. 証明書が予期どおりインポートされていることを確認し、インターネット接続前提条件チェックが予期どおり機能していることを確認します。




### <a name="connect-to-urls"></a>URL への接続

接続チェックは、URL のリストに接続することによって検証されます。

**URL** | **詳細**  | **前提条件のチェック**
--- | --- | ---
*.portal.azure.com | Azure サービス、および時刻の同期との接続を確認します。 | URL へのアクセスが必要です。<br/><br/> 接続がないと、前提条件チェックは失敗します。
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| PowerShell vCenter PowerCLI モジュールをダウンロードするために使用されます。 | URL へのアクセスは省略可能です。<br/><br/> 前提条件チェックは失敗しません。<br/><br/> Collector 仮想マシンで自動のモジュールのインストールは失敗します。 モジュールを手動でインストールする必要があります。


### <a name="install-vmware-powercli-module-manually"></a>VMware PowerCLI モジュールの手動でのインストール

1. このモジュールは[次の手順](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)を使用してインストールします。 次の手順では、オンラインおよびオフラインの両方のインストールについて説明します。
2. Collector 仮想マシンがオフラインであり、インターネットにアクセスできる別のコンピューターにモジュールをインストールする場合は、そのコンピューターから Collector 仮想マシンに VMware.* ファイルをコピーする必要があります。
3. インストール後に、PowerCLI がインストールされていることを確認するために、前提条件チェックを再起動することができます。

### <a name="connect-to-vcenter-server"></a>vCenter Server への接続

Collector は、vCenter Server に接続し、仮想マシンのメタデータとパフォーマンス カウンターのクエリを実行します。 この接続で必要なものを次に示します。

- vCenter Server のバージョン 5.5、6.0、および 6.5 のみがサポートされています。
- 検出用に、以下に要約されているアクセス許可を持つ読み取り専用のアカウントが必要です。 検出のためにアクセスできるのは、アカウントでアクセス可能なデータ センターのみです。
- 既定では、FQDN または IP アドレスで vCenter Server に接続します。 vCenter Server が別のポートでリッスンしている場合は、フォーム *IPAddress:Port_Number* または *FQDN:Port_Number* を使用してそれに接続します。
- ストレージやネットワークのパフォーマンス データを収集するには、vCenter Server の統計設定を 3 つのレベルに設定する必要があります。
- レベルが 3 つよりも少ない場合、検出は機能しますがパフォーマンス データは収集されません。 一部のカウンターは収集される可能性がありますが、その他は 0 に設定されます。
- ストレージとネットワークのパフォーマンス データが収集されない場合、推奨評価サイズは、CPU およびメモリのパフォーマンス データと、ディスクおよびネットワーク アダプターの構成データに基づいて計算されます。
- コレクターには、vCenter サーバーにつながるネットワーク接続が必要です。

#### <a name="account-permissions"></a>アカウントのアクセス許可

**アカウント** | **アクセス許可**
--- | ---
読み取り専用以上のユーザー アカウント | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用   


## <a name="collector-communications"></a>Collector の通信

Collector は、次の図と表にまとめたように通信します。

![コレクターの通信ダイアグラム](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Collector の通信先** | **ポート** | **詳細**
--- | --- | ---
Azure Migrate サービス | TCP 443 | Collector は、SSL 443 経由で Azure Migrate サービスと通信します。
vCenter Server | TCP 443 | Collector は、vCenter Server と通信できる必要があります。<br/><br/> 既定では、443 経由で vCenter に接続します。<br/><br/> vCenter Server が別のポートをリッスンしている場合は、そのポートが Collector の送信ポートとして使用可能である必要があります。
RDP | TCP 3389 |


## <a name="securing-the-collector-appliance"></a>Collector アプライアンスのセキュリティ保護

次の手順に従って Collector アプライアンスをセキュリティで保護することをお勧めします

- 管理者パスワードは、承認されていないパーティとの間で、共有したり誤って付与したりしないでください。
- アプライアンスは、未使用時にはシャットダウンします。
- アプライアンスはセキュリティで保護されたネットワークに配置します。
- 移行が完了したら、アプライアンス インスタンスを削除します。
- さらに、移行後には、ディスクにはキャッシュされた vCenter 資格情報が存在する可能性があるため、ディスク バックアップ ファイル (VMDK) も必ず削除します。

## <a name="os-license-in-the-collector-vm"></a>Collector 仮想マシンの OS ライセンス

Collector には、180 日間有効な Windows Server 2012 R2 評価版ライセンスが付属します。 Collector 仮想マシンの評価期間が期限切れになった場合は、新しい OVA をダウンロードして、新しいアプライアンスを作成することをお勧めします。

## <a name="updating-the-os-of-the-collector-vm"></a>Collector 仮想マシンの OS の更新

Collector アプライアンスに 180 日間の評価版ライセンスがある場合でも、アプライアンスの自動シャットダウンを回避するために、アプライアンス上の OS は継続的に更新する必要があります。

- Collector が 60 日間更新されない場合には、マシンのシャットダウンが自動的に開始されます。
- 検出を実行中の場合は、60 日が経過しても、コンピューターはオフになりません。 検出ジョブが完了した後に、コンピューターはオフになります。
- 60 日が経過しても Collector を使用してきた場合には、Windows Update の実行によってマシンが常に更新され続けるようにすることをお勧めします。

## <a name="upgrading-the-collector-appliance-version"></a>Collector アプライアンス バージョンのアップグレード

OVA を再度ダウンロードせずに、Collector を最新バージョンにアップグレードできます。

1. [リストされている最新のアップグレード パッケージ](concepts-collector-upgrade.md)のダウンロード
2. ダウンロードした修正プログラムが安全であることを確認するために、管理者コマンド ウィンドウを開き、以下のコマンドを実行して、zip ファイルのハッシュを生成します。 生成されたハッシュは、その特定のバージョンのハッシュと一致する必要があります。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (たとえば、C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256 のように使用します)
3. zip ファイルを Azure Migrate コレクター仮想マシン (コレクター アプライアンス) にコピーします。
4. zip ファイルを右クリックし、[すべて展開] を選択します。
5. Setup.ps1 を右クリックして [Run with PowerShell]\(PowerShell で実行\) を選択し、画面に指示に従って更新プログラムをインストールします。


## <a name="discovery-methods"></a>検出方法

Collector アプライアンスが検出に使用できる 2 つの方法として、1 回限りの検出と、継続的な検出があります。


### <a name="one-time-discovery"></a>1 回限りの検出

Collector は仮想マシンについてのメタデータを収集するために vCenter Server と 1 回限りの通信をします。 このメソッドは次の場合に使用します。

- アプライアンスが Azure Migrate プロジェクトに継続的に接続されていません。
- 検出の終了後に、Azure Migrate でオンプレミス環境内の変更が反映されません。 すべての変更を反映するには、同じプロジェクト内の同じ環境をもう一度検出する必要があります。
- この検出方法では、vCenter Server で統計設定をレベル 3 に設定する必要があります。
- レベルを 3 に設定したら、パフォーマンス カウンターを生成するために最長で 1 日かかります。 そのため、1 日経過してから検出を実行することをお勧めします。
- 仮想マシンのパフォーマンス データを収集するときに、アプライアンスは、vCenter Server に格納されている履歴パフォーマンス データに依存します。 それは過去の 1 か月のパフォーマンスの履歴を収集しています。
- Azure Migrate は各メトリックの (ピーク カウンターではなく) 平均カウンターを収集します。

### <a name="continuous-discovery"></a>継続的な検出

Collector アプライアンスが Azure Migrate プロジェクトに継続的に接続されていません。

- Collector は、20 秒ごとにリアルタイムの使用状況データを収集するために、オンプレミス環境をプロファイルします。
- このモデルは、パフォーマンス データを収集するために vCenter Server の統計設定に依存していません。
- アプライアンスは、20 秒のサンプルをロールアップし、15 分ごとに 1 つのデータ ポイントを作成します。
- データ ポイントを作成するために、アプライアンスは 20 秒のサンプルからピーク値を選択し、それを Azure に送信します。
- 継続的なプロファイルは、Collector からいつでも停止できます。

> [!NOTE]
> 継続的な検出の機能は、プレビュー段階にあります。 vCenter Server の統計設定がレベル 3 にセットされていない場合は、このメソッドを使用することをお勧めします。


## <a name="discovery-process"></a>検出プロセス

アプライアンスを設定した後は、検出を実行することができます。 そのしくみは次のとおりです。

- スコープによって検出を実行するとします。 指定した vCenter インベントリ パス内のすべての仮想マシンが検出されます。
    - 一度に 1 つのスコープを設定します。
    - スコープには仮想マシンを最大で 1,500 まで含めることができます。
    - スコープには、データ センター、フォルダー、または ESXi ホストのいずれかを指定できます。
- vCenter Server に接続したら、コレクションの移行プロジェクトを指定して接続します。
- 仮想マシンが検出され、そのメタデータとパフォーマンス データが Azure に送信されます。 これらのアクションは、コレクション ジョブの一部です。
    - Collector アプライアンスには、特定の Collector ID が付与されます。これは特定のマシンでの複数の検出で永続的に有効です。
    - 実行中のコレクション ジョブには特定のセッション ID が付与されます。 この ID はコレクション ジョブごとに変更され、トラブルシューティングに使用することができます。

### <a name="collected-metadata"></a>収集されたメタデータ

Collector アプライアンスでは、仮想マシンの次の静的メタデータを検出します。

- (vCenter Server 上の) 仮想マシンの表示名
- 仮想マシンのインベントリ パス (vCenter Server 上のホストまたはフォルダー)
- IP アドレス
- MAC アドレス
- オペレーティング システム
- コア、ディスク、NIC の数
- メモリ サイズ、ディスク サイズ
- 仮想マシン、ディスク、およびネットワークのパフォーマンス カウンター。

#### <a name="performance-counters"></a>パフォーマンス カウンター

- **1 回限りの検出**: カウンターが 1 回限りの検出で収集される場合には、次のことに注意してください。

    - 構成メタデータを収集してプロジェクトに送信するまでに最大 15 分かかることがあります。
    - 構成データの収集後には、パフォーマンス データがポータルで使用できるようになるまでに 1 時間かかることがあります。
    - メタデータがポータルで使用できるようになったら、仮想マシンのリストが表示され、評価のためのグループの作成を開始することができます。
- **継続的な検出**: 継続的な検出の場合は、次のことに注意してください。
    - 仮想マシンの構成データは、検出を開始してから 1 時間後に使用可能になります。
    - パフォーマンス データは、2 時間後に使用可能になります。
    - 検出を開始した後は、評価を作成する前に、アプライアンスが環境をプロファイルするのを少なくとも 1 日待機します。

**カウンター** | **Level** | **デバイスごとのレベル** | **評価への影響**
--- | --- | --- | ---
cpu.usage.average | 1 | 該当なし | 推奨される VM サイズとコスト  
mem.usage.average | 1 | 該当なし | 推奨される VM サイズとコスト  
virtualDisk.read.average | 2 | 2 | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.write.average | 2 | 2  | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.numberReadAveraged.average | 1 | 3 |  ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.numberWriteAveraged.average | 1 | 3 |   ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
net.received.average | 2 | 3 |  仮想マシン サイズおよびネットワーク コストを計算します                        |
net.transmitted.average | 2 | 3 | 仮想マシン サイズおよびネットワーク コストを計算します    

## <a name="next-steps"></a>次の手順

[オンプレミスの VMware VM のアセスメントを設定する](tutorial-assessment-vmware.md)
