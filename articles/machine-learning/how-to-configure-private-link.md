---
title: プライベート エンドポイントを構成する (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Private Link を使用して、仮想ネットワークから Azure Machine Learning ワークスペースに安全にアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: 83927c9df9a4f1a6ab32c15c481898ec68f53c4c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898151"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Machine Learning ワークスペース用に Azure Private Link を構成する (プレビュー)

このドキュメントでは、Azure Machine Learning ワークスペースで Azure Private Link を使用する方法について説明します。 Azure Machine Learning の仮想ネットワークの設定に関する詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

> [!IMPORTANT]
> Azure Machine Learning ワークスペースでの Azure Private Link の使用は、現在パブリック プレビュー段階です。 この機能は、**米国東部**、**米国中南部**、および**米国西部 2** リージョンでのみご利用いただけます。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 Private Link を使用すると、データ窃盗のリスクを軽減できます。 プライベート エンドポイントの詳細については、[Azure Private Link](/azure/private-link/private-link-overview) に関する記事を参照してください。

> [!IMPORTANT]
> Azure Private Link は、ワークスペースの削除やコンピューティング リソースの管理などの Azure コントロール プレーン (管理操作) には影響しません。 たとえば、コンピューティング先の作成、更新、削除などです。 これらの操作は、通常どおりパブリック インターネット経由で実行されます。 Azure Machine Learning Studio を使用するなどのデータ プレーン操作、API (公開されたパイプラインを含む)、または SDK では、プライベート エンドポイントが使用されます。
>
> Mozilla Firefox を使用している場合、ワークスペースのプライベート エンドポイントにアクセスしようとしたときに問題が発生することがあります。 この問題は、Mozilla の DNS over HTTPS に関連している可能性があります。 回避策として、Microsoft Edge または Google Chrome を使用することをお勧めします。

> [!TIP]
> Azure Machine Learning コンピューティング インスタンスは、ワークスペースとプライベート エンドポイントで使用できます。 この機能は現在、**米国東部**、**米国中南部**、および**米国西部 2** リージョンでパブリック プレビュー段階にあります。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>プライベート エンドポイントを使用するワークスペースを作成する

> [!IMPORTANT]
> 現時点では、新しい Azure Machine Learning ワークスペースを作成する場合にのみ、プライベート エンドポイントの有効化がサポートされています。

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) のテンプレートを使用し、プライベート エンドポイントでワークスペースを作成できます。

プライベート エンドポイントを含め、このテンプレートの使用に関する詳細については、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。](how-to-create-workspace-template.md)」を参照してください。

## <a name="using-a-workspace-over-a-private-endpoint"></a>プライベート エンドポイント経由でのワークスペースの使用

ワークスペースへの通信は仮想ネットワークからのみ許可されているため、ワークスペースを使用する開発環境はすべて仮想ネットワークのメンバーである必要があります。 たとえば、仮想ネットワーク内の仮想マシンを使用します。

> [!IMPORTANT]
> 接続の一時的な中断を回避するために、Private Link を有効にした後で、ワークスペースに接続しているコンピューターの DNS キャッシュをフラッシュすることをお勧めします。 

Azure Virtual Machines について詳しくは、「[Virtual Machines のドキュメント](/azure/virtual-machines/)」を参照してください。


## <a name="next-steps"></a>次の手順

Azure Machine Learning ワークスペースのセキュリティ保護の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。
