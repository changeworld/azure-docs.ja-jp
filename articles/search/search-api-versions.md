<properties
   pageTitle="Azure Search の API バージョン | Microsoft Azure | Search API"
   description="Azure Search REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# Azure Search の API バージョン

Azure Search は、機能の更新を定期的にロールアウトします。このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンを公開することが必要な場合があります (毎回とは限りません)。新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

原則として、新しい API バージョンの発行は、必要な場合にのみ行うようにしています。お客様側で新しいバージョンを使用できるようにコードをアップグレードする作業が必要になる可能性があるからです。下位互換性が保たれなくなる形で API の一部の要素を変更する必要がある場合に限り、新しいバージョンを発行します。下位互換性は、既存の機能に変更を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で維持できなくなることがあります。

SDK 更新プログラムの場合も同じルールに従っています。Azure Search SDK は[セマンティック バージョニング](http://semver.org/) ルールに従います。そのため、バージョンにはメジャー、マイナー、およびビルド番号という 3 つの部分があります (たとえば、1.1.0)。SDK の新しいメジャー バージョンは、下位互換性が保たれなくなる変更が生じた場合にのみリリースされます。下位互換性が保たれる機能更新の場合は、マイナー バージョンがインクリメントされ、バグ修正の場合は、ビルド バージョンのみがインクリメントされます。

##現在のバージョンのスナップショット 

Azure Search とのすべてのプログラミング インターフェイスの現行バージョンのスナップショットを次に示します。このドキュメントの後続のセクションでは、ロードマップとその他の詳細について説明します。

インターフェイス|最新のメジャー バージョン|状態
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1\.1|一般公開、2016年 2 月にリリース済み
[サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|一般公開
[サービス REST API プレビュー](search-api-2015-02-28-preview.md)|2015-02-28-Preview|Preview
[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|一般公開

REST API の場合は、各呼び出しに対して `api-version` を含める必要があります。これにより、容易にプレビュー API などの特定のバージョンを対象にすることができます。次の例に、`api-version` パラメーターを指定する方法を示します。

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] 各要求で `api-version` を指定するとしても、すべての API 要求で同じバージョンを使用することをお勧めします。このことは、特に、新しい API バージョンが、以前のバージョンで認識されない属性または操作を導入している場合に当てはまります。API バージョンを混在させると、意図しない結果を招くおそれがあるので、お勧めしません。
> 
サービス REST API と管理 REST API は、それぞれ別々にバージョン管理されます。バージョン番号の類似性は偶然によるものです。

一般公開 (またはGA) の API は、実稼働環境で使用でき、Azure サービス レベル アグリーメントの対象です。プレビュー バージョンには、必ずしも GA バージョンに移行されるとは限らない実験用の機能が含まれています。**実稼働アプリケーションではプレビュー API を使用しないことを強くお勧めします。**

##SDK バージョンのロードマップ

.NET SDK の各バージョンは、サービス REST API の特定のバージョンを対象としています。機能はまず REST API でロールアウトされ、次に SDK で実装されます。

.NET SDK は現在一般に公開されており、次のバージョンに向けた作業が進行中です。下表では、次に登場するものを把握できるように、SDK の今後のバージョンに目を向けます。

.NET SDK バージョン|REST API バージョン|特徴|ETA
----------------|----------------|--------|---
1\.1|2015-02-28|Lucene クエリ構文|2016 年 2 月
2\.x-preview|2015-02-28-Preview|カスタム アナライザー、Azure BLOB インデクサー、フィールド マッピング、ETag|機能の配布は、2016 年の第 1 四半期に開始します
2\.x|新しい GA API バージョン|2\.x-preview と同じ|2\.x-preview の直後に完了

##プレビュー バージョンと一般公開バージョンについて

Azure Search の場合は常に、まず REST API によって、次に .NET SDK のプレリリース版によって実験機能を事前に公開します。プレビュー機能の一覧については、「[Azure Search 関連の最新の更新履歴情報](search-latest-updates.md)」を参照してください。

プレビュー機能は必ずしも GA リリースに移行されるとは限りません。GA バージョンの機能は安定しており、小規模の下位互換性のある修正プログラムと機能強化以外は変更される可能性は少ないですが、プレビュー機能は機能の設計や実装についてのフィードバックを収集する目的で、テストや実験的な目的に使用できます。

ただし、プレビュー機能は変更される可能性があるため、プレビュー バージョンに依存した実稼働コードの作成はお勧めしません。古いプレビュー バージョンを使用している場合は、一般公開 (GA) バージョンに移行することをお勧めします。

- .NET SDK の場合: コード移行のガイダンスについては、「[Upgrade the .NET SDK (.NET SDK のアップグレード)](search-dotnet-sdk-migration.md)」を参照してください。
- REST API の場合: コード移行のガイダンスについては、「[Transition from preview to GA (プレビューから GA への移行)](search-transition-from-preview.md)」を参照してください。

一般的な可用性とは、Azure Search がサービス レベル アグリーメント (SLA) の下で稼働することを意味します。SLA については、「[Azure Search サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)」を参照してください。

<!---HONumber=AcomDC_0309_2016-->