---
title: チュートリアル - Azure に Jenkins サーバーを作成する
description: このチュートリアルでは、Jenkins ソリューション テンプレートから Azure Linux 仮想マシンに Jenkins をインストールし、サンプル Java アプリケーションをビルドします。
keywords: Jenkins, Azure, 開発, ポータル, 仮想マシン, ソリューション テンプレート
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274531"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>チュートリアル:Azure Linux VM に Jenkins サーバーを作成する 

このチュートリアルでは、Azure で動作するよう構成されているプラグインとツールを備えた [Jenkins](https://jenkins.io) を Ubuntu Linux VM にインストールする方法について説明します。 最終的には、[GitHub](https://github.com) からのサンプル Java アプリをビルドする Jenkins サーバーが Azure で稼働することになります。

> [!div class="checklist"]
> * Azure に Jenkins サーバーをインストールして構成する
> * SSH トンネルを使用して Jenkins コンソールにアクセスする
> * Freestyle プロジェクトを作成する
> * コードをコンパイルし、サンプル アプリをパッケージ化する

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]