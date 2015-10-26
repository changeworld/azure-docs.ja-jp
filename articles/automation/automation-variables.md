<properties 
   pageTitle="Azure Automation の変数資産 | Microsoft Azure"
   description="変数アセットとは、Azure Automation 内のすべての Runbook に使用できる値です。この記事では、変数の詳細およびテキスト作成とグラフィカル作成の両方で変数を使用する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="bwren" />

# Azure Automation での変数アセット

変数アセットとは、オートメーション アカウント内のすべての Runbook に使用できる値です。これらは、Azure ポータル、Windows PowerShell、Runbook 内から作成、変更、取得することができます。Automation 変数は、次のシナリオで役立ちます。

- 複数の Runbook 間で値を共有する。

- 同じ Runbook から複数のジョブ間で値を共有する。

- 管理ポータルまたは Runbook で使用される Windows PowerShell コマンドラインから値を管理する。

Automation 変数は、Runbook でエラーが発生した場合でも継続して使用できるように保存されます。また、1 つの Runbook で設定された値を他のユーザーが使用できるようにしたり、次回実行時に同じ Runbook で使用したりすることができます。

変数が作成されると、暗号化して保存するように指定できます。変数が暗号化されると、Azure Automation に安全に保存され、その値は Azure PowerShell モジュールに含まれている [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) コマンドレットからは取得できません。暗号化された値は、Runbook の **Get-AutomationVariable** アクティビティからのみ取得できます。

>[AZURE.NOTE]Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。These assets are encrypted and stored in the Azure Automation using a unique key that is generated for each automation account.This key is encrypted by a master certificate and stored in Azure Automation.セキュリティで保護された資産を格納する前に、Automation アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。

## 変数の型

Azure ポータルで変数を作成する場合、変数値を入力するための適切な制御をポータルに表示できるように、ドロップダウン リストからデータ型を指定する必要があります。変数は、このデータ型に制限されませんが、別の型の値を指定する場合は、Windows PowerShell を使用して変数を設定する必要があります。**Not defined** を指定した場合、変数の値は **$null** に設定され、[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) コマンドレットまたは **Set-AutomationVariable** アクティビティを使用して値を設定する必要があります。ポータルでは複雑な変数の型の値を作成したり、変更したりすることはできませんが、Windows PowerShell を使用すれば、任意の型の値を指定することが可能です。複合型は [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx) として返されます。

配列またはハッシュ テーブルを作成し、それを変数に保存することによって、複数の値を 1 つの変数に格納することができます。

## コマンドレットとワークフローのアクティビティ

Windows PowerShell で Automation 変数を作成および管理するには、次のテーブルのコマンドレットを使用します。これらは、Automation Runbook で使用できる [Azure PowerShell モジュール](../powershell-install-configure.md)に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|既存の変数の値を取得します。|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|新しい変数を作成し、その値を設定します。|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|既存の変数を削除します。|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|既存の変数の値を設定します。|

次のテーブルのワークフロー アクティビティは、Runbook で Automation 変数にアクセスするために使用されます。これらは、Runbook でのみ使用できるものであり、Azure PowerShell モジュールには付属していません。

|ワークフロー アクティビティ|説明|
|:---|:---|
|Get-AutomationVariable|既存の変数の値を取得します。|
|Set-AutomationVariable|既存の変数の値を設定します。|

>[AZURE.NOTE]Runbook 内で **Get-AutomationVariable** の –Name パラメーターに変数を使用すると、設計時に Runbook と Automation 変数の間の依存関係の検出が複雑になる可能性があるため、使用しないようにする必要があります。

## 新しい Automation 変数の作成

### Azure ポータルで新しい変数を作成するには

1. Automation アカウントから、ウィンドウ上部にある **[資産]** をクリックします。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[変数の追加]** をクリックします。
1. ウィザードを完了し、チェック ボックスをクリックして新しい変数を保存します。


### Azure プレビュー ポータルで新しい変数を作成するには

1. Automation アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
1. **[変数]** 部分をクリックして **[変数]** ブレードを開きます。
1. ブレード上部の **[変数の追加]** をクリックします。
1. フォームに入力し、**[作成]** をクリックして新しい変数を保存します。


### Windows PowerShell で新しい変数を作成するには

[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) コマンドレットは、新しい変数を作成し、その初期値を設定します。[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) を使用して、値を取得することができます。値が単純型である場合、その同じ型が返されます。値が複合型である場合、**PSCustomObject** が返されます。

次のサンプル コマンドは、文字列型の変数を作成してから、その値を返す方法を示しています。


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

次のサンプル コマンドは、複合型の変数を作成してから、そのプロパティを返す方法を示しています。この場合、**Get-AzureVM** から仮想マシンのオブジェクトが使用されます。

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## Runbook での変数の使用

