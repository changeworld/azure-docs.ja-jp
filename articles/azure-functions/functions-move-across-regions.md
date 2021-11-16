---
title: Azure Functions で関数アプリをリージョン間で移動する
description: 既存の Azure Functions リソースのコピーをターゲット リージョンに作成して、リージョン間で Azure Functions リソースを移動する方法について説明します。
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 05/11/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: c106ec2e5e4592937974f040e1ae14aabc45f354
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138664"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Azure Functions で関数アプリをリージョン間で移動する

この記事では、Azure Functions リソースを別の Azure リージョンに移動する方法について説明します。 次のいずれかの理由により、リソースを別のリージョンに移動する場合があります。
 + 新しい Azure リージョンを利用する
 + 特定のリージョンでのみ使用できる機能またはサービスをデプロイする
 + 内部ポリシーとガバナンスの要件を満たす
 + 容量計画の要件に対応する

Azure Functions リソースはリージョン固有のものであり、リージョン間で移動することはできません。 既存の関数アプリのリソースのコピーをターゲット リージョンに作成してから、関数のコードを新しいアプリにもう一度デプロイする必要があります。

最小のダウンタイムが要件である場合は、両方のリージョンで関数アプリを実行してディザスター リカバリー アーキテクチャを実装することを検討してください。
+ [Azure Functions geo ディザスター リカバリー](functions-geo-disaster-recovery.md)
+ [Azure Durable Functions のディザスター リカバリーと地理的分散](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>前提条件

+ ターゲット リージョンで Azure Functions と、そのリソースを移動する関連サービスがサポートされていることを確認する
+ 移行する関数のオリジナルのソース コードにアクセスできる

## <a name="prepare"></a>準備

ソース リージョンで使用されている関数アプリのリソースをすべて特定します。これには、以下が含まれる場合があります。

+ 関数アプリ
+ [ホスティング プラン](functions-scale.md#overview-of-plans)
+ [デプロイ スロット](functions-deployment-slots.md)
+ [Azure で購入したカスタム ドメイン](../app-service/manage-custom-dns-buy-domain.md)
+ [TLS/SSL の証明書と設定](../app-service/configure-ssl-certificate.md)
+ [構成済みのネットワーク オプション](functions-networking-options.md)
+ [マネージド ID](../app-service/overview-managed-identity.md)
+ [構成済みのアプリケーション設定](functions-how-to-use-azure-function-app-settings.md) - 十分なアクセス権を持つユーザーは、ポータルの [高度な編集] 機能を使用して、すべてのソース アプリケーション設定をコピーできます
+ [スケーリング構成](functions-scale.md#scale)

関数は、トリガーまたはバインドを使用して他のリソースに接続する場合があります。 これらのリソースをリージョン間で移動する方法については、それぞれのサービスのドキュメントを参照してください。

[既存のリソースからテンプレートをエクスポート](../azure-resource-manager/templates/export-template-portal.md)できる必要もあります。

## <a name="move"></a>詳細ビュー

関数アプリをターゲット リージョンにデプロイし、構成されたリソースを確認します。 

### <a name="redeploy-function-app"></a>関数アプリの再デプロイ

ソース リージョンで関数アプリを作成したデプロイおよび自動化リソースにアクセスできる場合は、ターゲット リージョンで同じデプロイ手順をもう一度実行して、アプリを作成および再デプロイします。 

ソース コードにしかアクセスできず、デプロイおよび自動化リソースにはアクセスできない場合は、使用可能な[デプロイ テクノロジ](functions-deployment-technologies.md)のいずれかを使用するか、[継続的なデプロイの方法](functions-continuous-deployment.md)のいずれかを使用して、関数アプリをターゲット リージョンにデプロイして構成することができます。

### <a name="review-configured-resources"></a>構成されたリソースの確認

前の「[準備](#prepare)」の手順で特定したリソースをデプロイ中に構成していない場合は、ここで確認してターゲット リージョンに構成します。

### <a name="move-considerations"></a>移動に関する考慮事項
+ 使用しているデプロイ リソースと自動化によって関数アプリが作成されない場合は、ターゲット リージョンに[同じ種類のアプリを新しいホスティング プランで作成](functions-scale.md#overview-of-plans)してください。
+ 関数アプリ名は Azure でグローバルに一意なので、ターゲット リージョンのアプリをソース リージョンのものと同じ名前にすることはできません。
+ 関数アプリを依存関係に結び付ける参照とアプリケーション設定を確認し、必要に応じて更新する必要があります。 たとえば、関数が呼び出すデータベースを移動する場合、ターゲット リージョンでデータベースに接続するには、アプリケーションの設定または構成も更新する必要があります。 関数アプリで使用する Application Insights のインストルメンテーション キーや Azure ストレージ アカウントなど、一部のアプリケーション設定はターゲット リージョンで既に構成されている可能性があり、その場合は更新不要です。
+ 忘れずに、ターゲット リージョンで構成を検証して関数をテストしてください。
+ カスタム ドメインが構成されている場合は、[ドメイン名を再マップ](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)します。
+ Functions を Dedicated プランで実行している場合は、Web アプリとプランを共有している場合の対応について、[App Service の移行計画](../app-service/manage-move-across-regions.md)に関するページも確認してください。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

移動が完了したら、ソース リージョンから関数アプリとホスティング プランを削除します。 アプリ自体が実行されていない場合でも、Premium または Dedicated プランでは関数アプリの料金が発生します。

## <a name="next-steps"></a>次のステップ

+ より高度なソリューション アーキテクチャの一環として、複数のリージョンで Azure Functions を実行する例については、[Azure アーキテクチャ センター](/azure/architecture/browse/?expanded=azure&products=azure-functions)を確認してください。
