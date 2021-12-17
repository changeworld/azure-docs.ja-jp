---
title: Microsoft AppSource で Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定する - Azure Marketplace
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 06/29/2021
ms.openlocfilehash: 247c36391826ef427ffe1ed8bcc8f7a8991a44d8
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864427"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Dynamics 365 for Customer Engagement & Power Apps オファーの技術的な構成を設定する

このページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続を使用すると、Microsoft はお客様のオファーを、その取得を選択した顧客に対してプロビジョニングできます。

## <a name="offer-information"></a>オファー情報

**基本ライセンス モデル** により、CRM 管理センターで顧客にアプリケーションが割り当てられる方法が決まります。 次のいずれかの操作を行います。

- インスタンス ベースのライセンスの場合は、 **[リソース]** を選択します。
- ライセンスがテナントごとに 1 つ割り当てられている場合、または Microsoft によるアプリ ライセンス管理を選択した場合は、 **[ユーザー]** を選択します。

**[S2S 送信と CRM Secure Store アクセスが必要]** チェック ボックスをオンにすると、CRM Secure Store またはサーバー間 (S2S) 送信アクセスの構成が有効になります。 この機能を使用するには、認定フェーズの間に Dynamics 365 チームによる特別な考慮が必要です。 この機能をサポートするための追加手順の実行について、Microsoft からご連絡します。

**[アプリケーション構成 URL]** は空白のままにします。これは将来使用するためのものです。

## <a name="crm-package"></a>CRM パッケージ

**[パッケージの場所の URL]** ボックスに、アップロードされた CRM パッケージの .zip ファイルを含む Azure Blob Storage アカウントの URL を入力します。 Microsoft が検証用にパッケージを取得できるよう、この URL に読み取り専用の SAS キーを含めます。

> [!IMPORTANT]
> 発行がブロックされないよう、Blob ストレージの URL に指定されている有効期限が切れていないことをご確認ください。 日付は、ポリシーにアクセスすることにより変更できます。 **有効期限** は、少なくとも 1 か月後にすることをお勧めします。

**[パッケージ ファイルに複数の CRM パッケージがあります]** ボックスをオンにします (該当する場合)。 その場合は、.zip ファイルにすべてのパッケージが含まれていることを確認してください。

パッケージをビルドし、その構造を更新する方法の詳細については、「[手順 3:アプリの AppSource パッケージを作成する](/powerapps/developer/common-data-service/create-package-app-appsource)」を参照してください。

## <a name="crm-package-availability"></a>CRM パッケージの利用可能性

**[+ リージョンの追加]** を選択して、顧客が CRM パッケージを利用できる地理的リージョンを指定します。 ソブリン リージョンとして US Gov Cloud Sovereign、China Cloud Sovereign、Germany Sovereign、US Gov High Cloud Sovereign、Test in production Sovereign、US DoD Cloud Sovereign は選択しないでください。

既定では、上記で入力した **アプリケーション構成 URL** が各リージョンに使用されます。 [アプリケーション構成 URL] フィールドは空白のままにします。

左側のナビゲーション メニューの次のタブである **[Microsoft との共同販売]** に進む前に、 **[下書きの保存]** を選択します。 Microsoft との共同販売 (省略可能) の設定については、「[Microsoft のセールス チームとパートナーによる共同販売の概要](./co-sell-overview.md)」を参照してください。 共同販売を設定していないか、完了した場合は、下の「**次のステップ**」に進みます。

## <a name="next-steps"></a>次のステップ

- [補足コンテンツを構成する](dynamics-365-customer-engage-supplemental-content.md)