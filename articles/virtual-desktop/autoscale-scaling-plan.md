---
title: Azure Virtual Desktop セッション ホストの自動スケーリング (プレビュー)
description: 自動スケーリング機能を使用してデプロイでリソースを割り当てる方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 644857c552b6e54d94746746f1c2ba9a230baeb6
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181537"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Azure Virtual Desktop ホスト プールの自動スケーリング (プレビュー)

> [!IMPORTANT]
> 自動スケール機能は、現在プレビューの段階にあります。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

自動スケーリング機能 (プレビュー) を使用すると、Azure Virtual Desktop デプロイの仮想マシン (VM) をスケールアップまたはスケールダウンして、デプロイ コストを最適化できます。 ニーズに応じて、次に基づいてスケーリング プランを作成できます。

- 時刻
- 特定の曜日
- セッション ホストごとのセッションの制限

>[!NOTE]
>Windows Virtual Desktop (クラシック) では、自動スケーリング機能はサポートされていません。 エフェメラル ディスクのスケーリングもサポートされていません。

最適な結果を得るには、Azure Virtual Desktop Azure Resource Manager テンプレートまたは Microsoft のファーストパーティ ツールを使用してデプロイした VM で自動スケーリングを使用することをお勧めします。

>[!IMPORTANT]
>現在、この機能のプレビュー バージョンには次の制限があります。
>
> - 自動スケーリングは、Azure パブリック クラウドでのみ使用できます。
> - 自動スケーリングは、Azure portal でのみ構成できます。
> - スケーリング プランをデプロイできるのは、米国とヨーロッパのリージョンのみです。

## <a name="requirements"></a>要件

最初のスケーリング プランを作成する前に、こちらのガイドラインに従っていることを確認します。

- 現在、自動スケーリングは、プールされた既存のホスト プールでのみ構成できます。
- 自動スケーリングするすべてのホスト プールに、MaxSessionLimit パラメーターが構成されている必要があります。 既定値は使用しないでください。 この値は、Azure portal のホスト プール設定で構成するか、PowerShell で [New-AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) または [Update-AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) のコマンドレットを実行して構成します。
- VM コンピューティング リソースの電源を管理するには、Azure Virtual Desktop へのアクセスを許可する必要があります。

## <a name="create-a-custom-rbac-role"></a>カスタム RBAC ロールを作成する

スケーリング プランの作成を開始するには、まず、サブスクリプションでカスタムのロールベースのアクセス制御 (RBAC) ロールを作成する必要があります。 このロールを使用すると、Windows Virtual Desktop でサブスクリプション内のすべての VM の電源を管理できます。 また、アクティブなユーザー セッションがない場合、サービスでホスト プールと VM の両方にアクションを適用させることもできます。

カスタム ロールを作成するには、この JSON テンプレートを使用して、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」の指示に従います。

```json
 "properties": {
 "roleName": "Autoscale",
 "description": "Friendly description.",
 "assignableScopes": [
 "/subscriptions/<SubscriptionID>"
 ],
  "permissions": [
   {
   "actions": [
                      "Microsoft.Insights/eventtypes/values/read",
           "Microsoft.Compute/virtualMachines/deallocate/action",
                      "Microsoft.Compute/virtualMachines/restart/action",
                      "Microsoft.Compute/virtualMachines/powerOff/action",
                      "Microsoft.Compute/virtualMachines/start/action",
                      "Microsoft.Compute/virtualMachines/read",
                      "Microsoft.DesktopVirtualization/hostpools/read",
                      "Microsoft.DesktopVirtualization/hostpools/write",
                      "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
                      "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
                      "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",                   "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
],
  "notActions": [],
  "dataActions": [],
  "notDataActions": []
  }
 ]
}
}
```

## <a name="assign-custom-roles"></a>カスタム ロールを割り当てる

次に、Azure portal を使用して、作成したカスタム ロールをサブスクリプションに割り当てる必要があります。

