---
title: Microsoft Azure Sentinel をデプロイするための前提条件
description: Microsoft Azure Sentinel のデプロイ前のアクティビティとデプロイの前提条件について学習します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bae2e792ab85ec87f1187c632f9582e9d38e87ed
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521132"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-microsoft-sentinel"></a>Microsoft Azure Sentinel のデプロイ前のアクティビティとデプロイの前提条件

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Azure Sentinel のデプロイ前のアクティビティとデプロイの前提条件を紹介します。

## <a name="pre-deployment-activities"></a>デプロイ前のアクティビティ

Microsoft Azure Sentinel をデプロイする前に、できるだけ早く次の手順を行って、デプロイの重点を最大の価値の提供に置くことをお勧めします。

1. デプロイの予算とタイムラインを正確に投影するのに役立つ、必要な[データ ソース](connect-data-sources.md)とデータ サイズの要件を決定します。

    この情報は、ビジネス ユース ケースのレビュー中に、または既にある現在の SIEM を評価することで決定できます。 SIEM が既にある場合は、データを分析して、最も価値が高く、Microsoft Azure Sentinel に取り込む必要があるデータ ソースを把握します。

1. Microsoft Azure Sentinel ワークスペース アーキテクチャを設計します。 次のようなパラメーターを検討します。

    - 1 つのテナントと複数のテナントのどちらを使用するか
    - データの収集と保存に関するコンプライアンス要件
    - Microsoft Azure Sentinel データへのアクセスを制御する方法

    詳細については、[ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)および[サンプル ワークスペースの設計](sample-workspace-designs.md)に関する記事を参照してください。

1. ビジネス ユース ケース、データ ソース、およびデータ サイズの要件が特定された後、計画された各シナリオのコストへの影響を考慮して、[予算の計画を開始](azure-sentinel-billing.md)します。

    Microsoft Azure Sentinel と Azure Log Analytics の両方、デプロイされるプレイブックなどのデータ インジェストのコストが予算に含まれていることを確認します。

    詳細については、次を参照してください。

    - [Microsoft Azure Sentinel のコストと課金](azure-sentinel-billing.md)
    - [Microsoft Azure Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)
    - [Logic Apps (プレイブック) の価格](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [長期的なログ保持のために Azure Data Explorer を統合する](store-logs-in-azure-data-explorer.md)

1. 要件とタイムラインに基づいて、デプロイを主導するエンジニアまたはアーキテクトを指名します。 この個人は、デプロイを主導し、チームの主要な連絡先となる必要があります。

## <a name="azure-tenant-requirements"></a>Azure テナントの要件

Microsoft Azure Sentinel をデプロイする前に、Azure テナントで次の要件が満たされていることを確認します。

- Azure にアクセスしてリソースをデプロイするには、[Azure Active Directory ライセンスとテナント](../active-directory/develop/quickstart-create-new-tenant.md)または[有効な支払い方法を持つ個々のアカウント](https://azure.microsoft.com/free/)が必要です。

- テナントの作成後、[Azure サブスクリプション](../cost-management-billing/manage/create-subscription.md)でリソースの作成と請求を追跡する必要があります。

- サブスクリプションの入手後、サブスクリプションの使用を開始する前に[関連アクセス許可](../role-based-access-control/index.yml)が必要になります。 新しいサブスクリプションを使用している場合は、AAD テナントの管理者以上をサブスクリプションの[所有者または共同作成者](../role-based-access-control/rbac-and-directory-admin-roles.md)として指定する必要があります。

    - 使用可能な最小特権アクセスを維持するには、リソース グループのレベルでロールを割り当てます。
    - アクセス許可とアクセスをより詳細に制御するには、カスタム ロールを設定します。 詳細については、[ロールベースのアクセス制御](../role-based-access-control/custom-roles.md)に関するページを参照してください。
    - ユーザーとセキュリティ ユーザー間を追加で分離するために、[リソース コンテキスト](resource-context-rbac.md)または[テーブルレベルの RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) を使用できます。

    Microsoft Azure Sentinel でサポートされている他のロールとアクセス許可の詳細については、「[Microsoft Azure Sentinel のアクセス許可](roles.md)」を参照してください。

- [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md)は、検出、分析、その他の機能のために Microsoft Azure Sentinel によって取り込まれて使用されるすべてのデータを収容するために必要です。 詳細については、「[Microsoft Azure Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)」を参照してください。

> [!TIP]
> Microsoft Azure Sentinel ワークスペースを設定するときに、Microsoft Azure Sentinel 専用の[リソース グループ](../azure-resource-manager/management/manage-resource-groups-portal.md)と、Log Analytics ワークスペース、プレイブック、ブックなど、Microsoft Azure Sentinel で使用されるリソースを作成します。
>
> 専用リソース グループを使用すると、アクセス許可をリソース グループ レベルで 1 回割り当て、関連するすべてのリソースにアクセス許可を自動的に適用できます。 リソース グループを使用してアクセスを管理すると、不適切なアクセス許可を発行する可能性なしに Microsoft Azure Sentinel を効率的に使用できることを確実にします。 リソースが複数のリソース グループに分散されている Microsoft Azure Sentinel のリソース グループがない場合、ユーザーまたはサービス プリンシパルは、アクセス許可が不十分なため、必要なアクションの実行やデータの表示ができない可能性があります。
>
> 階層別のリソースに対してよりきめ細かいアクセス制御を実装するには、追加のリソース グループを使用して、それらのグループによってのみアクセスされる必要があるリソースを収容する必要があります。 複数階層のリソース グループを使用すると、それらの階層間でアクセスを分離できます。
>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Microsoft Azure Sentinel のオンボード](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)
