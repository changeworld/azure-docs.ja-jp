---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285201"
---
デバイスは、Azure 内のデータの宛先として使用されるストレージ アカウントに関連付けられています。 ストレージ アカウントへのアクセスは、各ストレージ アカウントに関連付けられたサブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって制御されます。

Azure Stack Edge デバイスがストレージ アカウントにアクセスするときに、いずれかのキーが認証に使用されます。 もう一方のキーは予備で保持されているため、それらのキーを定期的にローテーションできます。

セキュリティ上の理由から、多くのデータ センターでキーのローテーションが義務化されています。 キーのローテーションに関しては、以下のベスト プラクティスに従うようお勧めします。

- ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 アカウント キーは慎重に保護してください。 このパスワードを他のユーザーに配布したり、ハード コードしたり、他のユーザーからアクセスできるプレーンテキストで保存したりしないでください。
- 侵害される可能性があると考えられる場合は、Azure Portal 経由で[アカウント キーを再生成](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys)します。
- Azure 管理者は、Azure Portal の [ストレージ] セクションを使用してストレージ アカウントに直接アクセスすることにより、プライマリまたはセカンダリ キーを定期的に変更または再生成する必要があります。
- 独自の暗号化キーを使用して、Azure ストレージ アカウントのデータを保護することもできます。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 データをセキュリティで保護する方法の詳細については、[Azure Storage アカウントに対するカスタマー マネージド キーの有効化](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account)に関する記事を参照してください。