カスタム ロールを割り当てるには、次のようにします。

1. Azure portal を開き、 **[サブスクリプション]** に移動します。

2. 次のスクリーンショットに示すように、画面の左上隅にある **[+]** ボタンを選択し、ドロップダウン メニューから **[カスタム ロールの追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure portal コントロール パネルのプラス記号ボタンを選択したときに表示されるドロップダウン メニューを示すスクリーンショット。 [カスタム ロールの追加] オプションが選択され、赤色の枠線で強調表示されています。](media/add-custom-role.png)

3. 次に、カスタム ロールに名前を指定し、説明を追加します。 このロールに "自動スケーリング" という名前を付けることをお勧めします。

4. **アクセス許可** タブで、このロールを割り当てるサブスクリプションに次のアクセス許可を追加します。

    ```azcopy
    "Microsoft.Compute/virtualMachines/deallocate/action", 
    "Microsoft.Compute/virtualMachines/restart/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action", 
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/read",
    "Microsoft.DesktopVirtualization/hostpools/read",
    "Microsoft.DesktopVirtualization/hostpools/write",
    "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
    "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
    "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
    "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
    "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
    ```

5. 完了したら、 **[OK]** をクリックします。

その後、ロールを割り当てて Azure Virtual Desktop へのアクセスを許可する必要があります。

アクセスを許可するカスタム ロールを割り当てるには、次のようにします。

1. **[アクセス制御 (IAM)]** タブで、 **[Add role assignments]\(ロールの割り当ての追加\)** を選択します。

2. 先ほど作成したロールを選択します。

3. 次のスクリーンショットに示されているように、検索バーに「**Windows Virtual Desktop**」と入力して選択します。

    > [!div class="mx-imgBorder"]
    > ![[ロールの割り当ての追加] メニューのスクリーンショット。 [選択] フィールドが赤で強調表示され、ユーザーによって検索フィールドに「Windows Virtual Desktop」と入力されています。](media/search-for-role.png)

Azure portal でカスタム ロールを追加する場合は、次のアクセス許可も選択されていることを確認してください。

   - Microsoft.Compute/virtualMachines/deallocate/action
   - Microsoft.Compute/virtualMachines/restart/action
   - Microsoft.Compute/virtualMachines/powerOff/action
   - Microsoft.Compute/virtualMachines/start/action 
   - Microsoft.Compute/virtualMachines/read
   - Microsoft.DesktopVirtualization/hostpools/read
   - Microsoft.DesktopVirtualization/hostpools/write
   - Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
   - Microsoft.DesktopVirtualization/hostpools/sessionhosts/write
   - Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete
   - Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action
   - Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read

これらのアクセス許可は、手順 4 で入力したものと同じです。

## <a name="how-creating-a-scaling-plan-works"></a>スケーリング プランの作成のしくみ

プランを作成する前に、次の点に注意してください。

- 1 つのスケーリング プランを、同じ種類のホスト プールの 1 つ以上のホスト プールに割り当てることができます。 スケーリング プランのスケジュールは、割り当てられているすべてのホスト プールにも適用されます。

- ホスト プールごとに 1 つのスケーリング プランのみを関連付けることができます。 1 つのスケーリング プランを複数のホスト プールに割り当てた場合、それらのホスト プールを別のスケーリング プランに割り当てることはできません。

- スケーリング プランは、それに構成されているタイム ゾーンでのみ動作します。

- スケーリング プランには、1 つまたは複数のスケジュールを設定できます。 たとえば、平日と週末で異なるスケジュールを設定できます。

- スケジュールを定義する前に、使用パターンを理解しておく必要があります。 次の時間帯にスケジュールを設定する必要があります。

    - Ramp-up\(増加\): 使用量が増える始まりの日。
    - ピーク時間: 使用量が最も高い時間帯。
    - Ramp-down\(減少\): 使用量が減少するとき。 これは通常、コストを節約するために VM をシャットダウンするときです。
    - オフピーク時間: 使用量が可能な限り少ない時間。 この期間中にアクティブにできる VM の最大数を定義できます。

