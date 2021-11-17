---
title: Azure Stack Edge 2110 リリース ノート
description: 2110 リリースを実行している Azure Stack Edge の重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 36c7e05e2cce3e93fcd35521582190f2d7cef2b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469163"
---
# <a name="azure-stack-edge-2110-release-notes"></a>Azure Stack Edge 2110 リリース ノート

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

下記のリリース ノートでは、お使いの Azure Stack Edge デバイスの 2110 リリースの、重大な未解決の問題と解決された問題について説明します。 このリリース ノートは、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R デバイスを対象としています。 機能および問題が特定のモデルのものである場合は、常にそれを明記しています。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 ご自分のデバイスを配置する前に、リリース ノートに含まれている情報を入念にご確認ください。

この記事は、ソフトウェア バージョン番号 **2.2.1758.4034** にマップされる **Azure Stack Edge 2110** リリースに適用されます。 このソフトウェアは、少なくとも Azure Stack Edge 2106 (2.2.1636.3457) ソフトウェアを実行している場合に、お使いのデバイスに適用できます。

## <a name="whats-new"></a>新機能

Azure Stack Edge 2110 リリースの新機能は以下のとおりです。 

- **Windows 更新プログラムとセキュリティ修正プログラム** - [Windows の最新の累積的な更新プログラム (LCU) と 9 月のセキュリティ修正プログラム](https://support.microsoft.com/topic/september-14-2021-kb5005623-monthly-rollup-bcdb6598-517e-4d53-aa7c-dd7fcfdca204)が、Azure Stack Edge の更新プログラム パッケージにまとめられました。
- **リモート サポート** - このリリースでは、お使いの Azure Stack Edge デバイスでリモート サポートを有効にすることで、Microsoft サポートがデバイスにリモートでアクセスして問題を診断および修復できるようになります。 この機能を有効にする場合、アクセスのレベルとアクセスの期間に同意することになります。 詳細については、[Azure Stack Edge のリモート サポートと診断を有効にする](azure-stack-edge-gpu-remote-support-diagnostics-repair.md)ことに関する記事を参照してください。
- **ハイ パフォーマンスのネットワーク仮想マシン** - このリリースより、ハイ パフォーマンスのネットワーク仮想マシンを Azure Stack Edge デバイスにデプロイできます。 詳細については、[ハイ パフォーマンスのネットワーク仮想マシンを Azure Stack Edge デバイスにデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network.md)ことに関する記事を参照してください。
- **Edge コンテナー レジストリと Kubernetes ダッシュボードの証明書** - Edge コンテナー レジストリと Kubernetes ダッシュボードの証明書がサポートされるようになりました。 ローカル UI を使用して証明書を作成およびアップロードできます。 詳細については、「[Kubernetes 証明書](azure-stack-edge-gpu-certificates-overview.md#kubernetes-certificates)」と「[Kubernetes 証明書のアップロード](azure-stack-edge-gpu-manage-certificates.md#upload-kubernetes-certificates)」を参照してください。
- **BGP モードの Metallb** - このリリースでは、Border Gateway Protocol (BGP) 経由で MetalLB を使用して、Azure Stack Edge デバイスで負荷分散を構成できます。 構成するには、デバイスの PowerShell インターフェイスに接続してから特定のコマンドレットを実行します。 詳細については、[Azure Stack Edge デバイス上で MetalLB を使用して負荷分散を構成する](azure-stack-edge-gpu-configure-metallb-bgp-mode.md)ことに関する記事を参照してください。



## <a name="issues-fixed-in-2110-release"></a>2110 リリースで修正された問題

次の表に、以前のリリースでリリース ノートに記載され、現在のリリースでは修正された問題を示します。

| いいえ。 | 特徴量 | 問題 | 
| --- | --- | --- |
|**1.**|Azure Arc 対応 Kubernetes |GA リリースでは、Azure Arc 対応 Kubernetes がバージョン 0.1.18 から 0.2.9 に更新されます。 Azure Stack Edge デバイスでは Azure Arc 対応 Kubernetes の更新がサポートされていないため、Azure Arc 対応 Kubernetes を再デプロイする必要があります。|
|**2.**|Azure Arc 対応 Kubernetes|Azure Stack Edge Pro デバイスで Web プロキシが構成されている場合、Azure Arc のデプロイはサポートされません。|
|**3.**|IoT Edge |IoT Edge によってデプロイされたモジュールは、ホスト ネットワークを使用できません。 | 
|**4.**|Kubernetes と更新 |2008 リリースなどの以前のソフトウェア バージョンでは、更新が ClusterConnectionException で失敗する競合状態の更新の問題があります。 |
|**5.**|Kubernetes ダッシュボード | Kubernetes ダッシュボードで SSL 証明書を使用する *Https* エンドポイントはサポートされていません。 | 
|**6.**|VM |VNF のデプロイ中に、VM 管理 NIC 用に静的 IP の重複チェックが追加されます。 明示的なエラー メッセージが返されます。 | 
|**7.**|VM |アドレス空間内の最初の 4 つの IP アドレスについて、IP 予約チェックが削除されました。  | 
|**8.**|マルチアクセス エッジ コンピューティング |VNF デプロイ中の Azure Resource Manager トークンの期限切れの問題を修正しました。 以前のリリースでは、VHD のダウンロードに時間がかかると、Azure Resource Manager トークンが期限切れになるため、VNF のデプロイが失敗しました。  | 
|**9.**|マルチアクセス エッジ コンピューティング |VNF のデプロイ中に、Azure Resource Manager の呼び出しにタイムアウトが追加されました。 以前のリリースでは、Azure Resource Manager の呼び出しに失敗した場合、VNF のデプロイに時間がかかっていました。   | 
|**10.**|マルチアクセス エッジ コンピューティング |マルチアクセス エッジ コンピューティングでは、VHD のダウンロードが完了すると、Azure Resource Manager テンプレートのデプロイがクリーンアップされます。 以前のリリースでは、多くの VNF デプロイの後、デプロイ クォータ超過エラーが発生していました。 既定のクォータは、リソース グループあたり 800 のデプロイでした。  | 



## <a name="known-issues-in-2110-release"></a>2110 リリースの既知の問題

次の表に、2110 リリースの既知の問題の概要を示します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
|**1.**|プレビュー機能 |このリリースでは、ローカル Azure Resource Manager、VM、VM のクラウド管理、Kubernetes クラウド管理、Azure Stack Edge Pro R および Azure Stack Edge Mini R 用の VPN、マルチプロセス サービス (MPS)、および Azure Stack Edge Pro GPU 用のマルチアクセス エッジ コンピューティング (MEC) の全機能をプレビューとしてご利用になれます。  |これらの機能は、今後のリリースで一般公開される予定です。 |



## <a name="known-issues-from-previous-releases"></a>以前のリリースの既知の問題

次の表に、前のリリースから持ち越されている既知の問題の概要を示します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL Database を作成するには、管理者アクセス権が必要です。   |「[SQL データベースの作成](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database)」の手順 1 から　2 の代わりに、次の手順を実行します。 <ol><li>デバイスのローカル UI で、コンピューティング インターフェイスを有効にします。 **[コンピューティング] > [ポート番号] > [Enable for compute]\(コンピューティングを有効にする\) > [適用]** を選択します。</li><li>[SQL コマンド ユーティリティ](/sql/tools/sqlcmd-utility)からクライアント マシンに `sqlcmd` をダウンロードします。 </li><li>コンピューティング インターフェイスの IP アドレス (有効なポート) に接続し、アドレスの末尾に ",1401" を追加します。</li><li>最終的なコマンドは次のようになります。sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd"</li>この後、現在のドキュメントの手順 3 から 4 は同じにする必要があります。 </li></ol> |
| **2.** |更新| **[更新]** によって復元された BLOB の増分変更はサポートされていません |BLOB エンドポイントの場合、[更新] 後に BLOB を部分的に更新すると、更新がクラウドにアップロードされない可能性があります。 たとえば、次のような一連のアクションがあります。<ol><li>クラウドに BLOB を作成します。 または、以前にアップロードした BLOB をデバイスから削除します。</li><li>更新機能を使用して、クラウドからアプライアンスに BLOB を更新します。</li><li>Azure SDK REST API を使用して、BLOB の一部のみを更新します。</li></ol>これらのアクションの結果、BLOB の更新されたセクションがクラウドで更新されない可能性があります。 <br>**回避策**:robocopy などのツール、またはエクスプローラーやコマンド ラインを介した通常のファイル コピーを使用して、BLOB 全体を置き換えます。|
|**3.**|Throttling|調整中に、デバイスへの新しい書き込みが許可されていない場合、NFS クライアントによる書き込みは "アクセス許可が拒否されました" エラーで失敗します。| 次のようなエラーが表示されます。<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: cannot create directory 'test': (mkdir: ディレクトリ 'test' を作成できません:)アクセス許可は拒否されました|
|**4.**|Blob Storage のインジェスト|Blob storage のインジェストに AzCopy バージョン 10 を使用する場合は、次の引数を指定して AzCopy を実行します。`Azcopy <other arguments> --cap-mbps 2000`| AzCopy にこれらの制限を指定しない場合、デバイスに大量の要求が送信され、サービスに問題が発生する可能性があります。|
|**5.**|階層型ストレージ アカウント|階層型ストレージ アカウントを使用する場合は、以下が適用されます。<ul><li> ブロック BLOB のみがサポートされています。 ページ BLOB はサポートされていません。</li><li>スナップショットまたはコピー API はサポートされていません。</li><li> `distcp` を使用した Hadoop ワークロードの取り込みは、コピー操作が頻繁に使用されるため、サポートされていません。</li></ul>||
|**6.**|NFS 共有接続|複数のプロセスによって同じ共有に対してコピーが実行され、`nolock` 属性が使用されていない場合、コピー中にエラーが発生する可能性があります。|ファイルを NFS 共有にコピーするには、`nolock` 属性を mount コマンドに渡す必要があります。 たとえば、 `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`と指定します。|
|**7.**|Kubernetes クラスター|Kubernetes クラスターを実行しているデバイスに更新プログラムを適用すると、Kubernetes 仮想マシンが再起動し、リブートされます。 このインスタンスでは、レプリカを指定して展開されたポッドのみが、更新後に自動的に復元されます。  |レプリケーション コントローラーの外部でレプリカ セットを指定せずに個別のポッドを作成した場合、これらのポッドはデバイスの更新後に自動的に復元されません。 これらのポッドを復元する必要があります。<br>レプリカ セットによって、ノードの障害や中断を伴うノードのアップグレードなど、何らかの理由で削除または中止されたポッドが置き換えられます。 このため、アプリケーションに必要なポッドが 1 つだけの場合でも、レプリカ セットを使用することをお勧めします。|
|**8.**|Kubernetes クラスター|Azure Stack Edge Pro 上の Kubernetes は、Helm v3 以降でのみサポートされています。 詳細については、「[Frequently asked questions: Removal of Tiller (よく寄せられる質問: Tiller の削除)](https://v3.helm.sh/docs/faq/)」を参照してください。|
|**9.**|Kubernetes |ポート 31000 は Kubernetes ダッシュボード用に予約されています。 ポート 31001 は、Edge コンテナー レジストリ用に予約されています。 同様に、既定の構成では、IP アドレス 172.28.0.1 と 172.28.0.10 は、それぞれ Kubernetes サービスと Core DNS サービス用に予約されています。|予約済みの IP は使用しないでください。|
|**10.**|Kubernetes |現在、Kubernetes ではマルチプロトコルの LoadBalancer サービスは許可されていません。 たとえば、TCP と UDP の両方をリッスンする必要がある DNS サービスです。 |MetalLB を使用して Kubernetes のこの制限を回避するには、同じポッド セレクター用に 2 つのサービス (TCP 用と UDP 用) を作成します。 これらのサービスでは、同じ共有キーと spec.loadBalancerIP を使用して、同じ IP アドレスを共有します。 使用可能な IP アドレスよりも多くのサービスがある場合は、IP を共有することもできます。 <br> 詳細については、「[IP address sharing (IP アドレスの共有)](https://metallb.universe.tf/usage/#ip-address-sharing)」を参照してください。|
|**11.**|Kubernetes クラスター|既存の Azure IoT Edge マーケットプレース モジュールは、Azure Stack Edge デバイス上の IoT Edge で実行するための変更が必要になる場合があります。|詳細については、「[Azure Stack Edge Pro FPGA デバイスの既存の IoT Edge モジュールを Azure Stack Edge Pro GPU デバイスで実行する](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)」を参照してください。|
|**12.**|Kubernetes |ファイルベースのバインド マウントは、Azure Stack Edge デバイス上の Kubernetes の Azure IoT Edge ではサポートされていません。|IoT Edge では、変換レイヤーを使用して、`ContainerCreate` オプションを Kubernetes コンストラクトに変換します。 `Binds` を作成すると、`hostpath` ディレクトリにマップされます。そのため、ファイルベースのバインド マウントを IoT Edge コンテナーのパスにバインドすることはできません。 可能であれば、親ディレクトリをマップします。|
|**13.**|Kubernetes |IoT Edge 用の独自の証明書を持ち込み、コンピューティングが構成された後の Azure Stack Edge デバイスにそれらの証明書を追加した場合、新しい証明書は取得されません。|この問題を回避するには、デバイスでコンピューティングを構成する前に、証明書をアップロードする必要があります。 コンピューティングが既に構成されている場合は、[デバイスの PowerShell インターフェイスに接続し、IoT Edge コマンドを実行 ます](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)。 `iotedged` および `edgehub` ポッドを再起動します。|
|**14.**|証明書 |場合によっては、ローカル UI の証明書の状態が更新されるまでに数秒かかることがあります。 |ローカル UI の次のシナリオが影響を受ける可能性があります。<ul><li>**[証明書]** ページの **[状態]** 列。</li><li>**[作業の開始]** ページの **[セキュリティ]** タイル。</li><li>**[概要]** ページの **[構成]** タイル。</li></ul>  |
|**15.**|コンピューティング + Kubernetes |コンピューティング/Kubernetes は NTLM Web プロキシをサポートしていません。 ||
|**16**|Internet Explorer|強化されたセキュリティ機能が有効になっている場合は、ローカル Web UI ページにアクセスできない可能性があります。 | 強化されたセキュリティを無効にし、ブラウザーを再起動してください。|
|**17.**|Kubernetes |Kubernetes は、.NET アプリケーションで使用される環境変数名の ":" をサポートしていません。 これは、イベント グリッド IoT Edge モジュールが Azure Stack Edge デバイスやその他のアプリケーションで機能するためにも必要です。 詳細については、[ASP.NET Core のドキュメント](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables)をご覧ください。|":" は二重のアンダースコアに置き換えてください。 詳細については、[Kubernetes の問題](https://github.com/kubernetes/kubernetes/issues/53201)に関する記事をご覧ください。|
|**18.** |Azure Arc と Kubernetes クラスター |Git リポジトリからリソース `yamls` を削除した場合、既定で Kubernetes クラスターから対応するリソースは削除されません。  |Git リポジトリからリソースを削除したときに、それらが削除されるようにするには、Arc OperatorParams に `--sync-garbage-collection` を設定します。 詳細については、「[構成を削除する](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)」をご覧ください。 |
|**19.**|NFS |お使いのデバイスの NFS 共有マウントを使用してデータを書き込むアプリケーションでは、排他的な書き込みを使用する必要があります。 これにより、書き込みがディスクに書き込まれるようになります。| |
|**20.**|コンピューティングの構成 |ネットワーク上に存在しないシステムに対するアドレス解決プロトコル (ARP) 要求にゲートウェイまたはスイッチまたはルーターが応答するネットワーク構成では、コンピューティング構成が失敗します。| |
|**21.**|コンピューティングおよび Kubernetes |お使いのデバイスで Kubernetes を最初に設定した場合、使用可能なすべての GPU が要求されます。 そのため、Kubernetes を設定した後で、GPU を使用して Azure Resource Manager の仮想マシンは作成できません。 |お使いのデバイスに GPU が 2 つある場合は、GPU を使用する仮想マシンを 1 つ作成してから Kubernetes を構成します。 この場合、Kubernetes が残りの使用可能な 1 つの GPU を使用します。 |
|**22.**|カスタム スクリプト VM 拡張機能 |以前のリリースで作成された Windows VM には既知の問題があり、デバイスは 2103 に更新されました。 <br> これらの VM でカスタム スクリプト拡張機能を追加すると、Windows VM ゲスト エージェント (バージョン 2.7.41491.901 のみ) が更新でスタックし、拡張機能のデプロイがタイムアウトになります。 | この問題の回避方法: <ol><li> リモート デスクトップ プロトコル (RDP) を使用して Windows VM に接続します。 </li><li> コンピューターで `waappagent.exe` が実行中であることを確認し ます: `Get-Process WaAppAgent`。 </li><li> `waappagent.exe` が実行されていない場合は、`rdagent` サービスを再起動します: `Get-Service RdAgent` \| `Restart-Service`。 5 分間待機します。</li><li> `waappagent.exe` が実行されている間に `WindowsAzureGuest.exe` プロセスを中止します。 </li><li>プロセスを中止した後に、新しいバージョンを使用して再度プロセスの実行が開始されます。</li><li>次のコマンドを使用して、Windows VM ゲスト エージェントのバージョンが 2.7.41491.971 であることを確認します: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion`。</li><li>[Windows VM にカスタム スクリプト拡張機能を設定します。](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md) </li><ol> |
|**23.**|GPU VM |このリリースより前は、GPU VM のライフサイクルは更新フロー内で管理されていませんでした。 そのため、2103 リリースに更新すると、GPU VM は更新中に自動的に停止されません。 デバイスを更新する前に、`stop-stayProvisioned` フラグを使用して GPU VM を手動で停止する必要があります。 詳細については、「[VM を中断またはシャットダウンする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)」を参照してください。<br> 更新前に実行が続けられていたすべての GPU VM は、更新後に起動されます。 このような場合、VM 上で実行されているワークロードは正常に終了されません。 また、更新後に VM が望ましくない状態になる可能性があります。 <br>更新前に `stop-stayProvisioned` によって停止された GPU VM はすべて、更新後に自動的に起動されます。 <br>Azure portal から GPU VM を停止した場合は、デバイスの更新後に手動で VM を起動する必要があります。| Kubernetes を使用して GPU VM を実行している場合は、更新の直前に GPU VM を停止します。 <br>GPU VM が停止されると、もともと VM によって使用されていた GPU は Kubernetes によって引き継がれます。 <br>GPU VM が停止状態になっている時間が長いほど、GPU が Kubernetes によって引き継がれる可能性が高くなります。 |
|**24.**|マルチプロセス サービス (MPS) |デバイス ソフトウェアと Kubernetes クラスターが更新されても、ワークロードの MPS 設定は保持されません。   |[MPS を再度有効にして](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)、MPS を使用していたワークロードを再デプロイします。 |


## <a name="next-steps"></a>次の手順

- [デバイスの更新](azure-stack-edge-gpu-install-update.md)
