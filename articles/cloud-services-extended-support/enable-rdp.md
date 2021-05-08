---
title: Cloud Services (延長サポート) でリモート デスクトップを適用する
description: Cloud Services (延長サポート) に対してリモート デスクトップを有効にする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773404"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>リモート デスクトップ拡張機能を Azure Cloud Services (延長サポート) に適用する

Azure portal では、アプリケーションをデプロイした後でもリモート デスクトップ拡張機能を使用することで、リモート デスクトップを有効にすることができます。 ご利用のクラウド サービスのリモート デスクトップ設定を使用すると、リモート デスクトップの有効化、ローカル管理者アカウントの更新、認証に使用する証明書の選択、およびそれらの証明書の有効期限の設定を行うことができます。 

## <a name="apply-remote-desktop--extension"></a>リモート デスクトップ拡張機能を適用する
1. リモート デスクトップを有効にするクラウド サービスに移動し、左側のナビゲーション ウィンドウで **[リモート デスクトップ]** を選択します。

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Azure portal での [リモート デスクトップ] オプションの選択を示す画像":::

2. **[追加]** を選択します。
3. リモート デスクトップを有効にするロールを選択します。
4. ユーザー名、パスワード、有効期限、証明書 (必須ではない) の各必須フィールドに入力します。
> [注] リモート デスクトップのパスワードは、長さが 8 から 123 文字であり、パスワードの複雑さに関する次の要件のうち 3 つ以上を満たしている必要があります。1) 大文字が含まれている 2) 小文字が含まれている 3) 数字が含まれている 4) 特殊文字が含まれている 5) 制御文字は使用できない

   :::image type="content" source="media/remote-desktop-2.png" alt-text="リモート デスクトップへの接続に必要な情報の入力を示す画像。":::

5. 終わったら、 **[保存]** を選択します。 ロール インスタンスが接続を受信する準備が完了するまで数分間かかります。

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>リモート デスクトップが有効になっているロール インスタンスに接続する
ロールに対してリモート デスクトップが有効になったら、Azure portal から直接接続を開始できます。

1. **[ロールとインスタンス]** をクリックして、インスタンスの設定を開きます。

    :::image type="content" source="media/remote-desktop-3.png" alt-text="[構成] ブレードでの [ロールとインスタンス] オプションの選択を示す画像。":::

2. リモート デスクトップが構成されたロール インスタンスを選択します。
3. **[接続]** をクリックして、リモート デスクトップ接続ファイルをダウンロードします。

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Azure portal での worker ロール インスタンスの選択を示す画像。":::
    
4. そのファイルを開いてロール インスタンスに接続します。


## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
