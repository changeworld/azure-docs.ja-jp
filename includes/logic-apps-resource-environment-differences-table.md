---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 08/18/2021
ms.openlocfilehash: b4b4bc65520ba3a8d2bec3bc4f3d4184b672702f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123108297"
---
| リソースの種類 | メリット | リソースの共有と使用 | [価格と課金モデル](../articles/logic-apps/logic-apps-pricing.md) | [制限の管理](../articles/logic-apps/logic-apps-limits-and-config.md) |
|---------------|----------|----------------------------|---------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **ロジック アプリ (従量課金)** <p><p>ホスト環境: マルチテナント Azure Logic Apps | - 最も簡単に開始できる <p><p>- 従量課金制 <p><p>- フル マネージド | 1 つのロジック アプリではワークフローを "*1 つだけ*" 使用できます。 <p><p>"*複数のテナントにわたる*" 顧客によって作成されたロジック アプリでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 | [従量課金プラン](../articles/logic-apps/logic-apps-pricing.md#consumption-pricing) (従量課金制) | Azure Logic Apps でこれらの制限の既定値が管理されますが、特定の制限に対してオプションが存在する場合は、これらの値の一部を変更できます。 |
| **ロジック アプリ (Standard)** <p><p>ホスト環境: <br>シングルテナントの Azure Logic Apps <p><p>**注**: シナリオでコンテナーが必要な場合は、[Azure Arc 対応 Logic Apps を使用してシングルテナント ベースのロジック アプリを作成します](../articles/logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)。 詳細については、「[Azure Arc 対応 Logic Apps とは](../articles/logic-apps/azure-arc-enabled-logic-apps-overview.md)」を参照してください。 | - シングルテナント Azure Logic Apps ランタイムを使用して Azure Functions を実行できる場所であれば、ほとんどどこでも実行できます。 デプロイ スロットは現在サポートされていません。 <p><p>- 大規模なスループット向上とコスト削減を実現するための追加の組み込みコネクタ <p><p>- ランタイムとパフォーマンスの設定に関する制御と微調整機能の強化 <p><p>- 仮想ネットワークとプライベート エンドポイントに対する統合サポート。 <p><p>- 独自の組み込みコネクタの作成。 | 1 つのロジック アプリに、複数の "[*ステートフル*" と "*ステートレス*](../articles/logic-apps/single-tenant-overview-compare.md#stateful-stateless)" のワークフローを含めることができます。 <p><p>"*1 つのロジック アプリとテナント*" のワークフローでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 | [Standard](../articles/logic-apps/logic-apps-pricing.md#standard-pricing) (選択した価格レベルのホスティング プランに基づく) <p><p>[外部ストレージ](../articles/azure-functions/storage-considerations.md#storage-account-requirements)を使用する "*ステートフル*" ワークフローを実行する場合は、Azure Logic Apps ランタイムによって、[Azure Storage 価格](https://azure.microsoft.com/pricing/details/storage/)に従うストレージ トランザクションが行われます。 | シナリオのニーズに応じて、多くの制限の既定値を変更できます。 <p><p>**重要**: 一部の制限には、ハード上限があります。 Visual Studio Code では、ロジック アプリのプロジェクト構成ファイル内の既定の制限値に対して行った変更は、デザイナー エクスペリエンスには表示されません。 詳細については、[シングルテナントの Azure Logic Apps におけるロジック アプリのアプリと環境設定の編集](../articles/logic-apps/edit-app-settings-host-settings.md)に関するページを参照してください。 |
| **ロジック アプリ (従量課金)** <p><p>ホスト環境: <br>統合サービス環境 (ISE) | - 大規模なワークロードに対応したエンタープライズ規模 <p><p>- 仮想ネットワークに直接接続する 20 個以上の ISE 固有コネクタ <p><p>- 含まれる使用量と顧客管理型スケーリングによる予測可能な価格 | 1 つのロジック アプリではワークフローを "*1 つだけ*" 使用できます。 <p><p>"*同じ環境内*" のロジック アプリでは、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 | [ISE](../articles/logic-apps/logic-apps-pricing.md#ise-pricing) (固定) | Azure Logic Apps でこれらの制限の既定値が管理されますが、特定の制限に対してオプションが存在する場合は、これらの値の一部を変更できます。 |
||||||