## <a name="create-a-project-zip-file"></a>プロジェクトの ZIP ファイルを作成する

まだサンプル プロジェクトのルート ディレクトリにいることを確認します。 プロジェクト内のすべての ZIP アーカイブを作成します。 次のコマンドには、お使いの端末の既定のツールを使用します。

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

その後、この ZIP ファイルを Azure にアップロードし、App Service にデプロイします。