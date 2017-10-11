Azure CLI を使用すると、API アプリのリモート展開の URL を取得します。 次のコマンドで置き換える *\<app_name >* web アプリの名前を持つ。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

リモートにプッシュできるローカル Git 展開を構成します。

```bash
git remote add azure <URI from previous step>
```

アプリを展開するリモートの Azure にプッシュします。 展開のユーザーを作成したときに作成したパスワードを求められます。 クイック スタートの前半で作成したパスワードと Azure ポータルへのログインに使用するパスワードは入力することを確認します。

```bash
git push azure master
```
