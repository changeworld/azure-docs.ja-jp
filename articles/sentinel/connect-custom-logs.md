---
title: カスタム ログ形式のデータを Microsoft Sentinel に収集する | Microsoft Docs
description: カスタム データ ソースからデータを収集し、Log Analytics エージェントを使用して Microsoft Sentinel に取り込みます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4625145126a88d11989bbccc8b31cb7c1368145f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724484"
---
# <a name="collect-data-in-custom-log-formats-to-microsoft-sentinel-with-the-log-analytics-agent"></a>Log Analytics エージェントを使用してカスタム ログ形式のデータを Microsoft Sentinel に収集する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

多くのアプリケーションでは、Windows イベント ログや Syslog などの標準のログ記録サービスを使わずに、テキスト ファイルにデータが記録されています。 Log Analytics エージェントを使用すれば、Windows と Linux コンピューターの両方から、非標準形式のテキスト ファイル内のデータを収集できます。 収集されたデータは、クエリで解析して個別のフィールドに格納するか、または収集時に個別のフィールドに抽出することができます。

この記事では、カスタム ログ形式を使用してデータ ソースを Microsoft Sentinel に接続する方法について説明します。 この方法を使用する、サポートされているデータ コネクタの詳細については、[データ コネクタのリファレンス](data-connectors-reference.md)記事を参照してください。

[Azure Monitor におけるカスタム ログに関するドキュメント](../azure-monitor/agents/data-sources-custom-logs.md)を参照してください。

Syslog と同様に、カスタム ログ収集を構成するための手順は 2 つあります。

- ログを生成する Linux または Windows マシンに Log Analytics エージェントをインストールします。

- アプリケーションのログ設定を構成します。

- Microsoft Sentinel から Log Analytics エージェントを構成します。

## <a name="install-the-log-analytics-agent"></a>Log Analytics エージェントをインストールする

ログを生成する Linux または Windows マシンに Log Analytics エージェントをインストールします。

> [!NOTE]
> 一部のベンダーでは、Log Analytics エージェントをデバイスに直接インストールするのではなく、別のログ サーバーにインストールすることを推奨しています。 [データ コネクタのリファレンス](data-connectors-reference.md)ページの該当製品のセクション、または製品自体のドキュメントを参照してください。

該当するコネクタのデータ コネクタ ページが Microsoft Sentinel にあるかどうかに応じて、以下の適切なタブを選択してください。

# <a name="from-a-specific-data-connector-page"></a>[特定のデータ コネクタ ページから](#tab/DCG)

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. デバイスの種類を選択してから、 **[コネクタ ページを開く]** を選択します。

1. ログを生成するデバイスにエージェントをインストールし、オンボードします。 Linux または Windows の内、該当する方を選択します。

    | マシンの種類 | Instructions |
    | --------- | --------- |
    | **Azure Linux VM の場合** | <ol><li>**[Choose where to install the Linux agent]\(Linux エージェントをインストールする場所を選択\)** の下で **[Install agent on Azure Linux virtual machine]\(Azure Linux 仮想マシンにエージェントをインストールする\)** を展開します。<br><br><li>**[Download & install agent for Azure Linux Virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\) >** リンクを選択します。<br><br><li>**[仮想マシン]** ブレードで、エージェントをインストールする仮想マシンを選択し、 **[接続]** を選択します。 接続する各 VM に対してこの手順を繰り返します。 |
    | **その他の Linux マシンの場合** | <ol><li>**[Choose where to install the Linux agent]\(Linux エージェントをインストールする場所を選択\)** の下で **[Install agent on non-Azure Linux Machine]\(Azure 以外の Linux マシンにエージェントをインストールする\)** を展開します。<br><br><li>**[Download & install agent for non-Azure Linux machines]\(Azure 以外の Linux マシン用のエージェントをダウンロードしてインストールする\) >** リンクを選択します。<br><br><li>**[エージェント管理]** ブレードで、 **[Linux サーバー]** タブを選択し、**Linux 用エージェントのダウンロードとオンボード** を行うコマンドをコピーして、Linux マシンで実行します。<br><br> Linux エージェントのインストール ファイルのローカル コピーを保持する場合は、"エージェントのダウンロードとオンボード" コマンドの上にある **[Linux エージェントのダウンロード]** リンクを選択します。|
    | **Azure Windows VM の場合** | <ol><li>**[Choose where to install the Windows agent]\(Windows エージェントをインストールする場所を選択\)** の下で **[Install agent on Azure Windows virtual machine]\(Azure Windows 仮想マシンにエージェントをインストールする\)** を展開します。<br><br><li>**[Download & install agent for Azure Windows Virtual machines]\(Azure Windows 仮想マシン用のエージェントをダウンロードしてインストールする\) >** リンクを選択します。<br><br><li>**[仮想マシン]** ブレードで、エージェントをインストールする仮想マシンを選択し、 **[接続]** を選択します。 接続する各 VM に対してこの手順を繰り返します。 |
    | **その他の Windows マシンの場合** | <ol><li>**[Choose where to install the Windows agent]\(Windows エージェントをインストールする場所を選択\)** の下で **[Install agent on non-Azure Windows Machine]\(Azure 以外の Windows マシンにエージェントをインストールする\)** を展開します。<br><br><li>**[Download & install agent for non-Azure Windows machines]\(Azure 以外の Windows マシン用のエージェントをダウンロードしてインストールする\) >** リンクを選択します。<br><br><li>**[エージェントの管理]** ブレードの **[Windows サーバー]** タブで、32 ビットまたは 64 ビット システム用の内、該当する方の **[Windows エージェントのダウンロード]** リンクを選択します。 |

