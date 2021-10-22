---
title: Azure Key Vault 開発者ガイド
description: 開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 7c7bc9f0c520529506e988f6980756a38ef7c8b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997556"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 開発者ガイド

Azure Key Vault を使用すると、アプリケーション内から機密情報に安全にアクセスできるようになります。

- 自分でコードを記述しなくても、キー、シークレット、証明書が保護され、自分のアプリケーションからそれらを簡単に使用できます。
- 顧客自身にキー、シークレット、証明書を所有して管理してもらうことができるため、主要なソフトウェア機能の提供に集中できます。 このようにすることで、アプリケーションで、お客様のテナント キー、シークレット、証明書に対して義務や潜在的責任を負うことはありません。
- アプリケーションで署名と暗号化にキーを使用しながら、キー管理をアプリケーションの外部で行うことができます。 詳細については、[キーについて](../keys/about-keys.md)のページを参照してください。
- パスワード、アクセス キー、SAS トークンなどの資格情報をシークレットとして Key Vault に格納することにより、管理することができます。 詳細については、[シークレットについて](../secrets/about-secrets.md)のページを参照してください。
- 証明書の管理。 詳細については、[証明書について](../certificates/about-certificates.md)のページを参照してください。

Azure Key Vault の一般的な情報については、「[Azure Key Vault について](overview.md)」を参照してください。

## <a name="public-previews"></a>パブリック プレビュー

Key Vault の新機能のパブリック プレビューは定期的にリリースされます。 パブリック プレビュー機能をお試しいただき、azurekeyvault@microsoft.com (フィードバック用のメール アドレス) までご意見をお寄せください。

## <a name="create-and-manage-key-vaults"></a>キー コンテナーの作成と管理

他の Azure サービスと同様に、Key Vault は [Azure Resource Manager](../../azure-resource-manager/management/overview.md) によって管理します。 Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 それを使用して、Azure アカウントで、リソースを作成、更新、削除することができます。 

[Azure のロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) は、管理レイヤーへのアクセスを制御し、[管理プレーン](security-features.md#managing-administrative-access-to-key-vault)とも呼ばれます。 Key Vault の管理プレーンを使用して、キーコンテナーとそれらの属性 (アクセス ポリシーを含む) を作成して管理します。 *データ プレーン* を使用して、キー、証明書、およびシークレットを管理します。 

定義済みの Key Vault 共同作成者ロールを使用して、Key Vault への管理アクセス権を付与できます。     

### <a name="apis-and-sdks-for-key-vault-management"></a>キー コンテナー管理のための API と SDK

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault)<br>[クイックスタート](quick-create-cli.md)|[リファレンス](/powershell/module/az.keyvault)<br>[クイックスタート](quick-create-powershell.md)|[リファレンス](/rest/api/keyvault/)|[リファレンス](/azure/templates/microsoft.keyvault/vaults)<br>[クイックスタート](./vault-create-template.md)|[参照](/dotnet/api/microsoft.azure.management.keyvault)|[参照](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[リファレンス](/java/api/com.microsoft.azure.management.keyvault)|[リファレンス](/javascript/api/@azure/arm-keyvault)|

インストール パッケージとソース コードについては、[クライアント ライブラリ](client-libraries.md)に関するページを参照してください。

## <a name="authenticate-to-key-vault-in-code"></a>コードでの Key Vault に対する認証

Key Vault では、Azure Active Directory (Azure AD) 認証が使用されます。これにはアクセスを許可する Azure AD セキュリティ プリンシパルが必要です。 Azure AD セキュリティ プリンシパルは、ユーザー、アプリケーション サービス プリンシパル、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)、またはこれらのいずれかの種類のグループなどです。

### <a name="authentication-best-practices"></a>認証のベスト プラクティス

Azure にデプロイされるアプリケーションにはマネージド ID を使用することが推奨されます。 マネージド ID がサポートされていない Azure サービスを使用する場合、またはアプリケーションをオンプレミスにデプロイしている場合は、他の可能な方法として[証明書を使用したサービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)があります。 そのシナリオでは、証明書を Key Vault に格納し、頻繁にローテーションする必要があります。

開発環境とテスト環境には、シークレットによるサービス プリンシパルを使用します。 ローカル開発および Azure Cloud Shell には、ユーザー プリンシパル使用します。

各環境では、次のセキュリティ プリンシパルが推奨されます。
- **運用環境**: 証明書を使用したマネージド ID またはサービス プリンシパル。
- **テスト環境と開発環境**: マネージド ID、証明書を使用したサービス プリンシパル、またはシークレットを使用したサービス プリンシパル。
- **ローカル開発**: シークレットを使用したユーザー プリンシパルまたはサービス プリンシパル。

### <a name="azure-identity-client-libraries"></a>Azure ID クライアント ライブラリ

上記の認証シナリオは、*Azure ID クライアント ライブラリ* でサポートされており、Key Vault SDK と統合されています。 コードを変更することなく、環境やプラットフォーム全体で Azure ID クライアント ライブラリを使用できます。 ライブラリによって、Azure CLI、Visual Studio、Visual Studio Code、およびその他の手段で Azure ユーザーにサインインしているユーザーから、自動的に認証トークンが取得されます。 

Azure ID クライアント ライブラリの詳細については、以下を参照してください。

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> Key Vault .NET SDK バージョン 3 用の[アプリ認証ライブラリ](/dotnet/api/overview/azure/service-to-service-authentication)が推奨されていましたが、それは現在非推奨になりました。 Key Vault .NET SDK バージョン 4 に移行するには、[AppAuthentication から Azure.Identity への移行ガイダンス](/dotnet/api/overview/azure/app-auth-migration)に関する記事に従ってください。

アプリケーションで Key Vault に対して認証する方法に関するチュートリアルについては、以下を参照してください。
- [.NET で仮想マシンを使用して Azure Key Vault を使用する](./tutorial-net-virtual-machine.md)
- [Python で仮想マシンを使用して Azure Key Vault を使用する](./tutorial-python-virtual-machine.md)
- [マネージド ID を使用して Key Vault を .NET の Azure Web アプリに接続する](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>キー、証明書、シークレットの管理

データ プレーンによって、キー、証明書、およびシークレットへのアクセスを制御します。 データ プレーンによるアクセス制御には、ローカル コンテナー アクセス ポリシーまたは Azure RBAC を使用できます。

### <a name="apis-and-sdks-for-keys"></a>キーの API と SDK

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/key)<br>[クイックスタート](../keys/quick-create-cli.md)|[参照](/powershell/module/az.keyvault/)<br>[クイックスタート](../keys/quick-create-powershell.md)|[参照](/rest/api/keyvault/#key-operations)|[参照](/azure/templates/microsoft.keyvault/vaults/keys)<br>[クイックスタート](../keys/quick-create-template.md)|[参照](/dotnet/api/azure.security.keyvault.keys)<br>[クイックスタート](../keys/quick-create-net.md)|[参照](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[クイックスタート](../keys/quick-create-python.md)|[リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[クイックスタート](../keys/quick-create-java.md)|[参照](/javascript/api/@azure/keyvault-keys/)<br>[クイックスタート](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>証明書の API と SDK

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/certificate)<br>[クイックスタート](../certificates/quick-create-cli.md)|[参照](/powershell/module/az.keyvault)<br>[クイックスタート](../certificates/quick-create-powershell.md)|[参照](/rest/api/keyvault/#certificate-operations)|該当なし|[参照](/dotnet/api/azure.security.keyvault.certificates)<br>[クイックスタート](../certificates/quick-create-net.md)|[リファレンス](/python/api/overview/azure/keyvault-certificates-readme)<br>[クイックスタート](../certificates/quick-create-python.md)|[参照](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[クイックスタート](../certificates/quick-create-java.md)|[参照](/javascript/api/@azure/keyvault-certificates/)<br>[クイックスタート](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>シークレットの API と SDK

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/secret)<br>[クイックスタート](../secrets/quick-create-cli.md)|[参照](/powershell/module/az.keyvault/)<br>[クイックスタート](../secrets/quick-create-powershell.md)|[参照](/rest/api/keyvault/#secret-operations)|[参照](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[クイックスタート](../secrets/quick-create-template.md)|[参照](/dotnet/api/azure.security.keyvault.secrets)<br>[クイックスタート](../secrets/quick-create-net.md)|[参照](/python/api/overview/azure/keyvault-secrets-readme)<br>[クイックスタート](../secrets/quick-create-python.md)|[参照](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[クイックスタート](../secrets/quick-create-java.md)|[参照](/javascript/api/@azure/keyvault-secrets/)<br>[クイックスタート](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>シークレットの使用方法
Azure Key Vault は、アプリケーションのシークレットを保存するためにのみ使用してください。 Key Vault に格納する必要があるシークレットの例には次のようなものがあります。

- クライアント シークレットのシークレット
- Connection strings
- パスワード
- 共有アクセス キー
- SSH キー

ユーザー名やアプリケーション ID などのシークレット関連情報は、シークレットにタグとして格納できます。 その他の機密構成設定については、[Azure App Configuration](../../azure-app-configuration/overview.md) を使用する必要があります。
 
### <a name="references"></a>関連項目 

インストール パッケージとソース コードについては、[クライアント ライブラリ](client-libraries.md)に関するページを参照してください。

Key Vault のデータ プレーンのセキュリティについては、「[Azure Key Vault のセキュリティ機能](security-features.md)」を参照してください。

## <a name="use-key-vault-in-applications"></a>アプリケーションでの Key Vault の使用

Key Vault の最新の機能を利用するため、アプリケーションでシークレット、証明書、キーを使用する際は、利用可能な Key Vault SDK を使用することが推奨されます。 Key Vault SDK と REST API は、製品の新機能がリリースされるときに更新され、ベスト プラクティスとガイドラインに従っています。

基本的なシナリオの場合、簡単に使用するためのライブラリや統合ソリューションがあり、Microsoft パートナーやオープン ソース コミュニティによってサポートが提供されています。

証明書の場合は、次を使用できます。

- Key Vault 仮想マシン (VM) 拡張機能。Azure Key Vault に格納されている証明書を自動更新します。 詳細については、次を参照してください。 
  - [Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md)
  - [Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md)
  - [Azure Arc 対応サーバー用の Key Vault 仮想マシン拡張機能](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- Azure App Service 統合。Key Vault から証明書をインポートし、自動的に更新できます。 詳細については、「[Key Vault から証明書をインポートする](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)」を参照してください。

シークレットの場合は、次を使用できます。

- App Service アプリケーション設定での Key Vault シークレット。 詳細については、「[App Service と Azure Functions の Key Vault 参照を使用する](../../app-service/app-service-key-vault-references.md)」を参照してください。
- Azure VM でホストされているアプリケーション用の App Configuration サービスでの Key Vault シークレット。 詳細については、「[App Configuration と Key Vault を使用してアプリケーションを構成する](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)」を参照してください。

## <a name="code-examples"></a>コード例

アプリケーションで Key Vault を使用する完全な例については、[Azure Key Vault コード サンプル](https://azure.microsoft.com/resources/samples/?service=key-vault)のページを参照してください。 

## <a name="task-specific-guidance"></a>タスク固有のガイダンス

次の記事とシナリオは、Azure Key Vault を操作するためのタスク固有のガイダンスを提供します。

- キー コンテナーにアクセスするには、クライアント アプリケーションが、各種機能のために複数のエンドポイントにアクセスできる必要があります。 [ファイアウォールの背後にあるキー コンテナーへのアクセス](access-behind-firewall.md)に関するページを参照してください。 
- Azure VM で実行されているクラウド アプリケーションには、証明書が必要です。 この VM に証明書を取り込むにはどうすればいいでしょうか。 「[Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md)」および「[Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md)」を参照してください。
- Azure CLI、PowerShell、または Azure portal を使用してアクセス ポリシーを割り当てるには、「[Key Vault アクセス ポリシーを割り当てる](assign-access-policy.md)」を参照してください。 
- キー コンテナーの使用方法とライフサイクル、および論理的な削除が有効な各種キー コンテナー オブジェクトのガイダンスについては、「[論理的な削除と消去保護を使用した Azure Key Vault の回復の管理](./key-vault-recovery.md)」を参照してください。
- デプロイ時にパラメーターとしてセキュリティで保護された値 (パスワードなど) を渡す必要がある場合は、キー コンテナーにその値をシークレットとして格納し、他の Resource Manager テンプレートで値を参照できます。 「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../../azure-resource-manager/templates/key-vault-parameter.md)」を参照してください。

## <a name="integration-with-key-vault"></a>Key Vault との統合

次のサービスとシナリオでは、Key Vault を使用し、統合します。

- [保存データの暗号化](../../security/fundamentals/encryption-atrest.md)によって、データが永続化されるときにエンコード (暗号化) することができます。 データ暗号化キーは多くの場合、さらにアクセスを制限するため、Azure Key Vault のキーの暗号化キーで暗号化されます。
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) を使用して、独自のテナント キーを管理できます。 たとえば、テナント キーの管理をマイクロソフトに任せる (既定値) 代わりに、組織に適用される特定の規則を遵守する独自のテナント キーを管理できます。 独自のテナント キーの管理は、*Bring Your Own Key* (BYOK) とも呼ばれます。
- [Azure Private Link](private-link-service.md) を使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (Azure Key Vault、Azure Storage、Azure Cosmos DB など) と、Azure でホストされている顧客またはパートナー サービスにアクセスできます。
- Key Vault と [Azure Event Grid](../../event-grid/event-schema-key-vault.md) の統合により、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知できます。 アプリケーションに新しいバージョンのシークレットを配布したり、期限切れが近いシークレットをローテーションしたりして、停止を防ぐことができます。
- [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) シークレットを Key Vault の望ましくないアクセスから保護することができます。
- Key Vault に格納されているシークレットを使用して、[Azure Databricks から Azure Storage に接続](./integrate-databricks-blob-storage.md)します。
- Kubernetes 上の[シークレット ストア CSI ドライバー](./key-vault-integrate-kubernetes.md)向けに Azure Key Vault プロバイダーを構成して実行します。 

## <a name="key-vault-overviews-and-concepts"></a>Key Vault の概要と概念

関連情報:

- 削除が偶発的なものか意図的なものかにかかわらず、削除されたオブジェクトの回復を可能にする機能については、「[Azure Key Vault の論理的な削除の概要](soft-delete-overview.md)」を参照してください。
- スロットリングの基本概念と、アプリのアプローチを取得する方法については、「[Azure Key Vault のスロットル ガイダンス](overview-throttling.md)」を参照してください。
- リージョンとセキュリティ領域の関係については、「[Azure Key Vault セキュリティ ワールドと地理的境界](overview-security-worlds.md)」を参照してください。

## <a name="social"></a>ソーシャル

- [Key Vault ブログ](/archive/blogs/kv/)
- [Microsoft Q&A](/answers/products/)
- [Key Vault 用の Stack Overflow](https://stackoverflow.com/questions/tagged/azure-keyvault)
