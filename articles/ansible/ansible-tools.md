---
title: "Azure で Ansible を使用するためのツール"
description: "Azure で Ansible を使用するための個々のツールをインストールして使用します。"
ms.service: ansible
keywords: "ansible, azure, DevOps, ツール, vs code, visual studio code, 拡張機能"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Azure で Ansible を使用するためのツール

Ansible と Azure を使った作業は、以下のツールを使うことで、より簡単に、効率よく行うことができます。

## <a name="visual-studio-code-extension-for-ansible"></a>Ansible 用 Visual Studio Code 拡張機能

[Ansible 用 Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)には、Visual Studio Code から Ansible を使用するためのさまざまな機能が用意されています。

- Ansible のディレクティブ、モジュール、プラグインのオート コンプリート。Ansible ドキュメントから入力補完機能を利用できます。
- コード スニペット。Ansible プレイブックを作成しているときに &lt;Ctrl>&lt;Space> キーをクリックすることでコード スニペットを表示できます。
- 構文の強調表示。YAML 構文に従って、Ansible プレイブックのコードをさまざまな色やフォントで表示できます。
- Visual Studio Code ターミナル ウィンドウからの実行。ターミナル ウィンドウから Ansible プレイブックを実行することができます。
    - (Windows のみ) Ansible を Docker コンテナーから実行できます。
    - (Linux および macOS) Ansible を Docker コンテナーから、またはローカルの Ansible インストール環境から実行できます。 
- Azure Cloud Shell からの実行。Ansible プレイブックを Azure Cloud Shell から実行できます。