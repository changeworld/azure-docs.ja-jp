### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>PowerShell をインストールする
ご利用のマシンに最新の PowerShell がまだインストールされていない場合はインストールします。 

1. Web ブラウザーで [Azure のダウンロード](https://azure.microsoft.com/downloads/) ページに移動します。 
2. **[コマンドライン ツール]** -> **[PowerShell]** セクションの **[Windows のインストール]** をクリックします。 
3. PowerShell をインストールするには、**MSI** ファイルを実行します。 

詳しい手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-azurerm-ps)」をご覧ください。 

#### <a name="log-in-to-powershell"></a>PowerShell にログインする

1. お使いのマシンで **PowerShell** を起動します。 PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    ![PowerShell を起動する](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. 次のコマンドを実行して、Azure Portal へのサインインに使用するのと同じ Azure ユーザー名とパスワードを入力します。
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
3. アカウントに複数のサブスクリプションが関連付けられている場合は、次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
