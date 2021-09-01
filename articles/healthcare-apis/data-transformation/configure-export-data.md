---
title: FHIR サービスでエクスポート設定を構成する
description: この記事では、FHIR サービスでエクスポート設定を構成する方法について説明します
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 327a36714b822669cbc41fc8b8fb195bed4872ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779510"
---
# <a name="configure-export-settings-and-set-up-a-storage-account"></a>エクスポート設定を構成してストレージ アカウントを設定する

FHIR サービスでは、FHIR サービス アカウントからストレージ アカウントにデータをエクスポートできるようにする $export コマンドがサポートされています。

FHIR サービスでエクスポートを構成するには、次の 3 つの手順があります。

1. FHIR サービスのサービスのマネージド ID を有効にする。
2. Azure ストレージ アカウントを作成し (まだ実行していない場合)、FHIR サービスへのアクセス許可をストレージ アカウントに割り当てる。
3. FHIR サービスでストレージ アカウントをエクスポート ストレージ アカウントとして選択する。

## <a name="enabling-managed-identity-on-fhir-service"></a>FHIR サービスのマネージド ID を有効にする

FHIR サービスのエクスポートを構成する最初の手順は、サービスに対するシステム全体のマネージド ID を有効にすることです。 Azure のマネージド ID の詳細については、[Azure リソースのマネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

これを行うには、FHIR サービスに移動し、 **[ID]** を選択します。 状態を **[オン]** に変更すると、FHIR サービスのサービスでマネージド ID が有効になります。

![マネージド ID の有効化](media/export-data/fhir-mi-enabled.png)

これで、ストレージ アカウントの作成とサービスへのアクセス許可の割り当てを実行し、次の手順に進むことができます。

## <a name="adding-permission-to-storage-account"></a>ストレージ アカウントへのアクセス許可の追加

エクスポート データの次の手順は、ストレージ アカウントに書き込むためのアクセス許可を FHIR サービスのサービスに割り当てることです。

ストレージ アカウントを作成したら、ストレージ アカウントの **[アクセスの制御 (IAM)]** に移動し、 **[ロールの割り当ての追加]** を選択します。 

Azure portal でロールに割り当てる方法の詳細については [Azure 組み込みロール](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

ここで、[BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)のロールをサービス名に追加し、 **[保存]** を選択します。

![[ロールの割り当ての追加] ページ](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

これで、$export の既定のストレージ アカウントとして FHIR サービスのストレージ アカウントを選択できるようになりました。

## <a name="selecting-the-storage-account-for-export"></a>$export 用のストレージ アカウントの選択

最後の手順は、FHIR サービスによるデータのエクスポート先として使用する Azure ストレージ アカウントを割り当てることです。 これを行うには、FHIR サービスのサービスの **[統合]** に移動し、ストレージ アカウントを選択します。

![FHIR エクスポート ストレージ](media/export-data/fhir-export-storage.png)

この最後の手順を完了すると、$export コマンドを使用してデータをエクスポートできます。

> [!Note]
> $export 操作の宛先として登録できるのは、FHIR サービスと同じサブスクリプション内のストレージ アカウントだけです。