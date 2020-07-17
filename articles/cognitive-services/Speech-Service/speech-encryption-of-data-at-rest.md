---
title: Speech サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Speech サービスによる保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372084"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Speech サービスによる保存データの暗号化

データは、クラウドに永続化されるときに Speech サービスによって自動的に暗号化されます。 Speech サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードやアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

Custom Speech とカスタム音声を使用すると、Speech サービスによって次のデータをクラウドに保存できます。  

* 音声トレース データ - カスタム エンドポイントに対してトレースをオンにした場合のみ
* アップロードされたトレーニングとテスト データ

既定では、データは Microsoft のストレージに格納され、サブスクリプションでは Microsoft のマネージド暗号化キーが使用されます。 独自のストレージ アカウントを準備するオプションもあります。 ストアへのアクセスはマネージド ID によって管理され、Speech サービスでは、音声トレース データ、カスタマイズ トレーニング データ、カスタム モデルなど、独自のデータに直接アクセスすることはできません。

詳しくは、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関する記事をご覧ください。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>カスタマイズとログ記録のための独自ストレージの持ち込み (BYOS)

独自ストレージの持ち込みへのアクセスを要求するには、 [Speech サービス - 独自ストレージの持ち込み (BYOS) 要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 承認されたら、カスタマイズとログ記録に必要なデータを格納するために、独自のストレージ アカウントを作成する必要があります。 ストレージ アカウントを追加すると、Speech サービス リソースによって、システム割り当てのマネージド ID が有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory (AAD) に登録されます。 登録された後、そのストレージ アカウントへのアクセス権がマネージド ID に付与されます。 マネージド ID について詳しくは、こちらをご覧ください。 詳しくは、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関する記事をご覧ください。

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、ストレージ アカウントへのアクセス権が削除されます。 これにより、ストレージ アカウントへのアクセスを必要とする Speech サービスの部分は動作を停止します。  

## <a name="regional-availability"></a>リージョン別の提供状況

現在、BYOS は次のリージョンで利用できます。

* 米国中南部
* 米国西部 2
* 米国東部

## <a name="next-steps"></a>次のステップ

* [Speech サービス - 独自ストレージの持ち込み (BYOS) 要求フォーム](https://aka.ms/cogsvc-cmk)
* [マネージド ID とは](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
