---
title: Azure portal を使用して Cloud Services (延長サポート) で監視を有効にする
description: Azure portal を使用して Cloud Services (延長サポート) インスタンスに対して監視を有効にする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 32f140ae27501b9f4f3b66e5f5815b8f3f20b3ff
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445113"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal を使用して Cloud Services (延長サポート) の監視を有効にする

この記事では、既存の Cloud Services (延長サポート) のデプロイでアラートを有効にする方法について説明します。 

## <a name="add-monitoring-rules"></a>監視ルールの追加
1. [Azure portal](https://portal.azure.com) にサインインします。 
2. アラートを有効にする Cloud Services (延長サポート) のデプロイを選択します。 
3. **[アラート]** ブレードを選択します。 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Azure portal の [アラート] タブを選択する方法を示す画像。":::

4. **[新しいアラート]** アイコンを選択します。
     :::image type="content" source="media/enable-alerts-2.png" alt-text="新しいアラートを追加するオプションの選択を示す画像。":::

5. 追跡対象のメトリックに基づいて、目的の条件と必要なアクションを入力します。 個々のメトリックまたはアクティビティ ログに基づいてルールを定義できます。 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="アラートに条件を追加する場所を示す画像。":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="シグナル ロジックの構成を示す画像。":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="アクション グループ ロジックの構成を示す画像。":::

6. アラートの設定が完了したら、変更を保存すると、構成されているメトリックに基づいて、**アラート** ブレードに時間の経過と共に表示されるようになります。

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.yml)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。