- スケーリング プランは、有効にするとすぐに有効になります。

また、こちらの制限事項にも留意してください。

- 自動スケーリングを、Microsoft の他のスケーリングまたはサードパーティ製のスケーリング ツールと組み合わせて使用しないでください。 スケーリング プランを適用するホスト プールでは、これらを必ず無効にしてください。

- 自動スケーリングによってドレイン モードが上書きされるため、ホスト プールで VM を更新するときは、除外タグを必ず使用してください。

- 自動スケーリングでは、ホスト プールの設定における既存の負荷分散アルゴリズムは無視され、代わりにスケジュールの構成に基づいて負荷分散が適用されます。

## <a name="create-a-scaling-plan"></a>スケーリング プランを作成する

スケーリング プランを作成するには、次のようにします。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

2. **[Azure Virtual Desktop]** > **[スケーリング プラン]** にアクセスし、**[作成]** を選択します。

3. **[基本]** タブで、**[プロジェクトの詳細]** の下を確認し、スケーリング プランを割り当てるサブスクリプションの名前を選択します。

4. 新しいリソース グループを作成する場合は、**[新規作成]** を選択します。 既存のリソース グループを使用する場合は、ドロップダウン メニューからその名前を選択します。

5. スケーリング プランの名前を **[名前]** フィールドに入力します。

6. 必要に応じて、ユーザーに表示される "わかりやすい" 名前と、プランの説明を追加することもできます。

7. **[リージョン]** で、スケーリング プランのリージョンを選択します。 オブジェクトのメタデータは、リージョンに関連付けられた地域に格納されます。 現時点では、自動スケーリングでサポートされているのは、米国中部と米国東部 2 のリージョンのみです。 リージョンの詳細については、[データの場所](data-locations.md)に関するページを参照してください。

8. **[タイム ゾーン]** で、プランで使用するタイム ゾーンを選択します。

9. **[除外タグ]** に、スケーリング操作に含めない VM のタグを入力します。 たとえば、この機能をメンテナンスに使用することができます。 ドレイン モードで VM を設定した場合は、そのタグを使用して、自動スケーリングによってドレイン モードがオーバーライドされないようにします。

10. **[次へ]** を選択します。**[スケジュール]** タブが表示されます。

## <a name="configure-a-schedule"></a>スケジュールを構成する

スケジュールを使用すると、自動スケーリングで 1 日を通して増加モードと減少モードをアクティブにする時間を定義できます。 スケジュールの各フェーズで、セッション ホストにアクティブなセッションがない場合にのみ、自動スケーリングによって VM がオフにされます。 スケジュールを作成しようとするときに表示される既定値は平日に推奨される値ですが、必要に応じて変更できます。 

スケジュールを作成または変更するには、次のようにします。

1. **[スケジュール]** タブで **[スケジュールの追加]** を選択します。

2. **[名前]** フィールドにスケジュールの名前を入力します。

3. **[Repeat on]\(次の日に繰り返す\)** フィールドで、スケジュールを繰り返す日を選択します。

4. **[Ramp up]\(増加\)** タブで、次のフィールドに入力します。

    - **[開始時刻]** で、営業時間のピーク時のために VM の準備を開始する時刻をドロップダウン メニューから選択します。

    - **[負荷分散アルゴリズム]** には、**幅優先アルゴリズム** を選択することをお勧めします。 幅優先の負荷分散により、既存の VM 全体にユーザーが分散され、アクセス時間が短縮されます。
        
        >[!NOTE]
        >ここで選択した負荷分散設定によって、元のホスト プール設定で選択した設定がオーバーライドされます。

    - **[ピーク時間]** には、1 日のうち使用率が最も高くなる開始時刻を入力します。 時刻が、スケーリング プランに指定したものと同じタイム ゾーンにあることを確認してください。 この時刻は、増加フェーズの終了時刻でもあります。

    - **[Minimum percentage of session host VMs]\(セッション ホスト VM の最小割合\)** に、増加時からピーク時の間に使用するセッション ホスト リソースの量を入力します。 たとえば、**10%** を選択し、ホスト プールに 10 のセッション ホストがある場合、自動スケーリングによって、増加時からピーク時の間に常に 1 つのセッション ホストをユーザー接続で使用できるようになります。
    
    - **[容量のしきい値]** に、増加フェーズとピーク フェーズの開始をトリガーするホスト プールの使用率を入力します。 たとえば、100 セッションを処理できるホスト プールに対して **60%** を選択した場合、自動スケーリングによって、ホスト プールが 60 セッションを超えた場合にのみ、追加のホストが有効になります。

