---
title: Azure Purview オープンソース ツールとユーティリティの詳細
description: このチュートリアルでは、Azure Purview で使用できるさまざまなツールとユーティリティを一覧表示し、それらの使用方法について説明します。
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/10/2021
ms.openlocfilehash: 8c87adc48a9a717a6ca9796b6d3bb2ec780220f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007579"
---
# <a name="azure-purview-open-source-tools-and-utilities"></a>Azure Purview オープンソース ツールとユーティリティ

この記事では、Azure Purview サービスを数分ですばやく開始するのに役立ついくつかのオープンソース ツールとユーティリティ (コマンド ライン、Python、PowerShell インターフェイス) の一覧を示します。 これらのツールは、Azure Purview 製品グループとオープンソース コミュニティの共同の取り組みによって作成および開発されました。 このようなツールの目的は、Purview の学習、開始、定期的な使用、長期的な導入を簡単かつ非常に高速化することです。

### <a name="intended-audience"></a>対象ユーザー

- 顧客、開発者、ISV、パートナー、エバンジェリスト、愛好家を含む Azure Purview コミュニティ。 

- Azure Purview カタログは [Apache Atlas](https://atlas.apache.org/) に基づいており、Apache Atlas API の完全なサポートを拡張します。 Apache Atlas コミュニティ、愛好家、開発者が、Azure Purview を心から推進し伝えることを歓迎します。

### <a name="purview-customer-journey-stages"></a>Purview の顧客体験ステージ

- "*Purview の学習者*": Azure Purview サービスを初めて使用し、マルチクラウドの統合データ ガバナンス ソリューションがどのように機能するかを理解し、調査することに熱心な学習者。 学習者のセクションには、Purview をデータ ガバナンス市場の他の競合ソリューションと比較対照し、長期使用のために導入する前に試してみたいユーザーが含まれます。

- "*Purview のイノベーター*": 既存および最新の機能を理解し、Purview で予定されている機能を考案し、概念化することに熱心なイノベーター。 この人たちは、顧客向けのソリューションの構築と開発に長けており、次世代の最先端のデータ ガバナンス製品に関する先進的な将来を見越したアイデアを持っています。

- "*Purview の愛好家またはエヴァンジェリスト*": 学習者とイノベーターの組み合わせである愛好家。 この人たちは、Purview についての確かな理解と知識を身につけているため、Purview の採用については楽観視しています。 サービスとしての Purview を伝道し、他の複数の Purview ユーザーと世界中の見込み客を教育するのを支援します。

- "*Purview の導入者*": Purview の立ち上げと探索から移行し、Purview を数か月以上円滑に使用している導入者。

- "*Purview の長期の通常ユーザー*": Purview を 1 年以上使用していて、現在は Azure portal と Purview Studio で最も高度な Purview のユース ケースを自信を持って快適に使用している長期ユーザー。さらに、Purview REST API と、Purview API を介してサポートされる追加のユース ケースについてほぼ完全な知識と認識を持っています。


## <a name="azure-purview-open-source-tools-and-utilities-list"></a>Azure Purview オープンソース ツールとユーティリティの一覧

1. [Purview-API-via-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/README.md) 

    - **推奨される顧客体験のステージ**:"*学習者、イノベーター、愛好家、導入者、長期の通常ユーザー*"
    - **説明**: このユーティリティは、[Azure Purview REST API リファレンス](/rest/api/purview/) Microsoft Docs のセット全体に基づいており、それらをカバーしています。[PowerShell ギャラリーからダウンロードしてインストールします](https://aka.ms/purview-api-ps)。 これは、簡単で使いやすい PowerShell インターフェイスを使用して、文書化されたすべての Purview REST API を実行するのに役立ちます。 コマンドラインとスクリプト化されたメソッドを使用して、定期的および長期的に使用するために Purview API を使用および自動化します。 これは、Azure portal と Purview Studio を使用する GUI の方法と比べた場合に、自動化された方法、バッチ モード、またはスケジュールされた cron ジョブで一括タスクを実行しようとしているお客様にとっての代替手段となります。 詳細なドキュメント、サンプル使用ガイド、セルフヘルプ、および例については、[GitHub の Azure-Purview-API-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell) を参照してください。

1. [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)

    - **推奨される顧客体験のステージ**: "*学習者、イノベーター、愛好家*"
    - **説明**: Purview アカウントの初期セットアップを実行する PowerShell スクリプト。 いくつかの新しい Purview アカウントを 5 分未満で設定しようとしている人に役立ちます。

1. [Purview Lab](https://aka.ms/purviewlab)

    - **推奨される顧客体験のステージ**: "*学習者、イノベーター、愛好家*"
    - **説明**: Purview の多種多様な機能を紹介し、実用的で実践的なアプローチで概念を学習するのに役立つハンズオン ラボです。ここでは、Purview の理解を可能な限り深めるために、各ステップを自分で実際に実行します。

1. [Purview CLI](https://aka.ms/purviewcli)

    - **推奨される顧客体験のステージ**: "*イノベーター、愛好家、導入者、長期の通常ユーザー*"
    - **説明**: [Purview-API-via-PowerShell](https://aka.ms/purview-api-ps) と同様に Purview API を実行する Python ベースのツールですが、PowerShell ベースのフレームワークよりも機能が制限されているか少なくなっています。

1. [Purview Demo](https://aka.ms/pvdemo)

    - **推奨される顧客体験のステージ**: "*学習者、イノベーター、愛好家*"
    - **説明**: 1 つのコマンドを発行するだけで、新しい Azure Purview アカウントをすばやく安全に自動的に設定してデプロイする、Azure Resource Manager (ARM) テンプレート ベースのツール。 [Purview-Starter-Kit](https://aka.ms/PurviewKickstart) に似ていますが、追加の機能として、いくつかの事前構成済みのデータ ソース (Azure SQL Database、Azure Data Lake Storage Gen2 アカウント、Azure Data Factory、Azure Synapse Analytics ワークスペース) がデプロイされます。

1. [PyApacheAtlas: Atlas API を使用した Azure Purview と Apache Atlas 間のインターフェイス](https://github.com/wjohnson/pyapacheatlas)

    - **推奨される顧客体験のステージ**: "*イノベーター、愛好家、導入者、長期の通常ユーザー*"
    - **説明**: Azure Purview と Apache Atlas API を使用する Python パッケージ。 Python の一連のクラスや Excel テンプレートからの一括読み込み、カスタム系列などがサポートされます。 このパッケージでは、プログラムによる相互作用と、少量のコードでのアップロードのための Excel テンプレートがサポートされています。

1. [Purview EventHub 通知リーダー](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/purview_atlas_eventhub_sample.py)

    - **推奨される顧客体験のステージ**: "*イノベーター、愛好家、導入者、長期の通常ユーザー*"
    - **説明**: このツールは、Purview の EventHub を読み取り、EventHub からリアルタイムの Kafka 通知を Atlas Notifications (https://atlas.apache.org/2.0.0/Notifications.html) 形式でキャッチする方法を示します。 さらに、スキャン中にライブで検出されたエンティティとアセットの Excel シート CSV と、Purview によって生成されるその他の関心のある通知が生成されます。


## <a name="feedback-and-disclaimer"></a>フィードバックと免責事項

いずれのツールにも、その有効性または機能の保証を検証する Microsoft からの明示的な保証は付帯されていません。 これらは、悪意のあるアクティビティやウイルスがないことが証明されており、個人データや機密データを収集しないことが保証されています。

使用中の有効性と機能に関するフィードバックや質問については、各ツールの所有者と作成者にお問い合わせください。連絡先の詳細は、それぞれの GitHub リポジトリに記載されています。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
