---
title: Azure SSIS 統合ランタイムの有料 (ライセンスあり) コンポーネントを開発する | Microsoft Docs
description: この記事では、ISV が Azure SSIS 統合ランタイムの有料 (ライセンスあり) コンポーネントをどのようにして開発し、インストールできるかについて説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムの有料 (ライセンスあり) カスタム コンポーネントを開発する

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>問題 - Azure SSIS IR で別の手法が必要となる

Azure SSIS 統合ランタイムは、その性質上、いくつかの難題を提示します。そのため、カスタム コンポーネントのオンプレミス インストールに使用される一般的なライセンス供与方法では不十分となります。

-   Azure SSIS IR のノードは一時的なものであり、いつでも割り当てたり、リリースしたりできます。 たとえば、ノードを開始したり、停止したりすることでコストを管理し、ノード サイズをスケールアップまたはスケールダウンします。 結果的に、MAC アドレスや CPU ID など、コンピューター固有の情報をサードパーティ コンポーネント ライセンスを特定のノードにバインドできなくなります。

-   また、ノードの数をいつでも拡大または縮小できるように、Azure SSIS IR をスケールインまたはスケールアウトできます。

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>解決策 - Windows 環境変数と SSIS システム変数によるライセンスのバインドと検証

前のセクションで述べた従来のライセンス供与方法には限界があることから、Azure SSIS IR では、Windows 環境変数と SSIS システム変数でサードパーティ コンポーネントのライセンスをバインドし、検証します。 ISV はこのような変数を使用し、クラスター ID やクラスター ノード数など、Azure SSIS IR の固有かつ永続的な情報を取得できます。 この情報が取得できたら、ISV は、顧客が開始/停止、スケールアップ/ダウン、スケールイン/アウトしたり、何らかの形式で Azure SSIS IR の構成を変更したりしても変化しない ID を利用し、そのコンポーネントのライセンスを Azure SSIS IR に*クラスターとして*バインドできます。

次の図は、これらの新しい変数を使用するサードパーティ コンポーネントの一般的なインストール、ライセンス認証、ライセンス バインディング、検証のフローを示したものです。

![ライセンス コンポーネントのインストール](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>このサンプルについての指示
1. ISV はさまざまな SKU または階層でライセンス コンポーネントを提供できます (シングル ノード、最大 5 個のノード、最大 10 個のノードなど)。 ISV は、顧客が製品を購入したときにプロダクト キーを提供します。 ISV は、ISV セットアップ スクリプトと関連ファイルが含まれる Azure Storage BLOB コンテナーも提供します。 顧客はこれらのファイルを自分のストレージ コンテナーに複製し、自分のプロダクト キーで変更できます (たとえば、`IsvSetup.exe -pid xxxx-xxxx-xxxx` を実行します)。 顧客は次に、自分のコンテナーの SAS URI をパラメーターとして Azure SSIS IR をプロビジョニングしたり、再構成したりできます。 詳細については、「[Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)」 (Azure SSIS 統合ランタイムのカスタム セットアップ) を参照してください。

2. Azure SSIS IR がプロビジョニングまたは再構成されると、ISV セットアップが各ノードで実行され、Windows 環境変数の `SSIS_CLUSTERID` と `SSIS_CLUSTERNODECOUNT` にクエリを実行します。 次に、Azure SSIS IR はそのクラスター ID とライセンス製品のプロダクト キーを ISV ライセンス認証サーバーに送信し、ライセンス認証キーを生成します。

3. ライセンス認証キーを受け取ると、ISV セットアップでは、各ノードにキーをローカル保存できます (たとえば、レジストリに)。

4. Azure SSIS IR のノードで ISV のライセンス コンポーネントを使用するパッケージを顧客が実行すると、そのパッケージはローカル保存されているライセンス認証キーを読み込み、ノードのクラスター ID に基づいて有効性を確認します。 そのパッケージはまた、クラスター ノード数を ISV ライセンス認証サーバーに任意で報告できます。

    次は、ライセンス認証キーの有効性を確認し、クラスター ノード数を報告するコード例です。

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>次の手順

-   [Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure SSIS 統合ランタイムの Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)