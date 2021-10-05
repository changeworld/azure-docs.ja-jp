---
title: 運用環境への修正プログラムの適用
description: Azure Data Factory パイプライン内で継続的インテグレーションと継続的デリバリーを使用して、修正プログラムを運用環境に適用する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788b576d351984c4f6a3dff7fd43056aa95aba3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219323"
---
# <a name="using-a-hotfix-production-environment"></a>運用環境への修正プログラムの適用

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ファクトリを運用環境にデプロイし、すぐに修正が必要なバグがあることが判明したが現在のコラボレーション ブランチをデプロイできない場合、修正プログラムのデプロイが必要なことがあります。 この方法は、クイック修正エンジニアリングまたは QFE と呼ばれています。

## <a name="steps-to-deploy-a-hotfix"></a>修正プログラムのデプロイ手順

次の手順を使用して、運用環境とテスト環境に修正プログラムをデプロイします。

1.    Azure DevOps で、運用環境にデプロイされたリリースに移動します。 デプロイされた最後のコミットを見つけます。

1.    コミット メッセージから、コラボレーション ブランチのコミット ID を取得します。

1.    そのコミットから新しい修正プログラム ブランチを作成します。

1.    Azure Data Factory Studio に移動し、修正プログラム ブランチに切り替えます。

1.    Azure Data Factory Studio を使用して、バグを修正します。 変更をテストします。

1.    修正が検証されたら、 **[ARM テンプレートのエクスポート]** を選択して、修正プログラムの Resource Manager テンプレートを取得します。

1.    このビルドを adf_publish ブランチに手動でチェックインします。

1.    adf_publish のチェックインに基づいて自動的にトリガーするようにリリース パイプラインを構成している場合、新しいリリースは自動的に開始します。 それ以外の場合は、手動でリリースをキューに配置します。

1.    テストと運用ファクトリに修正プログラム リリースをデプロイします。 このリリースには、以前の運用ペイロードに加えて、手順 5 で行った修正が含まれています。

1.   修正プログラムでの変更を開発ブランチに追加して、今後のリリースに同じバグが含まれないようにします。

## <a name="video-tutorial"></a>ビデオ チュートリアル
環境に修正プログラムを適用する方法については、次の詳細なビデオ チュートリアルの動画をご覧ください。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>次のステップ

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [各環境用の Resource Manager テンプレートを手動でプロモートする](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [サンプルのデプロイ前とデプロイ後のスクリプト](continuous-integration-delivery-sample-script.md)