# <a name="other-data-sources"></a>[その他のデータ ソース](#tab/CUS)

1. Microsoft Sentinel のナビゲーション メニューで **[設定]** を選択し、 **[ワークスペースの設定]** タブを選択します。

1. ログを生成するデバイスにエージェントをインストールし、オンボードします。 Linux または Windows の内、該当する方を選択します。

    | マシンの種類 | Instructions |
    | --------- | --------- |
    | **Azure VM (Windows または Linux)** | <ol><li>Log Analytics ワークスペースのナビゲーション メニューから、 **[仮想マシン]** を選択します。<br><br><li>**[仮想マシン]** ブレードで、エージェントをインストールする仮想マシンを選択し、 **[接続]** を選択します。<br>接続する各 VM に対してこの手順を繰り返します。 |
    | **その他の Windows または Linux マシン** | <ol><li>Log Analytics ワークスペースのナビゲーション メニューから、 **[エージェント管理]** を選択します。<br><br><li>**[Windows サーバー]** または **[Linux サーバー]** タブの内、該当する方を選択します。<br><br><li>Windows の場合、32 ビットまたは 64 ビット システム用の内、該当する方の **[Windows エージェントのダウンロード]** リンクを選択します。 Linux の場合は、**Linux 用エージェントのダウンロードとオンボード** を行うコマンドをコピーしてコマンド ラインから実行するか、 **[Linux エージェントのダウンロード]** リンクを選択してインストール ファイルのローカル コピーをダウンロードします。 |

---

## <a name="configure-the-logs-to-be-collected"></a>収集するログを構成する

多くのデバイスの種類には、Microsoft Sentinel の **[データ コネクタ]** ページに表示される独自のデータ コネクタがあります。 これらのコネクタの中には、Microsoft Sentinel でログ収集を適切に設定するために、特別な追加手順が必要なものがあります。 これらの手順に、Kusto 関数に基づくパーサーの実装が含まれる場合があります。 

ポータルにあるそれぞれのコネクタ ページ、および [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのセクションには、Microsoft Sentinel で一覧表示されているすべてのコネクタに関する具体的な手順が表示されています。

製品が **[データ コネクタ]** ページに表示されていない場合は、デバイスのログ記録を構成する手順について、ベンダーのドキュメントを参照してください。

## <a name="configure-the-log-analytics-agent"></a>Log Analytics エージェントの構成

1. コネクタ ページにある **[Open your workspace custom logs configuration]\(ワークスペースのカスタム ログの構成を開く\)** リンクをクリックします。

    または、Log Analytics ワークスペースのナビゲーション メニューから、 **[カスタム ログ]** を選択します。

1. **[カスタム テーブル]** タブで、 **[カスタム ログの追加]** を選択します。

1. **[サンプル]** タブで、デバイスからログ ファイルのサンプル (access.log や error.log など) をアップロードします。 次に、 **[次へ]** を選択します。

1. **[レコードの区切り記号]** タブで、 **[新しい行]** または **[タイムスタンプ]** (そのタブの説明を参照) のいずれかのレコード区切り記号を選択し、 **[次へ]** を選択します。

1. **[コレクション パス]** タブで、Windows または Linux のパスの種類を選択し、構成に基づいてデバイスのログへのパスを入力します。 次に、 **[次へ]** を選択します。

1. カスタム ログに名前を付け、必要に応じて説明を指定して、 **[次へ]** を選択します。  
    "_CL" は自動的に追加されるため、これを名前の最後には付けないようにしてください。


## <a name="find-your-data"></a>データの検索

**[ログ]** でカスタム ログ データのクエリを実行するには、カスタム ログに付けた名前 ("_CL" で終わる) をクエリ ウィンドウに入力します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、カスタム ログの種類からデータを収集して Microsoft Sentinel に取り込む方法について説明しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
