---
title: オンプレミスの MySQL から Azure Database for MySQL への移行ガイドの概要
description: オンプレミスの MySQL から Azure Database for MySQL への移行ガイド
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: 1a65469b38a82b4dabeeb2be1f2ea8db269b760d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958636"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-introduction"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイドの概要

この移行ガイドは、MySQL のワークロードを [Azure Database for MySQL](../overview.md) に移行しようとしている MySQL のお客様およびソフトウェア インテグレーター向けに、スタック可能で実用的な情報を提供するように設計されています。 このガイドでは、ほとんどのケースに適用可能な知識を提供し、MySQL から Azure への移行を正常に計画して実行するためのガイダンスを提供します。

既存のデータベースと MySQL ワークロードをクラウドに移行するプロセスでは、ワークロード機能と既存のアプリケーションの接続に関する課題に直面する可能性があります。 このガイドで紹介する情報は、移行を成功に導くのに役立つリンクと推奨事項を提供し、当初の意図どおりにワークロードとアプリケーションの運用を継続できるようにします。

提供されている情報は、Microsoft [クラウド導入フレームワーク](/azure/cloud-adoption-framework/get-started/)を使用して、Azure Database for MySQL 環境の評価、移行、および最適化後のアクティビティを実行することが中心となっています。

## <a name="mysql"></a>MySQL

MySQL はオープンソース コミュニティで長い歴史を持ち、世界中の企業のWeb サイトやその他のビジネスに不可欠なアプリケーションで人気を博してきました。 このガイドは、移行の範囲設定、計画、および実行を依頼された管理者を支援します。 MySQL を初めて使用する管理者は、MySQL の内部動作に関するより詳しい情報について、[MySQL のドキュメント](https://dev.mysql.com/doc/)もご覧ください。 さらに、このガイドでは、それぞれのセクションにいくつかの参照記事へのリンクが記載されており、役に立つ情報やチュートリアルを参照できます。

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

[Azure Database for MySQL](../overview.md) は、MySQL 環境がフル マネージドである、Microsoft が提供するサービスとしてのプラットフォーム (PaaS) です。 このフル マネージド環境では、オペレーティング システムとソフトウェアの更新が自動的に適用され、データの高可用性と保護が実装されます。

PaaS オファリングに加えて、Azure VM で MySQL を実行することもできます。 ターゲット データ ワークロードに最適な展開の種類を決定するには、「[Azure で適切な MySQL サーバー オプションを選択する](../select-right-deployment-type.md)」を参照してください。

![MySQL 環境の比較](./media/image3.jpg)

**MySQL 環境の比較。**

このガイドでは、スケールアップとスケールアウト、従量課金制、高可用性、セキュリティ、管理性など、サービスとしてのインフラストラクチャ (IaaS) に対するさまざまな利点があるため、オンプレミスの MySQL ワークロードをサービスとしてのプラットフォームの Azure Database for MySQL オファリングに移行することについて完全に焦点を当てています。  

## <a name="summary"></a>まとめ

このガイドを完了すると、[サンプル アプリケーションをデプロイして、自分でサーバー パラメーターを確認する](summary.md#next-steps)ことができます。

> [!div class="nextstepaction"]
> [代表的なユース ケース](./representative-use-case.md)