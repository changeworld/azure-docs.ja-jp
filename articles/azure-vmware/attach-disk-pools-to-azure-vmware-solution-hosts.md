---
title: ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)
description: iSCSI ターゲットを通じて表示されたディスク プールを、Azure VMware Solution プライベート クラウドの VMware データストアとしてアタッチする方法について説明します。 データストアが構成されたら、そこにボリュームを作成し、VMware インスタンスにアタッチすることができます。
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: 2487e26d887935f0d66f13d51ce7894edb2b2b6e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769302"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)

[Azure ディスク プール](../virtual-machines/disks-pools.md)は、Azure ディスクによってサポートされるアプリケーションとワークロードに永続的なブロック ストレージを提供します。 最適なコストとパフォーマンスを実現するために、Azure VMware Solution の永続ストレージとしてディスクを使用できます。 たとえば、記憶域を集中的に使用するワークロードをホストする場合は、クラスターのスケーリングではなくディスク プールを使用してスケールアップすることができます。 また、ディスクを使用して、オンプレミスまたはプライマリ VMware 環境からセカンダリ サイトのディスク ストレージにデータをレプリケートすることもできます。 Azure VMware Solution ホストから独立してストレージをスケーリングするために、データストアとしての [Ultra Disk](../virtual-machines/disks-types.md#ultra-disk) と [Premium SSD](../virtual-machines/disks-types.md#premium-ssd) の提示がサポートされています。  

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

   - [Ultra disks] を選択した場合は、Azure VMware Solution プライベート クラウドに対して Ultra Performance を使用し、次に [ExpressRoute FastPath を有効化](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)します。

   - Premium SSD を選択した場合は、Azure VMware Solution プライベート クラウドに Standard (1 Gbps) を使用します。  iSCSI をホストするには、Standard\_DS##\_v3 を使用する必要があります。  クォータの問題が発生した場合は、Dsv3 シリーズの Azure VM シリーズあたりの [vCPU クォータ制限](../azure-portal/supportability/per-vm-quota-requests.md) の引き上げを依頼してください。

- ディスク プール。バックアップ用ストレージとしてデプロイされ、各ディスクが個々の LUN として指定された iSCSI ターゲットとして公開されます。 詳細については、「[Azure ディスク プールをデプロイする](../virtual-machines/disks-pools-deploy.md)」を参照してください。

   >[!IMPORTANT]
   > ディスク プールは、VMware クラスターと同じサブスクリプションにデプロイする必要があり、VMware クラスターと同じ VNET にアタッチする必要があります。

## <a name="attach-a-disk-pool-to-your-private-cloud"></a>ディスク プールをプライベート クラウドにアタッチする
iSCSI ターゲットを通じて表示されたディスク プールを、Azure VMware Solution プライベート クラウドの VMware データストアとしてアタッチします。

>[!IMPORTANT]
>**パブリック プレビュー** 中は、テスト クラスターまたは非運用クラスターにのみディスク プールをアタッチします。

1. サブスクリプションが `Microsoft.AVS` に登録されているかどうかを確認します。

   ```azurecli
   az provider show -n "Microsoft.AVS" --query registrationState
   ```

   まだ登録されていない場合は、次のように登録します。

   ```azurecli
   az provider register -n "Microsoft.AVS"
   ```

2. サブスクリプションが Microsoft.AVS 内の `CloudSanExperience` AFEC に登録されているかどうかを確認します。

   ```azurecli
   az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
   ```

   - まだ登録されていない場合は、次のように登録します。

      ```azurecli
      az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      ```

      登録の完了には約 15 分かかる場合があり、現在の状態を確認できます。
      
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

3. `vmware ` 拡張機能がインストールされているかどうか確認します。 

   ```azurecli
   az extension show --name vmware
   ```

   - 拡張機能が既にインストールされている場合は、バージョンが **3.0.0** であるかどうかを確認します。 古いバージョンがインストールされている場合は、次のように拡張機能を更新します。

      ```azurecli
      az extension update --name vmware
      ```

   - まだインストールされていない場合は、インストールします。

      ```azurecli
      az extension add --name vmware
      ```

4. iSCSI ターゲットによって指定された `Microsoft.StoragePool` を使用して、Azure VMware Solution プライベート クラウド クラスターに iSCSI データストアを作成してアタッチします。 ディスク プールは委任されたサブネットを介して vNet にアタッチされ、これは Microsoft.StoragePool/diskPools リソース プロバイダーで行われます。  サブネットが委任されていない場合、デプロイは失敗します。

   ```bash
   az vmware datastore disk-pool-volume create --name iSCSIDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name lun0
   ```

   >[!TIP]
   >データストアでヘルプを表示できます。
   >
   >   ```azurecli
   >   az vmware datastore -h
   >   ```
   

5. プライベート クラウド クラスター内の iSCSI データストアの詳細を表示します。
   
   ```azurecli
   az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
   ```

6. プライベート クラウド クラスター内のすべてのデータストアの一覧を表示します。

   ```azurecli
   az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="delete-an-iscsi-datastore-from-your-private-cloud"></a>プライベート クラウドから iSCSI データストアを削除する

プライベート クラウドのデータストアを削除しても、ディスク プールのリソースは削除されません。 この操作に必要なメンテナンス期間がありません。

1. VM の電源をオフにし、次のような iSCSI データストアに関連付けられているすべてのオブジェクトを削除します。

   - VM (インベントリからの削除)

   - テンプレート

   - スナップショット

2. プライベート クラウド データストアを削除します。

   ```azurecli
   az vmware datastore delete --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="next-steps"></a>次の手順

Azure VMware Solution ホストにディスク プールをアタッチできましたので、次のことについて学習します。

- [Azure ディスク プールの管理](../virtual-machines/disks-pools-manage.md)。  ディスク プールをデプロイした後は、さまざまな管理操作を実行できます。 ディスク プールに対するディスクの追加または削除、iSCSI LUN マッピングの更新、ACL の追加を行うことができます。

- [ディスク プールの削除](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool)。 ディスク プールを削除すると、マネージド リソース グループ内のすべてのリソースも削除されます。

- [ディスク上の iSCSI サポートの無効化](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support)。 ディスク プールで iSCSI サポートを無効にすると、事実上ディスク プールを使用できなくなります。

- [ディスク プールの別のサブスクリプションへの移動](../virtual-machines/disks-pools-move-resource.md)。 Azure ディスク プールを別のサブスクリプションに移動します。これには、ディスク プール自体、含まれているディスク、マネージド リソース グループ、およびすべてのリソースの移動が含まれます。 

- [ディスク プールのトラブルシューティング](../virtual-machines/disks-pools-troubleshoot.md)。 Azure ディスク プール (プレビュー) に関連する一般的なエラー コードを確認します。 また、ディスク プールの状態に関して考えられる解決方法をわかりやすく説明します。