5. **[ピーク時間]** タブで、次のフィールドに入力します。

    - **[負荷分散]** には、幅優先または深さ優先の負荷分散を選択できます。 幅優先の負荷分散では、新しいユーザー セッションがホスト プール内のすべての使用可能なセッションに分散されます。 深さ優先の負荷分散では、セッション制限に達していない接続数が最も多い利用可能なセッション ホストに、新しいセッションが分散されます。 負荷分散の種類の詳細については、「[Azure Virtual Desktop の負荷分散方法を構成する](configure-host-pool-load-balancing.md)」を参照してください。

    >[!NOTE]
    >ここで容量のしきい値を変更することはできません。 代わりに、**[Ramp up]\(増加\)** で入力した設定がこの設定に引き継がれます。

    - **[Ramp-down]\(減少\)** には、**[Ramp up]\(増加\)** と同様のフィールドに値を入力しますが、今回は、ホスト プールの使用量が低下するときの値を入力します。 これには、次のフィールドが含まれます。

      - 開始時刻
      - 負荷分散アルゴリズム
      - ホストの最小割合 (%)
      - 容量のしきい値 (%)
      - Force logoff users\(ユーザーの強制ログオフ\)

    - 同様に、**[Off-peak hours]\(オフピーク時間\)** は、**[ピーク時間]** と同じように動作します。

      - 開始時刻。これは、減少期間の終わりでもあります。
      - 負荷分散アルゴリズム。 各 VM 上のセッションに基づいてセッション ホストの数を徐々に減らすには、**深さ優先** を選択することをお勧めします。
      - ピーク時間と同様に、ここで容量のしきい値を構成することはできません。 代わりに、**[Ramp-down]\(減少\)** に入力した値が引き継がれます。

## <a name="assign-host-pools"></a>ホスト プールを割り当てる

スケーリング プランを設定したので、次はホスト プールにプランを割り当てます。 含める各ホスト ポールの隣にあるチェック ボックスをオンにします。 自動スケーリングを有効にしない場合は、すべてのチェック ボックスをオフにします。 この設定は後でいつでも戻って変更できます。

>[!NOTE]
>ホスト プールに既に割り当てられているスケーリング プランを作成または更新すると、その変更が直ちに適用されます。

## <a name="add-tags"></a>タグを追加する 

その後、タグを入力する必要があります。 タグは、統合された課金のためにリソースを分類する名前と値のペアです。 同じタグを複数のリソースとリソース グループに適用できます。 リソースにタグを付ける方法の詳細については、[タグを使用して Azure リソースを整理する](../azure-resource-manager/management/tag-resources.md)ことに関する記事を参照してください。

>[!NOTE] 
>タグの作成後に他のタブでリソース設定を変更すると、タグが自動的に更新されます。

完了したら、**[確認と作成]** タブに移動し、**[作成]** を選択してホスト プールをデプロイします。

## <a name="next-steps"></a>次のステップ

スケーリング プランを作成したので、次の操作を実行できます。

- [新しいホスト プールおよび既存のホスト プールにスケーリング プランを割り当てる](autoscale-new-existing-host-pool.md)
- [スケーリング プランの診断を有効にする](autoscale-diagnostics.md)
