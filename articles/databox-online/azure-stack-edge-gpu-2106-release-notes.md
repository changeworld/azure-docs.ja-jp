---
title: Azure Stack Edge 2106 リリース ノート
description: 2106 リリースを実行している Azure Stack Edge の重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2021
ms.author: alkohli
ms.openlocfilehash: f536ac07a15b0b17405cf83233b2fbfba4193d25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714081"
---
# <a name="azure-stack-edge-2106-release-notes"></a>Azure Stack Edge 2106 リリース ノート

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

下記のリリース ノートでは、お使いの Azure Stack Edge デバイスの 2106 リリースの、重大な未解決の問題と解決された問題について説明します。 このリリース ノートは、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R デバイスを対象としています。 機能および問題が特定のモデルのものである場合は、常にそれを明記しています。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 ご自分のデバイスを配置する前に、リリース ノートに含まれている情報を入念にご確認ください。

この記事は、ソフトウェア バージョン番号 **2.2.1636.3457** にマップされる **Azure Stack Edge 2106** リリースに適用されます。 このソフトウェアは、少なくとも Azure Stack Edge 2010 (2.1.1377.2170) ソフトウェアを実行している場合に、お使いのデバイスに適用できます。

## <a name="whats-new"></a>新機能

Azure Stack Edge 2106 リリースの新機能は以下のとおりです。 

