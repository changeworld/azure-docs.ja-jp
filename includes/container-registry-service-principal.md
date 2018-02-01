## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

コンテナー レジストリへのアクセスを持つサービス プリンシパルを作成するには、次のスクリプトを使用できます。 `ACR_NAME` 変数をコンテナー レジストリの名前で更新し、必要に応じて、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドの `--role` 値で異なるアクセス許可を付与します。 このスクリプトを使うには、[Azure CLI](/cli/azure/install-azure-cli) をインストールしておく必要があります。

スクリプトを実行した後、サービス プリンシパルの **ID** と**パスワード**を書き留めます。 その資格情報を作成したら、サービス プリンシパルとして、コンテナー レジストリに対する認証を受けるアプリケーションやサービスを構成できます。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>既存のサービス プリンシパルの使用

既存のサービス プリンシパルにレジストリへのアクセスを与えるには、サービス プリンシパルに新しいロールを割り当てる必要があります。 新しいサービス プリンシパルの作成と同様に、プル、プッシュとプル、および所有者のアクセスを付与できます。

次のスクリプトでは、[az role assignment create][az-role-assignment-create] コマンドを使って、`SERVICE_PRINCIPAL_ID` 変数で指定したサービス プリンシパルに*プル* アクセス許可を付与します。 異なるレベルのアクセスを付与する場合は、`--role` の値を調整します。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create