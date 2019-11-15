---
title: Azure CDN Standard のルール エンジンを使用した HTTPS の強制 |Microsoft Docs
description: Standard のルール エンジンでは、Azure CDN from Microsoft が HTTP 要求をどのように処理するかをカスタマイズできます (特定の種類のコンテンツの配信のブロック、キャッシュ ポリシーの定義、HTTP ヘッダーの変更など)。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615830"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN Standard のルール エンジン

> [!NOTE]
> Standard のルール エンジンは、Azure CDN from Microsoft にのみ用意されています。 

Azure CDN Standard のルール エンジンでは、HTTP 要求を処理する方法をカスタマイズできます。 たとえば、特定のプロトコルを介したコンテンツの配信の強制、キャッシュ ポリシーの定義、HTTP ヘッダーの変更などを行うことができます。 このチュートリアルでは、ご自分のユーザーを HTTPS に自動的にリダイレクトするルールを作成する方法について説明します。 


## <a name="tutorial"></a>チュートリアル

1. Azure CDN from Microsoft プロファイルの **[CDN プロファイル]** ページで、ルールを構成するエンドポイントを選択します。
  
2. 左側の **[ルール エンジン]** タブを選択します。
   
    [ルール エンジン] ブレードが、グローバル ルールと共に表示されます。 
   
    [![CDN の新しいルール ページ](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 複数の規則が表示される順序は、これらの規則の処理方法に影響します。 前の規則で指定したアクションは、後続の規則でオーバーライドされます。
   >

3. **[ルールの追加]** ボタンをクリックし、ルール名を指定します。 ルール名は文字で開始し、数字と文字のみを含むようにする必要があります。

4. ルールを適用する要求の種類を指定します。 ドロップダウンを使用して**要求プロトコル**の一致条件を選択し、**Equals** **HTTP** の値を使用します。
   
   [![CDN ルールの一致条件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > ドロップダウン リストには複数の一致条件が用意されています。 一致条件の詳細な一覧については、[ルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)に関する記事をご覧ください。
   
5. 指定した要求に適用するアクションを選択します。 ドロップダウンを使用して **[URL のリダイレクト]** アクションを選択します。[種類] には **[検出 (302)]** 、[プロトコル] には **[HTTPS]** の値を使用します。 他のすべてのフィールドは空白のままにし、受信した値を使用します。
   
   [![CDN ルール アクション](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > ドロップダウン リストには複数のアクションがあります。 アクションの詳細な一覧については、[ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)に関する記事をご覧ください。

6. **[保存]** を選択して新しいルールを保存します。  これで、新しいルールがデプロイされます。
   
   > [!IMPORTANT]
   > Azure CDN 全体にルールの変更が反映されるには、最大 15 分かかります。
   >
   

## <a name="see-also"></a>関連項目

- [Azure CDN の概要](cdn-overview.md)
- [Standard のルール エンジンのリファレンス](cdn-standard-rules-engine-reference.md)
- [Standard のルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)
- [Standard のルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
