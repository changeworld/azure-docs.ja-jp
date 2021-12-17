---
title: Microsoft Defender for Cloud での Just-In-Time 仮想マシンアクセス | Microsoft Docs
description: Microsoft Defender for Cloud で Just-In-Time VM アクセス (JIT) を使用して、Azure 仮想マシンへのアクセスを制御する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: f1eb796873ceffced83a63c077ced3fa1a838c8b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525599"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud の Just-In-Time (JIT) 仮想マシン (VM) アクセス機能を使用して、Azure Virtual Machines への受信トラフィックをロックダウンします。 これにより、必要に応じて簡単に VM に接続できるようになる一方で、攻撃に晒される危険性は低くなります。

JIT の動作と基になるロジックの詳細については、[Just-In-Time の説明](just-in-time-access-overview.md)に関するページを参照してください。

このページでは、セキュリティ プログラムに JIT を組み込む方法について説明します。 学習内容は次のとおりです。 

- **VM で JIT を有効にする** - Defender for Cloud、PowerShell、または REST API を使用して、独自のカスタム オプションで 1 つ以上の VM に対して JIT を有効にすることができます。 または、Azure 仮想マシンから、ハードコーディングされた既定のパラメーターで JIT を有効にすることもできます。 有効にすると、JIT により、ネットワーク セキュリティ グループに規則が作成されて、Azure VM への受信トラフィックがロックダウンされます。
- **JIT が有効になっている VM へのアクセス権を要求する** - JIT の目的は、受信トラフィックがロックダウンされていても、必要に応じて Defender for Cloud で VM に簡単に接続できるようにすることです。 Defender for Cloud、Azure 仮想マシン、PowerShell、または REST API から、JIT が有効な VM へのアクセスを要求できます。
- **アクティビティを監査する** - VM が適切にセキュリティで保護されていることを確実にするため、通常のセキュリティ チェックの一環として、JIT 対応の VM へのアクセスを確認します。   



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|サポートされている VM:|:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Resource Manager を使用してデプロイされた VM。<br>:::image type="icon" source="./media/icons/no-icon.png"::: クラシック デプロイ モデルを使用してデプロイされた VM。 [これらのデプロイ モデルの詳細](../azure-resource-manager/management/deployment-models.md)。<br>:::image type="icon" source="./media/icons/no-icon.png"::: [Azure Firewall Manager](../firewall-manager/overview.md) によって制御される Azure Firewall によって保護されている VM|
|必要なロールとアクセス許可:|**閲覧者** と **セキュリティ閲覧者** ロールは、JIT の状態とパラメーターの両方を表示できます。<br>JIT で動作するカスタム ロールを作成するには、「[JIT を構成して使用するために必要なアクセス許可は何ですか?](just-in-time-access-overview.md#what-permissions-are-needed-to-configure-and-use-jit)」を参照してください。<br>VM への JIT アクセスを要求する必要があり、他の JIT 操作を実行しないユーザーに対して最小特権のロールを作成するには、Defender for Cloud GitHub コミュニティ ページにある [Set-JitLeastPrivilegedRole スクリプト](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role)を使用します。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||


## <a name="enable-jit-vm-access"></a>JIT VM アクセスを有効にする <a name="jit-configure"></a>

Defender for Cloud またはプログラムを使用して、独自のカスタム オプションで、1 つ以上の VM に対して JIT VM アクセスを有効にすることができます。 

または、Azure 仮想マシンから、ハードコーディングされた既定のパラメーターで JIT を有効にすることもできます。

これらの各オプションについては、以下の個別のタブで説明します。

### <a name="microsoft-defender-for-cloud"></a>[**Microsoft Defender for Cloud**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud から VM で JIT を有効にする <a name="jit-asc"></a>

:::image type="content" source="./media/just-in-time-access-usage/jit-config-security-center.gif" alt-text="Microsoft Defender for Cloud で JIT VM アクセスを構成する。":::

Defender for Cloud では、JIT VM アクセスの有効化と構成を行うことができます。

1. **ワークロード保護ダッシュボード** を開き、[高度な保護] 領域から **[Just-In-Time VM アクセス]** を選びます。

    **[Just-In-Time VM アクセス]** ページが開き、VM が次のタブにグループ化されます。

    - **[構成済み]** - Just-In-Time VM アクセスをサポートするように既に構成されている VM。 [構成済み] タブには VM ごとに以下が表示されます。
        - 過去 7 日間に承認された JIT 要求の数
        - 最終アクセス日時
        - 構成されている接続の詳細
        - 最後のユーザー
    - **[未構成]** - JIT は有効になっていないが、JIT をサポートできる VM。 これらの VM では JIT を有効にすることをお勧めします。
    - **[サポート外]** - JIT が有効になっておらず、機能をサポートしていない VM。 VM は次の理由によりこのタブに表示される場合があります。
      - ネットワーク セキュリティ グループ (NSG) または Azure ファイアウォールが見つからない - JIT では、NSG が構成されていることか、ファイアウォール構成 (またはその両方) が必要です
      - クラシック VM - Azure Resource Manager を使用してデプロイされた VM は JIT でサポートされますが、"クラシック デプロイ" はサポートされません。 [クラシックと Azure Resource Manager のデプロイ モデルの詳細](../azure-resource-manager/management/deployment-models.md)。
      - その他 - サブスクリプションまたはリソース グループのセキュリティ ポリシーで JIT ソリューションが無効になっている場合は、VM がこのタブに表示されることがあります。

1. **[未構成]** タブで、JIT で保護する VM をマークし、 **[VM で JIT を有効にする]** を選択します。 

    [JIT VM アクセス] ページが開き、Defender for Cloud で保護が推奨されるポートの一覧が表示されます。
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    既定の設定をそのまま使用するには、 **[保存]** を選択します。

1. JIT オプションをカスタマイズするには:

    - **[追加]** ボタンを使用してカスタム ポートを追加します。 
    - 一覧から既定のポートのいずれかを選択して変更します。

    ポート (カスタムおよび既定) ごとに、 **[ポート構成の追加]** ペインで次のオプションが提供されます。

    - **[プロトコル]** - 要求が承認されたときにこのポートで許可されるプロトコル
    - **[許可されているソース IP]** - 要求が承認されたときにこのポートで許可される IP 範囲
    - **[最大要求時間]** - 特定のポートを開放しておくことができる最大時間枠

     1. 必要に応じてポートのセキュリティを設定します。

     1. **[OK]** を選択します。

1. **[保存]** を選択します。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-defender-for-cloud"></a>Defender for Cloud を使用して JIT 対応 VM で JIT の構成を編集する <a name="jit-modify"></a>

VM に対して保護する新しいポートを追加して構成するか、既に保護されているポートに関連する他の設定を変更すると、VM の Just-In-Time の構成を変更できます。

VM の既存の JIT 規則を編集するには:

1. **ワークロード保護ダッシュボード** を開き、[高度な保護] 領域から **[Just-In-Time VM アクセス]** を選びます。

1. **[構成済み]** タブで、ポートを追加する VM を右クリックして、[編集] を選択します。 

    ![Microsoft Defender for Cloud で JIT VM アクセス構成を編集する。](./media/just-in-time-access-usage/jit-policy-edit-security-center.png)

1. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。

1. ポートの編集が終わったら、 **[保存]** を選択します。
 


### <a name="azure-virtual-machines"></a>[**Azure 仮想マシン**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Azure 仮想マシンから VM で JIT を有効にする

Azure portal の Azure 仮想マシンページから、VM での JIT を有効にすることができます。

![Azure 仮想マシンでの JIT VM アクセスの構成。](./media/just-in-time-access-usage/jit-config-virtual-machines.gif)

> [!TIP]
> VM で Just-In-Time が既に有効になっている場合、その構成ページに移動すると、Just-In-Time が有効になっていることが示されます。リンクを使用して Defender for Cloud で [Just-In-Time VM アクセス] ページを開き、設定を表示して変更することができます。

1. [Azure portal](https://ms.portal.azure.com) から、 **[仮想マシン]** を検索して選択します。 

1. JIT で保護する仮想マシンを選択します。

1. メニューで **[構成]** を選択します。

1. **[Just-In-Time アクセス]** で、 **[Just-In-Time を有効にする]** を選択します。 

    これにより、以下の既定の設定を使用する VM の Just-In-Time アクセスが有効になります。

    - Windows マシン:
        - RDP ポート 3389
        - 3 時間の最大許容アクセス
        - 許可されるソース IP アドレスは [すべて] に設定されます
    - Linux マシン:
        - SSH ポート 22
        - 3 時間の最大許容アクセス
        - 許可されるソース IP アドレスは [すべて] に設定されます

1. これらの値のいずれかを編集するか、JIT 構成にさらにポートを追加するには、Microsoft Defender for Cloud の Just-In-Time ページを使用します。

    1. Defender for Cloud のメニューから、 **[Just-In-Time VM アクセス]** を選びます。

    1. **[構成済み]** タブで、ポートを追加する VM を右クリックして、[編集] を選択します。 

        ![Microsoft Defender for Cloud で JIT VM アクセス構成を編集する。](./media/just-in-time-access-usage/jit-policy-edit-security-center.png)

    1. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。

    1. ポートの編集が終わったら、 **[保存]** を選択します。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>PowerShell を使用して VM で JIT を有効にする

PowerShell から Just-In-Time VM アクセスを有効にするには、公式の Microsoft Defender for Cloud PowerShell コマンドレット `Set-AzJitNetworkAccessPolicy` を使用します。

**例** - 次の規則を使用して、特定の VM で Just-In-Time VM アクセスを有効にします。

* ポート 22 と 3389 を閉じる
* 承認された要求ごとに開けるように、それぞれ最大 3 時間の時間枠を設定する
* アクセス権を要求しているユーザーがソース IP アドレスを制御するのを許可する
* アクセス権を要求しているユーザーが、承認された Just-In-Time アクセス要求時に正常なセッションを確立するのを許可する

以下の PowerShell コマンドで、この JIT 構成が作成されます。

1. VM に対する Just-In-Time VM アクセス規則を保持する変数を割り当てます。

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. VM の Just-In-Time VM アクセス規則を配列に挿入します。
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 選択した VM で Just-In-Time VM アクセス規則を構成します。
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    -Name パラメーターを使用して VM を指定します。 たとえば、2 つの異なる VM (VM1 と VM2) の JIT 構成を確立するには、```Set-AzJitNetworkAccessPolicy -Name VM1``` と ```Set-AzJitNetworkAccessPolicy -Name VM2``` を使用します。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>REST API を使用して VM で JIT を有効にする

Just-In-Time VM アクセス機能は、Microsoft Defender for Cloud API を介して使用できます。 構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセス権を要求したりするには、この API を使用します。 

詳細については、「[JIT ネットワーク アクセス ポリシー](/rest/api/securitycenter/jitnetworkaccesspolicies)」を参照してください。

---

## <a name="request-access-to-a-jit-enabled-vm"></a>JIT が有効な VM へのアクセス権を要求する

Azure portal (Defender for Cloud または Azure 仮想マシン) またはプログラムから、JIT が有効な VM へのアクセスを要求できます。

これらの各オプションについては、以下の個別のタブで説明します。

### <a name="microsoft-defender-for-cloud"></a>[**Microsoft Defender for Cloud**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud から JIT が有効な VM へのアクセスを要求する 

VM で JIT が有効になっている場合は、それに接続するためのアクセス権を要求する必要があります。 JIT を有効にした方法に関係なく、サポートされているいずれかの方法でアクセス権を要求できます。

:::image type="content" source="./media/just-in-time-access-usage/jit-request-security-center.gif" alt-text="Defender for Cloud から JIT アクセスを要求する。":::

1. **[Just In Time VM アクセス]** ページで、 **[構成済み]** タブを選択します。

1. アクセスしたい VM をマークします。

    - **[接続の詳細]** 列のアイコンによって、ネットワーク セキュリティ グループまたはファイアウォールで JIT が有効になっているかどうかが示されています。 両方で有効になっている場合は、ファイアウォールのアイコンだけが表示されます。

    - **[接続の詳細]** 列では、VM を接続するために必要な情報と、その開かれているポートが示されます。

1. **[アクセス権の要求]** を選択します。 **[アクセス権の要求]** ウィンドウが開きます。

1. **[アクセス権の要求]** では、開放するポート、ポートの開放先のソース IP アドレス、ポートを開放しておく時間枠を VM ごとに構成します。 構成済みのポートへのアクセス権だけを要求することができます。 ポートごとに、作成した JIT 構成から派生した最大許容時間があります。

1. **[Open ports]\(ポートを開く\)** を選択します。

> [!NOTE]
> アクセスを要求しているユーザーがプロキシの背後にいる場合は、**My IP** オプションが機能しない場合があります。 組織のすべての IP アドレス範囲を定義する必要がある場合があります。



### <a name="azure-virtual-machines"></a>[**Azure 仮想マシン**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Azure 仮想マシンの接続ページから JIT が有効な VM へのアクセス権を要求する

VM で JIT が有効になっている場合は、それに接続するためのアクセス権を要求する必要があります。 JIT を有効にした方法に関係なく、サポートされているいずれかの方法でアクセス権を要求できます。

  >![jit Just-In-Time 要求。](./media/just-in-time-access-usage/jit-request-vm.png)


Azure 仮想マシンからアクセス権を要求するには:

1. Azure portal で仮想マシンのページを開きます。

1. 接続先の VM を選択し、 **[接続]** ページを開きます。

    Azure により、その VM で JIT が有効になっているかどうかが確認されます。

    - VM に対して JIT が有効になっていない場合は、有効にするように求められます。

    - JIT が有効になっている場合は、 **[アクセス権の要求]** を選択し、要求元の IP、時間の範囲、その VM 用に構成されているポートを指定して、アクセス要求を渡します。

> [!NOTE]
> Azure Firewall によって保護されている VM に対する要求が承認されると、Defender for Cloud からユーザーに対して、VM に接続するために使用する適切な接続情報 (DNAT テーブルのポート マッピング) が提供されます。



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>PowerShell を使用して JIT が有効な VM へのアクセス権を要求する

次の例では、特定の IP アドレスおよび特定の期間にポート 22 を開くことを要求する、特定の VM への Just-In-Time VM アクセス要求が示されています。

PowerShell で以下を実行します。

1. VM 要求アクセス プロパティを構成します。

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. 配列内に VM アクセス要求パラメーターを挿入します。

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 要求アクセスを送信します (ステップ 1 のリソース ID を使用)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

詳細については、[PowerShell コマンドレットに関するドキュメント](/powershell/scripting/developer/cmdlet/cmdlet-overview)を参照してください。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>REST API を使用して JIT が有効な VM へのアクセス権を要求する

Just-In-Time VM アクセス機能は、Microsoft Defender for Cloud API を介して使用できます。 構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセス権を要求したりするには、この API を使用します。 

詳細については、「[JIT ネットワーク アクセス ポリシー](/rest/api/securitycenter/jitnetworkaccesspolicies)」を参照してください。

---

## <a name="audit-jit-access-activity-in-defender-for-cloud"></a>Defender for Cloud での JIT アクセス アクティビティの監査

ログ検索を使用して VM アクティビティについての情報が得ることができます。 ログを表示するには:

1. **[Just-In-Time VM アクセス]** で、 **[構成済み]** タブを選択します。

1. 監査対象の VM で、行の最後にある省略記号メニューを開きます。
 
1. メニューから **[アクティビティ ログ]** を選択します。

   ![Just-In-Time JIT アクティビティ ログの選択。](./media/just-in-time-access-usage/jit-select-activity-log.png)

   アクティビティ ログによって、その VM に対する前回の操作、時刻、日付、サブスクリプションがフィルター処理されて表示されます。

1. ログ情報をダウンロードするには、 **[CSV としてダウンロードする]** を選択します。



## <a name="next-steps"></a>次の手順

この記事では、Just-In-Time VM アクセスを構成して使用する "_方法_" について説明しました。 JIT を使用する必要がある "_理由_" については、防御対象の脅威について説明されている概念記事を参照してください。

> [!div class="nextstepaction"]
> [JIT の説明](just-in-time-access-overview.md)
