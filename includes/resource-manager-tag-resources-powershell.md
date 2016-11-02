### PowerShell の最新バージョンでの Tag コマンドレットの変更

[Azure PowerShell 2.0][powershell] の 2016 年 8 月付けのリリースには、タグの操作方法に関する大幅な変更が含まれています。次に進む前に、AzureRm.Resources モジュールのバージョンを確認してください。

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

Azure PowerShell の最後の更新を 2016 年 8 月より前に行った場合は、3.0 より前のバージョンが表示されます。

    Version
    -------
    2.0.2

Azure PowerShell を 2016 年 8 月以降に更新している場合は、バージョン 3.0 が表示されます。

    Version
    -------
    3.0.1
    
モジュールのバージョンが 3.0.1 以降であれば、最新のコマンドレットを使用してタグを操作できます。このバージョンの Azure リソース モジュールは、PowerShell ギャラリー、PowerShellGet、または Web Platform Installer を使用して Azure PowerShell をインストールまたはアップグレードするときに、自動的にインストールされます。バージョンが 3.0.1 より前であっても、そのバージョンを引き続き使用できますが、最新バージョンに更新することを検討してください。最新バージョンには、タグを簡単に操作できるようにする変更が含まれています。このトピックでは、両方の方法を紹介します。

### 最新バージョンの変更に合わせるためのスクリプトの更新 

最新のリリースでは、**Tags** パラメーターの名前が **Tag** に変更され、型が **Hashtable** から **Hashtable** に変更されています。エントリごとに **Name** と **Value** を指定する必要はなくなりました。代わりに、キーと値の組み合わせを **Key = "Value"** の形式で指定します。

既存のスクリプトを更新するには、**Tags** パラメーターを **Tag** に変更し、タグの書式を次の例のように変更します。

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

ただし、リソース グループとリソースは、依然としてそのメタデータの中に **Tags** プロパティを返すことに注意してください。このプロパティは変更されていません。

### バージョン 3.0.1 以降

タグはリソースやリソース グループ上に直接存在します。既存のタグを確認するには、**Get AzureRmResource** でリソースを、**Get AzureRmResourceGroup** でリソース グループを表示します。

それでは、リソース グループから始めましょう。

    Get-AzureRmResourceGroup -Name testrg1

このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

タグを含むリソースのメタデータを取得するには、次の例を使用します。

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

結果にタグの名前が表示されます。

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

タグの名前と値を取得するには、**Tags** プロパティを使用します。

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

次の結果が返されます。

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

通常は、特定のリソース グループまたはリソースのタグを表示する代わりに、特定のタグと値を持つリソースまたはリソース グループをすべて取得します。特定のタグが付けられたリソース グループを取得するには、**Find-AzureRmResourceGroup** コマンドレットに **-Tag** パラメーターを指定して使用します。

タグの値を持つリソース グループを取得するには、次の書式を使用します。

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

特定のタグと値を持つすべてのリソースを取得するには、**Find-AzureRmResource** コマンドレットを使用します。

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
既存のタグがないリソース グループにタグを追加するには、**Set-AzureRmResourceGroup** コマンドを使用してタグ オブジェクトを指定します。

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

これにより、新しいタグの値が指定されたリソース グループが返されます。

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
既存のタグがないリソースにタグを追加するには、**Set-AzureRmResource** コマンドを使用します。

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

タグは全体として更新されます。別のタグを持つリソースにタグを 1 つ追加するには、残しておきたいすべてのタグが含まれる配列を使用します。まず、既存のタグを選択してそのセットに 1 つ追加し、すべてのタグを再度適用します。

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

特定のタグを 1 つ以上削除するには、削除するタグが含まれない配列を保存します。

プロセスはリソースの場合も同じですが、**Get-AzureRmResource** および **Set-AzureRmResource** コマンドレットを使用する点のみ異なります。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、**Get-AzureRmTag** コマンドレットを使用します。

    Get-AzureRmTag
    
タグの名前と、そのタグを持つリソースとリソース グループの数が返されます。

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグであるため、変更せずに無視してください。

分類に新しいタグを追加するには、**New-AzureRmTag** コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、**Remove-AzureRmTag** コマンドレットを使用して分類から削除します。

### 3\.0.1 より前のバージョン

タグはリソースやリソース グループ上に直接存在します。既存のタグを確認するには、**Get AzureRmResource** でリソースを、**Get AzureRmResourceGroup** でリソース グループを表示します。

それでは、リソース グループから始めましょう。

    Get-AzureRmResourceGroup -Name testrg1

このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
リソースのメタデータを取得するには、次の例を使用します。リソースのメタデータは、タグを直接表示しません。

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

結果にタグは Hashtable オブジェクトとしてのみ表示されます。

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

実際のタグを表示するには、**Tags** プロパティを取得します。

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
これにより、書式設定された結果が返されます。
    
    Dept: Finance
    Environment: Production
    
通常は、特定のリソース グループまたはリソースのタグを表示する代わりに、特定のタグと値を持つリソースまたはリソース グループをすべて取得します。特定のタグが付けられたリソース グループを取得するには、**Find-AzureRmResourceGroup** コマンドレットに **-Tag** パラメーターを指定して使用します。

タグの値を持つリソース グループを取得するには、次の書式を使用します。

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
特定のタグと値を持つすべてのリソースを取得するには、Find-AzureRmResource コマンドレットを使用します。

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

既存のタグがないリソース グループにタグを追加するには、Set-AzureRmResourceGroup コマンドを使用してタグ オブジェクトを指定します。

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
    
これにより、新しいタグの値が指定されたリソース グループが返されます。

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

既存のタグがないリソースにタグを追加するには、Set-AzureRmResource コマンドを使用します。

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

タグは全体として更新されます。別のタグを持つリソースにタグを 1 つ追加するには、残しておきたいすべてのタグが含まれる配列を使用します。まず、既存のタグを選択してそのセットに 1 つ追加し、すべてのタグを再度適用します。

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

特定のタグを 1 つ以上削除するには、削除するタグが含まれない配列を保存します。

リソースの場合もプロセスは同じですが、Get-AzureRmResource コマンドレットと Set-AzureRmResource コマンドレットを使用する点が異なります。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、**Get-AzureRmTag** コマンドレットを使用します。

    Get-AzureRmTag
    
タグの名前と、そのタグを持つリソースとリソース グループの数が返されます。

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグであるため、変更せずに無視してください。

分類に新しいタグを追加するには、**New-AzureRmTag** コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、**Remove-AzureRmTag** コマンドレットを使用して分類から削除します。


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->