---
title: "Azure Automation のグラフィカル Runbook におけるエラー処理 | Microsoft Docs"
description: "この記事では、Azure Automation のグラフィカル Runbook でエラー処理ロジックを実装する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation のグラフィカル Runbook におけるエラー処理

Runbook の重要な設計原則として考慮する必要があるのは、成功、予期されるエラー状態、予期しないエラー状態など、Runbook で発生する可能性のあるさまざまな問題を特定することです。  Runbook には、問題を適宜検出するためのエラー処理が含まれている必要があります。  グラフィカル Runbook でアクティビティの出力を検証したり、エラーを適切な方法で処理したりしたい場合は、PowerShell コード アクティビティを追加して、その出力リンクに条件付きロジックを定義できます。また、それ以外の手法を利用することもできます。          

Runbook の実行時に、終了しないエラーがアクティビティで発生しても、多くの場合、その次のアクティビティはエラーに関係なく処理されます。  もちろん、例外が発生する場合もありますが、ここでのポイントは次のアクティビティが実行可能だという点です。 このような動作の理由は、PowerShell 言語でのエラー処理の方法にあります。    

実行中に発生する可能性がある PowerShell エラーには、終了するエラーと終了しないエラーがあります。  これらの違いは次のとおりです。

* 終了するエラー: 実行中の重大なエラー。コマンド (またはスクリプトの実行) は完全に停止します。 例として、存在しないコマンドレットの使用、コマンドレットが実行できなくなるような構文エラー、その他の致命的なエラーなどが挙げられます。

* 終了しないエラー: 重大でないエラー。無視して実行を継続できます。 例として、ファイルの検出エラー、アクセス許可の問題などの操作上のエラーが挙げられます。

Azure Automation のグラフィカル Runbook では、エラー処理を追加して、例外を終了しないエラーに変換し、エラー リンクをアクティビティ間で作成できるようになりました。 これにより、Runbook の作成者はエラーをキャッチし、検出した状態または予期しない状態を管理することができます。  

## <a name="when-to-use"></a>使用時の注意

エラーまたは例外をスローする重大なアクティビティが存在する場合に、Runbook 内で次のアクティビティに進んでしまうことを確実に回避し、エラーを適切に処理できるようにするためには、ワークフローの実行を制御することが重要です。  これは特に、Runbook がビジネス操作プロセスまたはサービス操作プロセスをサポートしている場合に必要となります。

Runbook の作成者は、エラーが発生する可能性のある各アクティビティに、他の任意のアクティビティを指すエラー リンクを追加できます。  接続先アクティビティとしては、コード アクティビティ、コマンドレットの呼び出し、別の Runbook の呼び出しなど、任意の種類のアクティビティを指定できます。 

さらに、接続先アクティビティでは、出力方向のリンクも含めることができます。このリンクは通常リンクまたはエラー リンクとなり、Runbook の作成者はコード アクティビティに頼らずに複雑なエラー処理ロジックを実装できます。  共通の機能を備えたエラー処理専用の Runbook を作成することが推奨されますが、必須というわけではなく、PowerShell コード アクティビティでのエラー処理ロジック以外にも代替手段は存在します。  

たとえば、VM を起動してアプリケーションをインストールする Runbook があるとします。この Runbook は、VM が正常に起動しなかった場合、次の 2 つのアクションを実行します。 

1. この問題に関する通知を送信する。
2. 代わりの新しい VM を自動的にプロビジョニングする別の Runbook を開始する。 

解決策の 1 つは、手順 1. を処理するアクティビティ (**Write-Warning** コマンドレットなど) を指し、かつ手順 2. のアクティビティ (**Start-AzureRmAutomationRunbook** コマンドレットなど) に接続されたエラー リンクを作成することです。 

この動作は複数の Runbook で使用できるように汎用化することもできます。また、以前に提示したガイダンスに従い、これら 2 つのアクティビティを個別のエラー処理 Runbook に分けることもできます。  このエラー処理 Runbook を呼び出す前に、元の Runbook 内のデータからカスタム メッセージを構築し、パラメーターとしてエラー処理 Runbook に渡すことができます。 

## <a name="how-to-use"></a>使用方法

各アクティビティには、例外を終了しないエラーに変換する構成が用意されています。 既定では、この構成は無効です。  エラーを処理するアクティビティでは、これを有効にする必要があります。  この構成を有効にすることで、アクティビティにおける終了するエラーと終了しないエラーの両方が終了しないエラーとして処理され、エラー リンクで処理できるようになります。  この設定を構成した後で、エラーを処理するアクティビティを作成します。  アクティビティで何らかのエラーが発生すると、出力方向のエラー リンクが使用されます。アクティビティがエラーだけでなく通常の出力も生成した場合でも、通常リンクは使用されません。<br><br> ![Automation Runbook のエラー リンクの例](media/automation-runbook-graphical-error-handling/error-link-example.png)

次の簡単な例に示す Runbook は、仮想マシンのコンピューター名を含む変数を取得し、次のアクティビティで仮想マシンの起動を試みます。<br><br> ![Automation Runbook のエラー処理の例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** アクティビティと **Start-AzureRmVm** は、例外をエラーに変換するように構成されています。  変数の取得や VM の起動に関する問題がある場合には、エラーが生成されます。<br><br> ![Automation Runbook のエラー処理アクティビティの設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

エラー リンクは、これらのアクティビティから単一の**Error Management ** (エラー管理) アクティビティ (コード アクティビティ) にリンクされています。このアクティビティは単純な PowerShell の式で構成されており、*Throw* キーワードで処理を停止し、*$Error.Exception.Message* で現在の例外に関するメッセージを取得するようになっています。<br><br> ![Automation Runbook のエラー処理コードの例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>次のステップ

* リンクおよびグラフィカル Runbook でのリンクの種類の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md#links-and-workflow)」を参照してください。

* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


