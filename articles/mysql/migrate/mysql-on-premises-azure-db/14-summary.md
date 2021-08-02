---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 概要'
description: このドキュメントでは、オンプレミスの MySQL から Azure Database for MySQL へのアプリケーションの移行に関連するいくつかのトピックについて説明します。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 06885636d299fa406550f9bd332724e2e01d775f
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083011"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-summary"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 概要

## <a name="prerequisites"></a>前提条件

[Security](13-security.md)

## <a name="overview"></a>概要

このドキュメントでは、オンプレミスの MySQL から Azure Database for MySQL へのアプリケーションの移行に関連するいくつかのトピックについて説明します。 プロジェクトを開始し、アプリケーションのカット オーバーまでプロジェクトを評価する方法を取り上げました。

移行チームは、行った選択によってプロジェクト タイムラインの効果が得られることがあるので、トピックを慎重に確認する必要があります。 提供されるエンタープライズ対応機能の多さを考えると、総保有コストは魅力的です。

移行プロジェクト アプローチが重要です。 チームは、変換時間を特定するために、アプリケーションとデータベースの複雑さを評価する必要があります。 変換ツールを使用すると、移行が容易になりますが、常に手動によるレビューと更新が必要な要素が生じます。 移行前のタスクと移行後のテストをスクリプト化することが重要です。

アプリケーションのアーキテクチャと設計により、必要な労力のレベルに関する強力なインジケーターが得られます。 たとえば、ORM フレームワークを利用したアプリケーションは、特にビジネス ロジックがデータベース オブジェクトではなくアプリケーションに含まれている場合に、最適な候補になる可能性があります。

最後に、無料のものから市販のものまで、複数のツールが市場に存在します。 このドキュメントでは、チームがより一般的なオープンソース ツール オプションのいずれかを使用してデータベースの移行を計画している場合に必要な手順を取り上げました。 どちらの方法を選択した場合でも、Microsoft と MySQL コミュニティには、データベースの移行を成功させるツールと専門知識が揃っています。

## <a name="questions-and-feedback"></a>質問とフィードバック

Azure Database for MySQL の操作についての質問や提案は、Azure Database for MySQL チーム (AskAzureDBforMySQL@service.microsoft.com) にメールでお送りください。 

> [!Important]
> Azure Database for MySQL チームは、サポート チケットではなく、一般的な質問のみを担当します。

また、必要に応じて次の連絡先をご利用ください。

  - Azure サポートに問い合わせるか、アカウントに関する問題を修正するには、[Azure portal からチケットを申請します](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

  - フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。
fwill
## <a name="find-a-partner-to-help-migrate"></a>移行の手助けをしてくれるパートナーを見つける

このガイドはかなりのものになる可能性がありますが、心配しないでください\! コミュニティには、移行に関する実証済みの実績を持った多くの専門家がいます。 最も適切な移行戦略を見つけるには、[Microsoft パートナー](https://www.microsoft.com/solution-providers/home)または [Microsoft MVP](https://mvp.microsoft.com/MvpSearch) を検索してください。 1 人ではありません\!

現実世界の詳細について、技術フォーラムおよびソーシャル グループも参照できます。

  - [Microsoft コミュニティ フォーラム](/answers/topics/azure-database-mysql.html)

  - [Azure Database for MySQL Tech Community ](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/bg-p/ADforMySQL)

  - [Azure MySQL Azure の StackOverflow ](https://stackoverflow.com/questions/tagged/azure-database-mysql)

  - [Azure Facebook グループ](https://www.facebook.com/groups/MsftAzure)

  - [LinkedIn Azure グループ](https://www.linkedin.com/groups/2733961/)

  - [LinkedIn Azure 開発者グループ](https://www.linkedin.com/groups/1731317/)

## <a name="next-steps"></a>次のステップ

エンドツーエンドの MySQL 移行ガイドを使用してサンプル アプリケーションをデプロイし、使用可能なサーバー パラメーターを確認するには、[付録](15-appendix.md)を参照してください。