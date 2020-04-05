---
title: Azure Connected Machine Agent CLI インターフェイス
description: Azure Connected Machine Agent CLI のリファレンス ドキュメント
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510396"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure Connected Machine Agent CLI インターフェイス

`Azcmagent` (Azure Connected Machine Agent) ツールは、Azure への Azure 以外のコンピューターの接続の構成とトラブルシューティングに使用されます。

エージェント自体は、Linux では `himdsd` と呼ばれるデーモン プロセスで、Windows では `himds` と呼ばれる Windows サービスです。

通常の使用では、このコンピューターと Azure の間の接続を確立するために `azcmagent connect` を使用し、その接続が不要になった場合は `azcmagent disconnect` を使用します。 他のコマンドは、トラブルシューティングなどの特殊な場合に使用します。

## <a name="options"></a>オプション

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>関連項目

* [azcmagent connect](#azcmagent-connect) - このコンピューターを Azure に接続します
* [azcmagent disconnect](#azcmagent-disconnect) - このコンピューターを Azure から切断します
* [azcmagent reconnect](#azcmagent-reconnect) - このコンピューターを Azure に再接続します
* [azcmagent show](#azcmagent-show) - コンピューターのメタデータとエージェントの状態を取得します。 これは主にトラブルシューティングに役立ちます。
* [azcmagent version](#azcmagent-version) - ハイブリッド管理エージェントのバージョンを表示します

## <a name="azcmagent-connect"></a>azcmagent connect

このコンピューターを Azure に接続します

### <a name="synopsis"></a>概要

このコンピューターを表すリソースを Azure に作成します。

これは、提供されている認証オプションを使用して、このコンピューターを表す Azure Resource Manager 内のリソースを作成します。 リソースは要求されたサブスクリプションとリソース グループにあり、コンピューターに関するデータは、location パラメーターで指定された Azure リージョンに格納されます。
既定のリソース名は、オーバーライドされていない場合はこのコンピューターのホスト名になります。

このコンピューターのシステム割り当て ID に対応する証明書がダウンロードされ、ローカルに保存されます。 この手順が完了すると、**Azure Connected Machine Metadata** Service とゲスト構成エージェントは、Azure クラウドとの同期を開始します。

認証オプション:

* アクセス トークン `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* サービス プリンシパルの ID とシークレット `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* デバイスのサインイン (インタラクティブ) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>構文

```none
azcmagent connect [flags]
```

### <a name="options"></a>オプション

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent disconnect

このコンピューターを Azure から切断します

### <a name="synopsis"></a>概要

このサーバーを表す Azure 内のリソースを削除します。

このコマンドは、提供されている認証オプションを使用して、このコンピューターを表す Azure Resource Manager リソースを削除します。 この時点で、**Azure Connected Machine Metadata Service** とゲスト構成エージェントが切断されます。 このコマンドを実行しても、サービスは停止も削除もされません。これを行うには、パッケージを削除します。

このコマンドには、"Azure に接続されたマシンのオンボード" ロールより高い特権が必要です。

コンピューターが切断されたら、Azure で新しいリソースを作成する場合は、`azcmagent connect` ではなく `azcmagent reconnect` を使用します。

認証オプション:

* アクセス トークン `azcmagent disconnect --access-token <>`
* サービス プリンシパルの ID とシークレット `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* インタラクティブなデバイスのサインイン `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>構文

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>オプション

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent reconnect

このコンピューターを Azure に再接続します

### <a name="synopsis"></a>概要

資格情報が無効なコンピューターを Azure に再接続します。

コンピューターが既に Azure にリソースを持っていても、Azure に対して認証できない場合は、このコマンドを使用して再接続できます。 この操作は、コンピューターの証明書の有効期限が切れるまで (少なくとも 45 日) コンピューターがオフになっている場合に行うことができます。

コンピューターが `azcmagent disconnect` で切断された場合は、代わりに `azcmagent connect` を使用します。

このコマンドは、提供されている認証オプションを使用して、このコンピューターを表す Azure Resource Manager リソースに対応する新しい資格情報を取得します。

このコマンドには、**Azure に接続されたマシンのオンボード** ロールよりも高い特権が必要です。

認証オプション

* アクセス トークン `azcmagent reconnect --access-token <>`
* サービス プリンシパルの ID とシークレット `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* インタラクティブなデバイスのサインイン `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>構文

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>オプション

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent show

コンピューターのメタデータとエージェントの状態を取得します。 これは主にトラブルシューティングに役立ちます。

### <a name="synopsis"></a>概要

コンピューターのメタデータとエージェントの状態を取得します。 これは主にトラブルシューティングに役立ちます。


### <a name="syntax"></a>構文

```
azcmagent show [flags]
```

### <a name="options"></a>オプション

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent version

ハイブリッド管理エージェントのバージョンを表示します

### <a name="synopsis"></a>概要

ハイブリッド管理エージェントのバージョンを表示します

### <a name="syntax"></a>構文

```none
azcmagent version [flags]
```

### <a name="options"></a>オプション

```none
  -h, --help   help for version
```
