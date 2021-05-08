---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & PowerApps オファーの技術的な構成を設定する
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & PowerApps オファーの技術的な構成を設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: 99e7edfb66639f3b2fcc1596ede4e36fd14100bb
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144981"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定する

このページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続によって、Microsoft は、エンド カスタマーがお客様のオファーを取得することを選択した場合に彼らに対してそれをプロビジョニングできます。

## <a name="offer-information"></a>オファー情報

**基本ライセンス モデル** により、CRM 管理センターで顧客にアプリケーションが割り当てられる方法が決まります。 インスタンスベースのライセンスには **[リソース]** を選択します。または、テナントごとにライセンスが 1 つ割り当てられている場合は、 **[ユーザー]** を選択します。

**[S2S 送信と CRM Secure Store アクセスが必要]** チェック ボックスをオンにすると、CRM Secure Store またはサーバー間 (S2S) 送信アクセスの構成が有効になります。 この機能を使用するには、認定フェーズの間に Dynamics 365 チームによる特別な考慮が必要です。 この機能をサポートするための追加手順の実行について、Microsoft からご連絡します。

**[アプリケーション構成 URL]** は空白のままにします。これは将来使用するためのものです。

## <a name="crm-package"></a>CRM パッケージ

**[パッケージの場所の URL]** には、アップロードされた CRM パッケージの .zip ファイルを含む Azure Blob Storage アカウントの URL を入力します。 Microsoft が検証用にパッケージを取得できるよう、この URL に読み取り専用の SAS キーを含めます。

> [!IMPORTANT]
> 発行がブロックされないよう、Blob ストレージの URL に指定されている有効期限が切れていないことをご確認ください。 日付は、ポリシーにアクセスすることにより変更できます。 **有効期限** は、少なくとも 1 か月後にすることをお勧めします。

**[パッケージ ファイルに複数の CRM パッケージがあります]** ボックスをオンにします (該当する場合)。 その場合は、.zip ファイルにすべてのパッケージが含まれていることを確認してください。

パッケージをビルドし、その構造を更新する方法の詳細については、「[手順 3:アプリの AppSource パッケージを作成する](/powerapps/developer/common-data-service/create-package-app-appsource)」を参照してください。

## <a name="crm-package-availability"></a>CRM パッケージの利用可能性

**[+ リージョンの追加]** を選択して、顧客が CRM パッケージを利用できる地理的リージョンを指定します。 次のソブリン リージョンにデプロイするには、認定プロセスの間に特別なアクセス許可と検証が必要です。[ドイツ](../germany/index.yml)、[米国政府クラウド](../azure-government/documentation-government-welcome.md)、TIP。

既定では、上記で入力した **アプリケーション構成 URL** が各リージョンに使用されます。 必要に応じて、1 つ以上の特定のリージョンに個別のアプリケーション構成 URL を入力できます。

左側のナビゲーション メニューの次のタブである **[Microsoft との共同販売]** に進む前に、 **[下書きの保存]** を選択します。 Microsoft との共同販売の設定 (省略可能) については、[共同販売のパートナー エンゲージメント](./co-sell-overview.md)に関する記事を参照してください。 共同販売を設定していないか、完了した場合は、下の「**次のステップ**」に進みます。

## <a name="next-steps"></a>次のステップ

- [補足コンテンツを構成する](dynamics-365-customer-engage-supplemental-content.md)