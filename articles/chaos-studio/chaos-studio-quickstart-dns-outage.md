---
title: Azure Chaos Studio を使って、ネットワーク セキュリティ グループの障害を使用したインターネットの DNS 停止を再現する
description: Chaos Studio の使用を開始するには、ネットワーク セキュリティ グループの障害を使用して DNS 停止を作成します。
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 08/26/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc7a04c7bd20f1b778aa23f163b57161c0fe7745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091679"
---
# <a name="quickstart-internet-dns-outage-using-network-security-group-fault"></a>クイック スタート: ネットワーク セキュリティ グループの障害を使用したインターネットの DNS 停止

ネットワーク セキュリティ グループの障害を使用すると、カオス実験の一環として、既存のネットワーク セキュリティ グループ規則を変更できます。 この障害を使用すると、接続の喪失や依存リソースの停止をシミュレートして、Azure リソースへのネットワーク トラフィックをブロックできます。 このクイック スタートでは、外部 (インターネット) の DNS サーバーへのすべてのトラフィックを 15 分間ブロックするカオス実験を作成します。 この実験を使用すると、ターゲットのネットワーク セキュリティ グループに関連付けられている Azure 仮想ネットワークに接続されているリソースが外部の DNS サーバーに依存していないことを検証でき、リスク脅威モデル要件の 1 つを検証できます。

## <a name="prerequisites"></a>前提条件

- 実験でターゲットとする、Azure リソースに関連付けられているネットワーク セキュリティ グループ。  
- [Azure Cloud Shell](../cloud-shell/quickstart.md) で Bash 環境を使用します。

## <a name="create-the-network-security-group-fault-provider"></a>ネットワーク セキュリティ グループの障害プロバイダーを作成する

まず、Chaos Studio から操作できるように、ネットワーク セキュリティ グループがホストされているサブスクリプションに障害プロバイダーを登録します。

1. 次の内容を含めた **AzureNetworkSecurityGroupChaos.json** という名前のファイルを作成して、ローカル コンピューターに保存します。

      ```json
      { 
        "properties": {
          "enabled": true,
          "providerConfiguration": {
            "type": "AzureNetworkSecurityGroupChaos"
          }
        }
      }
      ```

1. [Cloud Shell](https://shell.azure.com/) を起動します。
1. **$SUBSCRIPTION_ID** を、実験で使用するネットワーク セキュリティ グループが含まれる Azure サブスクリプション ID に置き換え、次のコマンドを実行して、プロバイダーが確実に正しいサブスクリプションに登録されるようにします。

    ```bash
    az account set --subscription $SUBSCRIPTION_ID
    ```

1. **AzureNetworkSecurityGroupChaos.json** をクラウド シェル ウィンドウにドラッグ アンド ドロップして、ファイルをアップロードします。
1. 前のステップで使用した **$SUBSCRIPTION_ID** を置き換え、次のコマンドを実行してAzureNetworkSecurityGroupChaos 障害プロバイダーを登録します。

    ```bash
    az rest --method put --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --body @AzureNetworkSecurityGroupChaos.json --resource "https://management.azure.com"
    ```

1. (省略可能) 先ほど作成した **AzureNetworkSecurityGroupChaos.json** ファイルはもう必要ないため削除し、Cloud Shell を閉じます。

## <a name="create-a-chaos-experiment"></a>カオス実験を作成する

ネットワーク セキュリティ グループの障害プロバイダーが作成されると、Chaos Studio で実験の作成を開始できるようになります。

1. Azure portal を Chaos Studio 機能フラグを使用して開きます。
    * @microsoft.com アカウントを使用している場合は、[こちらのリンクをクリック](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)してください。
    * 外部アカウントを使用している場合は、[こちらのリンクをクリック](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})してください。
1. **[Add an experiment]\(実験を追加する\)** をクリックします。

    ![Azure portal で実験を追加する](images/add-an-experiment.png)

1. 実験に付ける名前を入力し、実験を作成するサブスクリプション、リソース グループ、および場所 (リージョン) を選択します。
1. **[Next: Experiment designer >]\(次へ: 実験デザイナー >\)** をクリックします。

    ![Azure portal で実験を作成する](images/create-an-experiment-dns-outage.png)

1. **[Add fault]\(障害の追加\)** をクリックします。
1. 障害ドロップダウン メニューから **[network security group Fault]\(ネットワーク セキュリティ グループの障害\)** を選択します。

    ![Azure portal で NSG 障害を選択する](images/network-security-group-fault.png)

