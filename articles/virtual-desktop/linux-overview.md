---
title: Windows Virtual Desktop の Linux Support - Azure
description: Windows Virtual Desktop の Linux Support の簡単な概要。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422412"
---
# <a name="linux-support"></a>Linux Support

パートナーは、Windows Virtual Desktop 用の Linux SDK を使用して、スタンドアロンの Windows Virtual Desktop クライアントを構築できます。 また、これを使用して、クライアント アプリケーションで Windows Virtual Desktop のサポートを有効にすることもできます。 このクイック ガイドでは、Linux SDK の内容と使用を開始する方法について説明します。

## <a name="connect-with-your-linux-device"></a>Linux デバイスで接続する

Linux デバイス用 Windows Virtual Desktop クライアントは、次のパートナーによって承認されています。

|Partner|パートナー向けドキュメント|パートナー サポート|
|:------|:--------------------|:--------------|
|![IGEL ロゴ](./media/partners/igel.png)|[IGEL クライアントのドキュメント](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL サポート](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Linux SDK とは

SDK API を使用すると、リソース フィードの取得、デスクトップまたはリモート アプリケーション セッションへの接続、ファースト パーティのクライアントがサポートする多くのリダイレクトの使用を行うことができます。

> [!NOTE]
> SDK は現在開発中です。 一般提供されたら、SDK にアクセスする手順をこのドキュメントに含め更新します。

### <a name="supported-linux-distributions"></a>サポートされている Linux ディストリビューション

SDK は、Ubuntu 18.04 以降に基づくほとんどのオペレーティング システムと互換性があります。 Linux ディストリビューションが異なる場合は、お客様と協力して、お客様のニーズに最適なサポートを見つけることができます。

### <a name="feature-support"></a>機能のサポート

SDK では、デスクトップおよびリモート アプリケーション セッションへの複数の接続がサポートされています。 次のリダイレクトがサポートされています。

| リダイレクト       | サポートされています |
| :---------------- | :-------: |
| [キーボード]          | &#10004;  |
| マウス             | &#10004;  |
| オーディオ入力          | &#10004;  |
| オーディオ出力         | &#10004;  |
| クリップボード (テキスト)  | &#10004;  |
| クリップボード (イメージ) | &#10004;  |
| クリップボード (ファイル)  | &#10004;  |
| スマート カード         | &#10004;  |
| ドライブまたはフォルダー      | &#10004;  |

この SDK では、セッション用に選択したモニターが隣接している限り、複数のモニター ディスプレイの構成もサポートされています。

Microsoft は、新しい機能とリダイレクトのサポートを追加するときに、このドキュメントを更新します。 新機能とその他の改善点を提案する場合は、[UserVoice ページ](https://go.microsoft.com/fwlink/?linkid=2116523)にアクセスしてください。

## <a name="get-started-with-the-linux-sdk"></a>Linux SDK を使用して作業を開始する

Windows Virtual Desktop 用の Linux クライアントを開発する前に、次のことを行う必要があります。

1. テストまたは実稼働における使用のために Windows Virtual Desktop 環境を構築して配置します。
2. 使用可能なファースト パーティのクライアントをテストして、Windows Virtual Desktop のユーザー エクスペリエンスを理解します。

## <a name="next-steps"></a>次のステップ

次のクライアント用のドキュメントを確認してください。

- [Windows デスクトップ クライアント](connect-windows-7-and-10.md)
- [Web クライアント](connect-web.md)
- [Android クライアント](connect-android.md)
- [macOS クライアント](connect-macos.md)
- [iOS クライアント](connect-ios.md)