**Set-AutomationVariable** アクティビティを使用して、Runbook 内の Automation 変数の値を設定し、**Get-AutomationVariable** を使用して Automation 変数の値を取得します。**Set-AzureAutomationVariable** または **Get-AzureAutomationVariable** コマンドレットはワークフローのアクティビティよりも低効率であるため、Runbook では使用しないでください。また、**Get-AzureAutomationVariable** を使用して、セキュリティで保護された変数の値を取得することもできません。Runbook 内から新しい変数を作成する唯一の方法は、[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) コマンドレットを使用することです。


### テキスト形式の Runbook のサンプル

#### 変数からの単純値の設定と取得

次のサンプル コマンドは、テキスト形式の Runbook で変数を設定し取得する方法を示しています。このサンプルでは、*NumberOfIterations* および *NumberOfRunnings* という整数型の変数と、*SampleMessage* という文字列型の変数が既に作成されていることを前提にしています。

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### 変数での複雑なオブジェクトの設定および取得

次のサンプル コードは、テキスト形式の Runbook で複合値で変数を更新する方法を示しています。このサンプルでは、Azure 仮想マシンは **Get-AzureVM** で取得され、既存の Automation 変数に保存されます。[変数の型](#variable-types)で説明したように、これは PSCustomObject として格納されます。

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


次のコードでは、この値は変数から取得され、仮想マシンを起動するために使用されます。

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### 変数でのコレクションの設定および取得

次のサンプル コードは、テキスト形式の Runbook で複合値のコレクションで変数を使用する方法を示しています。このサンプルでは、複数の Azure 仮想マシンは **Get-AzureVM** で取得され、既存の Automation 変数に保存されます。[変数の型](#variable-types)で説明したように、これは PSCustomObject のコレクションとして格納されます。

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

次のコードでは、このコレクションは変数から取得され、各仮想マシンを起動するために使用されます。

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### グラフィカルな Runbook のサンプル

グラフィカルな Runbook で、グラフィカル エディターの [ライブラリ] ウィンドウの変数を右クリックして目的のアクティビティを選択することにより、**Get-AutomationVariable** または **Set-AutomationVariable** を追加します。

![キャンバスへの変数の追加](media/automation-variables/variable-add-canvas.png)

#### 変数での値の設定

次の図は、グラフィカルな Runbook の単純値で変数を更新するサンプル アクティビティを示しています。このサンプルでは、1 つの Azure 仮想マシンは **Get-AzureVM** で取得され、コンピューター名は既存の Automation 変数に文字列型で保存されます。出力では 1 つのオブジェクトのみが期待されているため、[リンクがパイプラインかシーケンス](automation-graphical-authoring-intro.md#links-and-workflow)かどうかは関係ありません。

![単純変数の設定](media/automation-variables/set-simple-variable.png)

次の図は、グラフィカルな Runbook の複合値で変数を更新するために使用するアクティビティを示しています。前述の例から唯一変更された点は、単にオブジェクトのプロパティでなく、オブジェクトが格納されるようにするため、**Set-AutomationVariable** アクティビティで**アクティビティの出力**の**フィールド パス**を指定していないことです。[変数の型](#variable-types)で説明したように、これは PSCustomObject として格納されます。

![複合変数の設定](media/automation-variables/set-complex-variable.png)

次の図は、複数の仮想マシンが変数に保存された状態での、前述の例と同様の機能を示しています。**Set-AutomationVariable** アクティビティが仮想マシンのセット全体を 1 つのコレクションとして受信するために、[シーケンス リンク](automation-graphical-authoring-intro.md#links-and-workflow)をここで使用する必要があります。[パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow)が使用された場合、**Set-AutomationVariable** アクティビティはオブジェクトごとに個別に実行され、その結果として、コレクションの最後の仮想マシンのみが保存されます。[変数の型](#variable-types)で説明したように、これは PSCustomObject のコレクションとして格納されます。

![複合コレクション変数の設定](media/automation-variables/set-complex-variable-collection.png)

#### 変数からの値の取得

次の図は、グラフィカルな Runbook で変数を取得および使用するサンプル アクティビティを示しています。最初のアクティビティは、前の例で変数に保存された仮想マシンを取得します。**Start-AzureVM** アクティビティが **Get-AutomationVariable** アクティビティから送信されたオブジェクトごとに 1 回実行されるように、リンクは[パイプライン](automation-graphical-authoring-intro.md#links-and-workflow)である必要があります。変数に格納されているのが 1 つのオブジェクトか複数のオブジェクトかに関係なく、同じように動作します。**Start-AzureVM** アクティビティは、各仮想マシンを表す PSCustomObject のプロパティを使用します。

![複合変数の取得](media/automation-variables/get-complex-variable.png)

次の図は、グラフィカルな Runbook で変数に格納されているオブジェクトをフィルター処理する方法を示しています。変数が設定されたときに停止されたこれらの仮想マシンのみをフィルター処理する場合は、[条件](automation-graphical-authoring-intro.md#links-and-workflow)が前の例のリンクに追加されます。

![フィルター処理された複合変数の取得](media/automation-variables/get-complex-variable-filter.png)


## 関連記事:

- [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=Oct15_HO3-->