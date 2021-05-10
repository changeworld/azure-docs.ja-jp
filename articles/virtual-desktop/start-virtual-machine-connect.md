---
title: 接続時に仮想マシンを起動 - Azure
description: 接続時に仮想マシンを起動機能を構成する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: af95cf5d3e4112c717d653062f186797d48fb515
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389810"
---
# <a name="start-virtual-machine-on-connect-preview"></a>接続時に仮想マシンを起動 (プレビュー)

> [!IMPORTANT]
> 現在、接続時に仮想マシンを起動機能はパブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

接続時に仮想マシン (VM) を起動 (プレビュー) 機能を使用すると、VM を使用していないときはその割り当てを解除できるため、コストを節約できます。 VM を再び使用する必要が生じたときは、VM を再び有効にするだけで済みます。

>[!NOTE]
>Windows Virtual Desktop (クラシック) では、この機能はサポートされていません。

## <a name="requirements-and-limitations"></a>要件と制限事項

接続時に仮想マシンを起動機能は、個人用ホスト プールに対してのみ有効にできます。 個人用ホスト プールの詳細については、「[Windows Virtual Desktop の環境](environment-setup.md#host-pools)」を参照してください。

次のリモート デスクトップ クライアントでは、接続時に仮想マシンを起動機能がサポートされています。

- [Web クライアント](connect-web.md)
- [Windows クライアント (バージョン1.2748 以降)](connect-windows-7-10.md)

更新プログラムとクライアント サポートに関するお知らせについては、[Tech Community フォーラム](https://aka.ms/wvdtc)のページをご覧ください。

現在、Azure Government クラウドでは、接続時の仮想マシンの起動はサポートしていません。

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>接続時に仮想マシンを起動用のカスタム ロールを作成する

接続時に仮想マシンを起動機能を構成する前に、VM にカスタム RBAC (ロールベースのアクセス制御) ロールを割り当てる必要があります。 このロールを使用すると、Windows Virtual Desktop でサブスクリプション内の VM を管理できます。 また、このロールを使用して、VM を有効にしたり、その状態を確認したり、診断情報を報告したりすることもできます。 各ロールの詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、接続時に仮想マシンを起動のカスタム ロールを割り当てるには、次のようにします。

1. Azure portal を開き、 **[サブスクリプション]** に移動します。

2. **[アクセス制御 (IAM)]** に移動し、 **[Add a custom role]\(カスタム ロールの追加\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アクセス制御 (IAM) の [追加] ボタンにあるドロップダウン メニューのスクリーンショット。 [Add a custom role]\(カスタム ロールの追加\) が赤色で強調表示されています。](media/add-custom-role.png)

3. 次に、カスタム ロールに名前を指定し、説明を追加します。 "start VM on connect" という名前を付けることをお勧めします。

4. **アクセス許可** タブで、このロールを割り当てるサブスクリプションに次のアクセス許可を追加します。 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. 完了したら、 **[OK]** をクリックします。

その後、ロールを割り当てて Windows Virtual Desktop へのアクセスを許可する必要があります。

カスタム ロールを割り当てるには、次のようにします。

1. **[アクセス制御 (IAM)]** タブで、 **[Add role assignments]\(ロールの割り当ての追加\)** を選択します。

2. 先ほど作成したロールを選択します。

3. 検索バーに「**Windows Virtual Desktop**」と入力して選択します。

      >[!NOTE]
      >Windows Virtual Desktop (クラシック) をデプロイしている場合は、2 つのアプリが表示されることがあります。 表示される両方のアプリにロールを割り当てます。
      >
      > [!div class="mx-imgBorder"]
      > ![[アクセス制御 (IAM)] タブのスクリーンショット。検索バーでは、Windows Virtual Desktop と Windows Virtual Desktop (クラシック) の両方が赤色で強調表示されています。](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>JSON ファイル テンプレートを使用してカスタム ロールを作成する

JSON ファイルを使用してカスタム ロールを作成する場合、使用できる基本的なテンプレートを次の例で示します。 サブスクリプション ID の値は、ロールを割り当てるサブスクリプション ID に置き換えてください。

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>接続時に仮想マシンを起動機能を構成する

サブスクリプションにロールを割り当てたので、次に、接続時に仮想マシンを起動機能を構成します。

### <a name="deployment-considerations"></a>デプロイに関する考慮事項 

接続時に仮想マシンを起動は、ホスト プール設定です。 選択したユーザー グループのみがこの機能を使用できるようにするには、必要なロールを、追加したいユーザーのみに割り当てるようにしてください。

>[!IMPORTANT]
> この機能は、既存のホスト プールにのみ構成できます。 新しいホスト プールを作成するときには、この機能は使用できません。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、接続時に仮想マシンを起動するには、次の手順を実行します。

1. Web ブラウザーを開いて [Azure portal](https://portal.azure.com) に移動します。

2. Azure portal で **[Windows Virtual Desktop]** に移動します。

3. **[ホスト プール]** を選択し、ロールを割り当てた個人用デスクトップを含むホスト プールを検索します。

   >[!NOTE]
   > この機能を設定したホスト プールには、直接ロールが割り当てられた個人用デスクトップが必要です。 ホスト プール内のデスクトップが正しく構成されていない場合、構成プロセスは機能しません。

4. ホスト プールで **[プロパティ]** を選択します。 **[Start VM on connect]\(接続時に仮想マシンを起動\)** で **[はい]** を選択し、 **[保存]** を選択して設定を直ちに適用します。

    > [!div class="mx-imgBorder"]
    > ![[プロパティ] ウィンドウのスクリーンショット。 [Start VM on connect]\(接続時に仮想マシンを起動\) オプションは赤で強調表示されます。](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>PowerShell の使用

PowerShell を使用してこの設定を構成するには、構成するリソース グループとホスト プールの名前が分かっている必要があります。 また、[Azure PowerShell モジュール (バージョン2.1.0 以降)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0) もインストールする必要があります。

PowerShell を使用して接続時に仮想マシンを起動を構成するには、次のようにします。

1. PowerShell コマンド ウィンドウを開きます。

2. 接続時に仮想マシンを起動を有効にするには、次のコマンドレットを実行します。

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. 接続時に仮想マシンを起動を無効にするには、次のコマンドレットを実行します。

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>ユーザー エクスペリエンス

一般的なセッションでは、割り当てが解除された VM にユーザーが接続するのにかかる時間は増加します。これは、物理コンピューターの電源をオンにするのと同様に、VM を再び有効にするのに時間が必要だからです。 リモート デスクトップ クライアントには、接続中に、PC の電源を入れていることをユーザーに通知するインジケーターがあります。

## <a name="troubleshooting"></a>トラブルシューティング

この機能で問題が発生した場合は、Windows Virtual Desktop の[診断機能](diagnostics-log-analytics.md)を使用して問題を確認することをお勧めします。 エラー メッセージが表示された場合は、メッセージの内容をよく読み、参照のためにエラー名をどこかにコピーしておいてください。

[Azure Monitor for Windows Virtual Desktop](azure-monitor.md) を使用して、問題の解決方法に関する提案を得ることもできます。

## <a name="next-steps"></a>次のステップ

トラブルシューティング ドキュメントや診断機能で解決できない問題が発生した場合は、[接続時に仮想マシンを起動の FAQ](start-virtual-machine-connect-faq.md) に関するページをご覧ください。