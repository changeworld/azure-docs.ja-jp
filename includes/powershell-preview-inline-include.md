このトピックには、Azure PowerShell 1.0 プレビューの PowerShell コマンドレットが含まれています。このプレビュー リリースを今すぐインストールしない場合は、Azure PowerShell 0.9.8 をそのまま使用できます。ほとんどの場合、2 つのバージョンは、1.0 プレビューのコマンドレット名が {verb}-AzureRm{noun} というパターンであり、0.9.8 の名前には **Rm** が含まれないという点のみ異なります (たとえば、New-AzureResourceGroup ではなく、New-AzureRmResourceGroup になる)。バージョン間の違いが著しい場合、このトピックでは、両方のバージョンの例を紹介しています。

Azure PowerShell 0.9.8 を使用している場合、まず、**Switch-AzureMode AzureResourceManager** コマンドを実行してリソース マネージャー モードを有効にする必要があります。このコマンドは、1.0 プレビューでは必要ありません。

推奨される使用に関する注意事項など、1.0 プレビュー リリースの詳細については、「[Azure PowerShell 1.0 Preview (Azure PowerShell 1.0 プレビュー)](https://azure.microsoft.com/blog/azps-1-0-pre/)」をご覧ください。Azure PowerShell 1.0 プレビューのインストールの詳細については、「[Azure Resource Manager Cmdlets (Azure リソース マネージャーのコマンドレット)](https://msdn.microsoft.com/library/mt125356.aspx)」をご覧ください。リソース マネージャーのコマンドの大幅な変更については、「[Changes to Azure Resource Manager management PowerShell cmdlets (Azure リソース マネージャー マネジメント PowerShell コマンドレットの変更点)](../articles/powershell-preview-resource-manager-changes.md)」をご覧ください。

<!---HONumber=Oct15_HO3-->