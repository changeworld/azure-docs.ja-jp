---
title: Azure portal を使用して Cloud Services (延長サポート) 用の ARM テンプレートを生成する
description: Azure portal を使用して Cloud Services (拡張サポート) 用の ARM テンプレートとパラメーター ファイルを生成してダウンロードする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077111"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal を使用して Cloud Services (延長サポート) 用の ARM テンプレートを生成する

この記事では、[Azure portal](https://portal.azure.com) からクラウド サービス用の ARM テンプレートとパラメーター ファイルをダウンロードする方法について説明します。 PowerShell を使用して、ARM テンプレートとパラメーター ファイルをデプロイで使用して、クラウド サービスを作成または更新できます。

## <a name="get-arm-template-via-portal"></a>ポータルを使用して ARM テンプレートを取得する

  1. Azure portal にアクセスし、[新しいクラウド サービスを作成](deploy-portal.md)します。 クラウド サービスの構成、パッケージ、定義ファイルを追加します。 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="作成中の [基本] タブのアップロード セクションの画像。":::
  
  2. すべてのフィールドの入力を終えたら、[確認と作成] タブに移動してデプロイ構成を検証し、クラウド サービス (延長サポート) で **[オートメーション用のテンプレートをダウンロードする]** をクリックします。
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Azure portal のクラウド サービス (延長サポート) でテンプレートをダウンロードしていることを示す図。":::
  
  3. ご自分のテンプレートとパラメーター ファイルをダウンロードします。 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Azure portal でのテンプレート ファイルのダウンロードを示す図。":::
  
  4. [レビューと作成] タブからパッケージの SAS URI と構成の SAS URI をコピーし、parameter.json ファイルに追加します。 これらのファイルは、PowerShell を使用して新しいクラウド サービスを作成するために使用できるようになりました。
    :::image type="content" source="media/download-template-portal-2.png" alt-text="イメージは、Azure portal 上のパッケージの SAS URI と構成の SAS URI パラメーターを示しています。":::
  
## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md) を使用してクラウド サービス (延長サポート) をデプロイする
  
