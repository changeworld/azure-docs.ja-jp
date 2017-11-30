## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure Portal の **adftutorial** コンテナー ページで **[最新の情報に更新]** をクリックして出力フォルダーを表示します。 
    
    ![更新](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. フォルダー一覧の **[output]** をクリックします。 
2. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    ![更新](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

リソース グループを削除すると、その中のデータ ファクトリも含めて、すべてのリソースが削除されます。 次のコマンドを実行して、リソース グループ全体を削除します。 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

リソース グループ全体ではなく、データ ファクトリだけを削除する場合は、次のコマンドを実行します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```