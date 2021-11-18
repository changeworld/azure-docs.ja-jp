---
title: Azure Virtual Machines の状態と課金状態
description: VM のさまざまな状態と、ユーザーが課金されるタイミングの概要。
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 02e7c76d9fea9801fd733aa6380cf063012cb491
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709085"
---
# <a name="states-and-billing-status-of-azure-virtual-machines"></a>Azure Virtual Machines の状態と課金状態

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure Virtual Machines (VM) はさまざまな状態に変化し、それらは "*プロビジョニング状態*" と "*電源の状態*" に分類することができます。 この記事の目的は、各状態について説明し、ユーザーがインスタンスの使用料金を課金されるタイミングを具体的に明示することです。 

## <a name="get-states-using-instance-view"></a>インスタンス ビューを使用して状態を取得する

インスタンス ビュー API によって、VM の実行状態に関する情報が提供されます。 詳細については、「[Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview)」(仮想マシン - インスタンス ビュー) の API ドキュメントをご覧ください。

Azure Resource Explorer には、VM の実行状態を表示するためのシンプルな UI ([リソース エクスプローラー](https://resources.azure.com/)) が備えられています。

VM のプロビジョニング状態は、VM のプロパティ `provisioningState` と InstanceView 内から (少し異なる形式で) 使用できます。 VM InstanceView では、`ProvisioningState/<state>[/<errorCode>]` 形式の `status` 配列内に要素があります。

自分のサブスクリプション内のすべての VM の電源状態を取得するには、**statusOnly** パラメーターを *true* に設定して [Virtual Machines の List All API](/rest/api/compute/virtualmachines/listall) を使用します。

> [!NOTE]
> [Virtual Machines の List All API](/rest/api/compute/virtualmachines/listall) を、**statusOnly** パラメーターを true に設定して使用した場合、サブスクリプション内のすべての VM の電源状態を取得できます。 ただし、取得プロセスでの一時的な問題のために、まれに電源状態を取得できない場合があります。 このような場合は、同じ API を使用するか、[Azure Resource Health](../service-health/resource-health-overview.md) を使用して再試行し、ご自分の VM の電源状態を確認することをお勧めします。
 
## <a name="power-states-and-billing"></a>電源の状態と課金

電源の状態は、最後に検出された VM の状態を表します。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="VM が取り得る電源状態を示す図。":::

インスタンスの各状態の説明と、インスタンスの使用料金を課金されるかどうかを、次の表に示します。

| 電源状態 | 説明 | 課金 |  
|---|---|---|
| 開始中| 仮想マシンは起動中です。 | 課金されます | 
| 実行中 | 仮想マシンは完全に起動しました。 これが標準の動作状態です。 | 課金されます | 
| 停止中 | これは、実行中と停止間で遷移している状態です。 | 課金されます| 
|停止済み | 仮想マシンはホスト上に割り当てられていますが、実行されていません。 PoweredOff 状態または *停止済み (割り当て済み)* とも呼ばれます。 これは、PowerOff API 操作を呼び出すか、ゲスト OS 内からシャットダウンを呼び出すことによって発生することがあります。 VM の作成中、または割り当て解除済み状態から VM を起動しているときに、停止済み状態が一時的に検出されることもあります。  | 課金されます | 
| 割り当て解除中 | これは、実行中と割り当て解除間で遷移している状態です。 | 課金されません* | 
| 割り当て解除済み | Virtual Machine は、基になるハードウェアに対するリースから解放され、完全に電源が切られました。 この状態は、*停止 (割り当て解除)* とも呼ばれます。 | 課金されません* | 


**JSON での PowerState の例**

```json
        {
          "code": "PowerState/running",
          "level": "Info",
          "displayStatus": "VM running"
        }
```

&#42; Azure の[ディスク](https://azure.microsoft.com/pricing/details/managed-disks)や[ネットワーク](https://azure.microsoft.com/pricing/details/bandwidth/)などの一部のリソースは引き続き課金されます。


## <a name="provisioning-states"></a>プロビジョニング状態

プロビジョニング状態とは、VM 上でユーザーが開始したコントロール プレーン操作の状態のことです。 これらの状態は、VM の電源の状態から独立しています。

| プロビジョニングの状態 | 説明 |
|---|---|
| 作成 | 仮想マシンの作成中です。 |
| 更新中 | 仮想マシンを最新のモデルに更新しています。 起動や再起動などのモデル変更ではない一部の変更は、更新中状態に分類されます。 |
| 失敗 | 仮想マシン リソースに対する最後の操作が成功しませんでした。 | 
| 成功 | 仮想マシン リソースに対する最後の操作が成功しました。 | 
| 削除中 | 仮想マシンの削除中です。 | 
| 移行 | Azure Service Manager から Azure Resource Manager に移行するときに表示されます。 | 

## <a name="os-provisioning-states"></a>OS のプロビジョニング状態
OS のプロビジョニング状態は、[生成済みの](./linux/imaging.md#generalized-images) OS イメージを使用して作成された仮想マシンにのみ該当します。 OS ディスクとして接続された[特殊化された](./linux/imaging.md#specialized-images)イメージとディスクでは、これらの状態は表示されません。 OS のプロビジョニング状態は個別には表示されません。 これは、VM instanceView のプロビジョニング状態のサブ状態です。 たとえば、`ProvisioningState/creating/osProvisioningComplete` のようにします。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="VM が取り得る OS のプロビジョニング状態を示す図。":::

| OS のプロビジョニング状態 | 説明 | 
|---|---|
| OSProvisioningInProgress | VM が実行中で、ゲスト OS の初期化 (設定) が進行しています。 |
| OSProvisioningComplete | これは短期の状態です。 仮想マシンは、この状態からすぐに **成功** に遷移します。 拡張機能がまだインストール中の場合は、完了するまでこの状態は表示され続けます。 |
| 成功 | ユーザーが開始したアクションが完了しました。 | 
| 失敗 | 操作が失敗したことを表します。 詳細と考えられる解決策は、エラー コードを参照してください。 | 

## <a name="troubleshooting-vm-states"></a>VM の状態のトラブルシューティング

VM の状態の特定の問題のトラブルシューティングを行うには、[Windows VM デプロイのトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows)と [Linux VM デプロイのトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)に関するページを参照してください。

その他のトラブルシューティングのヘルプについては、「[Azure Virtual Machines のトラブルシューティングに関するドキュメント](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)」を参照してください。


## <a name="next-steps"></a>次のステップ
- [Azure Cost Management および Billing のドキュメント](../cost-management-billing/index.yml)を参照してください。
- 自分のデプロイ計画には、[Azure の料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してください。
- お使いの VM の監視の詳細については、[Azure で仮想マシンを監視する方法](../azure-monitor/vm/monitor-vm-azure.md)に関するページを参照してください。