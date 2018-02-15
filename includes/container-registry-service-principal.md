## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

コンテナー レジストリへのアクセスを持つサービス プリンシパルを作成するには、次のスクリプトを使用できます。 `ACR_NAME` 変数をコンテナー レジストリの名前で更新し、必要に応じて、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドの `--role` 値で異なるアクセス許可を付与します。 このスクリプトを使うには、[Azure CLI](/cli/azure/install-azure-cli) をインストールしておく必要があります。

スクリプトを実行した後、サービス プリンシパルの **ID** と**パスワード**を書き留めます。 その資格情報を作成したら、サービス プリンシパルとして、コンテナー レジストリに対する認証を受けるアプリケーションやサービスを構成できます。

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>既存のサービス プリンシパルの使用

既存のサービス プリンシパルにレジストリへのアクセスを与えるには、サービス プリンシパルに新しいロールを割り当てる必要があります。 新しいサービス プリンシパルの作成と同様に、プル、プッシュとプル、および所有者のアクセスを付与できます。

次のスクリプトでは、[az role assignment create][az-role-assignment-create] コマンドを使って、`SERVICE_PRINCIPAL_ID` 変数で指定したサービス プリンシパルに*プル* アクセス許可を付与します。 異なるレベルのアクセスを付与する場合は、`--role` の値を調整します。

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

これらのスクリプトは、どちらも GitHub に置かれており、PowerShell バージョンも用意されています。

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
