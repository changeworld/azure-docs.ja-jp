---
title: Azure Web PubSub サービスの FAQ
description: Azure Web PubSub サービスに関してよく寄せられる質問への回答を紹介します。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e53823539194fe1082621f458e8b5b3d493c191d
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166948"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub サービスの FAQ

これは、Azure Web PubSub サービスの FAQ です。 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub サービスは運用環境に対応していますか?

Azure Web PubSub サービスはパブリック プレビュー段階であり、確約された SLA はありません。 

##  <a name="where-does-my-data-reside"></a>データはどこに存在するか?

Azure Web PubSub サービスは、データ プロセッサ サービスとして機能します。 お客様のコンテンツは格納されず、データ所在地は仕様で含まれています。 診断用の Azure Storage など、他の Azure サービスを Azure Web PubSub サービスと併用する場合は、データ所在地を Azure リージョンで維持する方法に関するガイダンスについて、[こちらのホワイト ペーパー](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)をご覧ください。