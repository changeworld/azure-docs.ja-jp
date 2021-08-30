---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: 概要'
description: オンプレミスの MySQL から Azure Database for MySQL への移行ガイド
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5023d53132985875fc0b927148a6efbfb0239cc3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085130"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

この移行ガイドは、MySQL ワークロードを [Azure Database for MySQL](../../overview.md) に移行しようとしている MySQL のお客様とソフトウェア インテグレーターに、スタック可能で実用的な情報を提供することを目的としています。 このガイドでは、ほとんどのケースに適用できる知識を提供し、MySQL から Azure への移行の計画と実行を成功に導くためのガイダンスを提供します。

既存のデータベースと MySQL ワークロードをクラウドに移行するプロセスでは、ワークロード機能と既存のアプリケーションの接続に関する課題に直面する可能性があります。 このガイドで紹介する情報は、移行の成功に重点を置き、ワークロードとアプリケーションの運用を当初の意図どおりに継続できるようにするために役立つリンクと推奨事項を提供します。

提供されている情報は、Microsoft [クラウド導入フレームワーク](/azure/cloud-adoption-framework/get-started/)を使用して、Azure Database for MySQL 環境の評価、移行、最適化後のアクティビティを実行する顧客体験が中心となっています。

## <a name="mysql"></a>MySQL

MySQL はオープンソース コミュニティで長い歴史を持ち、世界中の企業の Web サイトやその他のビジネスに不可欠なアプリケーションに普及しています。 このガイドは、移行の範囲設定、計画、および実行を依頼された管理者を支援します。 MySQL を初めて使用する管理者は、MySQL の内部動作に関するより詳しい情報について、[MySQL のドキュメント](https://dev.mysql.com/doc/)を参照することもできます。 さらに、このガイドでは、各セクションにいくつかの参照記事へのリンクが記載されており、役立つ情報やチュートリアルを参照できます。

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

[Azure Database for MySQL](../../overview.md) は、MySQL 環境がフル マネージドである、Microsoft が提供するサービスとしてのプラットフォーム (PaaS) です。 このフル マネージド環境では、オペレーティング システムとソフトウェアの更新プログラムが自動的に適用され、データの高可用性と保護が実装されます。

PaaS オファリングに加えて、Azure VM で MySQL を実行することもできます。 ターゲット データ ワークロードに最適なデプロイの種類を決定するには、「[Azure で適切な MySQL サーバー オプションを選択する](../../select-right-deployment-type.md)」を参照してください。

![MySQL 環境の比較](./media/image3.jpg)

**MySQL 環境の比較**

サービスとしてのプラットフォームである Azure Database for MySQL オファリングには、スケールアップとスケールアウト、従量課金制、高可用性、セキュリティ、管理性など、サービスとしてのインフラストラクチャ (IaaS) と比べてさまざまな利点があるため、このガイドでは、オンプレミスの MySQL ワークロードを Azure Database for MySQL オファリングに移行することに全面的に焦点を当てています。  

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [代表的なユース ケース](./02-representative-use-case.md)
