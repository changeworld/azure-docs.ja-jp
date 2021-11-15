---
title: 接続時に仮想マシンを起動 - Azure
description: 接続時に仮想マシンを起動機能を構成する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 09/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3f21a2d4eb0742bc5e91298816f9648e325b1955
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335330"
---
# <a name="start-virtual-machine-on-connect"></a>接続時に仮想マシンを起動

[接続時に仮想マシン (VM) を起動] の機能を使用すると、エンド ユーザーは必要時にのみ VM をオンにできるため、コストの節約になります。 必要でないときは VM をオフにすることができます。

>[!NOTE]
>Azure Virtual Desktop (クラシック) では、この機能はサポートされていません。

## <a name="requirements-and-limitations"></a>要件と制限事項

PowerShell と Azure portal を使用して、個人用またはプールされたホスト プールの "接続時に仮想マシンを起動" 機能を有効にすることができます。

次のリモート デスクトップ クライアントでは、接続時に仮想マシンを起動機能がサポートされています。

- [Web クライアント](./user-documentation/connect-web.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Windows クライアント (バージョン 1.2.2061 以降)](./user-documentation/connect-windows-7-10.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Android クライアント (バージョン 10.0.10 以降)](./user-documentation/connect-android.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [macOS クライアント (バージョン 10.6.4 以降)](./user-documentation/connect-macos.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [iOS クライアント (バージョン 10.2.5 以降)](./user-documentation/connect-ios.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Microsoft Store クライアント (バージョン 10.2.2005.0 以降)](./user-documentation/connect-microsoft-store.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- 「[シン クライアントのサポート](./user-documentation/linux-overview.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)」に記載されている シン クライアント

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>接続時に仮想マシンを起動用のカスタム ロールを作成する

接続時に仮想マシンを起動機能を構成する前に、VM にカスタム RBAC (ロールベースのアクセス制御) ロールを割り当てる必要があります。 このロールを使用すると、Azure Virtual Desktop でサブスクリプション内の VM を管理できるようになります。 また、このロールを使用して、VM を有効にしたり、その状態を確認したり、診断情報を報告したりすることもできます。 各ロールの詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

>[!NOTE]
>お使いの VM とホスト プールでサブスクリプションが異なる場合、その VM が含まれるサブスクリプションに RBAC ロールを割り当てる必要があります。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、接続時に仮想マシンを起動のカスタム ロールを割り当てるには、次のようにします。

1. Azure portal を開き、 **[サブスクリプション]** に移動します。

2. お使いの VM が含まれるサブスクリプションを選択します。
 
3. **[アクセス制御 (IAM)]** に移動し、 **[Add a custom role]\(カスタム ロールの追加\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アクセス制御 (IAM) の [追加] ボタンにあるドロップダウン メニューのスクリーンショット。 [Add a custom role]\(カスタム ロールの追加\) が赤色で強調表示されています。](media/add-custom-role.png)

4. 次に、カスタム ロールに名前を指定し、説明を追加します。 "start VM on connect" という名前を付けることをお勧めします。

5. **[アクセス許可]** タブで、このロールを割り当てるサブスクリプションに、次の 2 つのアクセス許可セットのいずれかを追加します。 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read 
   - Microsoft.Compute/virtualMachines/instanceView/read 

   代わりに、これらのアクセス許可を使用することもできます。

   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/*/read 

6. 完了したら、 **[OK]** をクリックします。

その後、ロールを割り当てて Azure Virtual Desktop へのアクセスを許可する必要があります。

カスタム ロールを割り当てるには、次のようにします。

1. **[アクセス制御 (IAM)]** タブで、 **[Add role assignments]\(ロールの割り当ての追加\)** を選択します。

2. 先ほど作成したロールを選択します。

3. 検索バーに「**Windows Virtual Desktop**」と入力して選択します (これは間もなく「Azure Virtual Desktop」に更新される予定です)。

      >[!NOTE]
      >Azure Virtual Desktop (クラシック) をデプロイしている場合は、2 つのアプリが表示されることがあります。 表示される両方のアプリにロールを割り当てます。
      >
      > [!div class="mx-imgBorder"]
      > ![[アクセス制御 (IAM)] タブのスクリーンショット。検索バーでは、Azure Virtual Desktop と Azure Virtual Desktop (クラシック) の両方が赤色で強調表示されています。](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>JSON ファイル テンプレートを使用してカスタム ロールを作成する

JSON ファイルを使用してカスタム ロールを作成する場合、使用できる基本的なテンプレートを次の例で示します。 *AssignableScopes* のサブスクリプション ID の値を、ロールを割り当てるサブスクリプション ID に置き換えてください。

```json
{
  "Name": "Start VM on connect (Custom)",
  "IsCustom": true,
  "Description": "Start VM on connect with AVD (Custom)",
  "Actions": [
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/*/read"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

JSON テンプレートを使用するには、JSON ファイルを保存し、関連するサブスクリプション情報を *[割り当て可能なスコープ]* に追加してから、PowerShell で次のコマンドレットを実行します。

```powershell
New-AzRoleDefinition -InputFile "C:\temp\filename"
```

カスタム ロールの作成の詳細については、「[Azure PowerShell を使用して Azure カスタム ロールを作成または更新する](../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template)」を参照してください。

## <a name="configure-the-start-vm-on-connect-feature"></a>接続時に仮想マシンを起動機能を構成する

サブスクリプションにロールを割り当てたので、次に、接続時に仮想マシンを起動機能を構成します。

### <a name="deployment-considerations"></a>デプロイに関する考慮事項 

接続時に仮想マシンを起動は、ホスト プール設定です。 選択したユーザー グループのみがこの機能を使用できるようにするには、必要なロールを、追加したいユーザーのみに割り当てるようにしてください。

個人用デスクトップの場合、この機能では、サービスによって既にユーザーに割り当てられているか、割り当てられる予定の既存の VM のみをオンにします。 プールされたホスト プールのシナリオでは、サービスは、どの VM もオンになっていない場合にのみ VM をオンにします。 この機能では、最初の VM がセッションの制限に達した場合にのみ、追加の VM をオンにします。

>[!IMPORTANT]
> この機能は、既存のホスト プールにのみ構成できます。 新しいホスト プールを作成するときには、この機能は使用できません。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、接続時に仮想マシンを起動するには、次の手順を実行します。

1. Web ブラウザーを開いて [Azure portal](https://portal.azure.com) に移動します。

2. Azure portal で **[Azure Virtual Desktop]** に移動します。

3. **[ホスト プール]** を選択し、設定を有効にするホスト プールに移動します。

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

この機能で問題が発生した場合は、Azure Virtual Desktop の[診断機能](diagnostics-log-analytics.md)を使用して問題を確認することをお勧めします。 エラー メッセージが表示された場合は、メッセージの内容をよく読み、参照のためにエラー名をどこかにコピーしておいてください。

[Azure Monitor for Azure Virtual Desktop](azure-monitor.md) を使用して、問題の解決方法に関する提案を得ることもできます。

VM がオンにならない場合は、他の操作を行う前に、オンにしようとしていた VM の正常性を確認する必要があります。

## <a name="next-steps"></a>次のステップ

トラブルシューティング ドキュメントや診断機能で解決できない問題が発生した場合は、[接続時に仮想マシンを起動の FAQ](start-virtual-machine-connect-faq.md) に関するページをご覧ください。
