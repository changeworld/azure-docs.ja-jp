---
title: ストレージ ポリシーの構成
description: Azure VMware ソリューションの仮想マシンのストレージ ポリシーを構成する方法について説明します。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: df8f6931dbae377833d3f380e3fd5fcad8bd73ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431334"
---
# <a name="configure-storage-policy"></a>ストレージ ポリシーの構成

vSAN ストレージ ポリシーには、仮想マシン (VM) のストレージ要件が定義されています。 これらのポリシーによって、ストレージを VM に割り当てる方法が決まるため、VM に必要なサービス レベルを確保できます。 vSAN データストアにデプロイされた各 VM には、少なくとも 1 つの VM ストレージ ポリシーが割り当てられます。

VM ストレージ ポリシーは、VM の初期デプロイで割り当てることも、複製や移行などの他の VM 操作を実行するときに割り当てることもできます。 デプロイ後の cloudadmin ユーザーまたは同等のロールでは、VM の既定のストレージ ポリシーを変更できません。 ただし、ディスクあたりの **VM ストレージ ポリシー** の変更は許可されています。 

実行コマンドを使用すると、承認されたユーザーは、既定または既存の VM ストレージ ポリシーを、デプロイ後の VM で使用可能なポリシーに変更できます。 ディスクレベルの VM ストレージ ポリシーに加えられた変更はありません。 ディスクレベルの VM ストレージ ポリシーは、要件に従っていつでも変更できます。


>[!NOTE]
>実行コマンドは、送信された順序で一度に 1 つずつ実行されます。


この方法ガイドでは、以下を行う方法について説明します。

> [!div class="checklist"]
> * すべてのストレージ ポリシーを一覧表示する
> * VM のストレージ ポリシーを設定する
> * クラスターのストレージ ポリシーを指定する



## <a name="prerequisites"></a>前提条件

[ホストの最小レベルが満たされている](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html)ことを確認します。

|  **RAID 構成** | **許容エラー (FTT)** | **最小ホスト要件** |
| --- | :---: | :---: |
| RAID-1 (ミラーリング) <br />既定の設定。  | 1  | 3  |
| RAID-5 (イレイジャー コーディング)  | 1  | 4  |
| RAID-1 (ミラーリング)  | 2  | 5  |
| RAID-6 (イレイジャー コーディング)  | 2  | 6  |
| RAID-1 (ミラーリング)  | 3  | 7  |


 

## <a name="list-storage-policies"></a>ストレージ ポリシーの一覧表示

VM に設定可能な vSAN ベースのストレージ ポリシーを一覧表示するには、`Get-StoragePolicy` コマンドレットを使用します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Get-StoragePolicies]** を選択します。

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="使用可能なストレージ ポリシー実行コマンドにアクセスする方法を示すスクリーンショット。" lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="利用可能な記憶域ポリシーを一覧表示する方法を示すスクリーンショット。":::
   
   | **フィールド** | **Value** |
   | --- | --- |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字から成る名前。たとえば **Get-StoragePolicies-Exec1**。 |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。




## <a name="set-storage-policy-on-vm"></a>VM でストレージ ポリシーを設定する

`Set-AvsVMStoragePolicy` コマンドレットを実行して、個々の VM で vSAN ベースのストレージ ポリシーを修正します。 

>[!NOTE]
>VSphere クライアントを使用して、VM の既定のストレージ ポリシーや既存のストレージ ポリシーを変更することはできません。 

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Set-AvsVMStoragePolicy]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **VMName** | ターゲット VM の名前。 |
   | **StoragePolicyName** | 設定するストレージ ポリシーの名前。 たとえば、**RAID-FTT-1**。 |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字から成る名前。たとえば **changeVMStoragePolicy**。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。


## <a name="specify-storage-policy-for-a-cluster"></a>クラスターのストレージ ポリシーを指定する

`Set-ClusterDefaultStoragePolicy` コマンドレットを実行して、クラスターの既定のストレージ ポリシーを指定します。

>[!NOTE]
>既定の管理クラスターのストレージ ポリシーを変更することは許可されていません。

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Set-ClusterDefaultStoragePolicy]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ClusterName** | クラスターの名前。 |
   | **StoragePolicyName** | 設定するストレージ ポリシーの名前。 たとえば、**RAID-FTT-1**。 |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字から成る名前。たとえば **Set-ClusterDefaultStoragePolicy-Exec1**。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。



## <a name="next-steps"></a>次のステップ

これで、vSAN ストレージ ポリシーを構成する方法を学習できました。詳細については、以下を参照してください。

- [ディスク プールを Azure VMware Solution ホストにアタッチする方法 (プレビュー)](attach-disk-pools-to-azure-vmware-solution-hosts.md) - 最適なコストとパフォーマンスを実現するために、Azure VMware Solution の永続ストレージとしてディスクを使用できます。

- [vCenter の外部 ID を構成する方法](configure-identity-source-vcenter.md) - vCenter には cloudadmin という組み込みのローカル ユーザーがあり、CloudAdmin ロールに割り当てられています。 このローカルの cloudadmin ユーザーを使用して、Active Directory (AD) にユーザーが設定されます。 実行コマンド機能を使用すると、Active Directory over LDAP または LDAPS for vCenter を外部 ID ソースとして構成できます。
