---
title: Microsoft Learn で Bicep を検出する
description: Bicep について Microsoft Learn で使用できるユニットの概要について説明します。
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 0238741f4ce6a246c9fd4c8279a55beb494e52ad
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017849"
---
# <a name="bicep-in-microsoft-learn"></a>Microsoft Learn での Bicep

Bicep を使用してインフラストラクチャを Azure にデプロイする手順のガイダンスについて、Microsoft Learn にいくつかの学習モジュールが用意されています。

## <a name="introductory-path"></a>概要パス

「[Bicep を使用して Azure でリソースをデプロイして管理する](/learn/paths/bicep-deploy/)」ラーニング パスは、始めるのに最適な場所です。 コードとしてのインフラストラクチャの概念が紹介されています。 このパスでは、段階的に複雑になる Bicep ファイルを構築する手順が説明されています。

このパスには、次のモジュールが含まれています。

| Learn モジュール | 説明 |
| ------------ | ----------- |
| [Bicep を使用したコードとしてのインフラストラクチャの概要](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | このモジュールでは、コードとしてのインフラストラクチャ、Azure Resource Manager、Bicep を使用して、クラウド デプロイを迅速かつ自信を持ってスケーリングする利点について説明します。 Bicep がデプロイ ツールとして適しているデプロイの種類を判断するのに役立ちます。 |
| [初めての Bicep テンプレートを作成する](/learn/modules/deploy-azure-resources-by-using-bicep-templates/) | このモジュールでは、Bicep テンプレートで Azure リソースを定義します。 デプロイの一貫性と信頼性を向上させ、必要な手作業の労力を減らし、環境間でデプロイを拡張します。 テンプレートは柔軟性があり、パラメーター、変数、式、モジュールを使用して再利用できます。 |
| [パラメーターを使用して再利用可能な Bicep テンプレートを構築する](/learn/modules/build-reusable-bicep-templates-parameters/) | このモジュールでは、Bicep パラメーターを使用して、各デプロイの間にテンプレートに情報を提供する方法について説明します。 パラメーター デコレーターについて学習します。これにより、パラメーターの理解と操作が簡単になります。 また、パラメーター値を提供するさまざまな方法と、セキュリティで保護された情報を操作するときにそれらを保護する方法についても説明します。 |
| [条件とループを使用して柔軟な Bicep テンプレートを作成する](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | 特定の制約が適用されている場合にのみリソースをデプロイするために条件を使用する方法について説明します。 また、ループを使用して、似たプロパティを持つ複数のリソースをデプロイする方法についても説明します。 |
| [Bicep を使用して子と拡張機能のリソースをデプロイする](/learn/modules/child-extension-bicep-templates/) | このモジュールでは、さまざまな Azure リソースを Bicep コードでデプロイする方法について説明します。 子と拡張機能リソース、および Bicep 内で定義および使用する方法について説明します。 Bicep を使用して、Bicep テンプレートまたはモジュール外で作成したリソースを操作します。 |
| [Bicep を使用してサブスクリプション、管理グループ、テナントにリソースをデプロイする](/learn/modules/deploy-resources-scopes-bicep/) | サブスクリプション、管理グループ、テナントのスコープで Azure リソースをデプロイします。 これらのリソースの概要、それらを使用する理由、およびそれらをデプロイするための Bicep コードを作成する方法について学習します。 また、1 つの操作で複数のスコープにデプロイできる Bicep ファイルの 1 つのセットを作成する方法についても学習します。 |
| [デプロイ スクリプトを使用してテンプレートを拡張する](/learn/modules/extend-resource-manager-template-deployment-scripts/) | デプロイ スクリプトを使用して、Bicep ファイルまたは Azure Resource Manager テンプレート (ARM テンプレート) にカスタム手順を追加する方法について学習します。 |

## <a name="other-modules"></a>その他のモジュール

上記のパスに加えて、次のモジュールにも Bicep に関するコンテンツが含まれます。

| Learn モジュール | 説明 |
| ------------ | ----------- |
| [What-If を使用して Azure デプロイの変更をプレビューする](/learn/modules/arm-template-whatif/) | このモジュールでは、What-If 操作で変更内容をプレビューする方法について学習します。 What-If を使用することにより、Bicep ファイルによって意図する変更のみが行われることを確認できます。 |

## <a name="next-steps"></a>次のステップ

* Bicep の概要については、[Bicep のクイックスタート](quickstart-create-bicep-use-visual-studio-code.md)に関する記事を参照してください。
* Bicep ファイルを向上させるための推奨事項については、「[Bicep のベスト プラクティス](best-practices.md)」を参照してください。
