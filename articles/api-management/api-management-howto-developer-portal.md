---
title: Azure API Management 開発者ポータルの概要 - Azure API Management | Microsoft Docs
description: API Management の開発者ポータルについて説明します。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1eef7b6505c4800acbe8aa69cf6f17eecc503aed
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796020"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management 開発者ポータルの概要

開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーはここで API を見つけ、使用方法を確認し、アクセスを要求し、試すことができます。

この記事では、API Management の開発者ポータルのセルフホステッド バージョンとマネージド バージョンの違いについて説明します。 また、アーキテクチャについて説明し、よく寄せられる質問とその回答を紹介します。

> [!WARNING]
> 新しい開発者ポータルは、現在 API Management サービスにロールアウト中です。
> サービスが新しく作成したものである場合や、Developer レベルのサービスの場合は、最新バージョンが既に存在している必要があります。 そうではない場合、問題が発生する可能性があります (たとえば、発行機能を使用する場合)。 機能のロールアウトは、2019 年 11 月 11 日に完了します。
>
> 開発者ポータルの[プレビュー バージョンから一般公開バージョンへの移行方法について確認](#preview-to-ga)してください。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>マネージド バージョンとセルフホステッド バージョン

開発者ポータルは 2 つの方法で構築できます。

- **マネージド バージョン** - API Management インスタンスに組み込まれたポータルを編集およびカスタマイズします。URL `<your-api-management-instance-name>.developer.azure-api.net` からアクセスできます。 マネージド ポータルにアクセスしてカスタマイズする方法については、[こちらのドキュメント記事](api-management-howto-developer-portal-customize.md)を参照してください。
- **セルフホステッド バージョン** - API Management インスタンスの外部にポータルをデプロイし、自らホスティングします。 このアプローチを使用すると、ポータルのコードベースを編集したり、提供されているコア機能を拡張したりできます。 また、自分でポータルを最新バージョンにアップグレードする必要もあります。 詳細および手順については、[ポータルのソース コードを含む GitHub リポジトリ][1]を参照してください。 [マネージド バージョンのチュートリアル](api-management-howto-developer-portal-customize.md)では、ポータルの管理パネルについて説明します。これは、セルフホステッド バージョンでも取り上げられています。

## <a name="portal-architectural-concepts"></a>ポータルのアーキテクチャの概念

ポータルのコンポーネントは、*コード*と*コンテンツ*の 2 つのカテゴリに論理的に分類することができます。

*コード*は [GitHub リポジトリ][1]で保持されており、次のものが含まれています。

- ウィジェット - ビジュアル要素を表します。HTML、JavaScript、スタイル設定機能、設定、およびコンテンツ マッピングが結合されます。 例としては、画像、テキスト段落、フォーム、API 一覧などがあります。
- スタイル定義 - ウィジェットのスタイル設定の方法を指定します。
- エンジン - ポータル コンテンツから静的 Web ページを生成します。JavaScript で記述されています。
- ビジュアル エディター - ブラウザー内でのカスタマイズおよび作成エクスペリエンスを可能にします。

*コンテンツ*は、*ポータル コンテンツ*と *API Management コンテンツ*の 2 つのサブカテゴリに分類されます。

*ポータル コンテンツ*はポータルに固有のものであり、次のものが含まれます。

- ページ - たとえば、ランディング ページ、API チュートリアル、ブログ投稿など
- メディア - 画像、アニメーション、およびその他のファイルベースのコンテンツ
- レイアウト - URL に対して照合され、ページの表示方法を定義するテンプレート
- スタイル - フォント、色、罫線などのスタイル定義の値
- 設定 - お気に入りアイコン、Web サイト メタデータなどの構成

*ポータル コンテンツ*は、メディアを除き、JSON ドキュメントとして表現されます。

*API Management コンテンツ*には、API、操作、製品、サブスクリプションなどのエンティティが含まれています。

ポータルは、[Paperbits フレームワーク](https://paperbits.io/)の適合されたフォークに基づいています。 元の Paperbits の機能が拡張され、API Management 固有のウィジェット (API の一覧、製品の一覧など)、およびコンテンツを保存および取得するための API Management サービスへのコネクタが提供されるようになりました。

## <a name="faq"></a>よく寄せられる質問

このセクションでは、新しい開発者ポータルに関してよく寄せられる一般的な質問に回答します。 セルフホステッド バージョンに関する質問については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki)を参照してください。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> ポータルのプレビューバージョンから移行するにはどうすればよいですか。

開発者ポータルのプレビュー バージョンを使用して、API Management サービスのプレビュー コンテンツのプロビジョニングを行いました。 ユーザー エクスペリエンスを向上させるために、一般公開バージョンでは既定のコンテンツが大幅に変更されています。 また、新しいウィジェットも含まれています。

マネージド バージョンを使用している場合は、 **[操作]** メニュー セクションの **[コンテンツのリセット]** をクリックして、ポータルのコンテンツをリセットします。 この操作を確認すると、ポータルのすべてのコンテンツが削除され、新しい既定のコンテンツがプロビジョニングされます。 ポータルのエンジンは、API Management サービスで自動的に更新されています。

![ポータル コンテンツのリセット](media/api-management-howto-developer-portal/reset-content.png)

セルフホステッド バージョンを使用している場合は、GitHub リポジトリの `scripts/cleanup.bat` と `scripts/generate.bat` を使用して、既存のコンテンツを削除し、新しいコンテンツをプロビジョニングします。 必ず事前に、ポータルのコードを GitHub リポジトリから最新リリースにアップグレードしてください。

ポータルのコンテンツをリセットしたくない場合は、新しく利用可能となったウィジェットをページ全体で使用することを検討してください。 既存のウィジェットは、最新バージョンに自動的に更新されています。

一般公開の発表後にポータルをプロビジョニングした場合は、新しい既定のコンテンツが既に取り入れられているはずです。 ユーザー側の操作は必要ありません。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>以前の開発者ポータルから新しい開発者ポータルに移行するにはどうすればよいですか。

ポータルには互換性がないため、手動でコンテンツを移行する必要があります。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新しいポータルには古いポータルのすべての機能がありますか。

新しい開発者ポータルでは、"*アプリケーション*" と "*問題*" はサポートされていません。 以前のポータルで "*問題*" を使用していて、新しいポータルでも必要な場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/122)にコメントを投稿してください。

