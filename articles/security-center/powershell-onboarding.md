---
title: PowerShell を使用して Microsoft Defender for Cloud にオンボードする
description: このドキュメントでは、PowerShell コマンドレットを使用して Microsoft Defender for Cloud を有効にするプロセスについて説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/20/2021
ms.author: memildin
ms.openlocfilehash: 5e2524121aea09142a04b06b5ee6736e977b2b94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452796"
---
# <a name="automate-onboarding-of-microsoft-defender-for-cloud-using-powershell"></a>PowerShell を使用して Microsoft Defender for Cloud のオンボードを自動化する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud PowerShell モジュールを使用して、Azure ワークロードをプログラムによってセキュリティで保護することができます。
PowerShell を使用することで、タスクを自動化し、手動タスクに固有の人的エラーを回避できます。 これは、すべて最初からセキュリティ保護する必要がある何百何千というリソースがある数十のサブスクリプションが関係した大規模な展開で特に便利です。

PowerShell を使用して Microsoft Defender for Cloud をオンボードすると、Azure リソースの使用開始と管理をプログラムで自動化し、必要なセキュリティ コントロールを追加できます。

この記事では、PowerShell スクリプトのサンプルを提供します。それを自分の環境で変更して使用し、サブスクリプション全体に Defender for Cloud をロールアウトできます。 

この例では、Defender for Cloud を ID: d07c0080-170c-4c24-861d-9c817742786c のサブスクリプションで有効にし、Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にして、高レベルの保護を提供する推奨設定を適用します。これにより高度な脅威防止および検出機能が提供されます。

1. [Microsoft Defender の高度なセキュリティ](enable-enhanced-security.md)を有効にします。
 
2. サブスクリプションに関連付けられている VM 上で Log Analytics エージェントが収集するデータの送信先である Log Analytics ワークスペースを設定します。この例では、既存のユーザーが定義したワークスペースです (myWorkspace)。

3. [Log Analytics エージェントをデプロイ](enable-data-collection.md#auto-provision-mma)する Defender for Cloud の自動エージェント プロビジョニングをアクティブにします。

5. 組織の [CISO を Defender for Cloud のアラートと注目すべきイベントのセキュリティ連絡先として](configure-email-notifications.md)設定します。

6. Defender for Cloud の[既定のセキュリティ ポリシー](tutorial-security-policy.md)を割り当てます。

## <a name="prerequisites"></a>前提条件

これらの手順は、Defender for Cloud コマンドレットを実行する前に行う必要があります。

1. PowerShell を管理者として実行します。

1. PowerShell で次のコマンドを実行します。
      
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy AllSigned
    ```

    ```powershell
    Install-Module -Name Az.Security -Force
    ```

## <a name="onboard-defender-for-cloud-using-powershell"></a>PowerShell を使用して Defender for Cloud をオンボードする

1. Defender for Cloud リソース プロバイダーにサブスクリプションを登録します。

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
    ```

1. 省略可能: サブスクリプションのカバレッジ レベル (Microsoft Defender for Cloud の強化されたセキュリティ機能のオンとオフ) を設定します。 未定義の場合、これらの機能はオフになります。

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Set-AzSecurityPricing -Name "default" -PricingTier "Standard"
    ```

1. エージェントのレポート先の Log Analytics ワークスペースを構成します。 サブスクリプションの VM のレポート先になる、事前に作成済みの Log Analytics ワークスペースが必要です。 同じワークスペースをレポート先にする複数のサブスクリプションを定義できます。 定義されていない場合、既定のワークスペースが使用されます。

    ```powershell
    Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"
    ```

1. Azure VM 上での Log Analytics エージェントの自動プロビジョニング インストール:
    
    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```
    
    ```powershell
    Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision
    ```

    > [!NOTE]
    > Azure 仮想マシンが Microsoft Defender for Cloud によって必ず自動的に保護されるように、自動プロビジョニングを有効にすることをお勧めします。
    >

1. 省略可能: オンボードするサブスクリプションの[セキュリティ連絡先の詳細を定義](configure-email-notifications.md)することを強くお勧めします。その連絡先は Defender for Cloud により生成されるアラートと通知の受信者として使用されます。

    ```powershell
    Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert
    ```

1. 既定の Defender for Cloud ポリシー イニシアティブを割り当てます。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
    ```

    ```powershell
    $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Defender for Cloud Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'
    ```

PowerShell を使用して Microsoft Defender for Cloud を正常にオンボードできました。

自動化スクリプトでこれらの PowerShell コマンドレットを使用して、サブスクリプションとリソースをプログラムで繰り返し処理できるようになりました。 これにより、時間が節約され、人的ミスの可能性が減ります。 これは参照用の[サンプル スクリプト](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)として使用できます。




## <a name="see-also"></a>関連項目
PowerShell を使用して Defender for Cloud へのオンボードを自動化する方法の詳細については、次の記事を参照してください。

* [Az.Security](/powershell/module/az.security)

Defender for Cloud の詳細については、次の記事を参照してください。

* [ でのセキュリティ ポリシーの設定](tutorial-security-policy.md) - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Microsoft Defender for Cloud でのセキュリティ アラートの管理と対応](managing-and-responding-alerts.md) -- セキュリティ アラートの管理と対応の方法について説明しています。
