---
title: Azure API Management の開発者ポータルの概要
titleSuffix: Azure API Management
description: API Management の開発者ポータルについて説明します。開発者ポータルは、API コンシューマーが API を確認することができるカスタマイズ可能な Web サイトです。
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4fa9b0e777ce64322ebad74c8279724817530790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664582"
---
# <a name="overview-of-the-developer-portal"></a>開発者ポータルの概要

開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーはここで API を見つけ、使用方法を確認し、アクセスを要求し、試すことができます。

この記事で紹介しているように、特定のシナリオに合わせて開発者ポータルをカスタマイズし、拡張することができます。 

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>従来のポータルから移行する

> [!IMPORTANT]
> 従来の開発者ポータルは非推奨となり、セキュリティ更新プログラムのみを受け取るようになりました。 2023 年 10 月に廃止され、すべての API Management サービスから削除されるまでは、通常どおりに引き続き使用できます。

新しい開発者ポータルへの移行については、[専用のドキュメント記事](developer-portal-deprecated-migration.md)で説明されています。

## <a name="customization-and-styling"></a>カスタマイズとスタイル設定

開発者ポータルは、ドラッグ アンド ドロップで操作できる組み込みのビジュアル エディターを使用して、カスタマイズおよびスタイル設定できます。 詳細については、[このチュートリアル](api-management-howto-developer-portal-customize.md)を参照してください。

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 拡張性

API Management サービスには、常に最新の組み込みの **マネージド** 開発者ポータルが含まれています。 Azure portal インターフェイスからアクセスできます。

既定ではサポートされていないカスタム ロジックを使用して拡張する必要がある場合は、そのコードベースを変更できます。 ポータルのコードベースは、[GitHub リポジトリで入手できます](https://github.com/Azure/api-management-developer-portal)。 たとえば、サードパーティのサポート システムと統合する新しいウィジェットを実装できます。 新しい機能を実装する場合は、次のいずれかのオプションを選択できます。

- API Management サービスの外部で、実装後のポータルを **セルフホスト** します。 ポータルをセルフホストする場合、管理者が保守を行う必要があり、アップグレードの責任を負うことになります。 Azure サポートによる支援は、[セルフホステッド ポータルの基本的なセットアップ](developer-portal-self-host.md)のみに限定されます。
- **マネージド** ポータルのコードベースに新機能をマージするように、API Management チームに対してプル要求を開きます。

拡張性に関する詳細および手順については、[GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)と、[ウィジェットの実装](developer-portal-implement-widgets.md)に関するチュートリアルを参照してください。 [マネージド ポータルのカスタマイズ](api-management-howto-developer-portal-customize.md)のチュートリアルに関するページでは、ポータルの管理パネルについて説明しています。これは、**マネージド** バージョンと **セルフホステッド** バージョンで共通です。


## <a name="next-steps"></a>次のステップ

新しい開発者ポータルの詳細を確認します:

- [マネージド開発者ポータルへのアクセスとカスタマイズ](api-management-howto-developer-portal-customize.md)
- [ポータルのセルフホステッド バージョンの設定](developer-portal-self-host.md)
- [独自のウィジェットの実装](developer-portal-implement-widgets.md)

その他のリソースを参照します:

- [ソース コードがある GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)
- [開発者ポータルについてよく寄せられる質問](developer-portal-faq.md)
