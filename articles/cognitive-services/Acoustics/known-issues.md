---
title: 音響プラグインの既知の問題 - Cognitive Services
description: Project Acoustics の Designer Preview の使用時に、以下で説明する既知の問題が発生する場合があります。
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: b0dd5e0c365c65fc7b29752f7885acb71bdb3df8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181431"
---
# <a name="known-issues"></a>既知の問題
Project Acoustics の Designer Preview の使用時に、以下で説明する既知の問題が発生する場合があります。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>シーンの名前を変更すると音響パラメーターが失われる

シーンの名前を変更する場合、そのシーンに属するすべての音響パラメーターが自動的に新しいシーンに移動するわけではありません。 ただし、古いアセット ファイルにはまだ存在します。 シーン ファイルに隣接する **Editor** ディレクトリ内の **SceneName_AcousticParameters.asset** ファイルを探します。 新しいシーン名を反映するようにファイルの名前を変更します。

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>[プローブ] タブで AcousticsData フォルダーの既定のパスが絶対パスである

共同作業者間でプロジェクトを共有しやすいよう、本来は相対パスが既定であるべきです。 回避策として、プロジェクト ディレクトリからの相対パスにパスを変更してください。

## <a name="runtime-voxels-are-a-different-size-than-design-time-voxels"></a>実行時ボクセルのサイズが設計時ボクセルと異なる

**[プローブ]** タブで **[計算]** を実行してボクセルを表示した後、同じシーンに対して実行時にベイクを実行してボクセルを表示した場合、ボクセルのサイズが異なります。 事前ベイクで表示されるボクセルはシミュレーションで使用されるボクセルです。 実行時に表示されるボクセルは、プローブ ポイント間の補間に使用されます。 このことは、実際には開いていないポータルが実行時に開いているように見える不一致の原因になることがあります。

## <a name="uwp-builds-not-working"></a>UWP ビルドが機能しない

Unity の最新バージョン (2018.2 以降) では、UWP ビルドは成功しません。 ビルドの実行フェーズがストールし、"Unity extensions are not yet initialized"(Unity 拡張機能がまだ初期化されていません) というエラーが表示されます。 この件は [Unity の問題](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d)として調査中です。

## <a name="unity-crashes-when-closing-project"></a>プロジェクトを閉じると Unity がクラッシュする

Unity の最新バージョン (2018.2 以降) には、プロジェクトを閉じると Unity がクラッシュするという既知のバグがあります。 この件は [Unity の問題](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)として調査中です。

## <a name="trouble-deploying-to-android"></a>Android へのデプロイの問題
Android で Project Acoustics を使用するには、ビルド ターゲットを Android に変更します。 Unity の一部のバージョンには、オーディオ プラグインのデプロイに関するバグがあります。[こちらのバグ](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)の影響を受けるバージョンを使用していないことを確認してください。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>"could not find metadata file System.Security.dll"(メタデータ ファイル System.Security.dll が見つかりませんでした) というエラーが表示される

Player の設定で [Scripting Runtime Version] が **[.NET 4.x Equivalent]** に設定されていることを確認して、Unity を再起動します。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Azure への接続時に認証の問題が発生している

Azure アカウントの正しい資格情報を使用していること、ベイクで要求されるノードの種類をアカウントがサポートしていること、および、システム クロックが正確であることを再確認してください。

## <a name="next-steps"></a>次の手順
* [実際の Unity プロジェクトに音響効果を統合](getting-started.md)してみる