- **Windows 更新プログラムとセキュリティ修正プログラム** - [Windows の最新の累積的な更新プログラム (LCU) と 6 月のセキュリティ修正プログラム](https://support.microsoft.com/en-us/topic/june-8-2021-kb5003697-monthly-rollup-457aa997-18a0-46e9-8612-497f01ccaa54)が、Azure Stack Edge の更新プログラム パッケージにまとめられました。
- **Azure プライベート マルチアクセス エッジ コンピューティングのバグ修正** - Azure プライベート MEC デプロイで複数の問題が修正されました。

    - リンク フラッピング、ブート ログのエラー、再起動など、ゲスト VM の正常性の監視に関連する問題。
    - 時間の経過に伴うメモリ リソース消費量。
    - Mellanox ドライバー、ファームウェア、およびツール。
    - VM 関連の問題とネットワークの正常性チェックをデバッグするためのツール。
    - シングル ルート I/O 仮想化 (SR-IOV) VM の送信パケット、または LAN/WAN VM NetAdapters からのトラフィックがドロップされる原因となった問題。
- **ログ収集の機能強化** - このリリースでは、Azure Stack Edge 更新シナリオに関連するログ収集が改善されています。



## <a name="issues-fixed-in-2106-release"></a>2106 リリースで修正された問題

次の表に、以前のリリースでリリース ノートに記載され、現在のリリースでは修正された問題を示します。

| いいえ。 | 特徴量 | 問題 | 
| --- | --- | --- |
|**1.**|Azure プライベート MEC |VM ネット アダプター リンク状態が、起動時に定期的に不安定になります。|
|**2.**|Azure プライベート MEC  |VFtoPF DHCP リダイレクト フラグを Mellanox ネットワーク インターフェイスで使用すると、パケットがドロップされる可能性があります。|
|**3.**|Azure プライベート MEC  |Mellanox ネットワーク インターフェイス ドライバー、ファームウェア、およびツールをバージョン 2.60 にアップグレードする必要があります。|
|**4.**|VM |デバイスの PowerShell インターフェイス経由で接続するときに VM ゲスト ログのコレクションに使用できるコマンドレット `Get-VMInguestLogs` は失敗します。|
|**5.**|Azure プライベート MEC  |Web プロキシが構成されている場合、Web プロキシ バイパスの設定のために VM のプロビジョニングが失敗します。 |
|**6.**|Azure プライベート MEC |2105 更新プログラムより前の MEC/NFM デプロイでは、LAN/WAN VM NetAdapters からのトラフィックがドロップされるという問題がまれに発生する可能性があります。 2106 では、この問題は、VM が 2105 リリースの前に作成されたか 2105 リリースの後に作成されたかに関係なく、VM LAN/WAN ネットワーク インターフェイスで enableIPForwarding を true に設定することで解決します。   |
|**7.**|Azure プライベート MEC  |シングル ルート I/O 仮想化 (SR-IOV) VM の送信パケットは、Mellanox ドライバー、SR-IOV 仮想機能 (VF)、vftopfDHCPRedirect 機能の組み合わせが使用されると、Mellanox ネットワーク インターフェイス (デバイスのポート 5 およびポート 6) によってドロップされる可能性があります。 2106 では、この問題は vftopfDHCPRedirect 機能を無効にすることで解決します。  |


## <a name="known-issues-in-2106-release"></a>2106 リリースの既知の問題

次の表に、2106 リリースの既知の問題の概要を示します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
|**1.**|プレビュー機能 |このリリースでは、ローカル Azure Resource Manager、VM、VM のクラウド管理、Kubernetes クラウド管理、Azure Arc 対応 Kubernetes、Azure Stack Edge Pro R および Azure Stack Edge Mini R 用の VPN、マルチプロセス サービス (MPS)、および Azure Stack Edge Pro GPU 用のマルチアクセス エッジ コンピューティング (MEC) の全機能をプレビューとしてご利用になれます。  |これらの機能は、今後のリリースで一般公開される予定です。 |


## <a name="known-issues-from-previous-releases"></a>以前のリリースの既知の問題

次の表に、前のリリースから持ち越されている既知の問題の概要を示します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL Database を作成するには、管理者アクセス権が必要です。   |「[SQL データベースの作成](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database)」の手順 1 から　2 の代わりに、次の手順を実行します。 <ul><li>デバイスのローカル UI で、コンピューティング インターフェイスを有効にします。 **[コンピューティング] > [ポート番号] > [Enable for compute]\(コンピューティングを有効にする\) > [適用]** を選択します。</li><li>[SQL コマンド ユーティリティ](/sql/tools/sqlcmd-utility)からクライアント マシンに `sqlcmd` をダウンロードします。 </li><li>コンピューティング インターフェイスの IP アドレス (有効なポート) に接続し、アドレスの末尾に ",1401" を追加します。</li><li>最終的なコマンドは次のようになります。sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd"</li>この後、現在のドキュメントの手順 3 から 4 は同じにする必要があります。 </li></ul> |
| **2.** |更新| **[更新]** によって復元された BLOB の増分変更はサポートされていません |BLOB エンドポイントの場合、[更新] 後に BLOB を部分的に更新すると、更新がクラウドにアップロードされない可能性があります。 たとえば、次のような一連のアクションがあります。<ul><li>クラウドに BLOB を作成します。 または、以前にアップロードした BLOB をデバイスから削除します。</li><li>更新機能を使用して、クラウドからアプライアンスに BLOB を更新します。</li><li>Azure SDK REST API を使用して、BLOB の一部のみを更新します。</li></ul>これらのアクションの結果、BLOB の更新されたセクションがクラウドで更新されない可能性があります。 <br>**回避策**:robocopy などのツール、またはエクスプローラーやコマンド ラインを介した通常のファイル コピーを使用して、BLOB 全体を置き換えます。|
|**3.**|Throttling|調整中に、デバイスへの新しい書き込みが許可されていない場合、NFS クライアントによる書き込みは "アクセス許可が拒否されました" エラーで失敗します。| 次のようなエラーが表示されます。<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: cannot create directory 'test': (mkdir: ディレクトリ 'test' を作成できません:)アクセス許可は拒否されました|
|**4.**|Blob Storage のインジェスト|Blob storage のインジェストに AzCopy バージョン 10 を使用する場合は、次の引数を指定して AzCopy を実行します。`Azcopy <other arguments> --cap-mbps 2000`| AzCopy にこれらの制限を指定しない場合、デバイスに大量の要求が送信され、サービスに問題が発生する可能性があります。|
|**5.**|階層型ストレージ アカウント|階層型ストレージ アカウントを使用する場合は、以下が適用されます。<ul><li> ブロック BLOB のみがサポートされています。 ページ BLOB はサポートされていません。</li><li>スナップショットまたはコピー API はサポートされていません。</li><li> `distcp` を使用した Hadoop ワークロードの取り込みは、コピー操作が頻繁に使用されるため、サポートされていません。</li></ul>||
|**6.**|NFS 共有接続|複数のプロセスによって同じ共有に対してコピーが実行され、`nolock` 属性が使用されていない場合、コピー中にエラーが発生する可能性があります。|ファイルを NFS 共有にコピーするには、`nolock` 属性を mount コマンドに渡す必要があります。 たとえば、 `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`と指定します。|
|**7.**|Kubernetes クラスター|Kubernetes クラスターを実行しているデバイスに更新プログラムを適用すると、Kubernetes 仮想マシンが再起動し、リブートされます。 このインスタンスでは、レプリカを指定して展開されたポッドのみが、更新後に自動的に復元されます。  |レプリケーション コントローラーの外部でレプリカ セットを指定せずに個別のポッドを作成した場合、これらのポッドはデバイスの更新後に自動的に復元されません。 これらのポッドを復元する必要があります。<br>レプリカ セットによって、ノードの障害や中断を伴うノードのアップグレードなど、何らかの理由で削除または中止されたポッドが置き換えられます。 このため、アプリケーションに必要なポッドが 1 つだけの場合でも、レプリカ セットを使用することをお勧めします。|
|**8.**|Kubernetes クラスター|Azure Stack Edge Pro 上の Kubernetes は、Helm v3 以降でのみサポートされています。 詳細については、「[Frequently asked questions: Removal of Tiller (よく寄せられる質問: Tiller の削除)](https://v3.helm.sh/docs/faq/)」を参照してください。|
|**9.**|Azure Arc 対応 Kubernetes |GA リリースでは、Azure Arc 対応 Kubernetes がバージョン 0.1.18 から 0.2.9 に更新されます。 Azure Stack Edge デバイスでは Azure Arc 対応 Kubernetes の更新がサポートされていないため、Azure Arc 対応 Kubernetes を再デプロイする必要があります。|次の手順のようにします。<ol><li>[デバイス ソフトウェアと Kubernetes 更新プログラムを適用します](azure-stack-edge-gpu-install-update.md)。</li><li>デバイスの [PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md)。</li><li>既存の Azure Arc エージェントを削除します。 次のコマンドを入力します: `Remove-HcsKubernetesAzureArcAgent`</li><li>[Azure Arc を新しいリソースに](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)デプロイします。 既存の Azure Arc リソースを使用しないでください。</li></ol>|
|**10.**|Azure Arc 対応 Kubernetes|Azure Stack Edge Pro デバイスで Web プロキシが構成されている場合、Azure Arc のデプロイはサポートされません。||
|**11.**|Kubernetes |ポート 31000 は Kubernetes ダッシュボード用に予約されています。 ポート 31001 は、Edge コンテナー レジストリ用に予約されています。 同様に、既定の構成では、IP アドレス 172.28.0.1 と 172.28.0.10 は、それぞれ Kubernetes サービスと Core DNS サービス用に予約されています。|予約済みの IP は使用しないでください。|
|**12.**|Kubernetes |現在、Kubernetes ではマルチプロトコルの LoadBalancer サービスは許可されていません。 たとえば、TCP と UDP の両方をリッスンする必要がある DNS サービスです。 |MetalLB を使用して Kubernetes のこの制限を回避するには、同じポッド セレクター用に 2 つのサービス (TCP 用と UDP 用) を作成します。 これらのサービスでは、同じ共有キーと spec.loadBalancerIP を使用して、同じ IP アドレスを共有します。 使用可能な IP アドレスよりも多くのサービスがある場合は、IP を共有することもできます。 <br> 詳細については、「[IP address sharing (IP アドレスの共有)](https://metallb.universe.tf/usage/#ip-address-sharing)」を参照してください。|
|**13.**|Kubernetes クラスター|既存の Azure IoT Edge マーケットプレース モジュールは、Azure Stack Edge デバイス上の IoT Edge で実行するための変更が必要になる場合があります。|詳細については、Azure Stack Edge デバイス上で実行するようにマーケットプレースの Azure IoT Edge モジュールを変更する方法に関するページを参照してください。<!-- insert link-->|
|**14.**|Kubernetes |ファイルベースのバインド マウントは、Azure Stack Edge デバイス上の Kubernetes の Azure IoT Edge ではサポートされていません。|IoT Edge では、変換レイヤーを使用して、`ContainerCreate` オプションを Kubernetes コンストラクトに変換します。 `Binds` を作成すると、`hostpath` ディレクトリにマップされます。そのため、ファイルベースのバインド マウントを IoT Edge コンテナーのパスにバインドすることはできません。 可能であれば、親ディレクトリをマップします。|
|**15.**|Kubernetes |IoT Edge 用の独自の証明書を持ち込み、コンピューティングが構成された後の Azure Stack Edge デバイスにそれらの証明書を追加した場合、新しい証明書は取得されません。|この問題を回避するには、デバイスでコンピューティングを構成する前に、証明書をアップロードする必要があります。 コンピューティングが既に構成されている場合は、[デバイスの PowerShell インターフェイスに接続し、IoT Edge コマンドを実行 ます](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)。 `iotedged` および `edgehub` ポッドを再起動します。|
|**16.**|証明書 |場合によっては、ローカル UI の証明書の状態が更新されるまでに数秒かかることがあります。 |ローカル UI の次のシナリオが影響を受ける可能性があります。<ul><li>**[証明書]** ページの **[状態]** 列。</li><li>**[作業の開始]** ページの **[セキュリティ]** タイル。</li><li>**[概要]** ページの **[構成]** タイル。</li></ul>  |
|**17.**|IoT Edge |IoT Edge によってデプロイされたモジュールは、ホスト ネットワークを使用できません。 | |
|**18.**|コンピューティング + Kubernetes |コンピューティング/Kubernetes は NTLM Web プロキシをサポートしていません。 ||
|**19.**|Kubernetes と更新 |2008 リリースなどの以前のソフトウェア バージョンでは、更新が ClusterConnectionException で失敗する競合状態の更新の問題があります。 |この問題は、新しいビルドを使用すると回避できます。 この問題が解決しない場合は、アップグレードを再試行することで対応できます。|
|**20**|Internet Explorer|強化されたセキュリティ機能が有効になっている場合は、ローカル Web UI ページにアクセスできない可能性があります。 | 強化されたセキュリティを無効にし、ブラウザーを再起動してください。|
|**21.**|Kubernetes ダッシュボード | Kubernetes ダッシュボードで SSL 証明書を使用する *Https* エンドポイントはサポートされていません。 | |
|**22.**|Kubernetes |Kubernetes は、.NET アプリケーションで使用される環境変数名の ":" をサポートしていません。 これは、イベント グリッド IoT Edge モジュールが Azure Stack Edge デバイスやその他のアプリケーションで機能するためにも必要です。 詳細については、[ASP.NET Core のドキュメント](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables)をご覧ください。|":" は二重のアンダースコアに置き換えてください。 詳細については、[Kubernetes の問題](https://github.com/kubernetes/kubernetes/issues/53201)に関する記事をご覧ください。|
|**23.** |Azure Arc と Kubernetes クラスター |Git リポジトリからリソース `yamls` を削除した場合、既定で Kubernetes クラスターから対応するリソースは削除されません。  |Git リポジトリからリソースを削除したときに、それらが削除されるようにするには、Arc OperatorParams に `--sync-garbage-collection` を設定します。 詳細については、「[構成を削除する](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)」をご覧ください。 |
|**24.**|NFS |お使いのデバイスの NFS 共有マウントを使用してデータを書き込むアプリケーションでは、排他的な書き込みを使用する必要があります。 これにより、書き込みがディスクに書き込まれるようになります。| |
|**25.**|コンピューティングの構成 |ネットワーク上に存在しないシステムに対するアドレス解決プロトコル (ARP) 要求にゲートウェイまたはスイッチまたはルーターが応答するネットワーク構成では、コンピューティング構成が失敗します。| |
|**26.**|コンピューティングおよび Kubernetes |お使いのデバイスで Kubernetes を最初に設定した場合、使用可能なすべての GPU が要求されます。 そのため、Kubernetes を設定した後で、GPU を使用して Azure Resource Manager の仮想マシンは作成できません。 |お使いのデバイスに GPU が 2 つある場合は、GPU を使用する仮想マシンを 1 つ作成してから Kubernetes を構成します。 この場合、Kubernetes が残りの使用可能な 1 つの GPU を使用します。 |
|**27.**|カスタム スクリプト VM 拡張機能 |以前のリリースで作成された Windows VM には既知の問題があり、デバイスは 2103 に更新されました。 <br> これらの VM でカスタム スクリプト拡張機能を追加すると、Windows VM ゲスト エージェント (バージョン 2.7.41491.901 のみ) が更新でスタックし、拡張機能のデプロイがタイムアウトになります。 | この問題の回避方法: <ul><li> リモート デスクトップ プロトコル (RDP) を使用して Windows VM に接続します。 </li><li> コンピューターで `waappagent.exe` が実行中であることを確認し ます: `Get-Process WaAppAgent`。 </li><li> `waappagent.exe` が実行されていない場合は、`rdagent` サービスを再起動します: `Get-Service RdAgent` \| `Restart-Service`。 5 分間待機します。</li><li> `waappagent.exe` が実行されている間に `WindowsAzureGuest.exe` プロセスを中止します。 </li><li>プロセスを中止した後に、新しいバージョンを使用して再度プロセスの実行が開始されます。</li><li>次のコマンドを使用して、Windows VM ゲスト エージェントのバージョンが 2.7.41491.971 であることを確認します: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion`。</li><li>[Windows VM にカスタム スクリプト拡張機能を設定します。](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md) </li><ul> |
|**28.**|GPU VM |このリリースより前は、GPU VM のライフサイクルは更新フロー内で管理されていませんでした。 そのため、2103 リリースに更新すると、GPU VM は更新中に自動的に停止されません。 デバイスを更新する前に、`stop-stayProvisioned` フラグを使用して GPU VM を手動で停止する必要があります。 詳細については、「[VM を中断またはシャットダウンする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)」を参照してください。<br> 更新前に実行が続けられていたすべての GPU VM は、更新後に起動されます。 このような場合、VM 上で実行されているワークロードは正常に終了されません。 また、更新後に VM が望ましくない状態になる可能性があります。 <br>更新前に `stop-stayProvisioned` によって停止された GPU VM はすべて、更新後に自動的に起動されます。 <br>Azure portal から GPU VM を停止した場合は、デバイスの更新後に手動で VM を起動する必要があります。| Kubernetes を使用して GPU VM を実行している場合は、更新の直前に GPU VM を停止します。 <br>GPU VM が停止されると、もともと VM によって使用されていた GPU は Kubernetes によって引き継がれます。 <br>GPU VM が停止状態になっている時間が長いほど、GPU が Kubernetes によって引き継がれる可能性が高くなります。 |
|**29.**|マルチプロセス サービス (MPS) |デバイス ソフトウェアと Kubernetes クラスターが更新されても、ワークロードの MPS 設定は保持されません。   |[MPS を再度有効にして](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)、MPS を使用していたワークロードを再デプロイします。 |


## <a name="next-steps"></a>次の手順

- [デバイスの更新](azure-stack-edge-gpu-install-update.md)
