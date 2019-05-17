---
title: Azure App Configuration の FAQ | Microsoft Docs
description: Azure App Configuration に関してよく寄せられる質問
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411853"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration の FAQ

この記事では、Azure App Configuration についてよく寄せられる質問にお答えします。

## <a name="how-is-app-configuration-different-from-key-vault"></a>App Configuration と Key Vault は何が違うのですか?

App Configuration は、開発者がアプリケーションの設定を管理したり機能の利用の可否を制御したりできるよう支援するという、明確なユース ケースを意図して設計されています。 複雑な構成データの扱いに伴うさまざまなタスクを単純化することを目的としています。 階層型名前空間、ラベル付け、充実したクエリ、一括取得、目的に特化した管理操作とユーザー インターフェイスをサポートしています。 App Configuration は、Key Vault を補完するものであり、両者は、ほとんどのアプリケーションのデプロイにおいて一緒に使用すべきものです。

## <a name="should-i-store-secrets-in-app-configuration"></a>App Configuration にはシークレットを格納すべきなのでしょうか?

App Configuration は強固なセキュリティを備えていますが、それでもアプリケーション シークレットの保管場所としては Key Vault が最も優れています。 ハードウェアレベルの暗号化、粒度の細かいアクセス ポリシー、管理操作 (証明書のローテーションなど) が利用できます。

## <a name="does-app-configuration-encrypt-my-data"></a>App Configuration によってデータが暗号化されるのですか?

はい。 App Configuration は、そこに格納されているすべてのキー値とネットワーク通信を暗号化します。 キー名は、構成データを取得するためのインデックスとして使用され、暗号化されません。

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>App Configuration は Azure Virtual Network (VNET) をサポートしていますか?

まだありません。 VNET のサポートは一般提供で予定されています。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>複数の環境 (テスト、ステージング、本番など) の構成は、どのように格納すべきでしょうか?

現在、App Configuration にアクセスできるユーザーをストアごとのレベルで制御できます。 環境ごとに異なるアクセス許可を必要とする別個のストアを使用することをお勧めします。 セキュリティ分離上、このアプローチが最も優れています。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>App Configuration のおすすめの使い方を教えてください。

[ベスト プラクティス](./howto-best-practices.md)を参照してください。

## <a name="how-much-does-app-configuration-cost"></a>App Configuration にはどのぐらいのコストがかかりますか?

パブリック プレビュー期間中は、このサービスを無料で使用できます。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>問題のレポート方法または提案の送信方法を教えてください。

[GitHub](https://github.com/Azure/AppConfiguration/issues) から直接お寄せいただけます。

## <a name="next-steps"></a>次の手順

* [Azure App Configuration について](./overview.md)
