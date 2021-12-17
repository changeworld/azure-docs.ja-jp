---
title: Azure Event Hubs の Azure スキーマ レジストリ
description: この記事では、Azure Event Hubs での Schema Registry のサポートに関する概要について説明します。
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f9e405a82b73530cc53ec98f22b2c1f4473b2550
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398523"
---
# <a name="azure-schema-registry-in-azure-event-hubs"></a>Azure Event Hubs の Azure スキーマ レジストリ
多くのイベント ストリーミングおよびメッセージング シナリオでは、イベントまたはメッセージのペイロードに構造化データが含まれています。 [Apache Avro](https://avro.apache.org/) などのスキーマ駆動型形式は、このような構造化データをシリアル化または逆シリアル化するためによく使用されます。 

:::image type="content" source="./media/schema-registry-overview/schema-driven-ser-de.svg" alt-text="スキーマ駆動型のシリアル化またはシリアル化解除":::

プロデューサー アプリケーションは、スキーマ ドキュメントを使用してイベント ペイロードをシリアル化し、Azure Event Hubs などのイベント ブローカーに発行します。 同様に、コンシューマー アプリケーションは、ブローカーからイベント ペイロードを読み取り、同じスキーマ ドキュメントを使用してシリアル化を解除します。 そのため、プロデューサーとコンシューマーの両方で、スキーマ ドキュメントを使用してデータの整合性を検証できます。 


## <a name="why-we-need-a-schema-registry"></a>スキーマ レジストリが必要な理由 
スキーマ駆動型形式を使用する場合、プロデューサー アプリケーションでは、発行されたイベントのスキーマをコンシューマーが使用できるようにする必要があります。 対応するスキーマを各イベント データに対して共有することはできますが、これは効率的ではありません。 新しいコンシューマーがイベント データを使用する場合は、発行されるデータの形式を理解する必要があります。 また、プロデューサーとコンシューマーの両方が異なる割合で進化できるように、スキーマの進化をサポートしていることを確認する必要もあります。 


## <a name="azure-schema-registry"></a>Azure スキーマ レジストリ
**Azure Schema Registry** は Event Hubs の機能で、イベント駆動型およびメッセージング中心のアプリケーション用のスキーマ ドキュメントの中央リポジトリとして機能します。 プロデューサーとコンシューマー アプリケーションに対し、スキーマを管理して共有することなく、データを交換できる柔軟性を提供します。 また、Schema Registry には、再利用可能なスキーマのための単純なガバナンス フレームワークが用意されており、グループ化構成体 (スキーマ グループ) を使用してスキーマ間のリレーションシップを定義します。

:::image type="content" source="./media/schema-registry-overview/schema-registry.svg" alt-text="スキーマ レジストリ":::

Apache Avro のようなスキーマ駆動型のシリアル化フレームワークを使用すると、共有スキーマにシリアル化メタデータを外部化するすることで、JSON などのタグ付きの形式の場合と同様に、すべてのデータ セットに含まれる型情報およびフィールド名の、メッセージごとのオーバーヘッドを大幅に削減することもできます。 スキーマをイベントと一緒にイベント インフラストラクチャ内に保持しておくことで、シリアル化または逆シリアル化に必要なメタデータに常にアクセスできるため、スキーマを間違えることがありません。 

> [!NOTE]
> この機能は、**basic** レベルでは使用できません。

## <a name="schema-registry-information-flow"></a>スキーマ レジストリ情報フロー 
スキーマ レジストリを使用する場合の情報フローは、Azure Event Hubs のイベントを発行または使用するために使用するすべてのプロトコルで同じです。 次の図は、ユーザーがスキーマ レジストリを持つ Kafka イベント プロデューサーとコンシューマー シナリオの情報フローを示しています。 

:::image type="content" source="./media/schema-registry-overview/information-flow.svg" lightbox="./media/schema-registry-overview/information-flow.svg" alt-text="スキーマ レジストリ情報フローを示す画像。":::


情報フローはプロデューサー側から開始され、Kafka プロデューサーはスキーマ ドキュメントを使用してデータをシリアル化します。 
- Kafka プロデューサー アプリケーションは、``KafkaAvroSerializer`` を使用して、スキーマを使用してクライアント側で指定されたイベント データをシリアル化します。 
- プロデューサー アプリケーションで、スキーマの検証に必要なスキーマ レジストリ エンドポイントとその他のオプション パラメーターの詳細を指定する必要があります。 
- シリアライザーは、プロデューサーがイベント データをシリアル化するために使用するスキーマ コンテンツを使用して、スキーマ レジストリ内で検索を行います。 
- このようなスキーマが見つかった場合は、対応するスキーマ ID が返されます。 プロデューサー アプリケーションは、スキーマが存在しない場合に構成できます。プロデューサー アプリケーションでは、スキーマを自動登録するようにスキーマ レジストリ クライアントを構成できます。 
- 次に、シリアライザーは、そのスキーマ ID を使用し、Azure Event Hubs に発行されるシリアル化されたデータにその前に付加します。 
- コンシューマー側では、``KafkaAvroDeserializer`` はスキーマ ID を使用して、スキーマ レジストリからスキーマの内容を取得します。 
- 次に、逆シリアライザーはスキーマ コンテンツを使用して、Azure Event Hubs から読み取られたイベント データを逆シリアル化します。 
- スキーマ レジストリ クライアントは、キャッシュを使用して、冗長なスキーマ レジストリ参照を防止します。  


## <a name="schema-registry-elements"></a>スキーマ レジストリ要素

Event Hubs 名前空間で、イベント ハブ (または Kafka トピック) と共にスキーマ グループをホストできるようになりました。 スキーマ レジストリをホストし、複数のスキーマ グループを持つことができます。 スキーマ レジストリは Azure Event Hubs でホストされますが、すべての Azure メッセージング サービスとその他のメッセージまたはイベント ブローカーで汎用的に使用できます。 これらの各スキーマ グループは、一連のスキーマに対して個別にセキュリティ保護できるリポジトリです。 グループは、特定のアプリケーションまたは組織単位に合わせることができます。 

:::image type="content" source="./media/schema-registry-overview/elements.png" alt-text="スキーマ レジストリ要素を示す画像。":::


### <a name="schema-groups"></a>スキーマ グループ
スキーマ グループは、ビジネス条件に基づく類似したスキーマの論理グループです。 スキーマ グループには、複数のバージョンのスキーマを保持できます。 スキーマ グループの互換性適用設定によって、新しいスキーマ バージョンに下位互換性を持たせることができます。

グループ化メカニズムによって課せられるセキュリティ境界によって、名前空間が複数のパートナー間で共有されている場合に、メタデータを通じて企業秘密が誤って漏洩するのを防ぐことができます。 また、同じ名前空間を共有する他のアプリケーションから独立して、アプリケーション所有者がスキーマを管理することもできます。

### <a name="schemas"></a>スキーマ
スキーマでは、プロデューサーとコンシューマーの間のコントラクトを定義します。 Event Hubs スキーマ レジストリで定義されるスキーマは、イベント データ外のコントラクトを管理するのに役立つため、ペイロードのオーバーヘッドを除去できます。 スキーマには、名前、型 (例: record、array など)、互換性モード (none、forward、backward、full)、およびシリアル化の種類 (現時点では Avro のみ) が含まれます。 複数のバージョンのスキーマを作成して、特定のバージョンのスキーマを取得して使用することができます。 

## <a name="schema-evolution"></a>スキーマの展開 

スキーマは、プロデューサーとコンシューマーのビジネス要件に合わせて進化する必要があります。 Azure スキーマ レジストリでは、スキーマ グループ レベルでの互換性モードの導入によってスキーマの進化がサポートされています。 スキーマ グループを作成するときに、そのスキーマ グループに含めるスキーマの互換性モードを指定できます。 スキーマを更新する場合、変更は、割り当てられた互換性モードに準拠している必要があります。その後、スキーマの新しいバージョンのみが作成されます。 

Azure Event Hubs 用の Azure スキーマ レジストリは、次の互換性モードに対応しています。 

### <a name="backward-compatibility"></a>旧バージョンとの互換性
旧バージョンとの互換性モードでは、コンシューマー コードでスキーマの新しいバージョンを使用できますが、古いバージョンのスキーマでメッセージを処理できます。 スキーマ グループで旧バージョンとの互換性モードを使用すると、スキーマに対して次の変更を行うことができます。 

- フィールドを削除します。 
- 省略可能なフィールドを追加します。 


### <a name="forward-compatibility"></a>上位互換性
上位互換性により、コンシューマー コードで古いバージョンのスキーマを使用できるようになりますが、新しいスキーマを使用してメッセージを読み取ることができます。 上位互換性モードでは、スキーマに対して次の変更を行うことができます。 
- フィールドを追加する 
- 省略可能なフィールドの削除 


### <a name="no-compatibility"></a>互換性なし
``None`` 互換性モードを使用すると、スキーマ レジストリ、スキーマを更新しても互換性チェックを行いません。 

## <a name="client-sdks"></a>クライアント SDK

Avro シリアライザーを含む次のライブラリのいずれかを使用できます。これは、スキーマ レジストリのスキーマ識別子と Avro エンコード データを含むペイロードをシリアル化および逆シリアル化するために使用できます。

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Azure Schema Registry によって提供される Kafka 統合 Apache Avro シリアライザーと逆シリアライザーを実行します。 Azure Schema Registry 用の Java クライアントの Apache Kafka クライアント シリアライザーは、任意の Apache Kafka シナリオで、Apache Kafka® ベースのデプロイまたはクラウド サービスと共に使用できます。 

## <a name="limits"></a>制限
Event Hubs の制限 (例: 名前空間内のスキーマ グループの数) については、[Event Hubs のクォータと制限](event-hubs-quotas.md)に関するページを参照してください

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御
プログラムを使用してスキーマ レジストリにアクセスする場合は、Azure Active Directory (Azure AD) にアプリケーションを登録し、Azure ロールベースのアクセス制御 (Azure RBAC) ロールのいずれかにアプリケーションのセキュリティ プリンシパルを追加する必要があります。

| Role | 説明 | 
| ---- | ----------- | 
| 所有者 | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |
| 共同作成者 | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |
| [スキーマ レジストリ閲覧者](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schema Registry グループおよびスキーマの読み取りと一覧表示を行います。 |
| [スキーマ レジストリ共同作成者](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |

Azure portal を使用してアプリケーションを登録する方法については、[Azure AD にアプリを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。 コードで使用するクライアント ID (アプリケーション ID)、テナント ID、およびシークレットをメモしておきます。 

## <a name="next-steps"></a>次の手順

- Azure portal を使用してスキーマ レジストリを作成する方法については、[Azure portal を使用して Event Hubs スキーマ レジストリを作成する](create-schema-registry.md)方法に関するページを参照してください。
- 次の **Schema Registry Avro クライアント ライブラリ** サンプルを参照してください。
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure Schema Registry の Kafka Avro 統合](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
