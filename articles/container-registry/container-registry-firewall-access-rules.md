---
title: ファイアウォール アクセス規則
description: ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成します。これには、("ホワイトリスト登録") REST API およびストレージ エンドポイントのドメイン名またはサービス固有の IP アドレス範囲へのアクセスを許可します。
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168012"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する

この記事では、Azure コンテナー レジストリへのアクセスを許可するようにファイアウォールの規則を構成する方法について説明します。 たとえば、ファイアウォールまたはプロキシ サーバーの背後にある Azure IoT Edge デバイスは、コンテナーのイメージをプルするためにコンテナー レジストリにアクセスする必要がある場合があります。 または、オンプレミス ネットワーク内のロックダウンされたサーバーが、イメージをプッシュするためのアクセスを必要とする場合があります。

代わりに、Azure 仮想ネットワーク内またはパブリック IP アドレス範囲からのみコンテナー レジストリに着信ネットワーク アクセス規則を構成する場合は、[仮想ネットワークから Azure コンテナー レジストリへのアクセスを制限する](container-registry-vnet.md)に関するページを参照してください。

## <a name="about-registry-endpoints"></a>レジストリ エンドポイントについて

イメージまたはその他の成果物を Azure コンテナー レジストリにプルまたはプッシュするには、Docker デーモンなどのクライアントが 2 つの異なるエンドポイントを使用して HTTPS 経由でやり取りする必要があります。

* **レジストリ REST API エンドポイント** - 認証およびレジストリ管理操作は、レジストリのパブリック REST API エンドポイントを介して処理されます。 このエンドポイントは、レジストリのログイン サーバー名、または関連付けられた IP アドレス範囲です。 

* **ストレージ エンドポイント** - Azure では、コンテナー イメージやその他の成果物のデータを管理するために、各レジストリに代わって Azure Storage アカウントに [BLOB ストレージが割り当てられます](container-registry-storage.md)。 クライアントは、Azure コンテナー レジストリ内のイメージ レイヤーにアクセスするときに、レジストリによって提供されるストレージ アカウントのエンドポイントを使用して要求を行います。

レジストリが [geo レプリケート](container-registry-geo-replication.md)されている場合、クライアントは、特定のリージョンまたは複数のレプリケートされたリージョンで REST エンドポイントとストレージ エンドポイントを操作する必要がある場合があります。

## <a name="allow-access-to-rest-and-storage-domain-names"></a>REST とストレージのドメイン名へのアクセスを許可する

* **REST エンドポイント** - `myregistry.azurecr.io` など、レジストリ ログイン サーバーの完全修飾名へのアクセスを許可します
* **ストレージ (データ) エンドポイント** - ワイルドカード `*.blob.core.windows.net` を使用して、すべての Azure BLOB ストレージ アカウントへのアクセスを許可します


## <a name="allow-access-by-ip-address-range"></a>IP アドレス範囲によりアクセスを許可する

組織に特定の IP アドレスまたはアドレス範囲のみへのアクセスを許可するポリシーがある場合は、[Azure の IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードしてください。

アクセスを許可する必要がある ACR REST エンドポイントの IP 範囲を検索するには、JSON ファイル内で **AzureContainerRegistry** を検索します。

> [!IMPORTANT]
> Azure サービスの IP アドレス範囲は変更でき、更新は毎週公開されます。 JSON ファイルを定期的にダウンロードし、アクセス規則で必要な更新を行います。 実際のシナリオで、Azure Container Registry にアクセスするために Azure 仮想ネットワークでネットワーク セキュリティ グループの規則を構成する必要がある場合は、代わりに **AzureContainerRegistry** の[サービス タグ](#allow-access-by-service-tag)を使用します。
>

### <a name="rest-ip-addresses-for-all-regions"></a>すべてのリージョンの REST IP アドレス

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>特定のリージョンの REST IP アドレス

特定のリージョン (**AzureContainerRegistry.AustraliaEast** など) を検索します。

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>すべてのリージョンのストレージ IP アドレス

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>特定のリージョンのストレージ IP アドレス

特定のリージョン (**Storage.AustraliaCentral** など) を検索します。

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>サービス タグによるアクセスを許可する

Azure 仮想ネットワークでは、ネットワーク セキュリティ規則を使用して、仮想マシンなどのリソースからコンテナー レジストリへのトラフィックをフィルター処理します。 Azure ネットワーク規則の作成を簡略化するには、**AzureContainerRegistry** の[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用します。 サービス タグは、グローバルまたは Azure リージョンごとに Azure サービスにアクセスするための IP アドレス プレフィックスのグループを表します。 アドレスが変更されると、タグは自動的に更新されます。 

たとえば、Azure コンテナー レジストリへのトラフィックを許可するために、宛先が **AzureContainerRegistry** の送信ネットワーク セキュリティ グループの規則を作成します。 特定のリージョンでのみサービス タグへのアクセスを許可するには、次の形式でリージョンを指定します。**AzureContainerRegistry**.[*リージョン名*]

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR 用のクライアント ファイアウォール規則を構成する

ファイアウォールの内側から Microsoft Container Registry (MCR) にアクセスする必要がある場合は、[MCR クライアント ファイアウォール規則](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)を構成するためのガイダンスを参照してください。 MCR は、Microsoft が公開しているすべての docker イメージ (Windows Server イメージなど) のプライマリ レジストリです。

## <a name="next-steps"></a>次のステップ

* [Azure のネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)について学習する

* Azure 仮想ネットワークの[セキュリティ グループ](/azure/virtual-network/security-overview)について学習する



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

