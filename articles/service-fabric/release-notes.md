---
title: Azure Service Fabric のリリース
description: Azure Service Fabric のリリース ノート。 Service Fabric の最新の機能および強化に関する情報が含まれます。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 36586f93425c6583dd3be497da9f7a018ebd4c9d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526738"
---
# <a name="service-fabric-releases"></a>Service Fabric のリリース

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">トラブルシューティング ガイド</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追跡</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">サポート オプション</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">サポートされているバージョン</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">コード サンプル</a>

この記事では、Service Fabric のランタイムと SDK に対する最新リリースと更新プログラムの詳細を提供します。

## <a name="whats-new-in-service-fabric"></a>Service Fabric の新機能

### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 の提供開始 Azure portal または Azure Resource Manager のデプロイを使用して、7.0 に更新できます。 休暇の時期のリリースに関するお客様のフィードバックにより、Microsoft は、自動アップグレードを受けるように設定されたクラスターの自動更新を 1 月まで開始しません。
1 月には、標準のロールアウト手順を再開します。自動アップグレードが有効になっているクラスターは、7.0 更新プログラムの自動的な受信を開始します。 ロールアウトを開始する前に、別の発表を提供します。
また、このポリシーが考慮されることを示すために、予定されているリリース日も更新します。 将来の[リリース スケジュール](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)についてはこちらをご覧ください。
 
これは Service Fabric の最新リリースであり、主要な機能と機能強化が組み込まれています。

### <a name="key-announcements"></a>重要な発表
 - [**アプリケーション サービスでの KeyVaultReference サポート (プレビュー)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)[マネージド ID](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) が有効になっている Service Fabric アプリケーションは、環境変数、アプリケーション パラメーター、またはコンテナー リポジトリ資格情報として Key Vault シークレット URL を直接参照できるようになりました。 Service Fabric は、アプリケーションのマネージド ID を使用してシークレットを自動的に解決します。 
     
- **ステートレス サービスのアップグレードの安全性の向上**:アプリケーションのアップグレード中に可用性を保証するために、使用可能と見なされる[ステートレス サービスの最小インスタンス数](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)を定義するための新しい構成を導入しました。 以前は、すべてのサービスでこの値は 1 であり、変更できませんでした。 この新しいサービスごとの安全性チェックにより、アプリケーションのアップグレード中、クラスターのアップグレード中、Service Fabric の正常性と安全性のチェックに依存するその他のメンテナンス中に、サービスの稼働インスタンスの最小数を維持できるようになります。
  
- [**ユーザー サービスのリソース制限**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)ユーザーは、ノード上のユーザー サービスのリソース制限を設定して、Service Fabric システム サービスのリソース不足などのシナリオを防ぐことができます。 
  
- 特定の種類のレプリカの[**非常に高いサービス移動コスト**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 移動コストが非常に高いレプリカは、他の方法では解決できないクラスター内での制約違反があった場合にのみ移動されます。 ”非常に高い” 移動コストの使用が妥当である状況、およびその他の考慮事項については、ドキュメントを参照してください。
  
-  **追加のクラスターの安全性チェック**:このリリースでは、構成可能なシード ノード クォーラム安全性チェックが導入されました。 これにより、クラスターのライフサイクルと管理のシナリオで使用できるようにする必要があるシード ノードの数をカスタマイズできます。 構成された値を下回るクラスターを取得する操作はブロックされます。 現在、既定値は常にシード ノードのクォーラムです。たとえば、7 つのシード ノードがある場合、5 つのシード ノードを下回る操作は既定でブロックされます。 この変更により、最小セーフ値 6 を作成できるようになりました。これにより、一度にダウンするシード ノードを 1 つだけにすることができます。
   
- [**Service Fabric Explorer でのバックアップと復元サービスの管理**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)のサポートが追加されました。 これにより、SFX 内から次のアクティビティを直接実行できるようになります。バックアップと復元サービスの検出、バックアップ ポリシーの作成、自動バックアップの有効化、アドホック バックアップの実行、復元操作のトリガー、および既存のバックアップの参照を実行できます。

- [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool) の提供のお知らせ:このツールは、信頼性の高いコレクションで使用されている型が、アプリケーションのローリング アップグレード中に上位互換性と下位互換性があることを検証するのに役立ちます。 これにより、互換性のない型のためのアップグレードの失敗、データの損失、データの破損を防ぐことができます。

- [**セカンダリ レプリカでの安定した読み取りの有効化**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): 安定した読み取りは、セカンダリ レプリカの戻り値をクォーラムで確認された戻り値に限定します。

さらに、このリリースには、バグ修正、サポート、信頼性とパフォーマンスの強化などの他の新機能が含まれています。 すべての変更点の一覧については、[リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)を参照してください。

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|


### <a name="service-fabric-65"></a>Service Fabric 6.5

このリリースには、サポート性、信頼性、パフォーマンスの向上、新機能、バグの修正、およびクラスターとアプリケーションのライフサイクル管理を容易にする拡張機能が含まれています。

> [!IMPORTANT]
> Service Fabric 6.5 は、Visual Studio 2015 での Service Fabric ツールのサポートの最終リリースです。 お客様には、今後は [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) に移行することをお勧めします。

Service Fabric 6.5 の新機能は以下のとおりです。

- Service Fabric Explorer には、イメージ ストアにアップロードしたアプリケーションを検査するための [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) が含まれます。

- [パッチ オーケストレーション アプリケーション (POA)](service-fabric-patch-orchestration-application.md) バージョン [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) には、自己診断に関する多くの機能強化が含まれます。 POA のお客様には、このバージョンに移行することをお勧めします。

- お客様がオプトアウトしていない限り、Service Fabric 6.5 クラスターでは [EventStore サービスが既定で有効になります](service-fabric-visualizing-your-cluster.md#event-store)。

- ステートフル サービス用の[レプリカ ライフサイクル イベント](service-fabric-diagnostics-event-generation-operational.md#replica-events)が追加されました。

- [シード ノードの状態の可視性が向上しました](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)。シード ノードが異常な場合のクラスター レベルの警告などです ("*ダウン*"、"*削除済み*"、または "*不明*")。

- [Service Fabric アプリケーション ディザスター リカバリー ツール](https://github.com/Microsoft/Service-Fabric-AppDRTool)を使用すると、プライマリ クラスターで災害が発生した場合に、Service Fabric ステートフル サービスを迅速に復旧できます。 プライマリ クラスターからのデータは、定期的なバックアップと復元を使用して、セカンダリ スタンバイ アプリケーションで継続的に同期されます。

- Visual Studio では、[Linux ベースのクラスターへの .NET Core アプリの発行](service-fabric-how-to-publish-linux-app-vs.md)がサポートされます。

- Azure で新しい Linux クラスターにアップグレードするか、またはクラスターを作成すると、Service Fabric 6.5 (およびそれ以降のバージョン) では [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) が自動的にインストールされます。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) は、既定で MacOS/Linux OneBox クラスターにインストールされます。

詳しくは[Service Fabric 6.5 のリリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)をご覧ください。

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 2019 年 8 月 23 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019 年 10 月 14 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>以前のバージョン

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2018 年 11 月 30 日 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Windows クラスター向け Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 年 2 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 年 3 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 年 4 月 8 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 年 5 月 2 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
