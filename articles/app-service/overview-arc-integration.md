---
title: Azure Arc 上の App Service
description: App Service と Azure オペレーター向け Azure Arc との 統合の概要。
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: 74cf4063d92aa5d563df881f2210e2ca5d08543e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440357"
---
# <a name="app-service-functions-and-logic-apps-on-azure-arc-preview"></a>Azure Arc の App Service、Functions、および Logic Apps (プレビュー)

App Service、Functions、Logic Apps を Azure Arc 対応 Kubernetes クラスターで実行できます。 Kubernetes クラスターは、オンプレミスにすることも、サードパーティのクラウドでホストすることもできます。 このアプローチにより、アプリ開発者は App Service の機能を利用できます。 同時に、IT 管理者は、内部のインフラストラクチャで App Service アプリをホストすることによって企業のコンプライアンスを維持することができます。 また、既存の Kubernetes クラスターで App Service を実行することで、他の IT オペレーターが他のクラウド プロバイダーへの以前の投資を保護することもできます。

> [!NOTE]
> App Service、Functions、Logic Apps 用に Kubernetes クラスターを設定する方法については、「[App Service Kubernetes 環境の作成 (プレビュー)](manage-create-arc-environment.md)」を参照してください。

ほとんどの場合、アプリ開発者が知る必要があるのは、デプロイされた Kubernetes 環境を表す適切な Azure リージョンにデプロイする方法だけです。 環境を提供し、基盤となる Kubernetes インフラストラクチャを維持するオペレーターの場合は、次の Azure リソースに注意する必要があります。

