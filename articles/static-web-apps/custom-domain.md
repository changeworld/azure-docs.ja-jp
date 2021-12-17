---
title: Azure Static Web Apps にカスタム ドメインを設定する
description: Azure Static Web Apps で無料の SSL/TLS 証明書を使用してカスタム ドメインを設定する方法について学習します
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: buhollan
ms.openlocfilehash: 07da31c48d70f25a2364b4af242bc0d1331d7036
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750669"
---
# <a name="set-up-a-custom-domain-with-free-certificate-in-azure-static-web-apps"></a>Azure Static Web Apps で無料の証明書を使用してカスタム ドメインを設定する

Azure Static Web Apps では既定で、自動生成されたドメイン名が提供されます。 この記事では、Azure Static Web Apps アプリケーションにカスタム ドメイン名をマップする方法について説明します。

## <a name="free-ssltls-certificate"></a>無料の SSL/TLS 証明書

Azure Static Web Apps では、自動生成されたドメイン名と追加したカスタム ドメインに対して、無料の SSL/TLS 証明書が自動的に提供されます。

## <a name="walkthrough-video"></a>チュートリアル ビデオ

> [!VIDEO https://channel9.msdn.com/Shows/5-Things/Configuring-a-custom-domain-with-Azure-Static-Web-Apps/player?format=ny]

## <a name="prerequisites"></a>前提条件

- 購入済みのドメイン名
- ドメインの DNS 構成プロパティへのアクセス

## <a name="dns-configuration-options"></a>DNS の構成オプション

アプリケーションに使用できる DNS 構成には、いくつかの種類があります。

| シナリオ                                                                                 | 例                                | ドメインの検証方法 | DNS レコードの種類 |
| ---------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------ | --------------- |
| [ルートまたは頂点ドメインを追加する](#add-domain-using-txt-record-validation)                        | `mydomain.com`, `example.co.uk`        | TXT                      | ALIAS           |
| [サブドメインを追加する](#add-domain-using-cname-record-validation)                             | `www.mydomain.com`, `foo.mydomain.com` | CNAME                    | CNAME           |
| [現在使用中のサブドメインを転送する](#add-domain-using-txt-record-validation) | `www.mydomain.com`, `foo.mydomain.com` | TXT                      | CNAME           |

## <a name="add-domain-using-cname-record-validation"></a>CNAME レコード検証を使用してドメインを追加する

カスタム ドメインを追加するには CNAME レコード検証を使用することをお勧めしますが、これはサブドメインに対してのみ機能します。 ルート ドメイン (`mydomain.com`) を追加する場合は、「[TXT レコード検証を使用してドメインを追加する](#add-domain-using-txt-record-validation)」に進んでから、[ALIAS レコードを作成](#create-an-alias-record)してください。

> [!IMPORTANT]
> 現在、サブドメインが稼働中のサイトに関連付けられていて、それを静的 Web アプリに転送する準備ができていない場合は、[TXT レコードの検証](#add-domain-using-txt-record-validation)を使用します。

### <a name="enter-your-subdomain"></a>サブドメインを入力する

1. [Azure portal](https://portal.azure.com) で静的 Web アプリを開きます。

1. メニューで **[カスタム ドメイン]** を選択します。

1. **[追加]** ボタンを選びます。

1. _[ドメイン名]_ フィールドに、サブドメインを入力します。 必ず、プロトコルなしで入力してください。 たとえば、「 `www.mydomain.com` 」のように入力します。

   :::image type="content" source="media/custom-domain/add-subdomain.png" alt-text="入力ボックスにカスタム サブドメインが表示されているドメインを追加する画面":::

1. **[次へ]** ボタンを選択して、 _[検証と構成]_ 手順に進みます。

### <a name="configure-cname-with-your-domain-provider"></a>ドメイン プロバイダーで CNAME を構成する

ドメイン プロバイダーで CNAME を構成する必要があります。 Azure DNS をお勧めしますが、これらの手順はどのドメイン プロバイダーでも機能します。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. "_ホスト名レコード タイプ_" ドロップダウン リストで **[CNAME]** が選択されていることを確認します。

1. **[コピー]** アイコンを選択して、"_値_" フィールドの値をクリップボードにコピーします。

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="CNAME が選択され、[コピー] アイコンに枠線が付いている検証と構成画面":::

1. 別のブラウザー タブまたはウィンドウで、Azure portal から Azure DNS ゾーンを開きます。

1. **[レコード セット]** ボタンを選択します。

1. 次の値を使用して、新しい **CNAME** レコード セットを作成します。

   | 設定          | 値                                     |
   | ---------------- | ----------------------------------------- |
   | 名前             | 実際のサブドメイン (`www` など)             |
   | Type             | CNAME                                     |
   | エイリアス レコード セット | No                                        |
   | TTL              | 既定値のままにする                    |
   | TTL の単位         | 既定値のままにする                    |
   | エイリアス            | クリップボードからドメイン名を貼り付ける |

1. **[OK]** を選択します。

   :::image type="content" source="media/custom-domain/azure-dns-cname.png" alt-text="名前、タイプ、別名のフィールドが強調表示された Azure DNS レコード セットの画面":::

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

# <a name="other-dns"></a>[その他の DNS](#tab/other-dns)

1. "_ホスト名レコード タイプ_" ドロップダウン リストで **[CNAME]** が選択されていることを確認します。

1. **[コピー]** アイコンを選択して、"_値_" フィールドの値をクリップボードにコピーします。

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="CNAME が選択され、[コピー] アイコンに枠線が付いている検証と構成画面":::

1. 別のブラウザー タブまたはウィンドウで、ドメイン プロバイダーの Web サイトにサインインします。

1. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

1. 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[ドメイン]** などのリンクを探します。 そのページに移動して、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

   以下のスクリーンショットは、DNS レコード ページの例です。

   :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="サンプルの DNS プロバイダー構成":::

1. 次の値を使用して、新しい **CNAME** レコードを作成します。

   | 設定             | 値                                     |
   | ------------------- | ----------------------------------------- |
   | Type                | CNAME                                     |
   | Host                | 実際のサブドメイン (`www` など)             |
   | 値               | クリップボードからドメイン名を貼り付ける |
   | TTL (該当する場合) | 既定値のままにする                    |

1. DNS プロバイダーに変更内容を保存します。

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

---

## <a name="add-domain-using-txt-record-validation"></a>TXT レコード検証を使用してドメインを追加する

Azure では、TXT レコードを使用して、ユーザーがドメインを所有していることを検証します。 これは次のような場合に役立ちます。

1. ルート ドメイン (つまり `mydomain.com`) を構成する必要がある場合。 ルート ドメインを構成する ALIAS レコードを作成するために、ドメインを所有していることを検証する場合。

1. ダウンタイムなしでサブドメインを転送する場合。 TXT レコードの検証方法を使用すると、ドメインを所有していることを検証し、静的な Web アプリがそのドメインの証明書を発行するプロセスを行うことができます。 その後、CNAME レコードを使用して、いつでもドメインを静的 Web アプリをポイントするよう切り替えることができます。

#### <a name="enter-your-domain"></a>ドメインを入力する

1. [Azure portal](https://portal.azure.com) で静的 Web アプリを開きます。

1. メニューで **[カスタム ドメイン]** を選択します。

1. **[追加]** ボタンを選びます。

1. "_ドメイン名_" フィールドに、ルート ドメイン (つまり `mydomain.com`) またはサブドメイン (つまり `www.mydomain.com`) を入力します。

   :::image type="content" source="media/custom-domain/add-domain.png" alt-text="入力ボックスにカスタム ドメインが表示されているドメインを追加する画面":::

1. **[次へ]** ボタンをクリックして、 _[検証と構成]_ 手順に進みます。

#### <a name="configure-txt-record-with-your-domain-provider"></a>ドメイン プロバイダーで TXT レコードを構成する

ドメイン プロバイダーで TXT レコードを構成する必要があります。 Azure DNS をお勧めしますが、これらの手順はどのドメイン プロバイダーでも機能します。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. "ホスト名レコード タイプ"ドロップダウンが "TXT" に設定されていることを確認します。

1. **[コードの生成]** ボタンを選択します。

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="コードの生成ボタンが強調表示されているカスタムの追加画面":::

   この操作によって一意のコードが生成されます (処理には最大で 1 分かかる場合があります)。

1. コードの横にあるクリップボード アイコンを選択して、値をクリップボードにコピーします。

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="コードのコピーボタンが強調表示されているカスタム ドメインの追加画面":::

1. 別のブラウザー タブまたはウィンドウで、Azure portal から Azure DNS ゾーンを開きます。

1. **[レコード セット]** ボタンを選択します。

1. 次の値を使用して、新しい **TXT** レコード セットを作成します。

   | 設定  | 値                                                                           |
   | -------- | ------------------------------------------------------------------------------- |
   | 名前     | ルート ドメインの場合は `@`、サブドメインの場合は `_dnsauth.<YOUR_SUBDOMAIN>` と入力します         |
   | 型     | TXT                                                                             |
   | TTL      | 既定値のままにする                                                          |
   | TTL の単位 | 既定値のままにする                                                          |
   | 値    | クリップボードからコードを貼り付ける                                              |

1. **[OK]** を選択します。

   :::image type="content" source="media/custom-domain/azure-dns-txt.png" alt-text="名前、タイプ、値のフィールドが強調表示された Azure DNS レコード セットの画面":::

[!INCLUDE [validate TXT record](../../includes/static-web-apps-validate-txt.md)]

# <a name="other-dns"></a>[その他の DNS](#tab/other-dns)

1. "ホスト名レコード タイプ"ドロップダウンが "TXT" に設定されていることを確認します。

1. **[コードの生成]** ボタンを選択します。

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="コードの生成ボタンが強調表示されているカスタムの追加画面":::

   この操作によって一意のコードが生成されます (処理には最大で 1 分かかる場合があります)。

1. コードの横にあるクリップボード アイコンを選択して、値をクリップボードにコピーします。

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="コードのコピーボタンが強調表示されているカスタム ドメインの追加画面":::

1. 別のブラウザー タブまたはウィンドウで、ドメイン プロバイダーの Web サイトにサインインします。

1. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

   > [!NOTE]
   > 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[ドメイン]** などのリンクを探します。 そのページに移動して、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

1. 次の値を使用して、新しい **CNAME** レコードを作成します

   | 設定             | 値                                                                        |
   | ------------------- | ---------------------------------------------------------------------------- |
   | 型                | TXT                                                                          |
   | Host                | ルート ドメインの場合は `@`、サブドメインの場合は `_dnsauth.<YOUR_SUBDOMAIN>` と入力します      |
   | 値               | クリップボードからコードを貼り付ける                                           |
   | TTL (該当する場合) | 既定値のままにする                                                       |

> [!NOTE]
> 一部の DNS プロバイダーでは、ルート ドメイン (mydomain.com など) に自動的に "@" to indicate a root domain or they change the "@" とは異なる規則を使用しています。 これは仕様で、検証プロセスは変わらず機能します。

[!INCLUDE [create repository from template](../../includes/static-web-apps-validate-txt.md)]

---

## <a name="create-an-alias-record"></a>ALIAS レコードを作成する

ALIAS レコードでは、あるドメインが別のドメインにマップされます。 これは、特にルート ドメイン (つまり `mydomain.com`) に使用されます。 このセクションでは、ルート ドメインを静的 Web アプリの自動生成 URL にマップする ALIAS レコードを作成します。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

> [!IMPORTANT]
> Azure DNS ゾーンは、静的 Web アプリと同じサブスクリプションに含める必要があります。

1. Azure portal でドメインの Azure DNS ゾーンを開きます。

1. **[レコード セット]** ボタンを選択します。

1. 次の値を使用して、新しい **A** レコード セットを作成します。

   | 設定          | 値                              |
   | ---------------- | ---------------------------------- |
   | 名前             | @                                  |
   | 型             | A - IPv4 アドレスへのエイリアス レコード   |
   | エイリアス レコード セット | Yes                                |
   | エイリアスの種類       | Azure リソース                     |
   | サブスクリプション     | \<Your Subscription>               |
   | Azure リソース   | \<Your Static Web App>             |
   | TTL              | 既定値のままにする             |
   | TTL の単位         | 既定値のままにする             |

1. **[OK]** を選択します。

   :::image type="content" source="media/custom-domain/azure-dns-alias.png" alt-text="名前、タイプ、別名、およびリソースのフィールドが強調表示された Azure DNS レコード セットの画面":::

これで、ルート ドメインが構成されました。DNS プロバイダーによって世界規模で変更が反映されるには、数時間かかる場合があります。

# <a name="other-dns"></a>[その他の DNS](#tab/other-dns)

> [!IMPORTANT]
> ドメイン プロバイダーでは、[ALIAS](../dns/dns-alias.md) または ANAME レコード、または CNAME フラット化がサポートされている必要があります。

1. [Azure portal](https://portal.azure.com) で静的 Web アプリを開きます。

1. メニューで **[カスタム ドメイン]** を選択します。

1. カスタム ドメイン画面から静的 Web アプリの自動生成 URL をコピーします。

   :::image type="content" source="media/custom-domain/auto-generated.png" alt-text="コピー URL アイコンが強調表示された静的 Web アプリの概要ページ":::

1. ドメイン プロバイダーの Web サイトにサインインします。

1. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

   > [!NOTE]
   > 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[ドメイン]** などのリンクを探します。 そのページに移動して、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

1. 次の値を使用して、新しい **ALIAS** レコードを作成します

   | 設定             | 値                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | 型                | ALIAS または ANAME (ALIAS が使用できない場合は CNAME を使用)                    |
   | Host                | @                                                              |
   | 値               | クリップボードからドメイン名を貼り付ける                      |
   | TTL (該当する場合) | 既定値のままにする                                         |

> [!IMPORTANT]
> ドメイン プロバイダーで ALIAS または ANAME レコード タイプが提供されていない場合は、代わりに CNAME タイプを使用してください。 多くのプロバイダーは、CNAME レコードのタイプと "CNAME フラット化" と呼ばれる機能を使用して、ALIAS レコードのタイプと同じ機能を提供しています。

これで、ルート ドメインが構成されました。DNS プロバイダーによって世界規模で変更が反映されるには、数時間かかる場合があります。

---

## <a name="redirect-requests-to-a-default-domain"></a>要求を既定のドメインにリダイレクトする

静的 Web アプリには、自動的に生成されたドメインと、構成したカスタム ドメインを使用してアクセスできます。 必要に応じて、すべてのトラフィックを既定のドメインにリダイレクトするようアプリを構成できます。

### <a name="set-a-default-domain"></a>既定のドメインを設定する

カスタム ドメインをアプリの既定のドメインとして指定すると、他のドメインへの要求は既定のドメインに自動的にリダイレクトされます。 既定として設定できるカスタム ドメインは 1 つのみです。

次の手順に従って、カスタム ドメインを既定として設定します。

1. 静的 Web アプリを Azure portal で開いた状態で、メニューから **[カスタム ドメイン]** を選択します。

1. 既定のドメインとして構成するカスタム ドメインを選択します。

1. **[既定値に設定]** を選択します。

   :::image type="content" source="media/custom-domain/set-default.png" alt-text="カスタム ドメインを既定として設定する":::

1. 操作が完了したら、テーブルを更新して、ドメインが "既定" としてマークされていることを確認します。

### <a name="unset-a-default-domain"></a>既定のドメインを設定解除する

ドメインが既定のドメインにリダイレクトされないようにするには、次の手順に従います。

1. 静的 Web アプリを Azure portal で開いた状態で、メニューから **[カスタム ドメイン]** を選択します。

1. 既定として構成したカスタム ドメインを選択します。

1. **[Unset default]\(既定を設定解除する\)** を選択します。

1. 操作が完了したら、テーブルを更新して、"既定" としてマークされているドメインがないことを確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](application-settings.md)
