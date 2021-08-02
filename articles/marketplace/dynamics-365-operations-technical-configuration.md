---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Operations オファーの技術的な構成を設定する
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Operations オファーの技術的な構成を設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 05/20/2021
ms.openlocfilehash: bd2a371d702ff7f99cd26408cd40329b8c3d7859
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971334"
---
# <a name="set-up-dynamics-365-for-operations-offer-technical-configuration"></a>Dynamics 365 for Operations オファーの技術的な構成を設定する

このページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続によって、Microsoft は、エンド カスタマーがお客様のオファーを取得することを選択した場合に彼らに対してそれをプロビジョニングできます。

## <a name="solution-identifier"></a>ソリューション識別子

ソリューションのソリューション識別子 (GUID) を入力します。 ソリューション識別子を検索するには:

1. Microsoft Dynamics Lifecycle Services (LCS) で、 **[ソリューション管理]** を選択します。
2. ソリューションを選択し、 **[パッケージの概要]** で **ソリューション識別子** を探します。 識別子が空白の場合、 **[編集]** を選択してパッケージを再発行してから、もう一度やり直してください。

## <a name="release-version"></a>リリース バージョン

このソリューションが動作する Dynamics 365 for Operations のバージョンを選択します。

左側のナビゲーション メニューの次のタブである **[Microsoft との共同販売]** に進む前に、 **[下書きの保存]** を選択します。 Microsoft との共同販売の設定 (省略可能) については、[共同販売のパートナー エンゲージメント](./co-sell-overview.md)に関する記事を参照してください。 共同販売を設定していないか、完了した場合は、下の「**次のステップ**」に進みます。

## <a name="test-drive-technical-configuration"></a>体験版の技術的な構成

このページでは、購入前に顧客がオファーを試すことができるデモ (体験版) を設定します。 詳細については、「[体験版とは](what-is-test-drive.md)」をご覧ください。

体験版を有効にするには、[[オファーのセットアップ]](dynamics-365-operations-offer-setup.md#test-drive) タブで **[体験版を有効にする]** チェック ボックスをオンにします。オファーから体験版を削除するには、このチェック ボックスをオフにします。

## <a name="next-steps"></a>次のステップ

- [補足コンテンツを構成する](dynamics-365-operations-supplemental-content.md)