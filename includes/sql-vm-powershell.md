
## <a name="start-your-powershell-session"></a>PowerShell セッションの開始
まず、最新の [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) をインストールして実行する必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

> [!NOTE]
> このトピックの例では、[Azure Resource Manager デプロイ モデル](../articles/azure-resource-manager/resource-group-overview.md)を使用するため、例では [Azure Resource Manager コマンドレット](http://msdn.microsoft.com/library/azure/mt125356.aspx)を使用します。 
> 
> 

[**Connect-AzureRmAccount**](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。 Azure ポータルにサインインするときと同じ資格情報を使用してください。

    Connect-AzureRmAccount

複数のサブスクリプションがある場合は、[**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) コマンドレットを使用して、PowerShell セッションで使用するサブスクリプションを選択します。 PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext) を実行します。 すべてのサブスクリプションを表示するには、[**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription) を実行します。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

