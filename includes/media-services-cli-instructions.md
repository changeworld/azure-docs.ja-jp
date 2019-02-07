---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104985"
---
## <a name="open-cli-shell"></a>CLI シェルを開く

CLI コマンドを実行するには、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を使用することをお勧めします。 **Cloud Shell** は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 Cloud Shell には一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 [コピー] ボタンを選択してコードをコピーし、Cloud Shell に貼り付けて Enter キーを押すだけで、コードを実行することができます。 Cloud Shell は、次のようにいくつかの方法で開くことができます。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 

### <a name="login"></a>ログイン

(クラウドまたはローカルで) CLI シェルを使い始めるには、`az login` を実行して Azure との接続を作成する必要があります。

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザー ページを開いて、お使いのブラウザーで https://aka.ms/devicelogin に移動した後、コマンド ラインの指示に従って承認コードを入力します。

### <a name="specify-location-of-files"></a>ファイルの場所を指定する

多くの Media Services CLI コマンドでは、ファイル名と共にパラメーターを渡すことができます。 

**Azure Cloud Shell** を使用している場合は、ファイルを **Azure Cloud Shell** にアップロードしてください。 シェル ウィンドウの上部にファイルのアップロード/ダウンロード ボタンがあります。 その後は、次のようにファイルを参照します。`@{FileName}.` 

![ファイルのアップロード]

Azure CLI をローカルで使用している場合は、ファイルのパス全体を指定します。 たとえば、「 `@c:\tracks.json` 」のように入力します。


[ファイルをアップロードする]: ./media/media-services-cli/upload-download-files.png
