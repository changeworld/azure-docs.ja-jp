---
title: 'チュートリアル: Azure Front Door を手動で Azure Static Web Apps 用に構成する'
description: Azure Static Web Apps 用の Azure Front Door の設定について
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400688"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>チュートリアル: Azure Front Door を手動で Azure Static Web Apps 用に構成する

静的 Web アプリ用の CDN として [Azure Front Door](../frontdoor/front-door-overview.md) を追加する方法について説明します。  Azure Front Door は、Web アプリを高速にデリバリーするためのセキュリティで保護されたスケーラブルなエントリ ポイントです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Azure Front Door インスタンスを作成する
> - Azure Font Door を自分の Azure Static Web Apps サイトに関連付ける

> [!NOTE]
> このチュートリアルでは、Azure Static Web Apps Standard プランが必要です。

## <a name="copy-url"></a>URL のコピー

1. Azure Portal に移動します。

1. Azure Front Door を適用したい静的 Web アプリを開きます。

1. *[概要]* セクションに移動します。

1. 後で使用するために、 *[URL]* をクリップボードにコピーします。

## <a name="add-azure-front-door"></a>Azure Front Door を追加する

1. Azure Portal に移動します。

1. **[リソースの作成]** を選択します。

1. "**Azure Front Door**" を検索します。

1. **[Front Door]** を選択します。

    Standard/Premium サービスの手順はこのチュートリアルに示されているものとは異なるため、"*Front Door Standard/Premium*" というラベルの付いたサービスを選択しないでください。

1. **［作成］** を選択します

1. *[基本]* タブで、以下の値を入力します。

    | 設定 | 値 |
    |---|---|
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | Resource group | リソース グループ名を入力します。 この名前は、多くの場合、静的 Web アプリで使用されるグループ名と同じです。 |
    | リソース グループの場所 | 新しいリソース グループを作成する場合は、最も近い場所を入力します。 |

    **Next:構成 >** をクリックします。

1. *[構成]* タブで、 *[フロントエンド/ドメイン]* の横にある "**プラス記号**" を選択し、次の値を入力します。

    | 設定 | 値 |
    |---|---|
    | ホスト名 | Front Door ホストの一意の名前を入力します。 |

    その他のフォームについては既定値を受け入れて、 **[追加]** を選択します。

1. *[バックエンド プール]* の "**プラス記号**" を選択し、次の値を入力します。

    | 設定 | 値 |
    |---|---|
    | 名前 | 「**my-static-web-app-pool**」と入力します。 |

1. **[+ バックエンドの追加]** を選択し、次の値を入力します。

    | 設定 | 値 |
    |---|---|
    | バックエンド ホストの種類 | **[カスタム ホスト]** を選択します。 |
    | バックエンド ホスト名 | 静的 Web アプリのホスト名を入力します。 値に末尾のスラッシュまたはプロトコルが含まれていないことを確認します。 (例: `desert-rain-04056.azurestaticapps.net`)  |
    | バックエンド ホスト ヘッダー | 静的 Web アプリのホスト名を入力します。 値に末尾のスラッシュ、プロトコルが含まれていないことを確認します。 (例: `desert-rain-04056.azurestaticapps.net`) |

    その他のフォームについては既定値を受け入れて、 **[追加]** を選択します。

1. **[追加]** を選択します。

1. *[ルーティング規則]* の "**プラス記号**" を選択し、次の値を入力します。

    | 設定 | 値 |
    |---|---|
    | 名前 | 「**my-routing-rule**」と入力します。 |

    その他のフォームについては既定値を受け入れて、 **[追加]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. **［作成］** を選択します

    作成プロセスが完了するまでに数分かかることがあります。

1. **[リソースに移動]** を選択します。

1. **[概要]** を選択します。

1. "*フロントエンド ホスト*" というラベルの付いたリンクを選択します。

    このリンクを選択すると、サイトが完全に反映されていない場合は 404 エラーが表示されることがあります。 ページを更新する代わりに、数分待って *[概要]* ウィンドウに戻り、"*フロントエンド ホスト*" というラベルの付いたリンクを選択します。

1. *[概要]* ウィンドウで、"**Front Door ID**" というラベルの付いた値をコピーし、後で使用するためにファイルに貼り付けます。

> [!IMPORTANT]
> 既定では、Azure Front Door で、トラフィックの統計情報に影響する可能性がある[正常性プローブ](../frontdoor/front-door-health-probes.md)が構成されます。 [正常性プローブ](../frontdoor/front-door-health-probes.md)の既定値を編集することもできます。

## <a name="update-static-web-app-configuration"></a>静的 Web アプリの構成を更新する

Front Door との統合を完了するには、アプリケーション構成ファイルを次のように更新する必要があります。

- サイトへのトラフィックを Front Door 経由のみに制限する
- サイトへのトラフィックを Front Door インスタンスからのみに制限する
- サイトにアクセスできるドメインを定義する

サイトの [staticwebapp.config.json](configuration.md) ファイルを開き、次の変更を行います。

1. 構成ファイルに次のセクションを追加して、Front Door のみを使用するようにトラフィックを制限します。

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. サイトにアクセスできる Azure Front Door のインスタンスとドメインを定義するには、`forwardingGateway` セクションを追加します。

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    最初に、Front Door インスタンスからのトラフィックのみを許可するようにアプリを構成します。 すべてのバックエンド要求で、Front Door で自動的に Front Door インスタンス ID を含む `X-Azure-FDID` ヘッダーが追加されます。 このヘッダーを必要とするように静的 Web アプリを構成することで、トラフィックを Front Door インスタンスのみに制限します。 構成ファイル の `forwardingGateway` セクションで、`requiredHeaders` セクションを追加し、`X-Azure-FDID` ヘッダーを定義します。 `<YOUR-FRONT-DOOR-ID>` を、前に準備しておいた *Front Door ID* に置き換えます。

    次に、Azure Front Door ホスト名 (Azure Static Web Apps ホスト名ではなく) を `allowedForwardedHosts` 配列に追加します。 Front Door インスタンスにカスタム ドメインが構成されている場合は、それらもこの一覧に含めます。

    この例では、`my-sitename.azurefd.net` をサイトの Azure Front Door ホスト名に置き換えます。

この構成では、生成された `*.azurestaticapps.net` ホスト名を介してサイトを使用できなくなりますが、Front Door インスタンスで構成されたホスト名を介してのみ使用できます。

> [!NOTE]
> 静的 Web アプリ内の既存のファイルに更新プログラムをデプロイする場合、Azure Front Door では、[有効期限](https://wikipedia.org/wiki/Time_to_live)が切れるまで、以前のバージョンのファイルが引き続き提供される場合があります。 最新のファイルが確実に提供されるようにするには、影響を受けるパスの [Azure Front Door のキャッシュを消去](../frontdoor/front-door-caching.md#cache-purge)してください。

これで、Front Door でサイトを管理しているので、Azure Static Web Apps のカスタム ドメイン機能は使用しなくなりました。 Azure Front Door にはカスタム ドメインを追加するための別のプロセスがあります。 「[Front Door にカスタム ドメインを追加する](../frontdoor/front-door-custom-domain.md)」を参照してください。 カスタム ドメインを Front Door に追加する場合は、静的 Web アプリ構成ファイルを `allowedForwardedHosts` リストに含めるように更新する必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](apis.md)
