---
title: Azure Database for MariaDB リレーショナル データベース サービスの概要
description: Azure Database for MariaDB リレーショナル データベース サービスの概要。
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249164"
---
# <a name="what-is-azure-database-for-mariadb"></a>Azure Database for MariaDB とは
Azure Database for MariaDB は、Microsoft クラウドにおける、[MariaDB コミュニティ エディション](https://mariadb.org/download/)のデータベース エンジンを基にしたリレーショナル データベース サービスです。 このサービスは、パブリック プレビューの段階です。 Azure Database for MariaDB では次のものが提供されます。

- 追加コストなしの組み込みの高可用性。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 必要に応じて数秒以内にスケーリング。
- 保存中や転送中の機密データのセキュリティ保護。
- 自動バックアップと最大 35 日間のポイントインタイム リストア。
- エンタープライズグレードのセキュリティとコンプライアンス。

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリ開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

この記事では、パフォーマンスとスケーラビリティ、管理容易性に関連する、Azure Database for MariaDB の中心概念と機能について紹介し、詳細を参照するためのリンクも提供します。 すぐに始めるには、次のクイック スタートをご覧ください。
- [Azure portal を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLI を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール
プレビューの Azure Database for MariaDB サービスには、複数のサービス レベル (Basic、汎用、およびメモリ最適化) が用意されています。 軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、レベルごとに異なるパフォーマンスと機能 を提供しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="monitoring-and-alerting"></a>監視とアラート
スケールを調整するときに、適切なパフォーマンス レベルはどのようにして見極めればよいのでしょうか。 組み込みのパフォーマンス監視機能およびアラート機能と、仮想コアに基づくパフォーマンス評価とを組み合わせて使用します。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、仮想コアのスケールアップまたはスケールダウンの影響をすばやく評価することができます。 <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント (SLA) (パブリック プレビュー中には提供されません) により、アプリの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure Database for MariaDB サーバーで、組み込みのセキュリティ、フォールト トレランス、データ保護を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 Azure Database for MariaDB では、ポイントインタイム リストアを使用して、サーバーを 35 日間分さかのぼって以前の状態に戻すことができます。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure データベース サービスは、アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えた Azure Database for MariaDB によって、データ セキュリティを維持してきました。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/en-us/trustcenter/security)をご覧ください。

Azure Database for MariaDB サービスでは、保存されるデータにストレージ暗号化が使用されます。 バックアップを含むデータはディスク上で暗号化されます (ただし、クエリの実行中にエンジンによって作成された一時ファイルを除きます)。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。

既定では、Azure Database for MariaDB サービスは、ネットワーク全体で移動中のデータに [SSL 接続セキュリティ](./concepts-ssl-connection-security.md)を要求するように構成されています。 データベース サーバーとクライアント アプリケーションの間に SSL 接続を適用すると、サーバーとアプリケーションの間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。 クライアント アプリケーションが SSL 接続をサポートしていない場合は、必要に応じて、データベース サービスに接続するための SSL 要求を無効にできます。

## <a name="contacts"></a>連絡先
Azure Database for MariaDB についての質問や提案は、Azure Database for MariaDB チームにメール ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)) でお送りください。 これはテクニカル サポートの別名ではないことに注意してください。

さらに、適切な連絡先について次の点を考慮してください。
- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb) でエントリを作成します。

## <a name="next-steps"></a>次の手順
ここでは、Azure Database for MariaDB の概要を紹介し、"Azure Database for MariaDB とは何か" という問いに対する答えを示したので、次のステップに進むことができます。
- コストの比較と計算については、価格のページを参照してください。 [料金](https://azure.microsoft.com/pricing/details/mariadb/)
- まず、最初のサーバーを作成します。 [Azure portal を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
