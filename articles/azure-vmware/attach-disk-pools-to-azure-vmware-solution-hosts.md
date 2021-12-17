---
title: ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)
description: iSCSI ターゲットを通じて表示されたディスク プールを、Azure VMware Solution プライベート クラウドの VMware データストアとしてアタッチする方法について説明します。 データストアが構成されたら、そこにボリュームを作成し、VMware インスタンスにアタッチすることができます。
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2719b135860f448732f5f36285ef2c33ff0115e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071645"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)

[Azure ディスク プール](../virtual-machines/disks-pools.md)は、Azure ディスクによってサポートされるアプリケーションとワークロードに永続的なブロック ストレージを提供します。 最適なコストとパフォーマンスを実現するために、Azure VMware Solution の永続ストレージとしてディスクを使用できます。 たとえば、記憶域を集中的に使用するワークロードをホストする場合は、クラスターのスケーリングではなくディスク プールを使用してスケールアップすることができます。 また、ディスクを使用して、オンプレミスまたはプライマリ VMware 環境からセカンダリ サイトのディスク ストレージにデータをレプリケートすることもできます。 Azure VMware Solution ホストから独立してストレージをスケーリングするために、データストアとしての [Ultra Disk](../virtual-machines/disks-types.md#ultra-disks)、[Premium SSD](../virtual-machines/disks-types.md#premium-ssds)、[Standard SSD](../virtual-machines/disks-types.md#standard-ssds) の提示がサポートされています。  

>[!IMPORTANT]
>Azure VMware Solution (プレビュー) の Azure ディスク プールは現在、パブリック プレビュー段階にあります。
>このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure マネージド ディスクは、Azure VMware Solution リソース グループにデプロイされた 1 つの iSCSI コントローラー仮想マシンにアタッチされます。 ディスクはストレージ ターゲットとしてディスク プールにデプロイされ、各ストレージ ターゲットは iSCSI ターゲットの下に iSCSI LUN として表示されます。 ディスク プールは、Azure VMware Solution ホストにデータストアとして接続されている iSCSI ターゲットとして公開できます。 ディスク プールは、ストレージ ターゲットとして追加された、基になるすべてのディスクの単一のエンドポイントとして表示されます。 各ディスク プールには、iSCSI コントローラーを 1 つだけ含めることができます。

この図は、Azure VMware Solution ホストでディスク プールがどのように機能するかを示しています。 各 iSCSI コントローラーでは、標準の Azure プロトコルを使用してマネージド ディスクにアクセスし、Azure VMware Solution ホストでは iSCSI 経由で iSCSI コントローラーにアクセスできます。


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Azure VMware Solution ホストでディスク プールがどのように機能するかを示す図。" border="false":::


## <a name="supported-regions"></a>サポートされているリージョン

ディスク プールは、同じリージョン内の Azure VMware Solution プライベート クラウドにのみ接続できます。 サポートされているリージョンの一覧については、「[リージョン別の提供状況](../virtual-machines/disks-pools.md#regional-availability)」をご覧ください。  プライベート クラウドがサポートされていないリージョンにデプロイされている場合は、サポートされているリージョンに再デプロイできます。 Azure VMware Solution プライベート クラウドとディスク プール コロケーションでは、最小限のネットワーク待機時間で最高のパフォーマンスを実現します。


## <a name="prerequisites"></a>前提条件

- ワークロードのスケーラビリティとパフォーマンスの要件が特定されています。 詳細については、[Azure ディスク プールの計画](../virtual-machines/disks-pools-planning.md)に関するページを参照してください。

- [仮想ネットワークを構成](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute)してデプロイされた [Azure VMware Solution プライベート クラウド](deploy-azure-vmware-solution.md)。 詳細については、[ネットワーク計画のチェックリスト](tutorial-network-checklist.md)および[VMware プライベート クラウド用のネットワークの構成](tutorial-configure-networking.md)に関するページを参照してください。 

   - Ultra Disk を選んだ場合は、Azure VMware Solution プライベート クラウドへのディスク プール ネットワーク接続に Ultra Performance ExpressRoute 仮想ネットワーク ゲートウェイを使用して、[ExpressRoute FastPath を有効化](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)してください。

   - Premium SSD または Standard SSD を選んだ場合は、Azure VMware Solution プライベート クラウドへのディスク プール ネットワーク接続に Standard (1 Gbps) または High Performance (2 Gbps) ExpressRoute 仮想ネットワーク ゲートウェイを使用します。  

- iSCSI をホストするには、Standard\_DS##\_v3 を使用する必要があります。  クォータの問題が発生した場合は、Dsv3 シリーズの Azure VM シリーズあたりの [vCPU クォータ制限](../azure-portal/supportability/per-vm-quota-requests.md) の引き上げを依頼してください。

- ディスク プール。バックアップ用ストレージとしてデプロイされ、各ディスクが個々の LUN として指定された iSCSI ターゲットとして公開されます。 詳細については、「[Azure ディスク プールをデプロイする](../virtual-machines/disks-pools-deploy.md)」を参照してください。

   >[!IMPORTANT]
   > ディスク プールは、VMware クラスターと同じサブスクリプションにデプロイする必要があり、VMware クラスターと同じ VNET にアタッチする必要があります。

## <a name="add-a-disk-pool-to-your-private-cloud"></a>ディスク プールをプライベート クラウドに追加する
iSCSI ターゲットを通じて表示されたディスク プールを、Azure VMware Solution プライベート クラウドの VMware データストアとしてアタッチします。

>[!IMPORTANT]
>**パブリック プレビュー** 中は、テスト クラスターまたは非運用クラスターにのみディスク プールをアタッチします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

サブスクリプションが `Microsoft.AVS` に登録されているかどうかを確認します。

```azurecli
az provider show -n "Microsoft.AVS" --query registrationState
```

まだ登録されていない場合は、次のように登録します。

```azurecli
az provider register -n "Microsoft.AVS"
```

サブスクリプションが Microsoft.AVS 内の `CloudSanExperience` AFEC に登録されているかどうかを確認します。

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

まだ登録されていない場合は、次のように登録します。

```azurecli
az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

登録の完了には約 15 分かかる場合があります。次のコマンドを使用して状態を確認できます。

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
```

>[!TIP]
>15 分以上経過しても登録が完了せず、途中で停止した場合は、フラグを登録解除してから再登録します。
>
>```azurecli
>az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
>az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
>```

`vmware ` 拡張機能がインストールされているかどうか確認します。 

```azurecli
az extension show --name vmware
```

拡張機能が既にインストールされている場合は、バージョンが **3.0.0** であるかどうかを確認します。 古いバージョンがインストールされている場合は、次のように拡張機能を更新します。

```azurecli
az extension update --name vmware
```

まだインストールされていない場合は、インストールします。

```azurecli
az extension add --name vmware
```

### <a name="attach-the-iscsi-lun"></a>iSCSI LUN をアタッチする

iSCSI ターゲットによって指定された `Microsoft.StoragePool` を使用して、Azure VMware Solution プライベート クラウド クラスターに iSCSI データストアを作成してアタッチします。 ディスク プールは委任されたサブネットを介して仮想ネットワークにアタッチされます。これは Microsoft.StoragePool/diskPools リソース プロバイダーで行われます。  サブネットが委任されていない場合、デプロイは失敗します。

```bash
#Initialize input parameters
resourceGroupName='<yourRGName>'
name='<desiredDataStoreName>'
cluster='<desiredCluster>'
privateCloud='<privateCloud>'
lunName='<desiredLunName>'

az vmware datastore disk-pool-volume create --name $name --resource-group $resourceGroupName --cluster $cluster --private-cloud $privateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name $lunName
```

>[!TIP]
>データストアでヘルプを表示できます。
>
>   ```azurecli
>   az vmware datastore -h
>   ```


アタッチが成功したことを確認するには、次のコマンドを使用できます。

プライベート クラウド クラスター内の iSCSI データストアの詳細を表示します。

```azurecli
az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
```

プライベート クラウド クラスター内のすべてのデータストアの一覧を表示します。

```azurecli
az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

### <a name="preview-registration"></a>登録のプレビュー

まず、Microsoft.AVS と CloudSanExperience にサブスクリプションを登録します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **サブスクリプション** を検索して選択します。
1. 使用するサブスクリプションを選択し、 **[設定]** で **[リソース プロバイダー]** を選択します。
1. **Microsoft.AVS** を検索して選択し、 **[再登録]** を選択します。
1. **[設定]** で、 **[プレビュー機能]** を選択します。
1. **CloudSanExperience** を検索して、登録します。

### <a name="connect-your-disk-pool"></a>ディスク プールを接続する

サブスクリプションが正しく登録されたので、ディスク プールを Azure VMware Solution プライベート クラウド クラスターに接続できます。

> [!IMPORTANT]
> ディスク プールは委任されたサブネットを介して仮想ネットワークにアタッチされます。これは Microsoft.StoragePool リソース プロバイダーで行われます。 サブネットが委任されていない場合、デプロイは失敗します。 詳細については、「[サブネット アクセス許可を委任する](../virtual-machines/disks-pools-deploy.md#delegate-subnet-permission)」を参照してください。

1. Azure VMware Solution に移動します。
1. **[管理]** で **[ストレージ (プレビュー)]** を選択します。
1. **[ディスク プールを接続します]** を選択します。
1. 使用するサブスクリプションを選択します。
1. ディスク プールと、それを接続するクライアント クラスターを選択します。
1. LUN を有効にし (存在する場合)、データストア名 (既定では LUN が使用されます) を指定して、 **[Connect]\(接続\)** を選択します。

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png" alt-text="ディスク プールの接続エクスペリエンスのスクリーンショット。" lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png":::

接続が成功すると、vCenter に追加されたデータストアが表示されます。

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png" alt-text="vSphere エクスペリエンスのスクリーンショット。ディスク プールはデータストアとしてアタッチされています。" lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png":::

---

## <a name="disconnect-a-disk-pool-from-your-private-cloud"></a>プライベート クラウドからディスク プールを切断する

ディスク プールを切断しても、ディスク プール リソースは削除されません。 この操作に必要なメンテナンス期間がありません。 ただし、これを行う場合は注意してください。

まず、VM の電源をオフにし、次のような、ディスク プール データストアに関連付けられているすべてのオブジェクトを削除します。

   - VM (インベントリからの削除)

   - テンプレート

   - スナップショット

その後、プライベート クラウド データストアを削除します。

1. Azure portal で Azure VMware Solutionに移動しますl。
1. **[管理]** で **[ストレージ]** を選択します。
1. 切断するディスク プールを選択し、 **[接続解除]** を選択します。

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png" alt-text="Azure VMware Solution の [ストレージ] ページのスクリーンショット。アタッチされているディスク プールの一覧と、[接続解除] が強調表示されています。" lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png":::

## <a name="next-steps"></a>次のステップ

Azure VMware Solution ホストにディスク プールをアタッチできましたので、次のことについて学習します。

- [Azure ディスク プールの管理](../virtual-machines/disks-pools-manage.md)。  ディスク プールをデプロイした後は、さまざまな管理操作を実行できます。 ディスク プールに対するディスクの追加または削除、iSCSI LUN マッピングの更新、ACL の追加を行うことができます。

- [ディスク プールの削除](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool)。 ディスク プールを削除すると、マネージド リソース グループ内のすべてのリソースも削除されます。

- [ディスク上の iSCSI サポートの無効化](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support)。 ディスク プールで iSCSI サポートを無効にすると、事実上ディスク プールを使用できなくなります。

- [ディスク プールの別のサブスクリプションへの移動](../virtual-machines/disks-pools-move-resource.md)。 Azure ディスク プールを別のサブスクリプションに移動します。これには、ディスク プール自体、含まれているディスク、マネージド リソース グループ、およびすべてのリソースの移動が含まれます。 

- [ディスク プールのトラブルシューティング](../virtual-machines/disks-pools-troubleshoot.md)。 Azure ディスク プール (プレビュー) に関連する一般的なエラー コードを確認します。 また、ディスク プールの状態に関して考えられる解決方法をわかりやすく説明します。
