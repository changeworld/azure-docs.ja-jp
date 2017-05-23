## <a name="configure-a-deployment-user"></a>デプロイ ユーザーの構成  

FTP とローカル Git の場合、デプロイを認証するには、サーバー上で構成されたデプロイ ユーザーが必要です。

> [!NOTE]
> FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。
> `username` と `password` はアカウント レベルです。 Azure サブスクリプションの資格情報とは異なります。
>

デプロイの資格情報を作成するには、[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) コマンドを実行します。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

ユーザー名は一意であり、パスワードは強力なパスワードにする必要があります。 ` 'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 ` 'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。

このデプロイ ユーザーの作成は 1 回だけ実行する必要があります。すべての Azure デプロイでこのユーザーを使用できます。

後の手順でアプリをデプロイするときに使用するため、ユーザー名とパスワードを記録しておきます。