- 接続されているクラスター。Kubernetes インフラストラクチャの Azure プロジェクションです。 詳細については、「[Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)」を参照してください。
- クラスターの拡張機能。接続されたクラスター リソースのサブリソースです。 App Service 拡張機能によって、[接続されているクラスターに必要なポッドがインストール](#pods-created-by-the-app-service-extension)されます。 クラスター拡張機能の詳細については、「[Azure Arc 対応 Kubernetes のクラスター拡張機能](../azure-arc/kubernetes/conceptual-extensions.md)」を参照してください。
- カスタムの場所。拡張機能のグループをまとめ、それらを作成されたリソースの名前空間にマップします。 詳細については、「[Azure Arc 対応 Kubernetes でのカスタムの場所](../azure-arc/kubernetes/conceptual-custom-locations.md)」を参照してください。
- App Service Kubernetes 環境。これにより、クラスター操作に関係なく、アプリ間で共通の構成が可能になります。 概念的には、これはカスタムの場所のリソースにデプロイされ、アプリ開発者がこの環境にアプリを作成します。 これについては、「[App Service Kubernetes 環境](#app-service-kubernetes-environment)」で詳しく説明します。

## <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

App Service Kubernetes 環境には、次のパブリック プレビューの制限が適用されます。 これらは、変更があった場合に随時更新されます。

| 制限事項                                              | 詳細                                                                               |
|---------------------------------------------------------|---------------------------------------------------------------------------------------|
| サポート対象の Azure リージョン                                 | 米国東部、西ヨーロッパ                                                                  |
| クラスターのネットワーク要件                          | サービスの種類として `LoadBalancer` をサポートし、パブリックにアドレス指定可能な静的 IP を提供する必要がある |
| クラスター ストレージの要件                             | 必要に応じて、コード ベースのアプリのデプロイとビルドをサポートするために、拡張機能で使用できるクラスター接続ストレージ クラスが必要です                      |
| 機能: ネットワーク                                     | [使用不可 (クラスター ネットワークに依存)](#are-networking-features-supported)      |
| 機能: マネージド ID                             | [使用不可](#are-managed-identities-supported)                                    |
| 機能: Key Vault 参照                           | 使用不可 (マネージド ID によって異なる)                                         |
| 機能: マネージド ID を使用して ACR からイメージをプルする     | 使用不可 (マネージド ID によって異なる)                                         |
| 機能: Functions と Logic Apps のポータル内編集 | 使用不可                                                                         |
| 機能: FTP 発行                                 | 使用不可                                                                         |
| ログ                                                    | Log Analytics は、サイトごとではなく、クラスター拡張機能を使用して構成する必要がある                 |

## <a name="pods-created-by-the-app-service-extension"></a>App Service 拡張機能によって作成されたポッド

Azure Arc 対応 Kubernetes クラスターに App Service 拡張機能がインストールされると、指定されたリリース名前空間で作成された複数のポッドが表示されます。 これらのポッドを使用すると、Kubernetes クラスターを Azure の `Microsoft.Web` リソース プロバイダーの拡張機能にして、アプリの管理と操作をサポートできます。 必要に応じて、拡張機能を使用してイベント ドリブン スケーリング用に [KEDA](https://keda.sh/) をインストールできます。
 <!-- You can only have one installation of KEDA on the cluster. If you have one already, you must disable this behavior during installation of the cluster extension `TODO`. -->

次の表では、既定で作成される各ポッドの役割について説明します。

| Pod                                   | Description                                                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `<extensionName>-k8se-app-controller` | クラスター上にリソースを作成し、コンポーネントの状態を維持するコア オペレーター ポッド。                                |
| `<extensionName>-k8se-envoy`          | すべてのデータ プレーン要求のフロントエンド プロキシ レイヤー。 受信トラフィックを正しいアプリにルーティングします。                           |
| `<extensionName>-k8se-activator`      | システムによって使用可能な最初のインスタンスが取得される間に、ゼロにスケーリングされたアプリに役立つ代替ルーティング先。 |
| `<extensionName>-k8se-build-service`  | デプロイ操作をサポートし、[高度なツール機能](resources-kudu.md)を提供します。                                        |
| `<extensionName>-k8se-http-scaler`    | [KEDA](https://keda.sh) にスケーリング情報を提供するために、受信要求のボリュームを監視します。                               |
| `<extensionName>-k8se-img-cacher`     | プレースホルダーとアプリのイメージをノードのローカル キャッシュにプルします。                                                                  |
| `<extensionName>-k8se-log-processor`  | アプリやその他のコンポーネントからログを収集し、Log Analytics に送信します。                                                      |
| `placeholder-azure-functions-*`       | Azure Functions のコールド スタートを高速化するために使用されます。                                                                                 |

## <a name="app-service-kubernetes-environment"></a>App Service Kubernetes 環境

アプリを作成するには、事前に App Service Kubernetes 環境リソースが必要です。 これにより、カスタムの場所にあるアプリに共通する構成 (既定の DNS サフィックスなど) が可能になります。

カスタムの場所 1 つに作成できる Kubernetes 環境リソースは 1 つだけです。 ほとんどの場合、アプリを作成してデプロイする開発者は、リソースを直接認識する必要はありません。 指定されたカスタムの場所 ID から直接推論できます。 ただし、Azure Resource Manager テンプレートを定義する場合、すべてのプラン リソースは環境のリソース ID を直接参照する必要があります。 プランと指定した環境のカスタムの場所の値が一致している必要があります。

## <a name="faq-for-app-service-functions-and-logic-apps-on-azure-arc-preview"></a>Azure Arc の App Service、Functions、および Logic Apps (プレビュー) に関する FAQ

- [コストはいくらかかりますか?](#how-much-does-it-cost)
- [Windows と Linux のアプリはどちらもサポートされていますか?](#are-both-windows-and-linux-apps-supported)
- [どの組み込みアプリケーション スタックがサポートされていますか?](#which-built-in-application-stacks-are-supported)
- [すべてのアプリのデプロイの種類がサポートされていますか?](#are-all-app-deployment-types-supported)
- [どの App Service 機能がサポートされていますか?](#which-app-service-features-are-supported)
- [ネットワーク機能はサポートされていますか?](#are-networking-features-supported)
- [マネージド ID はサポートされていますか?](#are-managed-identities-supported)
- [スケーリングの制限はありますか?](#are-there-any-scaling-limits)
- [どのようなログが収集されますか?](#what-logs-are-collected)
- [プロバイダー登録エラーが発生した場合はどうすればよいですか?](#what-do-i-do-if-i-see-a-provider-registration-error)
- [ARM64 で動作しているクラスターに、Application のサービスの拡張機能をデプロイできますか?](#can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster)

### <a name="how-much-does-it-cost"></a>料金はどのくらいですか。

Azure Arc 上の App Service は、パブリック プレビュー期間中は無料です。

### <a name="are-both-windows-and-linux-apps-supported"></a>Windows と Linux のアプリはどちらもサポートされていますか?

コードとカスタム コンテナーの両方がサポートされているのは、Linux ベースのアプリのみです。 Windows アプリはサポートされていません。

### <a name="which-built-in-application-stacks-are-supported"></a>どの組み込みアプリケーション スタックがサポートされていますか?

すべての組み込み Linux スタックがサポートされています。

### <a name="are-all-app-deployment-types-supported"></a>すべてのアプリのデプロイの種類がサポートされていますか?

FTP のデプロイはサポートされていません。 現時点では、`az webapp up` もサポートされていません。 Git、ZIP、CI/CD、Visual Studio、Visual Studio Code など、その他のデプロイ方法はサポートされています。

### <a name="which-app-service-features-are-supported"></a>どの App Service 機能がサポートされていますか?

プレビュー期間中は、特定の App Service 機能が検証されています。 サポートされている場合は、Azure portal の左側のナビゲーション オプションがアクティブ化されます。 まだサポートされていない機能は淡色表示のままです。

### <a name="are-networking-features-supported"></a>ネットワーク機能はサポートされていますか?

いいえ。 ハイブリッド接続、Virtual Network 統合、または IP 制限などのネットワーク機能はサポートされていません。 ネットワークは、Kubernetes クラスター自体のネットワーク規則で直接処理する必要があります。

### <a name="are-managed-identities-supported"></a>マネージド ID はサポートされていますか?

いいえ。 Azure Arc で実行している場合、アプリにマネージド ID を割り当てることはできません。アプリで別の Azure リソースを操作するために ID が必要な場合は、代わりに[アプリケーション サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)の使用を検討してください。

### <a name="are-there-any-scaling-limits"></a>スケーリングの制限はありますか?

Azure Arc を使用する Kubernetes 上の Azure App Service によってデプロイされたすべてのアプリケーションは、基盤となる Kubernetes クラスターの制限内でスケーリングできます。  基盤となる Kubernetes クラスターにおいて使用可能なコンピューティング リソース (主に CPU とメモリ) が不足すると、アプリケーションがスケーリングできるのは、Kubernetes において使用可能なリソースでスケジュールできるアプリケーションのインスタンス数までに限られます。

### <a name="what-logs-are-collected"></a>どのようなログが収集されますか?

システム コンポーネントとアプリケーションの両方のログが標準出力に書き込まれます。 どちらのログの種類も、標準の Kubernetes ツールを使用して分析用に収集できます。 また、[Log Analytics ワークスペース](../azure-monitor/logs/log-analytics-overview.md)を使用して App Service クラスター拡張機能を構成し、そのワークスペースにすべてのログを送信することもできます。

既定では、システム コンポーネントからのログは Azure チームに送信されます。 アプリケーション ログは送信されません。 拡張機能の構成設定として `logProcessor.enabled=false` を設定することで、これらのログが転送されないようにすることができます。 これにより、Log Analytics ワークスペースへのアプリケーションの転送も無効になります。 ログ プロセッサを無効にすると、サポート ケースに必要な時間に影響する可能性があります。また、他の方法を使用して標準出力からログを収集するように求められます。

### <a name="what-do-i-do-if-i-see-a-provider-registration-error"></a>プロバイダー登録エラーが発生した場合はどうすればよいですか?

Kubernetes 環境リソースを作成するときに、一部のサブスクリプションで、"登録済みのリソース プロバイダーが見つかりませんでした" というエラーが表示される場合があります。 エラーの詳細に、有効と見なされる一連の場所と API バージョンが含まれていることがあります。 この場合、サブスクリプションを Microsoft.Web プロバイダーに再登録することが必要である可能性があります。この操作は、既存のアプリケーションや API には影響しません。 再登録するには、Azure CLI を使用して `az provider register --namespace Microsoft.Web --wait` を実行します。 次に、Kubernetes 環境コマンドを再試行します。

### <a name="can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster"></a>ARM64 で動作しているクラスターに、Application のサービスの拡張機能をデプロイできますか。

ARM64 で動作しているクラスターは現在サポートしていません。  

## <a name="extension-release-notes"></a>拡張機能のリリース ノート

### <a name="application-services-extension-v-090-may-2021"></a>アプリケーション サービス拡張機能 v 0.9.0 (2021 年 5 月)

- アプリケーション サービス拡張の初期パブリック プレビュー リリース。
- Web、関数、およびロジック アプリケーションのコードとコンテナーベースのデプロイのサポート。
- Web アプリケーション ランタイム サポート -.NET 3.1 および 5.0 ノード JS 12 および 14。Python 3.6、3.7、3.8。PHP 7.3 および 7.4。Ruby 2.5、2.5.5、2.6、および 2.6.2。Java SE 8u232、8u242、8u252、11.05、11.06、11.07。Tomcat 8.5、8.5.41、8.5.53、8.5.57、9.0、9.0.20、9.0.33、9.0.37。

### <a name="application-services-extension-v-0100-november-2021"></a>アプリケーション サービス拡張 v 0.10.0 (2021 年 11 月)

拡張機能が安定したバージョンであり、自動アップグレード マイナー バージョンが有効に設定されている場合、拡張機能は自動的にアップグレードされます。  拡張機能を最新バージョンの拡張機能に手動でアップグレードするには、次のコマンドを実行します。

- Envoy エンドポイントへの割り当てに必要な、事前割り当て済み静的 IP アドレスの要件を削除しました
- Keda を v2.4.0 にアップグレードする
- Envoy を v1.19.0 にアップグレードする
- Azure Function runtime を v3.3.1 にアップグレードする
- App Controller と Envoy Controller の既定のレプリカ数を 2 に設定して安定性をさらに向上させる

拡張機能が安定したバージョンであり、auto-upgrade-minor-version が true に設定されている場合、拡張機能は自動的にアップグレードされます。  拡張機能を最新バージョンに手動でアップグレードするには、次のコマンドを実行します。

```azurecli-interactive
    az k8s-extension update --cluster-type connectedClusters -c <clustername> -g <resource group> -n <extension name> --release-train stable --version 0.10.0
```


## <a name="next-steps"></a>次の手順

[App Service Kubernetes 環境の作成 (プレビュー)](manage-create-arc-environment.md)
