---
title: Azure Kubernetes Service (AKS) ノード プールのノード構成をカスタマイズする (プレビュー)
description: Azure Kubernetes Service (AKS) クラスター ノードおよびノード プールの構成をカスタマイズする方法について説明します。
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779977"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Azure Kubernetes Service (AKS) ノード プールのノード構成をカスタマイズする (プレビュー)

ノード構成をカスタマイズすることで、ワークロードのニーズに合わせてオペレーティング システム (OS) 設定または Kubelet パラメーターを構成または調整することができます。 AKS クラスターを作成するか、クラスターにノード プールを追加すると、一般的に使用される OS と Kubelet の設定のサブセットをカスタマイズできます。 このサブセット以外の設定を構成するには、[デーモン セットを使用して、ノードの AKS サポートを失うことなく必要な構成をカスタマイズします](support-policies.md#shared-responsibility)。

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>`CustomNodeConfigPreview` プレビュー機能を登録する

Kubelet パラメーターまたは OS 設定をカスタマイズできる AKS クラスターまたはノード プールを作成するには、サブスクリプションで `CustomNodeConfigPreview` 機能フラグを有効にする必要があります。

`CustomNodeConfigPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

Kubelet パラメーターまたは OS 設定をカスタマイズできる AKS クラスターまたはノード プールを作成するには、最新の *aks-preview* Azure CLI 拡張機能が必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>カスタム ノード構成を使用する

### <a name="kubelet-custom-configuration"></a>Kubelet カスタム構成

サポートされている Kubelet パラメーターと許容される値を以下に示します。

| パラメーター | 許容される値/間隔 | Default | 説明 |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | none、static | なし | 静的ポリシーを使用すると、整数 CPU 要求を含む[保証されたポッド](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/)内のコンテナーが、ノード上の排他的 CPU にアクセスできます。 |
| `cpuCfsQuota` | true、false | true |  CPU の制限を指定するコンテナーに対して、CPU CFS クォータの適用を有効または無効にします。 | 
| `cpuCfsQuotaPeriod` | ミリ秒 (ms) 単位の間隔 | `100ms` | CPU の CFS クォータ期間値を設定します。 | 
| `imageGcHighThreshold` | 0 ～ 100 | 85 | イメージ ガベージ コレクションが常に実行されるようになる、ディスク使用量の割合。 ガベージ コレクションをトリガー **する** 最小ディスク使用量。 イメージ ガベージ コレクションを無効にするには、100 に設定します。 | 
| `imageGcLowThreshold` | 0 から 100、`imageGcHighThreshold` 以下 | 80 | イメージ ガベージ コレクションを実行できるようになる、ディスク使用量の割合。 ガベージ コレクションをトリガー **できる** 最小ディスク使用量。 |
| `topologyManagerPolicy` | none、best-effort、restricted、single-numa-node | なし | NUMA ノードの配置を最適化します。詳細については、[こちら](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)をご覧ください。 Kubernetes v1.18 以降のみに該当します。 |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | なし | 安全でない sysctls パターンまたは安全でない sysctl パターンの許容リスト。 | 

### <a name="linux-os-custom-configuration"></a>Linux OS カスタム構成

サポートされている OS 設定と許容される値を以下に示します。

#### <a name="file-handle-limits"></a>ファイル ハンドルの制限

大量のトラフィックを処理している場合、処理するトラフィックが多数のローカル ファイルから送信されていることがよくあります。 多少のシステム メモリを費やして、より多くの処理を実行できるように、以下のカーネル設定と組み込みの制限を調整することができます。

| 設定 | 許容される値/間隔 | Default | 説明 |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 から 12000500 | 709620 | Linux カーネルによって割り当てられるファイル ハンドルの最大数。この値を増やすことで、開いているファイルの最大許容数を増やすことができます。 |
| `fs.inotify.max_user_watches` | 781250 から 2097152 | 1048576 | システムで許容されるファイル ウォッチの最大数。 各 "*ウォッチ*" は 32 ビット カーネルでは約 90 バイト、64 ビット カーネルでは約 160 バイトです。 | 
| `fs.aio-max-nr` | 65536 から 6553500 | 65536 | aio-nr は、現在のシステム全体における非同期 io 要求の数を示しています。 aio-max-nr では、aio-nr の可能な最大値を変更できます。 |
| `fs.nr_open` | 8192 から 20000500 | 1048576 | プロセスで割り当てることができるファイル ハンドルの最大数。 |


#### <a name="socket-and-network-tuning"></a>ソケットとネットワークの調整

非常に多数の同時セッションを処理することが期待されるエージェント ノードの場合、ノード プールごとに調整できる以下の TCP オプションとネットワーク オプションのサブセットを使用できます。 

| 設定 | 許容される値/間隔 | Default | 説明 |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 から 3240000 | 16384 | リッスンしている特定のソケットに対してキューに入れることができる接続要求の最大数。 [listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) 関数に渡されるバックログ パラメーターの値の上限。 バックログ引数が `somaxconn` より大きい場合、通知なしにこの制限に切り捨てられます。
| `net.core.netdev_max_backlog` | 1000 から 3240000 | 1000 | カーネルで処理できる速度を超えてパケットをインターフェイスで受信した場合に、INPUT 側でキューに入れられたパケットの最大数。 |
| `net.core.rmem_max` | 212992 から 134217728 | 212992 | 受信ソケット バッファーの最大サイズ (バイト単位)。 |
| `net.core.wmem_max` | 212992 から 134217728 | 212992 | 送信ソケット バッファーの最大サイズ (バイト単位)。 | 
| `net.core.optmem_max` | 20480 から 4194304 | 20480 | ソケットごとに許容される付加バッファーの最大サイズ (オプション メモリ バッファー)。 ソケットのオプション メモリは、ソケットの使用に関連する追加の構造体を格納するために、いくつかのケースで使用されます。 | 
| `net.ipv4.tcp_max_syn_backlog` | 128 から 3240000 | 16384 | 接続しているクライアントから受信確認を受信していない、キューに入れられた接続要求の最大数。 この数を超えると、カーネルによって要求の破棄が開始されます。 |
| `net.ipv4.tcp_max_tw_buckets` | 8000 から 1440000 | 32768 | システムで同時に保持される `timewait` ソケットの最大数。 この数を超えると、time-wait ソケットが直ちに破棄され、警告が出力されます。 |
| `net.ipv4.tcp_fin_timeout` | 5 から 120 | 60 | ローカル側で中止されるまで FIN_WAIT_2 状態のままになる、孤立した (どのアプリケーションからも参照されなくなった) 接続の時間の長さ。 |
| `net.ipv4.tcp_keepalive_time` | 30 から 432000 | 7200 | `keepalive` が有効になっている場合に、TCP によって `keepalive` メッセージが送信される頻度。 |
| `net.ipv4.tcp_keepalive_probes` | 1 から 15 | 9 | 接続が切断されたと判断されるまで、TCP によって送信される `keepalive` プローブの数。 |
| `net.ipv4.tcp_keepalive_intvl` | 1 から 75 | 75 | プローブが送信される頻度。`tcp_keepalive_probes` で乗算することで、プローブが開始された後、応答していない接続を強制終了する時間が算出されます。 | 
| `net.ipv4.tcp_tw_reuse` | 0 または 1 | 0 | プロトコルの観点から安全な場合に、新しい接続に対して `TIME-WAIT` ソケットを再利用できるようにします。 | 
| `net.ipv4.ip_local_port_range` | 最初:1024 から 60999、最後:32768 から 65000 | 最初:32768、最後:60999 | TCP および UDP トラフィックによってローカル ポートを選択するために使用されるローカル ポート範囲。 2 つの数値で構成されます。最初の番号は、エージェント ノードで TCP および UDP トラフィックに対して許容される最初のローカル ポートで、2 番目は最後のローカル ポート番号です。 | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 から 80000 | 4096 | ARP キャッシュ内のエントリの最小数。 エントリの数がこの設定を下回る場合、ガベージ コレクションはトリガーされません。 | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 から 90000 | 8192 | ARP キャッシュ内のエントリのソフト最大数。 このソフト最大数に達してから 5 秒後に ARP ガベージ コレクションがトリガーされるため、この設定は最も重要です。 |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 から 100000 | 16384 | ARP キャッシュ内のエントリのハード最大数。 |
| `net.netfilter.nf_conntrack_max` | 131072 から 589824 | 131072 | `nf_conntrack` は、Linux 内の NAT の接続エントリを追跡するモジュールです。 `nf_conntrack` モジュールでは、ハッシュ テーブルを使用して、TCP プロトコルの "*確立された接続*" レコードが記録されます。 `nf_conntrack_max` は、ハッシュ テーブル内のノードの最大数、つまり `nf_conntrack` モジュールまたは接続追跡テーブルのサイズでサポートされる接続の最大数です。 | 
| `net.netfilter.nf_conntrack_buckets` | 65536 から 147456 | 65536 | `nf_conntrack` は、Linux 内の NAT の接続エントリを追跡するモジュールです。 `nf_conntrack` モジュールでは、ハッシュ テーブルを使用して、TCP プロトコルの "*確立された接続*" レコードが記録されます。 `nf_conntrack_buckets` は、ハッシュ テーブルのサイズです。 | 

#### <a name="worker-limits"></a>ワーカーの制限

ファイル記述子の制限と同様に、プロセスで作成できるワーカーまたはスレッドの数は、カーネル設定とユーザー制限の両方により制限されます。 AKS のユーザー制限は無制限です。 

| 設定 | 許容される値/間隔 | Default | 説明 |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 から 513785 | 55601 | プロセスによってワーカー スレッドをスピンアップできます。 作成できるすべてのスレッドの最大数は、カーネル設定 `kernel.threads-max` で設定されます。 | 

#### <a name="virtual-memory"></a>仮想メモリ

次の設定を使用して、Linux カーネルの仮想メモリ (VM) サブシステムの動作と、ディスクへのダーティ データの `writeout` を調整できます。

| 設定 | 許容される値/間隔 | Default | 説明 |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 から 262144 | 65530 | このファイルには、プロセスが持つことのできるメモリ マップ領域の最大数が含まれています。 メモリ マップ領域は、`malloc` の呼び出しの副作用として `mmap`、`mprotect`、および `madvise` によって直接使用されるほか、共有ライブラリの読み込み時にも使用されます。 | 
| `vm.vfs_cache_pressure` | 1 から 500 | 100 | この割合値により、ディレクトリおよび inode オブジェクトのキャッシュに使用されるメモリを再利用するカーネルの傾向が制御されます。 |
| `vm.swappiness` | 0 ～ 100 | 60 | このコントロールは、カーネルによってメモリ ページをどの程度アグレッシブにスワップするかを定義するために使用されます。 値を大きくすると、よりアグレッシブになり、値を小さくするとスワップの量が減少します。 値を 0 に設定すると、空きページとファイルに格納されたページの量がゾーン内の高基準値を下回るまで、スワップを開始しないようにカーネルに指示されます。 | 
| `swapFileSizeMB` | 1 MB - [一時ディスク](../virtual-machines/managed-disks-overview.md#temporary-disk)のサイズ (/dev/sdb) | なし | SwapFileSizeMB により、このノード プールからエージェント ノードに作成されるスワップ ファイルのサイズが MB 単位で指定されます。 | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) は、プロセッサのメモリ マッピング ハードウェアをより効率的に使用することでパフォーマンスを向上させることを目的とした Linux カーネル機能です。 有効にすると、カーネルにより、可能な場合は常に `hugepages` の割り当てが試行され、`mmap` 領域に 2 MB が自然に配置されている場合、Linux プロセスは 2 MB のページを受け取ります。 `hugepages` がシステム全体で有効になっている場合は、アプリケーションでより多くのメモリ リソースが割り当てられることがあります。 アプリケーションで、大きな領域に対して `mmap` が実行されたが、影響を受けたのはそのうち 1 バイトだけであったとします。その場合、特に理由なく、4 K のページの代わりに 2 MB のページが割り当てられる可能性があります。 このシナリオでは、システム全体で `hugepages` を無効にしたり、`MADV_HUGEPAGE madvise` 領域内にのみ配置したりできます。 | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | この値により、`hugepages` をより多く使用できるように、カーネルでメモリの最適化をアグレッシブに使用する必要があるかどうかが制御されます。 | 

> [!IMPORTANT]
> 検索のしやすさと読みやすさを高めるために、このドキュメントには OS 設定が名前で記載されていますが、構成 JSON ファイルまたは AKS API には [camelCase の大文字と小文字の規則](/dotnet/standard/design-guidelines/capitalization-conventions)を使用して追加する必要があります。

次の内容を持つ新しいファイル `kubeletconfig.json` を作成します。

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
次の内容を持つ新しいファイル `linuxosconfig.json` を作成します。

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

前の手順で作成した JSON ファイルを使用して、kubelet と OS の構成を指定する新しいクラスターを作成します。 

> [!NOTE]
> クラスターを作成するときに、Kubelet の構成、OS の構成、またはその両方を指定できます。 クラスターを作成するときに構成を指定した場合、初期ノード プール内のノードにのみ、その構成が適用されます。 JSON ファイルで構成されていない設定は、既定値のままになります。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

作成した JSON ファイルを使用して、Kubelet パラメーターを指定する新しいノード プールを追加します。

> [!NOTE]
> 既存のクラスターにノード プールを追加する場合は、Kubelet 構成、OS 構成、またはその両方を指定できます。 ノード プールを追加するときに構成を指定した場合、新しいノード プール内のノードにのみ、その構成が適用されます。 JSON ファイルで構成されていない設定は、既定値のままになります。

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>次のステップ

- [AKS クラスターを構成する方法](cluster-configuration.md)を学習する。
- クラスター内の[ノード イメージをアップグレード方法](node-image-upgrade.md)を学習する。
- 「[Azure Kubernetes Service (AKS) クラスターのアップグレード](upgrade-cluster.md)」を参照し、クラスターを最新バージョンの Kubernetes にアップグレードする方法について学習する。
- [AKS についてよく寄せられる質問](faq.md)に関する記事の一覧を参照し、AKS についての一般的な質問への回答を確認する。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why