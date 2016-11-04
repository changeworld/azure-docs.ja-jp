
## PowerShell セッションの開始
まず、最新の [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) をインストールして実行する必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../articles/powershell-install-configure.md)」をご覧ください。

> [!NOTE]
> SQL Database の新機能の多くは [Azure Resource Manager デプロイメント モデル](../articles/resource-group-overview.md)の使用下でのみサポートされているため、例では、Resource Manager の [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)を使用しています。既存のクラシック デプロイ モデルの [Azure SQL Database (クラシック) コマンドレット](https://msdn.microsoft.com/library/azure/dn546723.aspx)は下位互換性を確保するためにサポートされていますが、Resource Manager のコマンドレットの使用をお勧めします。
> 
> 

[**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを実行すると、資格情報を入力するサインイン画面が表示されます。Azure ポータルにサインインするときと同じ資格情報を使用してください。

    Add-AzureRmAccount

複数のサブスクリプションがある場合は、[**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) コマンドレットを使用して、PowerShell セッションで使用するサブスクリプションを選択します。PowerShell セッションが現在使用しているサブスクリプションを確認するには、[**Get AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx) を実行します。すべてのサブスクリプションを表示するには、[**Get AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx) を実行します。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0803_2016-->