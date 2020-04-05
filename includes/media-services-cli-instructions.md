---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461216"
---
## <a name="use-cli-shell"></a>CLI シェルを使用する

CLI コマンドを実行するには、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を使用することをお勧めします。 **Cloud Shell** は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 Cloud Shell には一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell には、業務に最適のシェル操作を選択できる柔軟性があります。 Linux ユーザーは Bash を、Windows ユーザーは PowerShell を選ぶことができます。

CLI をローカルにインストールすることもできます。 プラットフォームごとの手順については、「[Install the Azure CLI (Azure CLI をインストールする)](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

### <a name="sign-in"></a>サインイン

CLI のローカル インストールを使用するには、Azure へのサインインが必要です。 Azure Cloud Shell には、この手順は必要ありません。 `az login` コマンドを使用してサインインします。

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザー ページを開いて、お使いのブラウザーで https://aka.ms/devicelogin に移動した後、コマンド ラインの指示に従って承認コードを入力します。

### <a name="specify-location-of-files"></a>ファイルの場所を指定する

多くの Media Services CLI コマンドでは、ファイル名と共にパラメーターを渡すことができます。 **Cloud Shell** を使用している場合は、(Bash または PowerShell を使用して) ファイルを clouddrive にアップロードできます。 

![ファイルをアップロードする]

ローカル CLI を使用しているか **Cloud Shell** を使用しているかにかかわらず、使用している OS または Cloud Shell (Bash または PowerShell) に従ってファイルのパスを指定する必要があります。 以下に例をいくつか示します。

ファイルの相対パス (すべての OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac および Windows の OS の絶対ファイル パス

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

コマンドでファイルへのパスが求められた場合は、`{file}` を使用します。 たとえば、「 `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json` 」のように入力します。 <br/> 指定したファイルをコマンドで読み込む場合は、`@{file}` を使用します。 たとえば、「 `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json` 」のように入力します。

[ファイルをアップロードする]: ./media/media-services-cli/upload-download-files.png
