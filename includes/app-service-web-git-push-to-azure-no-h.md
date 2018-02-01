ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 _&lt;paste\_copied\_url\_here>_ を、「[Web アプリを作成する](#create)」で保存した Git リモートの URL に置き換えます。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