1. 次のパラメーターを設定します。

    | パラメーター | 値 |
    | -- | -- |
    | Duration | `15` |
    | 名前 | `block_internet_dns` |
    | ソース アドレスのプレフィックス | `*` |
    | 宛先アドレスのプレフィックス | `Internet` |
    | Access | `Deny` |
    | ソース ポートの範囲 | `*` |
    | 宛先ポートの範囲 | `53` |
    | 優先度 | `1001` |
    | Direction | `Outbound` |

    ![Azure portal でのネットワーク セキュリティ グループ パラメーター](images/qs-network-outage-dns-parameters.png)

> [!NOTE]
> ターゲット ネットワーク セキュリティ グループの既存の規則に既に存在する場合は、名前と優先度のフィールドの調整が必要になることがあります。

> [!NOTE]
> インターネットへのポート 53 トラフィックを明示的に許可する規則がある場合は、優先度を下げる処理が必要になることがあります。

1. **[Next: Target resources >]\(次へ: ターゲット リソース >\)** をクリックします。
1. この実験で対象にするネットワーク セキュリティ グループを選択します。
1. **[追加]** をクリックします。

    ![ターゲット リソースを選択する](images/nsg-fault-targets.png)

1. **[確認と作成]** をクリックします

    ![実験を確認して作成する](images/review-create.png)

1. **[作成]**

    ![[Create experiment]\(実験の作成\) ボタン](images/create.png)

## <a name="grant-the-chaos-experiment-access-to-the-network-security-group"></a>カオス実験にネットワーク セキュリティ グループへのアクセスを許可する

安全上の予防措置として、すべてのカオス実験には、実験の対象にする Azure リソースへのアクセス権を付与する必要があります。

1. 実験の対象にするリソースに移動します。
1. **[アクセス制御 (IAM)]** をクリックします

    ![アクセス権を変更する](images/access-control.png)

1. **[追加]** をクリックします。

    ![[追加] ボタン](images/add.png)

1. **[ロールの割り当ての追加]** をクリックします

    ![[ロールの割り当ての追加] ボタン](images/add-role-assignment.png)

1. **[ロール]** で、`Network Contributor` を選択します

    ![ネットワーク共同作成者を選択する](images/role-network-contributor.png)

1. **[選択]** にカオス実験の名前を入力し、それをクリックします。

    ![実験名を選択する](images/qs-network-outage-dns-select.png)

1. **[保存]**

    ![変更を保存する](images/save-discard.png)

## <a name="run-the-chaos-experiment"></a>カオス実験を実行する

1. Azure portal を Chaos Studio 機能フラグを使用して開きます。
    * @microsoft.com アカウントを使用している場合は、[こちらのリンクをクリック](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)してください。
    * 外部アカウントを使用している場合は、[このリンクをクリックします](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})。
1. 実験名の横にあるチェック ボックスをオンにし、 **[Start Experiment]\(実験の開始\)** をクリックします。

    ![実験を開始する](images/qs-network-outage-dns-start.png)

1. **[はい]** をクリックして、カオス実験を開始することを確認します。

    ![実験を開始することを確認する](images/start-experiment-confirmation.png)
1. (省略可能) 実験名をクリックすると、実験の実行状態の詳細ビューが表示されます。
1. 実験が開始したら、監視、テレメトリ、ログの既存ツールを使用して、カオス実験の実行がサービスに与えた影響を確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この実験を引き続き使用せず、削除したい場合は、次の手順に従います。

Azure portal を Chaos Studio 機能フラグを使用して開きます。
    * @microsoft.com アカウントを使用している場合は、[こちらのリンクをクリック](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)してください。
    * 外部アカウントを使用している場合は、[こちらのリンクをクリック](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})してください。
1. 実験名の横にあるチェック ボックスをオンにし、 **[削除]** をクリックします。
1. **[はい]** をクリックして実験を削除することを確認します。

ネットワーク セキュリティ グループに関連する障害を引き続き使用しない場合は、次の手順に従います。

1. [Cloud Shell](https://shell.azure.com/) を起動します。
1. **$SUBSCRIPTION_ID** を、ネットワーク セキュリティ グループの障害プロバイダーがプロビジョニングされた Azure サブスクリプション ID に置き換え、次のコマンドを実行します。

    ```bash
    az rest --method delete --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --resource "https://management.azure.com"
    ```
