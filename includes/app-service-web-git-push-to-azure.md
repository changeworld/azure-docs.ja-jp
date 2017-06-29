## <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <URI from previous step>
```

アプリをデプロイするために、Azure リモートにプッシュします。 前にデプロイ ユーザーを作成したときに作成したパスワードを入力するよう求められます。 Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

上記のコマンドにより、次の例のような情報が表示されます。
