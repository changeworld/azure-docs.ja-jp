---
title: 継続的インテグレーションと配信
description: 継続的インテグレーションとデリバリーを使用して Azure Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21223cc29857b439f87231a5b4649ea9864e2202
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400394"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory における継続的インテグレーションとデリバリー

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。  継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリー (CI/CD) とは、Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動することを意味します。 Azure Data Factory では [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を活用し、さまざまな ADF エンティティ (パイプライン、データセット、データ フローなど) の構成を保存します。 データ ファクトリを別の環境に昇格させる手法が 2 つ提案されています。

-    Data Factory と [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) の統合を利用した自動化されたデプロイ
-    Data Factory UX と Azure Resource Manager の統合を利用した Resource Manager テンプレートの手動アップロード。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD のライフサイクル

Azure Repos Git で構成された Azure Data Factory での CI/CD のライフサイクルのサンプル概要を以下に示します。 Git リポジトリを構成する方法の詳細については、「[Azure Data Factory のソース管理](source-control.md)」を参照してください。

1.  Azure Repos Git で開発データ ファクトリが作成され、構成されます。 すべての開発者には、パイプラインやデータセットなどの Data Factory リソースを作成するためのアクセス許可が必要です。

1.  開発者は変更を行う目的で[機能ブランチを作成](source-control.md#creating-feature-branches)します。 一番新しい変更を利用し、パイプライン実行をデバッグします。 パイプライン実行をデバッグする方法の詳細については、「[Azure Data Factory での反復開発とデバッグ](iterative-development-debugging.md)」を参照してください。

1.  開発者は、変更の結果に問題がなければ、各自の機能ブランチからメインまたはコラボレーション ブランチへのプル要求を作成して、同僚が変更をレビューできるようにします。

1.  プル要求が承認され、変更がメイン ブランチにマージされたら、変更が開発ファクトリに発行されます。

1.  チームは、変更をテストまたは UAT (ユーザー受け入れテスト) ファクトリにデプロイする準備ができたら、Azure Pipelines リリースに進み、望ましいバージョンの開発ファクトリを UAT にデプロイします。 このデプロイは Azure Pipelines タスクの一環として行われ、Resource Manager テンプレート パラメーターを使用し、適切な構成を適用します。

1.  テスト ファクトリで変更の妥当性が確認されたら、パイプライン リリースの次のタスクを利用し、運用環境のファクトリにデプロイします。

> [!NOTE]
> 開発ファクトリのみ、Git リポジトリに関連付けられます。 テスト ファクトリと運用環境のファクトリには Git リポジトリを関連付けません。更新には必ず Azure DevOps パイプラインか Resource Management テンプレートを利用してください。

下の図は、このライフサイクルのさまざまなステップを示しています。

:::image type="content" source="media/continuous-integration-delivery/continuous-integration-image12.png" alt-text="Azure Pipelines を使用した継続的インテグレーションの図":::

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

データ ファクトリとの Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **Git 統合**。 Git 統合で開発データ ファクトリのみを構成します。 テスト環境と運用環境への変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。

-   **デプロイ前とデプロイ後のスクリプト**。 CI/CD の Resource Manager のデプロイ手順の前に、トリガーの停止と再起動やクリーンアップの実行など、特定のタスクを完了する必要があります。 デプロイ タスクの前後に PowerShell スクリプトを使用することをお勧めします。 詳細については、「[アクティブなトリガーを更新する](continuous-integration-delivery-automate-azure-pipelines.md#updating-active-triggers)」を参照してください。 データ ファクトリ チームは、このページの終わりに使用する[スクリプトを提供](continuous-integration-delivery-sample-script.md)しています。

-   **統合ランタイムと共有**。 統合ランタイムは頻繁には変更されず、CI/CD のすべてのステージで類似しています。 そのため、Data Factory では、CI/CD のすべてのステージで統合ランタイムの名前と種類を同じにすることが求められます。 すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

-   **マネージド プライベート エンドポイント デプロイ**。 プライベート エンドポイントがファクトリに既に存在している場合、同じ名前と変更されたプロパティを持つプライベート エンドポイントを含む ARM テンプレートをデプロイしようとすると、デプロイが失敗します。 つまり、既にファクトリに存在するものと同じプロパティを持つ限り、プライベート エンド ポイントを正常にデプロイできます。 環境によってプロパティが異なる場合は、そのプロパティをパラメーター化し、デプロイ時にそれぞれの値を指定することでオーバーライドできます。

-   **Key Vault**。 リンクされているサービスを使用するとき、その接続情報が Azure Key Vault に格納されている場合、キー コンテナーを環境別に保持することが推奨されます。 キー コンテナーごとに個別のアクセス許可レベルを構成することもできます。 たとえば、運用環境のシークレットへのアクセス許可をチーム メンバーに持たせたくない場合があります。 このアプローチに従う場合は、すべてのステージで同じシークレット名を保持することをお勧めします。 同じシークレット名を保持する場合、CI/CD 環境全体で各接続文字列をパラメーター化する必要はありません。変わるのは別個のパラメーターであるキー コンテナーの名前だけだからです。

-  **リソースの名前付け**。 ARM テンプレートの制約により、リソースの名前にスペースが含まれていると、デプロイで問題が発生する可能性があります。 Azure Data Factory チームは、リソースではスペースの代わりに "_" または "-" 文字を使用することをお勧めします。 たとえば、"Pipeline 1" ではなく "Pipeline_1" という名前を使用します。

- **露出調整と機能フラグ**。  チームで作業しているときに、変更をマージしても、PROD や QA などより高度な環境では実行したくない場合があるとします。 このようなシナリオに対応するため、ADF チームでは、[機能フラグを使用する DevOps 構想](/azure/devops/migrate/phase-features-with-feature-flags)をお勧めしています。 ADF では、[グローバル パラメーター](author-global-parameters.md)と [If Condition アクティビティ](control-flow-if-condition-activity.md)を組み合わせて、これらの環境フラグに基づいてロジックのセットを非表示にすることができます。

    機能フラグを設定する方法については、次のビデオ チュートリアルをご覧ください。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="unsupported-features"></a>サポートされていない機能

- 設計上、Data Factory では、コミットのチェリーピックやリソースの選択的発行は許可されません。 発行には、データ ファクトリで加えられたすべての変更が含まれます

    - データ ファクトリのエンティティは相互に依存しています。 たとえば、トリガーはパイプラインに依存し、パイプラインはデータセットや他のパイプラインに依存しています。 リソースのサブセットを選択的に発行すると、予期しない動作やエラーにつながる可能性があります。
    - まれに、選択的な発行が必要になった場合は、修正プログラムの使用を検討してください。 詳細については、「[運用環境への修正プログラムの適用](continuous-integration-delivery-hotfix-environment.md)」を参照してください。

- Azure Data Factory チームでは、データ ファクトリ内の個々のエンティティ (パイプライン、データセットなど) に Azure RBAC 制御を割り当てることを推奨していません。 たとえば、開発者がパイプラインまたはデータセットにアクセスできる場合、データ ファクトリ内のすべてのパイプラインまたはデータセットにアクセスできるはずです。 データ ファクトリ内に多数の Azure RBAC ロールを実装する必要があると思う場合は、2 つ目のデータ ファクトリをデプロイすることを検討してください。

-   非公開のブランチから発行することはできません。

-   現在、Bitbucket でプロジェクトをホストすることはできません。

-   現在、アラートとマトリックスをパラメーターとしてエクスポートおよびインポートすることはできません。 

- *adf_publish* ブランチの下のコード リポジトリには、ソース管理による公開の一環として、'PartialArmTemplates' という名前のフォルダーが、'linkedTemplates' フォルダー、'ARMTemplateForFactory.json' および 'ARMTemplateParametersForFactory.json' ファイルの横に追加されています。 

    :::image type="content" source="media/continuous-integration-delivery/partial-arm-templates-folder.png" alt-text="'PartialArmTemplates' フォルダーを示す図。":::

    2021 年 11 月 1 日以降、*adf_publish* ブランチへの 'PartialArmTemplates' の公開は終了します。    

    **'PartialArmTemplates' を使用している場合を除き、ユーザーによるアクションは必要ありません。そうでない場合は、'ARMTemplateForFactory.json' や 'linkedTemplates' ファイルを使用する、サポートされているデプロイ用のメカニズムに切り替えてください。**

## <a name="next-steps"></a>次のステップ

- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [各環境用の Resource Manager テンプレートを手動でプロモートする](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [修正プログラム運用環境の適用](continuous-integration-delivery-hotfix-environment.md)
- [サンプルのデプロイ前とデプロイ後のスクリプト](continuous-integration-delivery-sample-script.md)
