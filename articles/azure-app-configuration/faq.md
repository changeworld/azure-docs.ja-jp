---
title: Azure App Configuration の FAQ | Microsoft Docs
description: Azure App Configuration に関してよく寄せられる質問
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: dbfb6a1c4c53b1bd255560e688d3dc0cf3835a3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469636"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration の FAQ

この記事では、Azure App Configuration についてよく寄せられる質問にお答えします。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>App Configuration と Azure Key Vault は何が違うのですか?

App Configuration は、開発者がアプリケーションの設定を管理したり機能の利用の可否を制御したりできるよう支援するという、明確なユース ケースを意図して設計されています。 複雑な構成データの扱いに伴うさまざまなタスクを単純化することを目的としています。

App Configuration では、以下がサポートされています。

- 階層型名前空間
- ラベル付け
- 広範なクエリ
- 一括取得
- 特殊化された管理操作
- 機能管理のユーザー インターフェイス

App Configuration は Key Vault を補完するものであり、両者はほとんどのアプリケーションのデプロイにおいて一緒に使用する必要があります。

## <a name="should-i-store-secrets-in-app-configuration"></a>App Configuration にはシークレットを格納すべきなのでしょうか?

App Configuration は強固なセキュリティを備えていますが、それでもアプリケーション シークレットの保管場所としては Key Vault が最も優れています。 Key Vault では、ハードウェアレベルの暗号化、粒度の細かいアクセス ポリシー、管理操作 (証明書のローテーションなど) が利用できます。

## <a name="does-app-configuration-encrypt-my-data"></a>App Configuration によってデータが暗号化されるのですか?

はい。 App Configuration は、そこに格納されているすべてのキー値を暗号化し、ネットワーク通信を暗号化します。 キー名は、構成データを取得するためのインデックスとして使用され、暗号化されません。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>App Configuration に格納されているキーや値に対するサイズ制限はありますか?

1 つのキーと値の項目に対して、10 KB の制限があります。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>複数の環境 (テスト、ステージング、本番など) の構成は、どのように格納すべきでしょうか?

現在、App Configuration にアクセスできるユーザーをストアごとのレベルで制御できます。 異なるアクセス許可を必要とする環境ごとに、個別のストアを使用してください。 セキュリティ分離上、このアプローチが最も優れています。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>App Configuration のおすすめの使い方を教えてください。

[ベスト プラクティス](./howto-best-practices.md)を参照してください。

## <a name="how-much-does-app-configuration-cost"></a>App Configuration にはどのぐらいのコストがかかりますか?

パブリック プレビュー期間中は、このサービスを無料で使用できます。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>App Configuration に関する新しいリリースやその他の情報についてのお知らせを受け取るにはどうすればよいですか?

[GitHub のお知らせ用のリポジトリ](https://github.com/Azure/AppConfiguration-Announcements)にサブスクライブしてください。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>問題のレポート方法または提案の送信方法を教えてください。

[GitHub](https://github.com/Azure/AppConfiguration/issues) から直接お寄せいただけます。

## <a name="next-steps"></a>次の手順

* [Azure App Configuration について](./overview.md)
