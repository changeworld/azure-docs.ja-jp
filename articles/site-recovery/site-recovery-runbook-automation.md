---
title: "復旧計画への Azure Automation Runbook の追加 | Microsoft Docs"
description: "この記事では、Azure Site Recovery において、Azure Automation を使用して復旧計画を拡張し、Azure への復旧中に複雑なタスクを実行可能にする方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/23/2016
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 9f9a71cd7c6688dfd42dcb7ce52847f2016daf58


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>復旧計画への Azure Automation Runbook の追加
このチュートリアルでは、復旧計画に拡張性を持たせるために Azure Site Recovery と Azure Automation を統合する方法について説明します。 復旧計画では、セカンダリ クラウドへのレプリケーションと Azure へのレプリケーションという両方のシナリオで、Azure Site Recovery を使用して保護された仮想マシンの復旧を調整できます。 復旧計画により、復旧を**常に正確**、**反復可能**で、**自動化**されるようにすることもできます。 仮想マシンを Azure にフェールオーバーする場合は、Azure Automation と統合して復旧計画を拡張し、Runbook を実行する機能を使用できます。そのため、強力な自動タスクを実行できます。

Azure Automation について聞いたことがない場合は、[こちら](https://azure.microsoft.com/services/automation/)でサインアップして、[こちら](https://azure.microsoft.com/documentation/scripts/)からサンプル スクリプトをダウンロードしてください。 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) の詳細と、復旧計画を使用した Azure への復旧の調整方法については、[こちら](https://azure.microsoft.com/blog/?p=166264)を参照してください。

このチュートリアルでは、Azure Automation Runbook を復旧計画に統合する方法を説明します。 以前は手動での介入を必要とした単純なタスクを自動化し、複数の手順の復旧を 1 回のクリックの復旧アクションに変換する方法を説明します。 また、単純なスクリプトで問題が発生する場合に、トラブルシューティングする方法も説明します。

## <a name="customize-the-recovery-plan"></a>復旧計画のカスタマイズ
1. はじめに、復旧計画のリソース ブレードを開きます。 復旧計画に、復旧用に 2 つの仮想マシンが追加されています。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. カスタマイズ ボタンをクリックして、Runbook の追加を開始します。 復旧計画のカスタマイズ ブレードが開きます。

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 開始グループ 1 を右クリックし、[事後アクションの追加] を選択して追加します。
2. 新しいブレードでスクリプトをクリックして選択します。
3. スクリプトに "Hello World" という名前を付けます。
4. Automation アカウント名を選択します。 これは Azure Automation アカウントです。 このアカウントは、Azure のいずれの geo のものでもかまいませんが、Site Recovery コンテナーと同じサブスクリプションのものである必要があります。
5. Automation アカウントから Runbook を選択します。 これは、最初のグループの復旧後、復旧計画の実行中に実行されるスクリプトです。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. [OK] を選択してスクリプトを保存します。 これにより、スクリプトが [グループ 1: 開始] の事後アクション グループに追加されます。

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>スクリプト追加の要点
1. スクリプトを右クリックし、"手順を削除" するか、"スクリプトを更新" します。
2. スクリプトはオンプレミスから Azure へのフェールオーバー時に Azure で実行でき、また、シャットダウン前にプライマリ側スクリプトとして、Azure からオンプレミスへのフェールオーバー時に Azure で実行できます。
3. スクリプトの実行時に、復旧計画のコンテキストが挿入されます。

以下に、コンテキスト変数がどのようになっているかについて、例を示します。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


以下の表には、コンテキスト内の各変数の名前と説明が示されています。

| **変数名** | **説明** |
| --- | --- |
| RecoveryPlanName |実行される計画の名前です。 同じスクリプトを使用して名前を基にアクションを実行するのに役に立ちます。 |
| FailoverType |テスト用のフェールオーバーか、計画されたフェールオーバーか、それとも計画外のフェールオーバーかを指定します。 |
| FailoverDirection |プライマリへの復旧か、それともセカンダリへの復旧かを指定します。 |
| GroupID |復旧計画が実行される場合に、その計画内のグループ番号を識別します。 |
| VmMap |グループ内のすべての仮想マシンの配列です。 |
| VMMap キー |VM ごとの一意のキー (GUID) です。 これは、該当する仮想マシンの VMM ID と同じです。 |
| RoleName |復元される Azure VM の名前です。 |
| CloudServiceName |仮想マシンが作成される Azure Cloud Service の名前です。 |
| CloudServiceName (Resource Manager デプロイ モデル) |仮想マシンが作成される Azure リソース グループの名前です。 |

## <a name="using-complex-variables-per-recovery-plan"></a>復旧計画ごとの複合変数の使用
Runbook では、RecoveryPlanContext 以外の情報も求められることがあります。 パラメーターを Runbook に渡すための優れたメカニズムはありませんが、 複数の復旧計画に同じスクリプトを使用したい場合、復旧計画コンテキスト変数 RecoveryPlanName を使用したり、以下の試験的な手法を使って Runbook で Azure Automation 複合変数を使用したりすることができます。 次の例では、3 つの異なる複合変数資産を作成して、復旧計画の名前に基づく Runbook でそれらを使用する方法を説明します。

Runbook で 3 つの追加パラメーターを使用すると仮定します。 これらを JSON フォーム {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"} にエンコードします。

[AA 複合変数](../automation/automation-variables.md#variable-types)を使用して、新しい Automation 資産を作成します。
変数に <RecoveryPlanName>- params という名前を付けます。
複合変数を作成する方法について詳しくは、[こちら](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)をご覧ください。

それぞれの復旧計画について、変数に以下の名前を付けます。

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

次に、スクリプトで params を以下のように参照しています。

1. $rpname = $Recoveryplancontext 変数から RP 名を取得します。
2. $paramValue = "$($rpname)-params" の資産を取得します。
3. これを、Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue を呼び出して、復旧計画の複合変数として使用します。

例として、SharepointApp 復旧計画の複合変数/パラメーターを取得するために、SharepointApp-params という Azure Automation 複合変数を作成します。

これを復旧計画で使用するために、資産から変数を抽出します。このためには、ステートメント Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue を使用します。 詳しくは、[こちらをご覧ください](https://msdn.microsoft.com/library/dn913772.aspx)。

この方法では、資産の計画固有の複合変数を格納して、異なる復旧計画に同じスクリプトを使用できます。

## <a name="sample-scripts"></a>サンプルのスクリプト
Automation アカウントに直接インポートできるスクリプトのリポジトリについては、[Kristian Nese のスクリプトの OMS リポジトリ](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)に関する記事を参照してください。

このスクリプトは Azure Resource Manager テンプレートで、以下のすべてのスクリプトをデプロイします。

* NSG

NSG Runbook は、Public IP Addresses を復旧計画内のすべての VM に割り当てて、その仮想ネットワーク アダプターを、既定の通信を許可するネットワーク セキュリティ グループにアタッチします。

* PublicIP

Public IP Runbook は Public IP Addresses を復旧計画内のすべての VM に割り当てます。 マシンおよびアプリケーションへのアクセスは、各ゲスト内のファイアウォール設定に基づきます。

* CustomScript

CustomScript Runbook は、Public IP Addresses を復旧計画内のすべての VM に割り当て、テンプレートのデプロイ中に参照するスクリプトをプルするカスタム スクリプト拡張機能をインストールします。

* NSGwithCustomScript

NSGwithCustomScript Runbook は Public IP Addresses を復旧計画内のすべての VM に割り当て、拡張機能を使用してカスタム スクリプトをインストールし、仮想ネットワーク アダプターを NSG に接続して、リモート アクセス用の受信と送信の通信を可能にします。

## <a name="additional-resources"></a>その他のリソース
[Azure Automation サービスの実行アカウント](../automation/automation-sec-configure-azure-runas-account.md)

[Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要) (Azure Automation の概要)](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation Overview (Azure Automation の概要) (Azure Automation の概要)")

[Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Sample Azure Automation Scripts (サンプルの Azure Automation スクリプト) (サンプルの Azure Automation スクリプト)")



<!--HONumber=Nov16_HO3-->


