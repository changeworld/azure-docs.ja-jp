1. [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 ログインの詳細については、「[Azure CLI 2.0 を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

  ```azurecli
  az login
  ```
2. 複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションが一覧表示されます。

  ```azurecli
  az account list --all
  ```
3. 使用するサブスクリプションを指定します。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```