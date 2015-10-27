このトピックでは、Azure PowerShell コマンドレットの使用例を紹介します。Azure PowerShell には現在、1.0 Preview と 0.9.8 の 2 つのリリースがあります。既存のスクリプトがあって今は変更したくない場合、0.9.8 リリースをそのまま使用してかまいません。1.0 Preview リリースを使用する場合は、予期しない影響を防ぐために、運用前の環境でスクリプトを慎重にテストしてから運用環境で使用してください。

ほとんどの場合、2 つのバージョンは、1.0 プレビューのコマンドレット名が {verb}-AzureRm{noun} というパターンであり、0.9.8 の名前には **Rm** が含まれないという点のみ異なります (たとえば、New-AzureResourceGroup ではなく、New-AzureRmResourceGroup になる)。バージョン間の違いが著しい場合、このトピックでは、両方のバージョンの例を紹介しています。

Azure PowerShell 0.9.8 を使用している場合、まず、**Switch-AzureMode AzureResourceManager** コマンドを実行してリソース マネージャー モードを有効にする必要があります。このコマンドは、1.0 プレビューでは必要ありません。

インストールとアンインストールの方法を含む 1.0 Preview リリースの情報については、[Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/) のページを参照してください。リソース マネージャーのコマンドの大幅な変更については、[Azure リソース マネージャーの管理 PowerShell コマンドレットに対する変更](../articles/powershell-preview-resource-manager-changes.md)に関するページをご覧ください。

<!---HONumber=Oct15_HO4-->