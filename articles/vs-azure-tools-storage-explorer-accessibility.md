---
title: Azure Storage Explorer のアクセシビリティ (プレビュー) | Microsoft Docs
description: Azure Storage Explorer のアクセシビリティ (プレビュー)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: 617a5c35ce059bf13fcf0b5aab415d8e431041e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525940"
---
# <a name="storage-explorer-accessibility"></a>Storage Explorer のアクセシビリティ
## <a name="screen-readers"></a>スクリーン リーダー
Storage Explorer は、Windows と Mac でスクリーン リーダーの使用をサポートします。 各プラットフォームでは、次のスクリーン リーダーをお勧めします。
* Windows: NVDA
* Mac: Voice Over
* Linux: Linux ではスクリーン リーダーはサポートされません

Storage Explorer の使用時にアクセシビリティの問題が発生した場合は、[GitHub に問題を開設](https://github.com/Microsoft/AzureStorageExplorer/issues)してください。

## <a name="zoom"></a>Zoom
Storage Explorer では、ズーム インによってテキストを拡大できます。 ズーム インするには、[ヘルプ] メニューの **[ズーム イン]** をクリックします。 [ヘルプ] メニューを使用して、ズーム アウトしたり、ズーム レベルを既定のレベルのリセットしたりすることもできます。

![[ヘルプ] メニューのズーム オプション][0]

ズーム設定によって、ほとんどの UI 要素のサイズが拡大されます。 また、すべての UI 要素が適切にスケーリングされるように、お使いの OS の大きなテキストおよび拡大/縮小設定を有効にすることを勧めします。

## <a name="high-contrast-theming"></a>ハイ コントラスト テーマ
Storage Explorer には、**ハイ コントラスト （淡色）** と**ハイ コントラスト (濃色)** という 2 つのハイ コントラスト テーマがあります。 [ヘルプ] メニューの [テーマ] サブメニューを使用して、テーマを変更できます。

![[テーマ] サブメニュー][1]

テーマの設定によって、ほとんどの UI 要素の色が変更されます。 また、すべての UI が適切に色付けされるように、お使いの OS に適合するハイ コントラストのテーマを有効にすることをお勧めします。

## <a name="shortcut-keys"></a>ショートカット キー
### <a name="window-commands"></a>ウィンドウ コマンド
|コマンド|キーボード ショートカット|
|--------------|------------------------|
|新しいウィンドウ|**Ctrl + Shift + N**|
|エディターを閉じる|**Ctrl + F4**|
|終了する|**Ctrl + Shift + W**|

### <a name="navigation-commands"></a>ナビゲーション コマンド
|コマンド|キーボード ショートカット|
|--------------|------------------------|
|次のパネルにフォーカス|**F6**|
|前のグループにフォーカス|**Shift + F6**|
|エクスプローラー|**Ctrl + Shift + E**|
|アカウント管理|**Ctrl + Shift + A**|
|サイドバーの切り替え|**Ctrl + B**|
|アクティビティ ログ|**Ctrl + Shift + L**|
|アクションとプロパティ|**Ctrl + Shift + P**|
|現在のエディター|**Ctrl + Home**|
|次のエディター|**Ctrl + PageDown**|
|前のエディター|**Ctrl + PageUp**|

### <a name="zoom-commands"></a>ズーム コマンド
|コマンド|キーボード ショートカット|
|--------------|------------------------|
|拡大|**Ctrl + =**|
|縮小|**Ctrl + -**|

### <a name="blob-and-file-share-editor-commands"></a>BLOB およびファイル共有エディター コマンド
|コマンド|キーボード ショートカット|
|--------------|------------------------|
|戻る|**Alt + ←**|
|転送|**Alt + →**|
|上へ|**Alt + ↑**|

### <a name="other-commands"></a>その他のコマンド
|コマンド|キーボード ショートカット|
|--------------|------------------------|
|コピー|**Ctrl + C**|
|切り取り|**Ctrl + X**|
|貼り付け|**Ctrl + V**|
|開発者ツールの切り替え|**F12**|
|再読み込み|**Ctrl + R**|

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