### <a name="has-the-old-portal-been-deprecated"></a>古いポータルは非推奨となりましたか。

以前の開発者ポータルと発行者ポータルは、*レガシ機能*となりました。セキュリティ更新プログラムのみ受け取ることになります。 新しい機能は、新しい開発者ポータルにのみ実装されます。

レガシ ポータルの廃止については、あらためて発表されます。 質問、懸念事項、またはコメントがある場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/121)に提起してください。

### <a name="how-can-i-automate-portal-deployments"></a>ポータルのデプロイを自動化するにはどうすればよいですか。

マネージド バージョンとセルフホステッド バージョンのどちらを使用している場合でも、REST API を通じて開発者ポータルのコンテンツにプログラムでアクセスし、管理することができます。

API については、[GitHub リポジトリの wiki セクション][2]に記載されています。 また、たとえばテスト環境から運用環境など、環境間でのポータル コンテンツの移行を自動化するために使用することもできます。 このプロセスの詳細については、GitHub にある[このドキュメント記事](https://aka.ms/apimdocs/migrateportal)を参照してください。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>ポータルは、Azure Resource Manager テンプレートをサポートしていますか。また、API Management DevOps リソース キットと互換性がありますか。

No.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>マネージド ポータルの依存関係に対して追加の VNET 接続を有効にする必要がありますか。

No.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>対話型コンソールを使用すると、CORS エラーが発生します。 どうすればよいですか。

対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)を追加して、CORS の問題を解決することができます。 すべてのパラメーターを手動で指定するか、ワイルドカード `*` の値を使用することができます。 例:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

## <a name="next-steps"></a>次の手順

新しい開発者ポータルの詳細を確認します:

- [マネージド開発者ポータルへのアクセスとカスタマイズ](api-management-howto-developer-portal-customize.md)
- [ポータルのセルフホステッド バージョンの設定][2]

その他のリソースを参照します:

- [ソース コードがある GitHub リポジトリ][1]
- [プロジェクトのパブリック ロードマップ][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects