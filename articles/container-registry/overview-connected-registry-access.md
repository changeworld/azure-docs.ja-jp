---
title: 接続されたレジストリへのアクセスについて
description: Azure Container Registry の接続されたレジストリに対するトークン ベースの認証と認可の概要
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a98772cced7b5f7e72c66d134e9ff652f64b4086
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091110"
---
# <a name="understand-access-to-a-connected-registry"></a>接続されたレジストリへのアクセスについて

[接続されたレジストリ](intro-connected-registry.md)にアクセスして管理するには、現在、ACR の[トークン ベースの認証](container-registry-repository-scoped-permissions.md)だけがサポートされています。 次の図に示すように、それぞれの接続されたレジストリでは、2 種類のトークンが使用されます。

* [**クライアント トークン**](#client-tokens) - オンプレミスのクライアントが接続されたレジストリで認証を行い、イメージと成果物をそのレジストリとの間でプッシュまたはプルするために使用する 1 つ以上のトークン。
* [**同期トークン**](#sync-token) - それぞれの接続されたレジストリがその親にアクセスしてコンテンツを同期するために使用するトークン。

![接続されたレジストリの認証の概要](media/overview-connected-registry-access/connected-registry-authentication-overview.svg)

> [!IMPORTANT]
> それぞれの接続されたレジストリのトークン パスワードを安全な場所に格納します。 作成後は、トークン パスワードを取得できません。 トークン パスワードは、いつでも再生成できます。

## <a name="client-tokens"></a>クライアント トークン

接続されたレジストリへのクライアント アクセスを管理するには、1 つ以上のリポジトリでのアクションを対象にしたトークンを作成します。 トークンを作成した後、[az acr connected-registry update](/cli/azure/acr/connected-registry#az_acr_connected_registry_update) コマンドを使用して、そのトークンを受け入れるように接続されたレジストリを構成します。 その後、クライアントは、トークン資格情報を使用して接続されたレジストリ エンドポイントにアクセスできます。たとえば、Docker CLI コマンドを使用して、接続されたレジストリとの間でイメージをプルまたはプッシュします。

クライアント トークン アクションを構成するためのオプションは、接続されたレジストリでプッシュ操作とプル操作の両方が許可されるか、プル専用ミラーとして機能するかによって異なります。 
* 既定の [ReadWrite](intro-connected-registry.md#modes) モードの接続されたレジストリでは、プル操作とプッシュ操作の両方が許可されます。そのため、そのレジストリ内のリポジトリ コンテンツの "*読み取り*" と "*書き込み*" の両方を行うアクションを許可するトークンを作成できます。 
* [ReadOnly](intro-connected-registry.md#modes) モードの接続されたレジストリの場合、クライアント トークンでは、リポジトリ コンテンツを "*読み取る*" アクションのみを許可できます。

### <a name="manage-client-tokens"></a>クライアント トークンの管理

必要に応じてクライアント トークン、パスワード、またはスコープ マップを更新するには、[az acr token](/cli/az/acr#az_acr_token) および [az acr scope-map](/cli/az/acr#az_acr_scope-map) コマンドを使用します。 クライアント トークンの更新は、トークンを受け入れる接続されたレジストリに自動的に反映されます。

## <a name="sync-token"></a>同期トークン

それぞれの接続されたレジストリは、同期トークンを使用して、その直接の親 (別の接続されたレジストリまたはクラウド レジストリである可能性があります) で認証します。 接続されたレジストリは、親とコンテンツを同期したり、他の更新を実行したりするときに、このトークンを自動的に使用します。 

* 同期トークンとパスワードは、接続されたレジストリ リソースを作成するときに自動的に生成されます。 パスワードを再生成するには、[az acr connected-registry install renew-credentials][az-acr-connected-registry-install-renew-credentials] コマンドを実行します。
* 接続されたレジストリをオンプレミスにデプロイするために使用する構成には、同期トークンの資格情報を含めます。 
* 既定では、選択したリポジトリをその親と同期するためのアクセス許可が同期トークンに付与されます。 接続されたレジストリ リソースを作成するときには、既存の同期トークンまたは同期する 1 つ以上のリポジトリを指定する必要があります。
* また、接続されたレジストリの親との通信に使用されるゲートウェイで同期メッセージを読み書きするためのアクセス許可も持っています。 これらのメッセージは、同期スケジュールを制御し、接続されたレジストリとその親の間の他の更新を管理します。

### <a name="manage-sync-token"></a>同期トークンの管理

必要に応じて同期トークン、パスワード、またはスコープ マップを更新するには、[az acr token](/cli/az/acr#az_acr_token) および [az acr scope-map](/cli/az/acr#az_acr_scope-map) コマンドを使用します。 同期トークンの更新は、接続されたレジストリに自動的に反映されます。 同期トークンを更新するときは、パスワードをローテーションする標準的な方法に従ってください。

> [!NOTE]
> 同期トークンは、そのトークンに関連付けられている接続されたレジストリが削除されるまでは、削除できません。 同期トークンの状態を `disabled` に設定することで、接続されたレジストリを無効にすることができます。 

## <a name="registry-endpoints"></a>レジストリ エンドポイント

接続されたレジストリのトークン資格情報には、特定のレジストリ エンドポイントにアクセスするためのスコープが設定されます。

* クライアント トークンは、接続されたレジストリのエンドポイントにアクセスします。 接続されたレジストリのエンドポイントは、ログイン サーバー URI です。これは通常、それをホストするサーバーまたはデバイスの IP アドレスです。

* 同期トークンは、親レジストリ (別の接続されたレジストリ エンドポイントまたはクラウド レジストリ自体) のエンドポイントにアクセスします。 クラウド レジストリにアクセスするスコープを設定する場合、この同期トークンは次の 2 つのレジストリ エンドポイントに到達できる必要があります。

    - 完全修飾ログイン サーバー名 (例: `contoso.azurecr.io`)。 このエンドポイントは認証に使用されます。
    - クラウド レジストリの完全修飾リージョン [データ エンドポイント](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) (例: `contoso.westus2.data.azurecr.io`)。 このエンドポイントは、接続されたレジストリと同期の目的でメッセージを交換するために使用されます。 

## <a name="next-steps"></a>次のステップ

次の記事に進み、接続されたレジストリを利用できる特定のシナリオについて学習します。

> [!div class="nextstepaction"]
> [概要: 接続されたレジストリと IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[az-acr-connected-registry-update]: /cli/azure/acr/connected-registry#az_acr_connected_registry_update
[az-acr-connected-registry-install-renew-credentials]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_install_renew_credentials
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
