---
title: Azure App Configuration とは | Microsoft Docs
description: Azure App Configuration サービスの概要。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885021"
---
# <a name="what-is-azure-app-configuration"></a>Azure App Configuration とは

Azure App Configuration は、アプリケーション設定を一元的に管理するためのサービスを提供します。 近年のプログラム、特にクラウドで実行されるプログラムは、その性質上、分散されたコンポーネントが多数存在するのが一般的です。 これらのコンポーネント全体に構成設定を分散させることは、トラブルシューティングすることの難しいエラーがアプリケーションのデプロイ中に発生する原因となります。 App Configuration を使用すると、アプリケーションのすべての設定を 1 か所に格納して、そのアクセスをセキュリティで保護することができます。

## <a name="why-use-app-configuration"></a>App Configuration を使用する理由

クラウドベースのアプリケーションは、複数リージョンにわたる複数の仮想マシンまたはコンテナーで実行され、複数の外部サービスを使用することが少なくありません。 そのような分散アプリケーションを堅牢でスケーラブルに作成することは、本当に難しいことです。 そうしたアプリケーションの構築に伴う増大する複雑さに取り組む開発者を支援するために、さまざまなプログラミング手法が提起されてきました。 たとえば、"12 ファクター アプリ" には、クラウド アプリケーションで使用される実証済みのアーキテクチャ パターンとベスト プラクティスが列挙されています。 このガイドの主な推奨事項の 1 つは、コードから構成を切り離すことです。 つまり、アプリケーションの構成 (設定など) をその実行可能ファイルとは別の場所に置いておき、その実行環境または外部ソースから読み取るということです。

これは任意のアプリケーションから利用できますが、App Configuration の利用が推奨されるアプリケーションの種類として、適切な例を次に示します。

* AKS、Service Fabric など、1 つまたは複数の地域にデプロイされるコンテナー化されたアプリをベースにしたマイクロサービス。
* サーバーレス アプリ (Azure Functions などイベント駆動型のステートレスなコンピューティング アプリなど)。
* 継続的デプロイ パイプライン。

App Configuration には次の利点があります。

* 数分で設定できるフル マネージド サービス。
* 柔軟性に優れたキーによる表現とマッピング。
* ラベルを使用したタグ付け。
* 特定時点の設定の再生。
* 独自に定義したディメンションでの 2 つの構成群の比較。
* Azure のマネージド ID で強化されたセキュリティ。
* 完全なデータ暗号化 (保存データと転送中のデータ)。
* 広く使われているフレームワークとのネイティブ統合。

## <a name="how-to-use-app-configuration"></a>App Configuration の使用方法

アプリ構成ストアを自分のアプリケーションに追加する最も簡単な方法は、Microsoft が提供するクライアント ライブラリを使用することです。 プログラミング言語とフレームワークに応じて、使用できる最適な方法を次に示します。

| プログラミング言語とフレームワーク | 接続する方法 |
|---|---|
| **.NET Core** と **ASP.NET Core** | .NET Core 用 App Configuration 構成プロバイダー。 |
| **.NET** と **ASP.NET** | .NET 用 App Configuration 構成ビルダー |
| **Java Spring** | Spring Cloud 用 App Configuration 構成クライアント。 |
| その他 | App Configuration の REST API。 |

## <a name="next-steps"></a>次の手順

* [クイック スタート:ASP.NET Web アプリを作成する](quickstart-aspnet-core-app.md)  
