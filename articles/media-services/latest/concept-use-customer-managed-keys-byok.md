---
title: Media Services での Bring Your Own Key (カスタマー マネージド キー)
description: Media Services ではカスタマー マネージド キーを使用できます (つまり Bring Your Own Key)。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a89ff56eb9e0f0a29b5b1fed7543c5f718425e51
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325933"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Media Services での Bring Your Own Key (カスタマー マネージド キー)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) は、お客様のワークロードをクラウドに移行するのに役立つ Azure 全体でのイニシアティブです。 カスタマー マネージド キーを使用すると、お客様は業界のコンプライアンス規制に準拠し、サービスのテナント分離を強化できます。 お客様に暗号化キーの制御を委ねることは、不必要なアクセスや制御を最小限にして、Microsoft サービスへの信頼を育む方法となっています。

## <a name="keys-and-key-management"></a>キーとキー管理

Media Services 2020-05-01 API を使用するときには、Media Services で独自のキーを使用できます。 Media Services が所有するシステム キーで暗号化されるすべてのアカウントに対して、既定のアカウント キーが作成されます。 お客様独自のキーを使用するときには、アカウント キーはそのキーで暗号化されます。 コンテンツ キーは、アカウント キーによって暗号化されます。 JobInputHttp URL と、トークン検証用の対称キーも暗号化されます。

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="カスタマー マネージド キーによってシステム管理キーが置き換えられます&quot;:::

Media Services では、Media Services アカウントのマネージド ID を使用して、ユーザーが所有する Key Vault からキーを読み取ります。 Media Services では、その Key Vault がアカウントと同じリージョンにあり、そこで論理的な削除と消去保護が有効になっている必要があります。

お客様のキーには、2048、3072、または 4096 RSA キーを使用できます。また、HSM キーとソフトウェア キーの両方がサポートされています。

> [!NOTE]
> EC キーはサポートされていません。

キー名とキー バージョン、またはキー名だけを指定できます。 キー名のみを使用すると、Media Services では最新のキー バージョンが使用されます。 新しいバージョンのカスタマー キーが自動的に検出され、アカウント キーが再暗号化されます。

> [!WARNING]
> Media Services では、カスタマー キーへのアクセスが監視されます。 カスタマー キーにアクセスできなくなった場合 (キーが削除された場合、Key Vault が削除された場合、アクセスの付与が削除された場合など)、アカウントは Media Services によって、&quot;カスタマー キー アクセス不能状態" に移行されます (事実上のアカウントの無効化)。 ただし、この状態のアカウントは削除可能です。 サポートされる操作は、アカウントの GET、LIST、DELETE だけで、その他のすべての要求 (エンコード、ストリーミングなど) は、アカウント キーへのアクセスが復元されるまで失敗します。

## <a name="tutorial"></a>チュートリアル
「[Media Services でのカスタマー マネージド キーまたは Bring Your Own Key (BYOK) の使用](tutorial-byok.md)」というチュートリアルを試して、Postman と Azure REST API を使用してカスタマー マネージド キーを設定し、使用する方法を確認してください。

## <a name="next-steps"></a>次の手順

* [チュートリアル: Media Services でのカスタマー マネージド キーまたは Bring Your Own Key (BYOK) の使用](tutorial-byok.md)
