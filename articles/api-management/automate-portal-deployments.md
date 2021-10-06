---
title: 開発者ポータルのデプロイを自動化する
titleSuffix: Azure API Management
description: 2 つの API Management サービス間でセルフホステッド開発者ポータル コンテンツを自動的に移行する方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a2fab946179fb0bef9db0068bb8f660f91ca8c04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669153"
---
# <a name="automate-developer-portal-deployments"></a>開発者ポータルのデプロイを自動化する

API Management 開発者ポータルでは、コンテンツへのプログラムによるアクセスをサポートしています。 これにより、[コンテンツ管理 REST API](/rest/api/apimanagement/) を使用して、API Management サービスとの間でデータをインポートしたりエクスポートしたりすることができます。 REST API アクセスは、マネージとセルフホステッドの両方のポータルで機能します。

## <a name="automated-migration-script"></a>自動化された移行スクリプト

API を使用して、テスト環境のサービスと運用環境のサービスなどの 2 つの API Management サービス間で、コンテンツの移行を自動化することができます。 API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js)にある `scripts.v3/migrate.js` スクリプトにより、この自動化プロセスが簡略化されます。

> [!WARNING]
> このスクリプトにより、移行先の API Management サービス内の開発者ポータルのコンテンツが削除されます。 それについて不安がある場合は、必ずバックアップを実行してください。

> [!NOTE]
> メディア ファイルをホストするために明示的に定義されたカスタム ストレージ アカウントを持つセルフホステッド ポータルを使用している場合 (つまり、`config.design.json` 構成ファイルで `blobStorageUrl` 設定を定義した場合) は、元の `scripts/migrate.js` [スクリプト](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js)を使用する必要があります。 元のスクリプトは、メディア ストレージ アカウントが API Management によって管理されているマネージド ポータルまたはセルフホステッド ポータルでは機能しません。 その場合は、代わりに `/scripts.v3` フォルダーのスクリプトを使用してください。

このスクリプトは、次のステップを実行します。

1. ポータルのコンテンツとメディアをソース API Management サービスからキャプチャします。
1. ポータルのコンテンツとメディアを移行先の API Management サービスから削除します。
1. ポータルのコンテンツとメディアを移行先の API Management サービスにアップロードします。
1. 必要に応じて、マネージド ポータルの場合のみ、ポータルを自動的に発行します。

このスクリプトが正常に実行されると、ターゲットの API Management サービスにはソース サービスと同じポータル コンテンツが含まれ、管理者として表示できるようになります。

* マネージド ポータルを使用している場合は、移行先ポータルを自動発行するようにスクリプトを設定して、移行されたバージョンが訪問者に自動的に提供されるようにすることができます。 
* セルフホステッド ポータルを使用している場合は、移行先ポータルを手動で発行する必要があります。 チュートリアルの発行とホスティングの説明に従って、[セルフホステッド開発者ポータルを設定](developer-portal-self-host.md)してください。

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)
- [開発者ポータルのセルフホスト](developer-portal-self-host.md)