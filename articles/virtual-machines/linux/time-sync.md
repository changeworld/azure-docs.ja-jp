---
title: Azure での Linux VM の時刻同期
description: Linux 仮想マシンの時刻同期。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/03/2021
ms.author: cynthn
ms.openlocfilehash: 324373fc56ae1a57adfb522ca77f06f2c080074c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776521"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure での Linux VM の時刻同期

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット 

時刻同期は、セキュリティとイベントの関連付けで重要になります。 場合によっては、分散トランザクションの実装で使用されます。 複数のコンピューター システム間での時刻の精度は、同期によって実現されます。 同期は、再起動や、時刻をフェッチするコンピューターとタイム ソースとの間のネットワーク トラフィックなど、複数の要因に影響を受ける可能性があります。 

Azure は、Windows Server 2016 を実行しているインフラストラクチャによってサポートされます。 Windows Server 2016 では、時刻を修正し、ローカル クロックを調整して UTC と同期するために使用されるアルゴリズムが改善されています。  Windows Server 2016 の正確な時刻機能では、正確な時刻を確保するためにホストで VM を制御する VMICTimeSync サービスの動作が大幅に改善されています。 この改善には、VM の開始時や VM の復元時の初期時刻の精度の向上と、割り込み待ち時間の修正が含まれます。 

