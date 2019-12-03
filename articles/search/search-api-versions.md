---
title: .NET と REST の API のバージョン管理
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03dbb679c25ea692d2c52f80b9493889e367823d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112152"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search の API のバージョン

Azure Cognitive Search では、定期的に機能更新がロールアウトされています。 このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンが必要になる場合があります (毎回とは限りません)。 新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

Azure Cognitive Search のチームでは、原則として必要な場合にのみ新しい API バージョンを公開しています。新しい API バージョンの使用には、お客様側でコードをアップグレードする作業が必要になる可能性があるためです。 下位互換性が保たれなくなる形で API の一部の要素が変更された場合に限り、新しいバージョンが必要です。 このような変更は、既存の機能に修正を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で発生することがあります。

SDK 更新プログラムにも同じルールが適用されます。 Azure Cognitive Search SDK は[セマンティック バージョニング](https://semver.org/) ルールに従っています。つまり、バージョンにはメジャー、マイナー、およびビルド番号という 3 つの部分があります (たとえば、1.1.0)。 SDK の新しいメジャー バージョンは、下位互換性が保たれなくなる変更が生じた場合にのみリリースされます。 下位互換性が保たれる機能更新の場合はマイナー バージョンがインクリメントされ、バグ修正の場合はビルド バージョンのみがインクリメントされます。

> [!NOTE]
> お使いの Azure Cognitive Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートしています。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

## <a name="snapshot-of-current-versions"></a>現在のバージョンのスナップショット
Azure Cognitive Search とのすべてのプログラミング インターフェイスの現行バージョンのスナップショットを次に示します。


| インターフェイス | 最新のメジャー バージョン | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |一般公開、2019 年 5 月にリリース済み |
| [.NET SDK のプレビュー](https://aka.ms/search-sdk-preview) |8.0-preview |プレビュー、2019 年 4 月にリリース済み |
| [サービス REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |一般公開 |
| [サービス REST API 2019-05-06 プレビュー](search-api-preview.md) |2019-05-06 プレビュー |プレビュー |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |3.0 |一般公開 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |一般公開 |

REST API の場合は、各呼び出しに対して `api-version` を含める必要があります。 `api-version` を使用すると、プレビュー API などの特定のバージョンを対象にするのが容易になります。 次の例に、 `api-version` パラメーターを指定する方法を示します。

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> 各要求で `api-version` を指定するとしても、すべての API 要求で同じバージョンを使用することをお勧めします。 このことは、特に、新しい API バージョンが、以前のバージョンで認識されない属性または操作を導入している場合に当てはまります。 API バージョンを混在させると、意図しない結果を招くおそれがあるので、お勧めしません。
>
> サービス REST API と管理 REST API は、それぞれ別々にバージョン管理されます。 バージョン番号の類似性は偶然によるものです。

一般公開 (またはGA) の API は、実稼働環境で使用でき、Azure サービス レベル アグリーメントの対象です。 プレビュー バージョンには、必ずしも GA バージョンに移行されるとは限らない実験用の機能が含まれています。 **運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。**

## <a name="about-preview-and-generally-available-versions"></a>プレビュー バージョンと一般公開バージョンについて
Azure Cognitive Search では常に、まず REST API によって、次に .NET SDK のプレリリース版で実験機能を事前公開しています。

プレビュー機能は、機能の設計と実装に関するフィードバックの収集を目的とするテストと実験のために提供されています。 このため、プレビュー機能は、下位互換性を損なう可能性のある形で、時間の経過と共に変更されることがあります。 これは、下位互換性のある小規模な修正プログラムと機能強化以外は変更される可能性が低い安定した GA バージョンの機能とは対照的です。 また、プレビュー機能は必ずしも GA リリースに反映されません。

そのため、プレビュー バージョンに依存した実稼働コードの作成はお勧めしません。 古いプレビュー バージョンを使用している場合は、一般公開 (GA) バージョンに移行することをお勧めします。

.NET SDK の場合:コード移行のガイダンスについては、[.NET SDK のアップグレード](search-dotnet-sdk-migration-version-9.md)に関する記事を参照してください。

一般提供とは、Azure Cognitive Search がサービス レベル アグリーメント (SLA) の下で使用されることを意味します。 SLA については、[Azure Cognitive Search のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に関する記事を参照してください。
