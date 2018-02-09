---
title: "Azure Site Recovery での復旧計画にスクリプトを追加する方法 | Microsoft Docs"
description: "VMM から Azure への復旧計画に新しいスクリプトを追加するための前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>復旧計画に VMM スクリプトを追加する


この記事では、[Azure Site Recovery](site-recovery-overview.md) での復旧計画に使用する VMM スクリプトを作成して追加する方法について取り上げます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>復旧計画にスクリプトを追加するための前提条件

復旧計画では、PowerShell スクリプトを使用できます。 それらを作成し、VMM ライブラリに格納することで、復旧計画から利用することができます。 スクリプトを作成する際のいくつかの考慮事項を次に示します。

* スクリプトでは、try-catch ブロックを使用して例外を適切に処理する必要があります。
    - スクリプト内で例外が発生すると、実行が停止し、タスクが失敗したことが表示されます。
    - エラーが発生すると、スクリプトの残りの部分は実行されません。
    - 計画されていないフェールオーバーの実行時にエラーが発生した場合、復旧計画は続行されます。
    - 計画されたフェールオーバーの実行時にエラーが発生した場合、復旧計画は停止します。 スクリプトを修正し、想定どおりに実行されることを確認して、復旧計画を再び実行する必要があります。
        - Write-Host コマンドは、復旧計画スクリプトで動作しないので、このコマンドが含まれていると、スクリプトは失敗します。 出力を作成するには、続いてメイン スクリプトを実行するプロキシ スクリプトを作成します。 すべての出力が >> コマンドを使用してパイプ処理されることを確認します。
        - スクリプトは、600 秒以内に実行制御を戻さないとタイムアウトとなります。
        - STDERR に何かが書き出されると、スクリプトは "失敗" と分類されます。 この情報は、スクリプト実行についての詳細画面に表示されます。

* 復旧計画のスクリプトは VMM サービス アカウントのコンテキストで実行されます。 このアカウントに、スクリプトが配置されているリモート共有に対する読み取りアクセス許可が付与されていることを確認してください。 VMM サービス アカウントの権限レベルで実行できるかどうか、スクリプトをテストします。
* VMM コマンドレットは、Windows PowerShell モジュール内で配布されます。 モジュールは、VMM コンソールと一緒にインストールされます。 スクリプトで次のコマンドを使用して、スクリプトに読み込むことができます。
   - Import-Module -Name virtualmachinemanager。 [詳細情報](https://technet.microsoft.com/library/hh875013.aspx)。
* VMM デプロイに 1 つ以上のライブラリ サーバーが存在することを確認します。 既定では、VMM サーバーのライブラリ共有パスは、VMM サーバーにローカルに配置され、MSCVMMLibrary というフォルダー名を持ちます。
    * ライブラリ共有パスがリモートである (または、ローカルであるが MSCVMMLibrary と共有されていない) 場合は、次の手順を実行して共有パスを構成します (ここでは、例として \\libserver2.contoso.com\share\ が使用されています):
      * レジストリ エディターを開き、**HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration** に移動します。
      * **ScriptLibraryPath** の値を \\libserver2.contoso.com\share\. に設定します。 完全な FQDN を指定します。 共有場所にアクセス許可を設定します。 これは共有のルート ノードであることに注意してください。 **このことを確認するには、VMM のルート ノードでライブラリを参照します。表示されたパスはパスのルートとなり、変数で使用する必要があります**。
      * VMM サービス アカウントと同じアクセス許可を備えたユーザー アカウントを使用して、スクリプトをテストします。 このテストで、スタンドアロンのテスト済みスクリプトが復旧計画でも同じように実行できることを確認します。 VMM サーバーで、次の手順を実行して、実行ポリシーを bypass に設定します。
        * 昇格された特権を使用して、**64 ビット Windows PowerShell** コンソールを開きます。
        * 「 **Set-executionpolicy bypass**」と入力します。 [詳細情報](https://technet.microsoft.com/library/ee176961.aspx)。

> [!IMPORTANT]
> 64 ビットの PowerShell のみでバイパスする実行ポリシーを設定してください。 32 ビットの PowerShell 用に設定した場合、スクリプトが実行されません。

## <a name="add-the-script-to-the-vmm-library"></a>VMM ライブラリへのスクリプトの追加

VMM ソース サイトが存在する場合、VMM サーバー上にスクリプトを作成し、復旧計画に含めることができます。

1. ライブラリ共有内に、新しいフォルダーを作成します。 たとえば、\<VMMServerName>\MSSCVMMLibrary\RPScripts を作成します。 このフォルダーを、ソース VMM サーバーとターゲット VMM サーバーに配置します。
2. スクリプト (たとえば、RPScript) を作成し、期待どおりに動作することを確認します。
3. スクリプトを、ソース VMM サーバーとターゲット VMM サーバーの \<VMMServerName>\MSSCVMMLibrary に配置します。

## <a name="add-the-script-to-a-recovery-plan"></a>復旧計画へのスクリプトの追加

グループには、VM またはレプリケーション グループを追加し、計画を作成した後で、スクリプトを追加できます。

1. 復旧計画を開きます。
2. **[ステップ]** リストの項目をクリックし、**[スクリプト]** または **[手動アクション]** をクリックします。
3. 選択した項目の前または後のどちらにスクリプトまたはアクションを追加するかを指定します。 **[上へ移動]** と **[下へ移動]** のボタンを使用して、スクリプトの位置を上下に移動します。
4. VMM スクリプトを追加する場合は、**[Failover to VMM script (VMM へのフェールオーバー スクリプト)]** を選択します。 **[スクリプト パス]** で、共有への相対パスを入力します。 次の VMM の例では、**\RPScripts\RPScript.PS1** というパスを指定します。
5. Azure Automation Runbook を追加する場合は、Runbook が配置されている Azure Automation アカウントを指定し、適切な Azure Runbook スクリプトを選択します。
6. 復旧計画のテスト フェールオーバーを実行して、スクリプトが想定どおりに動作することを確認します。


## <a name="next-steps"></a>次の手順

フェールオーバーの実行については、[こちら](site-recovery-failover.md)を参照してください。
