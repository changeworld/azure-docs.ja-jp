Azure CLI を使って API アプリのリモート デプロイの URL を取得し、リモートにプッシュできるようにローカル Git のデプロイを構成します。

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

アプリをデプロイするために、Azure リモートにプッシュします。 前にデプロイ ユーザーを作成したときに作成したパスワードを入力するよう求められます。 Azure Portal へのログインに使用するパスワードではなく、クイック スタートの前の手順で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```
