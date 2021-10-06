---
title: 正規化と Azure Sentinel 情報モデル (ASIM) | Microsoft Docs
description: この記事では、Azure Sentinel が Azure Sentinel 情報モデル (ASIM) を使用してさまざまなソースからデータを正規化する方法について説明します
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: e90171faf59daf70bde2150036fdf0a757e53979
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621397"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>正規化と Azure Sentinel 情報モデル (ASIM) (パブリック プレビュー)

Azure Sentinel は、多くのソースからデータを取り込みます。 さまざまなデータ型とテーブルを組み合わせて使用するには、それらを理解し、各型またはスキーマの分析ルール、ブック、ハンティング クエリに対して一意のデータ セットを記述し、使用する必要があります。


データ型がファイアウォール デバイスなどの共通要素を共有している場合でも、個別のルール、ブック、クエリが必要な場合があります。 調査とハンティング中は、さまざまな種類のデータを関連付けるのも困難な場合があります。

この記事では、Azure Sentinel 情報モデル (ASIM) の概要について説明します。これにより、複数の種類のデータを扱う際の課題に対処するためのソリューションが利用できます。

> [!TIP]
> また、[ASIM ウェビナー](https://www.youtube.com/watch?v=WoGD-JeC7ng)を視聴したり、[ウェビナーのスライド](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)を確認したりしてください。 詳細については、「[次のステップ](#next-steps)」を参照してください。
>

> [!IMPORTANT]
> 現在、ASIM はプレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="common-asim-usage"></a>一般的な ASIM の使用

Azure Sentinel 情報モデル (ASIM) は、次の機能に用意することで、様々なソースを統一された正規のビューで扱うためのシームレスなエクスペリエンスを提供します。

- **クロス ソース検出**。 正規化された分析ルールが、ソース、オンプレミス、クラウドにわたって適用され、ブルート フォースや、Okta、AWS、Azure といったシステム間でのあり得ない移動などの攻撃が検出されます。

- **ソースに依存しないコンテンツ**。 ASIM を使用した組み込みコンテンツとカスタム コンテンツの両方のカバレッジは、コンテンツの作成後にソースが追加された場合でも、ASIM をサポートする任意のソースに自動的に拡張されます。 たとえば、プロセス イベント分析では、Microsoft Defender for Endpoint、Windows イベント、Sysmon など、ユーザーがデータを取り込むために使用できるすべてのソースがサポートされます。

- 組み込み分析における **カスタム ソースのサポート**

- **使いやすさ**。 アナリストが ASIM を学習した後は、フィールド名が常に同じであるため、クエリの記述がはるかに簡単になります。

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM と Open Source Security Events Metadata

Azure Sentinel 情報モデルは、[Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) の共通情報モデルと連携して、正規化されたテーブル間での予測可能なエンティティの相関を実現します。

OSSEM は、多様なデータ ソースとオペレーティング システムからのセキュリティ イベント ログのドキュメントと標準化に主に重点を置いたコミュニティ主導のプロジェクトです。 さらに、このプロジェクトでは、データ エンジニアがデータの正規化手順時に使用できる Common Information Model (CIM) を提供しているため、セキュリティ アナリストが多様なデータソースで、データをクエリし、分析できます。

詳細については、「[OSSEM のリファレンス ドキュメント](https://ossemproject.com/cdm/guidelines/entity_structure.html)」を参照してください。

## <a name="asim-components"></a>ASIM コンポーネント

次の図は、正規化されていないデータを正規化されたコンテンツに変換し、Azure Sentinel で使用する様子を示しています。 たとえば、カスタムの製品固有の正規化されていないテーブルから始め、パーサーと正規化スキーマを使用して、そのテーブルを正規化されたデータに変換できます。 Microsoft とカスタム分析、ルール、ブック、クエリの両方で正規化されたデータを使用します。

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Azure Sentinel での非正規から正規へのデータ変換フローと使用方法":::

Azure Sentinel Information Model には、次のコンポーネントが含まれています。

|コンポーネント  |説明  |
|---------|---------|
|**正規化されたスキーマ**     |   統合機能を構築する際に使用できる予測可能なイベント種類の標準セットについて説明します。 <br><br>各スキーマは、イベントを表すフィールド、正規化された列の名前付け規則と、フィールド値の標準形式を定義します。 <br><br> 現在、ASIM では次のスキーマが定義されています。<br> - [ネットワーク セッション](normalization-schema.md)<br> - [DNS アクティビティ](dns-normalization-schema.md)<br> - [プロセス イベント](process-events-normalization-schema.md)<br> - [認証イベント](authentication-normalization-schema.md)<br> - [レジストリ イベント](registry-event-normalization-schema.md)<br> - [ファイル アクティビティ](file-event-normalization-schema.md)  <br><br>詳細については、「[Azure Sentinel 情報モデルのスキーマ](normalization-about-schemas.md)」を参照してください。  |
|**パーサー**     |  [KQL 関数](/azure/data-explorer/kusto/query/functions/user-defined-functions)を使用して、既存のデータを正規化されたスキーマにマップします。 <br><br>Microsoft が開発した正規化パーサーを、[Azure Sentinel GitHub パーサー フォルダー](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) からデプロイします。 正規化されたパーサーは、**ASim*** で始まるサブフォルダーに存在します。  <br><br>詳細については、「[Azure Sentinel 情報モデルのパーサー](normalization-about-parsers.md)」を参照してください。     |
|**正規化されたスキーマごとのコンテンツ**     |    これには、分析ルール、ブック、ハンティング クエリなどが含まれます。 正規化されたスキーマごとのコンテンツは、ソース固有のコンテンツを作成することなく、正規化されたデータに対して機能します。 <br><br>詳細については、「[Azure Sentinel 情報モデルのコンテンツ](normalization-content.md)」を参照してください。   |
| | |

### <a name="asim-terminology"></a>ASIM の用語

Azure Sentinel Information Model では、次の用語を使用します。

|期間  |説明  |
|---------|---------|
|**レポート デバイス**     |   レコードを Azure Sentinel に送信するシステム。 このシステムは、送信されるレコードのサブジェクト システムではない可能性があります。      |
|**レコード**     |レポート デバイスから送信されるデータの単位。 レコードは、`log`、`event`、`alert` と呼ばれることが多いですが、他の種類のデータであることもあります。         |
|**コンテンツ**、または **コンテンツ項目**     |Azure Sentinel で使用可能な、異なる、カスタマイズ可能な、またはユーザーが作成した成果物です。 これらの成果物には、分析ルール、ハンティング クエリ、ブックなどが含まれます。 コンテンツ項目は、そのような成果物の 1 つです。|
| | |

<br>

## <a name="getting-started-with-asim"></a>ASIM の使用を開始する

ASIM の使用を開始するには:

1. すべての ASIM パーサーを [Azure Sentinel GitHub リポジトリ](https://aka.ms/AzSentinelASim)からすばやくデプロイします。

1. ASIM を使用する分析ルールのテンプレートをアクティブにします。 詳細については、「[Azure Sentinel 情報モデル (ASIM) のコンテンツ リスト](normalization-content.md#builtin)」を参照してください。

1. 次の方法を使用して、ワークスペースで ASIM を使用します。

    - Azure Sentinel の **[ログ]** ページで KQL のログをクエリする場合は、Azure Sentinel GitHub リポジトリからの ASIM ハンティング クエリを使用します。 詳細については、「[Azure Sentinel 情報モデル (ASIM) のコンテンツ リスト](normalization-content.md#builtin)」を参照してください。

    - ASIM を使用して独自の分析ルールを作成するか、[既存のものを変換](normalization-content.md#builtin)します。

    - カスタム ソースの[パーサーを作成](normalization-about-parsers.md)し、それらを関連するソースに依存しないパーサーに[追加](normalization-about-parsers.md#include)することによって、カスタム データが組み込みの分析を使用できるようにします。

## <a name="next-steps"></a><a name="next-steps"></a>次のステップ

この記事では、Azure Sentinel での正規化の概要と、Azure Sentinel 情報モデルについて説明しています。

詳細については、次を参照してください。

- [ASIM ウェビナー](https://www.youtube.com/watch?v=WoGD-JeC7ng)を視聴したり、[スライド](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)を確認したりしてください
- [Azure Sentinel 情報モデルのスキーマ](normalization-about-schemas.md)
- [Azure Sentinel 情報モデルのパーサー](normalization-about-parsers.md)
- [Azure Sentinel 情報モデルのコンテンツ](normalization-content.md)
