---
title: 概要 - Azure Database for MySQL
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービス、Azure Database for MySQL サービスについて説明します。
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: e93432347de7df3b2743143c68078b3dfc853848
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894357"
---
# <a name="what-is-azure-database-for-mysql"></a>Azure Database for MySQL とは -

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL は、Microsoft クラウドにおける、(GPLv2 ライセンスで利用可能な) [MySQL Community Edition](https://www.mysql.com/products/community/) のデータベース エンジン バージョン 5.6 (廃止)、5.7、および 8.0 をベースとしたリレーショナル データベース サービスです。 Azure Database for MySQL には、次の特長があります。

- ゾーン冗長と同一ゾーンの高可用性。
- スケジュールされたメンテナンス時間を選択できる最大制御。
- 自動バックアップとポイントインタイム リストアを使用したデータ保護 (最大 35 日間)。
- 基盤となるハードウェア、オペレーティング システム、データベース エンジンのサービスをセキュリティで保護し、最新の状態に保つための自動修正とメンテナンス。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 秒単位のエラスティック スケーリング。
- 低コストのバースト可能な SKU と、サーバーを停止/起動する機能が備わったコスト最適化制御。
- 保存中および移動中の機密データを保護するエンタープライズ レベルのセキュリティ、業界をリードするコンプライアンス、およびプライバシー。
- 大規模なデプロイの管理と監視を容易にする監視と自動化。
- 業界をリードするサポート エクスペリエンス。

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリ開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL の概念図":::

## <a name="deployment-models"></a>デプロイ モデル

MySQL コミュニティ エディションを搭載した Azure Database for MySQL は、次の 2 つのデプロイ モードで利用できます。
- フレキシブル サーバー
- シングル サーバー 

### <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバー

Azure Database for MySQL フレキシブル サーバーは、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性のために設計された、運用環境対応のフル マネージド データベース サービスです。 フレキシブル サーバー アーキテクチャにより、ユーザーは単一の可用性ゾーン内および複数の可用性ゾーンにまたがる高可用性を選択できます。 また、フレキシブル サーバーでは、より優れたコスト最適化制御によって、サーバーを停止/起動する機能や、完全なコンピューティング能力を継続的には必要としないワークロードに最適な、バースト可能なコンピューティング層を実現できます。 フレキシブル サーバーでは、予約インスタンスもサポートされ、最大 63% のコストを削減でき、コンピューティング容量要件が予測できる運用環境ワークロードに最適です。 サービスでは、MySQL 5.7 と 8.0 のコミュニティ バージョンがサポートされています。 このサービスは現時点で一般提供されており、さまざまな [Azure リージョン](flexible-server/overview.md#azure-regions)で利用できます。

フレキシブル サーバー デプロイ オプションには、Burstable、General Purpose、Memory Optimized という 3 つのコンピューティング レベルが用意されています。 各レベルには、データベース ワークロードをサポートする異なるコンピューティングおよびメモリ容量が用意されています。 月数ドルでバースト可能レベルで最初のアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[コンピューティングとストレージ](flexible-server/concepts-compute-storage.md)に関するページを参照してください。

フレキシブル サーバーは、以下に適しています
- バックアップ、高可用性、セキュリティ、監視などの機能の簡単なデプロイ、簡素化されたスケーリング、データベース管理の低いオーバーヘッド
- 制御とカスタマイズに優れた、MySQL のコミュニティ バージョンを必要とするアプリケーション開発
- 同じゾーン、ゾーン冗長による高可用性、管理されたメンテナンス期間のある運用環境ワークロード
- 簡略化された開発エクスペリエンス 
- エンタープライズ グレードのセキュリティ

フレキシブル サーバー デプロイ モードの詳細については、[フレキシブル サーバーの概要](flexible-server/overview.md)を参照してください。 フレキシブル サーバーの最新情報については、[Azure Database for MySQL - フレキシブル サーバーの新機能](flexible-server/whats-new.md)に関するページを参照してください。

### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 単一サーバー 

Azure Database for MySQL 単一サーバーは、最小限のカスタマイズ用に設計されたフル マネージド データベース サービスです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、最小限のユーザー構成と制御によるセキュリティなど、データベース管理機能のほとんどを処理するよう設計されています。 このアーキテクチャは、単一の可用性ゾーンで 99.99% の可用性を備えた組み込みの高可用性を実現するよう最適化されています。 MySQL 5.6 (廃止)、5.7、8.0 のコミュニティ バージョンをサポートしています。 このサービスは現時点で一般提供されており、さまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用できます。

単一サーバーは、**既に単一サーバーを活用している既存のアプリケーションにのみ** 最適です。 新たに開発または移行する場合は、デプロイ オプションとしてフレキシブル サーバーが推奨されます。 フレキシブル サーバーと単一サーバーのデプロイ オプションの違いについては、[最適なデプロイ オプションの選択](select-right-deployment-type.md)に関するドキュメントを参照してください。

単一サーバー デプロイ モードの詳細については、[単一サーバーの概要](single-server-overview.md)を参照してください。 フレキシブル サーバーの最新情報については、[Azure Database for MySQL - 単一サーバーの新機能](single-server-whats-new.md)に関するページを参照してください。

## <a name="contacts"></a>連絡先
Azure Database for MySQL についての質問や提案は、Azure Database for MySQL チームにメール ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、<bpt id="p1">[</bpt>UserVoice<ept id="p1">](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)</ept> でエントリを作成します。

## <a name="next-steps"></a>次のステップ

Azure Database for MySQL の 2 つのデプロイ モードの詳細を確認し、ニーズに応じて適切なオプションを選択してください。

- [単一サーバー](single-server/index.yml)
- [フレキシブル サーバー](flexible-server/index.yml)
- [ワークロードに適した MySQL のデプロイ オプションを選択する](select-right-deployment-type.md)
