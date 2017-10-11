1. Azure サブスクリプションへのログインに、 [az ログイン](/cli/azure/#login)コマンドを次の画面に表示される指示します。 ログ記録の詳細については、次を参照してください。 [Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli)です。

  ```azurecli
  az login
  ```
2. 1 つ以上の Azure サブスクリプションがある場合は、アカウントのサブスクリプションを一覧表示されます。

  ```azurecli
  az account list --all
  ```
3. 使用するサブスクリプションを指定します。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```