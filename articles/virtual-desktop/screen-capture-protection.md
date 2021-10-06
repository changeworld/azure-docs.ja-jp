---
title: Azure Virtual Desktop の画面キャプチャの保護
titleSuffix: Azure
description: Azure Virtual Desktop の画面キャプチャの保護を設定する方法。
author: gundarev
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: denisgun
ms.service: virtual-desktop
ms.openlocfilehash: ec7efa35e6b6f036935e6a81f01348f3633f83be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627848"
---
# <a name="screen-capture-protection"></a>画面キャプチャ保護

>[!IMPORTANT]
>スクリーン キャプチャ保護は、Microsoft 365 Government (GCC)、GCC-High、Microsoft 365 DoD の環境ではまだサポートされていません。

画面キャプチャの保護機能を使用することで、機密情報がクライアント エンドポイントでキャプチャされないようにします。 この機能を有効にすると、スクリーンショットおよびスクリーン共有でリモート コンテンツが自動的にブロックまたは非表示になります。 また、リモート デスクトップ クライアントは、画面をキャプチャしている可能性のある悪意のあるソフトウェアからコンテンツを隠します。

## <a name="prerequisites"></a>前提条件

画面キャプチャの保護機能は、セッション ホスト レベルで構成されてクライアントに適用されます。 この機能をサポートするクライアントだけが、リモート セッションに接続できます。 現在、画面キャプチャの保護をサポートするのは Windows デスクトップ クライアントだけです。 完全なデスクトップのみがサポートされています。 ユーザーがサポート対象外のクライアントを使用して、保護されたセッション ホストに接続しようとしたり、保護されたセッション ホストで公開された RemoteApp にアクセスしようとしたりするとどうなるのでしょうか。 その場合は、エラー 0x1151 が発生して接続に失敗します。 

## <a name="configure-screen-capture-protection"></a>画面キャプチャの保護を構成する

1. 画面キャプチャの保護を構成するには、Azure Virtual Desktop のルールと設定を追加する管理用テンプレートをインストールする必要があります。 
2. [Azure Virtual Desktop ポリシー テンプレート ファイル](https://aka.ms/avdgpo) (AVDGPTemplate.cab) をダウンロードして、cab ファイルと zip アーカイブの内容を抽出します。
3. *terminalserver-avd.admx* ファイルを *%windir%\PolicyDefinitions* フォルダーにコピーします
4. *en-us\terminalserver-avd.adml* ファイルを *%windir%\PolicyDefinitions\en-us* フォルダーにコピーします
5. ファイルが正しくコピーされたかを確認するために、グループ ポリシー エディターを開き、 **[コンピューターの構成]**  ->  **[管理用テンプレート]**  ->  **[Windows コンポーネント]**  ->  **[リモート デスクトップ サービス]**  ->  **[リモート デスクトップ セッション ホスト]**  ->  **[Azure Virtual Desktop]** の順に移動します
6. 次に示すように、1 つまたは複数の Azure Virtual Desktop ポリシーが表示されます。

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="グループ ポリシー エディターのスクリーンショット" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!TIP]
   > Active Directory ドメインのグループ ポリシー セントラル ストアに管理用テンプレートをインストールすることもできます。
   > グループ ポリシー管理用テンプレートのセントラル ストアについて詳しくは、「[Windows でグループ ポリシー管理用テンプレート用のセントラル ストアを作成および管理する方法](/troubleshoot/windows-client/group-policy/create-and-manage-central-store)」を参照してください。

7. **[Enable screen capture protection]\(画面キャプチャ保護を有効にする\)** ポリシーを開き、 **[有効]** に設定します。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

- この機能は、公開されているオペレーティング システム機能と API の特定のセットを使用して、リモート デスクトップ ウィンドウがキャプチャされないよう保護するものです。 ただし、この機能によってコンテンツが厳密に保護される保証はありません。たとえば、だれかが画面の写真を撮影する場合などです。
- お客様は、この機能を使用すると共に、クリップボード、ドライブ、プリンターのリダイレクトを無効にする必要があります。 リダイレクトを無効にすると、キャプチャした画面の内容をユーザーがリモート セッションからコピーすることはできなくなります。
- この機能が有効になっている場合、Microsoft Teams などのローカル コラボレーション ソフトウェアを使用してリモート デスクトップ ウィンドウを共有することはできません。 Microsoft Teams が使用されている場合、メディア最適化を使用して実行されている Teams とローカル Teams アプリの両方で、保護されたコンテンツを共有することはできません。
- 保護されたリソースに対し、ユーザーが Web クライアント 1.0.24.14 で接続しようとすると、エラー メッセージではなく認証プロンプトが表示されます。

## <a name="next-steps"></a>次のステップ

* Azure Virtual Desktop のセキュリティ ベスト プラクティスについては、「[Azure Virtual Desktop のセキュリティに関するベスト プラクティス](security-guide.md)」を参照してください。
