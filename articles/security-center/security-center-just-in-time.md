---
title: Azure Security Center での Just-In-Time 仮想マシン アクセス | Microsoft Docs
description: このドキュメントでは、Azure Security Center での Just-In-Time VM アクセスにより、Azure 仮想マシンへのアクセスを制御しやすくする方法を示します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: cc4e267c6912b8938db1ba5497a27f9c0026bd79
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887335"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する

Security Center の Standard 価格レベル ([価格](/azure/security-center/security-center-pricing)に関するページを参照) を使用している場合は、Just-In-Time (JIT) 仮想マシン (VM) アクセスを使用して、Azure VM へのインバウンド トラフィックをロックダウンすることができます。 これにより、必要に応じて簡単に VM に接続できるようになると同時に、攻撃に晒される危険性が低減されます。

> [!NOTE]
> Security Center の Just-In-Time VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 クラシック デプロイ モデルと Resource Manager デプロイ モデルの詳細については、[Azure Resource Manager とクラシック デプロイの比較](../azure-resource-manager/management/deployment-models.md)に関するページを参照してください。

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>VM で JIT を構成する

VM で JIT ポリシーを構成するには、3 つの方法があります。

- [Azure Security Center で JIT アクセスを構成する](#jit-asc)
- [Azure VM ページで JIT アクセスを構成する](#jit-vm)
- [VM でプログラムから JIT ポリシーを構成する](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Azure Security Center で JIT を構成する

Security Center から JIT ポリシーを構成し、JIT のポリシーを使用して VM へのアクセスを要求できます。

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Security Center で VM の JIT アクセスを構成する <a name="jit-asc"></a>

1. **[Security Center]** ダッシュボードを開きます。

1. 左のウィンドウで、 **[Just-in-time VM access] (Just-In-Time VM アクセス)** を選択します。

    ![[Just-in-time VM access] (Just-In-Time VM アクセス) タイル](./media/security-center-just-in-time/just-in-time.png)

    **[Just-In-Time VM アクセス]** ウィンドウが開いて、以下のような VM の状態に関する情報が表示されます。

    - **[構成済み]** - Just-In-Time VM アクセスをサポートするように構成されている VM。 表示されるデータは 1 週間以内のものであり、VM ごとに承認済みの要求の数、最終アクセス日時、最後のユーザーの情報が含まれます。
    - **[推奨]** - Just-In-Time VM アクセスをサポートできるが、そのように構成されてはいない VM。 これらの VM では Just-In-Time VM アクセス制御を有効にすることをお勧めします。
    - **[推奨なし]** - 以下のような VM には、ジャスト イン タイム VM アクセスは推奨されない場合があります。
      - NSG がない - Just-In-Time ソリューションには設定済みの NSG が必要です。
      - クラシック VM - Security Center の Just-In-Time VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 Just-In-Time ソリューションではクラシック デプロイはサポートされていません。 
      - その他 - VM がこのカテゴリに含まれるのは、サブスクリプションまたはリソース グループのセキュリティ ポリシー内で Just-In-Time ソリューションが無効になっている場合か、VM にパブリック IP と設定済みの NSG がない場合です。

1. **[推奨]** タブを選びます。

1. **[仮想マシン]** で、有効にする VM をクリックします。 VM の横にチェックマークが付きます。

      ![ジャストインタイム アクセスを有効にする](./media/security-center-just-in-time/enable-just-in-time.png)

1. **[<選択した VM 数> 台の VM で JIT を有効にする]** をクリックします。 ペインが開いて、Azure Security Center で推奨される既定のポートが表示されます。
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. 必要に応じて、一覧にカスタム ポートを追加できます。

      1. **[追加]** をクリックします。 **[ポート構成の追加]** ウィンドウが開きます。
      1. 既定でもカスタムでも、構成するポートごとに、以下の設定をカスタマイズできます。
            - **[プロトコルの種類]** - 要求が承認されたときにこのポートで許可されるプロトコル。
            - **[Allowed source IP addresses] (許可されるソース IP アドレス)** - 要求が承認されたときにこのポートで許可される IP 範囲。
            - **[最大要求時間]** - 特定のポートを開放しておくことができる最大時間枠。

     1. **[OK]** をクリックします。

1. **[保存]** をクリックします。

> [!NOTE]
>VM に対して JIT VM アクセスが有効になっている場合、Azure Security Center では、関連付けられているネットワーク セキュリティ グループと Azure Firewall 内の選択されたポートについて、"すべての受信トラフィックを拒否" という規則が作成されます。 選択したポートに対して他の規則が作成されている場合は、既存の規則が新しい "すべての受信トラフィックを拒否" 規則よりも優先されます。 選択したポートに既存の規則がない場合は、新しい "すべての着信トラフィックを拒否する" 規則がネットワーク セキュリティ グループと Azure Firewall で最優先されます。


## <a name="request-jit-access-via-security-center"></a>Security Center から JIT アクセスを要求する

Security Center から VM へのアクセス権を要求するには、次の手順に従います。

1. **[Just-in-time VM access] (Just-In-Time VM アクセス)** で、 **[構成済み]** タブを選択します。

1. **[仮想マシン]** で、アクセスを要求する VM をクリックします。 VM の横にチェックマークが付きます。

    - **[接続の詳細]** 列のアイコンは、NSG または FW で JIT が有効かどうかを示します。 両方が有効な場合は、ファイアウォールのアイコンのみ表示されます。

    - **[接続の詳細]** 列では、VM を接続するために必要な情報と、その開かれているポートが示されます。

      ![Just-In-Time アクセスを要求する](./media/security-center-just-in-time/request-just-in-time-access.png)

1. **[アクセス権の要求]** をクリックします。 **[アクセス権の要求]** ウィンドウが開きます。

      ![JIT の詳細](./media/security-center-just-in-time/just-in-time-details.png)

1. **[アクセス権の要求]** では、開放するポート、ポートの開放先のソース IP アドレス、ポートを開放しておく時間枠を VM ごとに構成します。 Just-In-Time ポリシーで構成されているポートへのアクセス権のみ要求できます。 ポートごとに Just-In-Time ポリシーから派生した最大許容時間があります。

1. **[ポートを開く]** を選択します。

> [!NOTE]
> アクセスを要求しているユーザーがプロキシの背後にいる場合は、**My IP** オプションが機能しない場合があります。 組織のすべての IP アドレス範囲を定義する必要がある場合があります。



## <a name="edit-a-jit-access-policy-via-security-center"></a>Security Center から JIT アクセス ポリシーを編集する

VM の既存の Just-In-Time ポリシーを変更する場合、その VM 用に保護する新しいポートを追加して構成するか、既に保護されているポートに関連するその他の設定を変更します。

VM の既存の Just-In-Time ポリシーを編集するには:

1. **[構成済み]** タブの **[VM]** で、ポートを追加する VM の行内にある 3 つの点をクリックしてその VM を選択します。 

1. **[編集]** を選択します。

1. **[JIT VM アクセス構成]** では、既に保護されているポートの既存の設定を編集するか、新しいカスタム ポートを追加できます。 
  ![JIT VM アクセス](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Security Center から JIT アクセス アクティビティを監査する

ログ検索を使用して VM アクティビティについての情報が得ることができます。 ログを表示するには、以下の手順に従います。

1. **[Just-in-time VM access] (Just-In-Time VM アクセス)** で、 **[構成済み]** タブを選択します。
2. **[VM]** で、情報を表示する VM の行内にある 3 つの点をクリックしてその VM を選択し、メニューから **[アクティビティ ログ]** を選択します。 **[アクティビティ ログ]** が開きます。

   ![[アクティビティ ログ] を選択する](./media/security-center-just-in-time/select-activity-log.png)

   **[アクティビティ ログ]** には、選択した VM の前回の操作、時間、日付、サブスクリプションがフィルター処理されて表示されます。

**[すべての項目を csv としてダウンロードするにはここをクリックしてください。]** を選択すると、ログ情報をダウンロードできます。

フィルターを変更し、 **[適用]** をクリックして検索ログを作成します。



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Azure VM のページから JIT アクセスを構成する <a name="jit-vm"></a>

作業を容易にするため、Security Center の VM のページ内から直接 JIT を使用して VM に接続できます。

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Azure VM ページで VM への JIT アクセスを構成する

VM への Just-In-Time アクセスのロールアウトを容易にするには、VM 内からの直接的な Just-In-Time アクセスのみを許可するように VM を設定できます。

1. [Azure portal](https://ms.portal.azure.com) から、 **[仮想マシン]** を検索して選択します。 
2. Just-In-Time アクセスに制限する仮想マシンを選択します。
3. メニューで **[構成]** を選択します。
4. **[Just-In-Time アクセス]** で、 **[Just-In-Time を有効にする]** を選択します。 

これにより、以下の設定を使用する VM の Just-In-Time アクセスが有効になります。

- Windows サーバー:
    - RDP ポート 3389
    - 3 時間の最大許容アクセス
    - 許可されるソース IP アドレスは [すべて] に設定されます
- Linux サーバー:
    - SSH ポート 22
    - 3 時間の最大許容アクセス
    - 許可されるソース IP アドレスは [すべて] に設定されます
     
VM で Just-In-Time が既に有効になっている場合、VM の構成ページに移動すると、Just-In-Time が有効になっていることが示され、リンクを使用して Azure Security Center でポリシーを開き、設定を確認および変更できます。

![VM での JIT の構成](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Azure VM のページで VM への JIT アクセスを要求する

Azure portal では、VM に接続しようとすると、Azure は、Just-In-Time アクセス ポリシーをその VM 上で構成しているかどうかを確認します。

- VM 上で JIT を構成している場合、 **[アクセスの要求]** をクリックすると、その VM 用に設定された JIT ポリシーに従ってアクセスを付与することができます。 

  >![JIT 要求](./media/security-center-just-in-time/jit-request.png)

  アクセスを要求するには、次の既定のパラメーターを使用します。

  - **接続元 IP アドレス**:’任意’ (*) (変更できません)
  - **時間範囲**:3 時間 (変更できません) <!--Isn't this set in the policy-->
  - **[ポート番号]** : Windows では RDP ポート 3389/Linux ではポート 22 (変更可能)

    > [!NOTE]
    > Azure Firewall によって保護されている VM に対する要求が承認されると、Security Center からユーザーに対して、VM に接続するために使用する適切な接続情報 (DNAT テーブルのポート マッピング) が提供されます。

- VM 上で JIT を構成していない場合、その VM 上で JIT ポリシーを構成するように求められます。

  ![JIT プロンプト](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>VM でプログラムから JIT ポリシーを構成する <a name="jit-program"></a>

REST API および PowerShell から Just-In-Time を設定し使用できます。

### <a name="jit-vm-access-via-rest-apis"></a>REST API による JIT VM アクセス

Just-In-Time VM アクセス機能は、Azure Security Center API から使用できます。 この API を使用すると、構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセスを要求したりすることができます。 Just-In-Time REST API について詳しくは、「[Jit Network Access Policies (JIT ネットワーク アクセス ポリシー)](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)」をご覧ください。

### <a name="jit-vm-access-via-powershell"></a>PowerShell による JIT VM アクセス

PowerShell による Just-In-Time VM アクセス ソリューションを使用するには、公式の Azure Security Center の PowerShell コマンド (つまり、`Set-AzJitNetworkAccessPolicy`) を使用します。

次の例では、特定の VM 上で Just-In-Time VM アクセス ポリシーを設定し、以下のように設定します。

1.    ポート 22 と 3389 を閉じます。

2.    承認された要求ごとに開けるように、それぞれ最大 3 時間の時間枠を設定します。
3.    アクセス権を要求するユーザーがソース IP アドレスを制御できるようにし、承認された Just-In-Time アクセス要求時に正常なセッションを確立できるようにします。

これを実現するには、PowerShell で以下を実行します。

1.    VM に対する Just-In-Time VM アクセス ポリシーを保持する変数を割り当てます。

        $JitPolicy = (@{    id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"   ports=(@{        number=22;        protocol="\*";        allowedSourceAddressPrefix=@("\*");        maxRequestAccessDuration="PT3H"},        @{        number=3389;        protocol="\*";        allowedSourceAddressPrefix=@("\*");        maxRequestAccessDuration="PT3H"})})

2.    VM の Just-In-Time VM アクセス ポリシーを配列に挿入します。
    
        $JitPolicyArr=@($JitPolicy)

3.    選択した VM で Just-In-Time VM アクセス ポリシーを構成します。
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>PowerShell から VM へのアクセスを要求する

次の例では、特定の IP アドレスおよび特定の期間にポート 22 を開くことを要求する、特定の VM への Just-In-Time VM アクセス要求が示されています。

PowerShell で以下を実行します。
1.    VM 要求アクセス プロパティを構成します。

        $JitPolicyVm1 = (@{     id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"   ports=(@{      number=22;      endTimeUtc="2018-09-17T17:00:00.3658798Z";      allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.    配列内に VM アクセス要求パラメーターを挿入します。

        $JitPolicyArr=@($JitPolicyVm1)
3.    要求アクセスを送信します (手順 1 で取得したリソース ID を使用)。

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

詳細については、[PowerShell コマンドレットに関するドキュメント](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)を参照してください。


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>冗長な JIT 規則の自動クリーンアップ 

JIT ポリシーを更新するたびに、クリーンアップ ツールが自動的に実行され、規則セット全体の有効性がチェックされます。 このツールでは、ポリシー内の規則と NSG 内の規則の不一致が検索されます。 クリーンアップ ツールで不一致が検出されると、原因が特定され、削除した方が安全と見なされる場合には不要な組み込み規則が削除されます。 ユーザーが作成した規則がこのツールによって削除されることはありません。

クリーンアップ ツールによって組み込みルールが削除される可能性のあるシナリオの例:

- 同一の定義を持つ 2 つの規則が存在し、一方がもう一方より優先度が高い場合 (つまり、優先度の低い規則は使用されません)
- 規則の説明に、規則内の宛先 IP と一致しない VM 名が含まれている場合 


## <a name="next-steps"></a>次のステップ

この記事では、Security Center の Just-In-Time VM アクセスを活用して Azure 仮想マシンへのアクセスを制御する方法について説明しました。

セキュリティ センターの詳細については、次を参照してください。

- Microsoft Learn モジュール「[Azure Security Center を使用して、ブルート フォースおよびマルウェア攻撃からサーバーと VM を保護する](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)」
- [セキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [セキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
- [セキュリティ正常性の監視](security-center-monitoring.md) -- Azure リソースの正常性を監視する方法について説明します。
