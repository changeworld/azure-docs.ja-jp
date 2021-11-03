---
title: 接続されたレジストリとは
description: Azure Container Registry の接続されたレジストリ機能の概要とシナリオ
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ccc80f2ce5fb8393824686101d2069beee58efa4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029719"
---
# <a name="what-is-a-connected-registry"></a>接続されたレジストリとは? 

この記事では、[Azure Container Registry](container-registry-intro.md) の *接続されたレジストリ* 機能について学習します。 接続されたレジストリは、コンテナー イメージや他の OCI 成果物をクラウドベースの Azure コンテナー レジストリと同期するオンプレミスまたはリモートのレプリカです。 接続されたレジストリを使用すると、オンプレミスのレジストリ成果物へのアクセスを高速化し、[入れ子になった IoT Edge](../iot-edge/tutorial-nested-iot-edge.md) の使用など、高度なシナリオを構築するのに役立ちます。

> [!NOTE]
> 接続されたレジストリは、**Premium** コンテナー レジストリ サービス レベルのプレビュー機能であり、[制限](#limitations)が適用されます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関する記事を参照してください。

## <a name="available-regions"></a>対応リージョン

* 東アジア
* 北ヨーロッパ
* 西ヨーロッパ
* 米国東部

## <a name="scenarios"></a>シナリオ

クラウドベースの Azure コンテナー レジストリは、geo レプリケーション、統合セキュリティ、Azure マネージド ストレージ、Azure 開発およびデプロイ パイプラインとの統合などの[機能](container-registry-intro.md#key-features)を提供します。 同時に、顧客はクラウドへの投資をオンプレミスおよび現場のソリューションに拡張しています。

オンプレミスまたはリモート環境で必要なパフォーマンスと信頼性を保って実行するには、コンテナー ワークロードがコンテナー イメージおよび関連する成果物を近くで使用できる必要があります。 接続されたレジストリは、クラウドベースの Azure コンテナー レジストリとコンテンツを定期的に同期する、パフォーマンスの高いオンプレミスのレジストリ ソリューションを提供します。

接続されたレジストリのシナリオには、次のものが含まれます。

* 接続された工場
* 小売の場所の販売時点管理
* 出荷、石油の掘削、採鉱など、時々接続される環境

## <a name="how-does-the-connected-registry-work"></a>接続されたレジストリのしくみ

次の図は、接続されたレジストリの一般的なデプロイ モデルを示しています。

:::image type="content" source="media/intro-connected-registry/connected-registry-overview.svg" alt-text="接続されたレジストリの概要":::

### <a name="deployment"></a>展開

それぞれの接続されたレジストリは、クラウドベースの Azure コンテナー レジストリを使用して管理するリソースです。 接続されたレジストリ階層の最上位の親は、Azure クラウド内、または [Azure Stack Hub](/azure-stack/operator/azure-stack-overview) のプライベート デプロイ内の Azure コンテナー レジストリです。

接続されたレジストリは、オンプレミスのサーバーまたはデバイス上、あるいは [Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md) などのオンプレミスのコンテナー ワークロードをサポートする環境上に、Azure ツールを使用してインストールします。

接続されたレジストリの *アクティブ化の状態* は、それがオンプレミスにデプロイされているかどうかを示します。

* **アクティブ** - 接続されたレジストリは現在オンプレミスにデプロイされています。 非アクティブ化されるまでは、もう一度デプロイすることはできません。 
* **非アクティブ** - 接続されたレジストリはオンプレミスにデプロイされていません。 現時点でデプロイできます。  
 
### <a name="content-synchronization"></a>コンテンツの同期

接続されたレジストリは、コンテナー イメージおよび OCI 成果物を同期するために、クラウド レジストリに定期的にアクセスします。 

また、クラウド レジストリからリポジトリのサブセットを同期するように構成したり、特定の間隔でのみ同期するように構成したりして、クラウドとオンプレミス間のトラフィックを減らすこともできます。

### <a name="modes"></a>モード

接続されたレジストリは、"*ReadWrite*" または "*ReadOnly*" という 2 つのモードのいずれかで動作できます

- **ReadWrite モード** - 既定のモードでは、クライアントは接続されたレジストリとの間で成果物をプルおよびプッシュする (読み取ったり書き込んだりする) ことができます。 接続されたレジストリにプッシュされた成果物は、クラウド レジストリと同期されます。 
        
  ReadWrite モードは、ローカル開発環境が配置されている場合に便利です。 イメージは、ローカルの接続されたレジストリにプッシュされ、そこからクラウドに同期されます。

- **ReadOnly モード** - 接続されたレジストリが ReadOnly モードの場合、クライアントは成果物をプルする (読み取る) ことのみできます。 この構成は、入れ子になった IoT Edge シナリオ、またはクライアントが動作するためにコンテナー イメージをプルする必要があるその他のシナリオで使用されます。

### <a name="registry-hierarchy"></a>レジストリの階層

それぞれの接続されたレジストリは、親に接続されている必要があります。 最上位の親はクラウド レジストリです。 [入れ子になった IoT Edge](overview-connected-registry-and-iot-edge.md) など、階層状のシナリオでは、接続されたレジストリをどちらのモードでも入れ子にすることができます。 クラウド レジストリに接続されている親は、どちらのモードでも動作できます。 

子レジストリは、親の機能と互換性がある必要があります。 したがって、ReadWrite モードと ReadOnly モードの接続されたレジストリはどちらも ReadWrite モードで動作する接続されたレジストリの子になることができますが、ReadOnly モードで動作する接続されたレジストリの子になれるのは ReadOnly モードのレジストリのみです。  

## <a name="client-access"></a>クライアント アクセス

オンプレミスのクライアントは、Docker CLI などの標準ツールを使用して、接続されたレジストリからコンテンツをプッシュまたはプルします。 クライアント アクセスを管理するには、それぞれの接続されたレジストリにアクセスするための Azure コンテナー レジストリ [トークン][repository-scoped-permissions]を作成します。 プルまたはプッシュ アクセス用のクライアント トークンは、レジストリ内の 1 つ以上のリポジトリに対してスコープを設定できます。

また、それぞれの接続されたレジストリは、その親レジストリと定期的に通信する必要があります。 このため、レジストリにはクラウド レジストリによって同期トークン (*sync token*) が発行されます。 このトークンは、同期および管理操作用に親レジストリで認証するために使用されます。

詳細については、「[接続されたレジストリへのアクセスの管理][overview-connected-registry-access]」を参照してください。

## <a name="limitations"></a>制限事項

- トークンとスコープ マップの数は、1 つのコンテナー レジストリに対してそれぞれ 20,000 に[制限](container-registry-skus.md)されています。 これにより、クラウド レジストリの接続されたレジストリの数が間接的に制限されます。すべての接続されたレジストリには同期およびクライアント トークンが必要だからです。
- スコープ マップ内のリポジトリ アクセス許可の数は 500 に制限されています。
- 接続されたレジストリのクライアントの数は現在、20 に制限されています。
- リポジトリ、マニフェスト、またはタグ メタデータによる[イメージ ロック](container-registry-image-lock.md)は、接続されたレジストリでは現在サポートされていません。
- ReadOnly モードを使用する接続されたレジストリでは、[リポジトリの削除](container-registry-delete.md)はサポートされていません。
- 接続されたレジストリの[リソース ログ](monitor-service-reference.md#resource-logs)は、現在サポートされていません。
- 接続されたレジストリは、レジストリのホーム リージョン データ エンドポイントと結合されます。 [geo レプリケーション](container-registry-geo-replication.md)の自動移行はサポートされていません。
- 接続されたレジストリを削除するには、オンプレミスのコンテナーを手動で削除し、クラウド内のそれぞれのスコープ マップまたはトークンを削除する必要があります。
- 接続されたレジストリの同期に関する制限事項は次のとおりです。
  - 継続的同期の場合:
    - `minMessageTtl` は 1 日
    - `maxMessageTtl` は 90 日
  - 時々接続されるシナリオで同期ウィンドウを指定する場合:
    - `minSyncWindow` は 1 時間
    - `maxSyncWindow` は 7 日

## <a name="next-steps"></a>次のステップ

この概要では、接続されたレジストリといくつかの基本的な概念について学習しました。 次のいずれかの記事に進み、接続されたレジストリを利用できる特定のシナリオについて学習してください。

> [!div class="nextstepaction"]
> [概要: 接続されたレジストリのアクセス][overview-connected-registry-access]
> 
> [!div class="nextstepaction"]
> [概要: 接続されたレジストリと IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[overview-connected-registry-access]:overview-connected-registry-access.md
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