> [!NOTE]
> Windows Time サービスの概要については、こちらの[概要ビデオ](https://aka.ms/WS2016TimeVideo)をご覧ください。
>
> 詳細については、「[Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)」を参照してください。 

## <a name="overview"></a>概要

コンピューター クロックの精度は、そのコンピューター クロックと協定世界時 (UTC) 標準時間との誤差で測定されます。 UTC は、300 年に 1 秒しか誤差が生じない正確な原子時計の世界各国のサンプルによって定義されます。 しかし、UTC を直接読み取るには特殊なハードウェアが必要です。 代わりに、タイム サーバーが UTC と同期され、拡張性と堅牢性を提供するために他のコンピューターからアクセスされます。 すべてのコンピューターで時刻同期サービスが実行されており、サーバーの使用時刻が認識され、コンピューター クロックを修正する必要があるかどうかが定期的に確認され、必要に応じて調整されます。 

Azure ホストは内部の Microsoft タイム サーバーに同期されています。このサーバーでは、GPS アンテナを使用して、Microsoft が所有する Stratum 1 デバイスから時刻を取得します。 Azure の仮想マシンでは、そのホストに依存して正確な時刻 (*ホスト時刻*) を VM に渡すことも、VM でタイム サーバーから直接時刻を取得することも、あるいはこれらの両方を組み合わることもできます。 

スタンドアロン ハードウェアでは、Linux OS によって、起動時にホスト ハードウェア クロックのみが読み取られます。 その後、クロックは、Linux カーネルの割り込みタイマーを使用して維持されます。 この構成では、クロックは時間の経過と共に誤差が生じます。 Azure の新しい Linux ディストリビューションでは、VM で VMICTimeSync プロバイダーを使用できます。このプロバイダーは LIS (Linux Integration Services) に含まれており、ホストからより頻繁にクロック更新についてクエリを実行するためのものです。

仮想マシンとホストとのやりとりがクロックに影響する場合もあります。 [メモリ保持メンテナンス](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)中に、VM は最大で 30 秒間一時停止します。 たとえば、メンテナンスの開始前に、VM クロックには午前 10 時 00 分 00 秒と示され、この状態が 28 秒間続きます。 VM の再開後、VM のクロックはまだ午前 10 時 00 分 00 秒と示されており、28 秒遅れていることになります。 これを修正するため、VMICTimeSync サービスでは、ホストで何が発生しているのかを監視し、補正する Linux VM の時刻機構を更新します。

時刻同期が動作しないと、VM のクロックでエラーが蓄積されます。 VM が 1 つのみであれば、ワークロードで高精度のタイムキーピングが必要でない限り、影響は大きくならない場合があります。 しかし、ほとんどの場合、相互接続された VM が複数あり、それらの VM ではトランザクションを追跡するために時間を使用し、その時間はデプロイ全体で一貫している必要があります。 VM 間の時間が異なると、次のような影響が見られる場合があります。

- 認証は失敗します。 Kerberos または証明書に依存するテクノロジのようなセキュリティ プロトコルでは、システム全体で時間が一貫していることが前提となります。
- ログ (またはその他のデータ) が時間と一致しない場合、システムで何が発生したかを把握するのは非常に困難です。 異なる時間に同じイベントが発生したように見え、関連付けが困難になります。
- クロックに誤差がある場合、請求額を正しく計算できません。


## <a name="configuration-options"></a>構成オプション

時刻の同期では、Linux VM で時刻同期サービスを実行することと、同期する正確な時刻情報のソースが必要です。
通常、ntpd または chronyd は時刻同期サービスとして使用されます。しかし、他にも使用できるオープン ソースの時刻同期サービスがあります。
正確な時刻情報のソースは、Azure ホストまたはパブリック インターネットを使用してアクセスされる外部タイム サービスです。
VMICTimeSync サービス自体は、前述のようにホストメンテナンスのために一時停止した後を除き、Azure ホストと Linux VM の間で継続的な時刻同期を提供しません。 

従来、Linux を使用する Azure Marketplace のほとんどのイメージは、次の 2 つの方法のいずれかを使用して構成されています。
- 既定では、時刻同期サービスは実行されません
- ntpd は時刻同期サービスとして実行され、ネットワークを通してアクセスされる外部 NTP タイム ソースに対して同期しています。 たとえば、Ubuntu 18.04 LTS の Marketplace イメージでは、**ntp.ubuntu.com** が使用されます。

ntpd が正しく同期していることを確認するには、`ntpq -p` コマンドを実行します。

2021 暦年初頭から、Linux による最新の Azure Marketplace イメージは時刻同期サービスとして chronyd を使用するように変更され、chronyd は外部 NTP タイム ソースではなく Azure ホストと同期するように構成されています。 Azure ホスト時間は、通常、非常に正確かつ確実に維持され、パブリック インターネットを使用した外部 NTP タイム ソースへのアクセスに固有の可変ネットワーク遅延なしでアクセス可能であるため、同期に最適なタイム ソースです。

VMICTimeSync は並列で使用され、次の 2 つの関数を提供します。
- ホスト メンテナンス イベントの後、Linux VM の時刻機構を直ちに更新します
- IEEE 1588 Precision Time Protocol (PTP) ハードウェア クロック ソースを、Azure ホストから正確な時刻を提供する /dev/ptp デバイスとしてインスタンス化します。  Chronyd は、このタイム ソース (最新の Linux イメージの既定の構成) と同期するように構成できます。 カーネル バージョン 4.11 以降 (または RHEL/CentOS 7 の場合はバージョン 3.10.0-693 以降) の Linux ディストリビューションでは、/dev/ptp デバイスがサポートされます。  Azure ホスト時間に対して、/dev/ptp をサポートしない以前のバージョンのカーネルでは、外部タイムソースに対してのみ同期を実行できます。

もちろん、既定の構成は変更できます。 ntpd と外部タイム ソースを使用するように構成されている古いイメージは、Azure ホスト時間に対しては、chronyd と /dev/ptp デバイスを使用するように変更できます。  同様に、アプリケーションまたはワークロードで必要になった場合に、外部の NTP タイム ソースを使用するように構成することもできます。


## <a name="tools-and-resources"></a>ツールとリソース

時刻同期構成を確認するための基本的なコマンドがいくつかあります。 Linux ディストリビューションのドキュメントには、そのディストリビューション用の時刻同期を構成する最適な方法の詳細が示されます。

### <a name="integration-services"></a>統合サービス

統合サービス (hv_utils) が読み込まれているかどうかを確認します。

```bash
lsmod | grep hv_utils
```
次のように表示されます。

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### <a name="check-for-ptp-clock-source"></a>PTP クロック ソースを確認する

新しいバージョンの Linux では、Azure ホストに対応する PTP (Precision Time Protocol) クロック ソースを VMICTimeSync プロバイダーの一部として利用できます。
以前のバージョンの Red Hat Enterprise Linux や CentOS 7.x では、[Linux Integration Services](https://github.com/LIS/lis-next) をダウンロードし、それを使用して更新されたドライバーをインポートできます。 PTP クロック ソースを使用できる場合、Linux デバイスの形式は /dev/ptp *x* になります。 

どの PTP クロック ソースを使用できるかを確認します。

```bash
ls /sys/class/ptp
```

この例では、返される値は *ptp0* であるため、これを使用してクロック名を確認します。 デバイスを確認するには、クロック名を調べます。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

これにより `hyperv` 、つまり Azure ホストが返されます。

高速ネットワークが有効になっている Linux VM では、Mellanox mlx5 ドライバーによっても /dev/ptp デバイスが作成されるため、複数の PTP デバイスが一覧表示されることがあります。
初期化の順序は Linux が起動するたびに異なる可能性があるため、Azure ホストに対応する PTP デバイスは、 /dev/ptp0 または /dev/ptp1 になることがあります。これにより、正しいクロックソースで chronyd を構成するのが困難になります。 この問題を解決するために、最新の Linux イメージには、/dev/ptp エントリが Azure ホストに対応する場合は常に /dev/ptp_hyperv シンボリックリンクを作成すという udev ルールがあります。 Chrony では、/dev/ptp0 または /dev/ptp1 の代わりにこのシンボリックリンクを使用するように構成する必要があります。

### <a name="chrony"></a>chrony

Ubuntu 19.10 以降のバージョン、Red Hat Enterprise Linux、および CentOS 8.x では、PTP ソース クロックを使用するように [chrony](https://chrony.tuxfamily.org/) が構成されています。 以前の Linux リリースでは、chrony ではなく、PTP ソースがサポートされない ntpd (Network Time Protocol Daemon) が使用されています。 これらのリリースで PTP を有効にするには、chrony.conf で次のステートメントを使用して chrony を手動でインストールして構成する必要があります。

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0 stratum 2
```

/dev/ptp_hyperv シンボリックリンクが使用可能な場合は、/dev/ptp0 の代わりに使用して、Mellanox mlx5 ドライバーによって作成された /dev/ptp デバイスとの混同を避けてください。

階層情報は、Azure ホストから Linux ゲストに自動的には伝達されません。 前の構成行では、Azure ホスト タイム ソースが階層 2 として扱われることを指定しています。これで、Linux ゲストではそれ自体を階層 3 として報告します。 Linux ゲストでそれ自体を異なる方法で報告するように必要がある場合は、構成行の階層設定を変更できます。

既定では、chronyd は、時間の誤差を修正するために、システム クロックを加速または減速します。 誤差が大きすぎる場合、chrony は誤差の修正に失敗します。 これを解決するために、 **/etc/chrony.conf** の `makestep` パラメーターを変更して、誤差が、指定されたしきい値を超えた場合に時刻同期を強制的に適用することができます。

 ```bash
makestep 1.0 -1
```

ここでは、chrony は、誤差が 1 秒よりも大きい場合に時間の更新を強制します。 変更を適用するには、chronyd サービスを再起動します。

```bash
systemctl restart chronyd
```

Ubuntu および NTP の詳細については、[時刻同期](https://ubuntu.com/server/docs/network-ntp)に関するページを参照してください。

Red Hat および NTP の詳細については、[NTP の構成](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)に関するページを参照してください。 

chrony の詳細については、[chrony の使用](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)に関するページを参照してください。

### <a name="systemd"></a>systemd 

19.10 より前の SUSE と Ubuntu では、時刻同期は [systemd](https://www.freedesktop.org/wiki/Software/systemd/) を使用して構成されます。 Ubuntu の詳細については、[時刻同期](https://help.ubuntu.com/lts/serverguide/NTP.html)に関するページを参照してください。 SUSE の詳細については、[SUSE Linux Enterprise Server 12 SP3 に関するリリース ノート](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)の第 4.5.8 条を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、「[Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)」を参照してください。


