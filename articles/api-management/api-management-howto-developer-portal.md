---
title: 新しい開発者ポータルへのアクセスとそのカスタマイズ - Azure API Management | Microsoft Docs
description: API Management で新しい開発者ポータルを使用する方法について説明します。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743362"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Azure API Management での新しい開発者ポータルへのアクセスとそのカスタマイズ

この記事では、新しい Azure API Management 開発者ポータルにアクセスする方法を示します。 コンテンツの追加と編集、Web サイトの外観のカスタマイズなど、ビジュアル エディターのエクスペリエンスについても説明します。

![新しい API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
- Azure API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新しい開発者ポータルは現在プレビュー段階です。

## <a name="managed-and-self-hosted-versions"></a>マネージド バージョンとセルフホステッド バージョン

開発者ポータルは 2 つの方法で構築できます。

- **マネージド バージョン** - API Management インスタンスに組み込まれたポータルを編集およびカスタマイズします。URL `<your-api-management-instance-name>.developer.azure-api.net` からアクセスできます。
- **セルフホステッド バージョン** - API Management インスタンスの外部にポータルをデプロイし、自らホスティングします。 このアプローチを使用すると、ポータルのコードベースを編集したり、提供されているコア機能を拡張したりできます。 詳細および手順については、[ポータルのソース コードを含む GitHub リポジトリ][1]を参照してください。

## <a name="access-the-managed-version-of-the-portal"></a>マネージド バージョンのポータルにアクセスする

マネージド バージョンのポータルにアクセスするには、次の手順に従います。

1. Azure portal で API Management サービス インスタンスに移動します。
1. 上部ナビゲーション バーにある **[New developer portal (preview)] \(新しい開発者ポータル (プレビュー)\)** ボタンをクリックします。 ブラウザーの新しいタブで、管理バージョンのポータルが開きます。 ポータルに初めてアクセスする場合、既定のコンテンツが自動的にプロビジョニングされます。

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>マネージド バージョンのポータルを編集およびカスタマイズする

次のビデオでは、ポータルのコンテンツを編集する方法、Web サイトの外観をカスタマイズする方法、変更を公開する方法を示します。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>次の手順

新しい開発者ポータルの詳細を確認します:

- [ソース コードがある GitHub リポジトリ][1]
- [ポータルの自己ホストの手順][2]
- [プロジェクトのパブリック ロードマップ][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects