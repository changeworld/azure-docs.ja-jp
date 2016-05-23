
## PowerShell セッションの開始

まず、[Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) (1.0 以降) をインストールして実行する必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../articles/powershell-install-configure.md)」をご覧ください。


>[AZURE.NOTE] SQL Database の新機能の多くは [Azure Resource Manager (ARM) デプロイ モデル](../articles/resource-group-overview.md)でのみサポートされているため、例では、ARM ベースの [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)が使われています。下位互換性のために既存のクラシック デプロイ モデル [Azure SQL Database (クラシック) コマンドレット](https://msdn.microsoft.com/library/azure/dn546723.aspx)がサポートされていますが、ARM ベースのコマンドレットを使用することをお勧めします。


[**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。Azure ポータルにサインインするときと同じ資格情報を使用してください。

	Add-AzureRmAccount

複数のサブスクリプションがある場合は、[**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) コマンドレットを使用して、PowerShell セッションを使用するサブスクリプションを選択します。PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx) を実行します。すべてのサブスクリプションを表示するには、[**Get AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx) を実行します。

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

