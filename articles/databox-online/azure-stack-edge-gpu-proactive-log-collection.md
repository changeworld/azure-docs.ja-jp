---
title: Azure Stack Edge Pro デバイスにおける事前ログ収集について
description: Azure Stack Edge Pro デバイスでの事前ログ収集がどのように行われるかについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465531"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスにおける事前ログ収集

システム正常性インジケーターに基づく Azure Stack Edge デバイスの事前ログ収集を有効にして、デバイスの問題のトラブルシューティングを効率的に行うことができます。 この記事では、事前ログ収集の概要、有効にする方法、および事前ログ収集が有効になっている場合にデータがどのように処理されるかについて説明します。
   
この記事の情報は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。

## <a name="about-proactive-log-collection"></a>事前ログ収集について

Microsoft カスタマー サポートとエンジニアリング チームは、Azure Stack Edge デバイスのシステム ログを使用して、操作中に発生する可能性のある問題を効率的に特定し、修正します。 事前ログ収集とは、お客様の Azure Stack Edge アプライアンスによって問題またはイベント (追跡対象のイベントについては、事前ログ収集インジケーターのセクションを参照) が検出されたことを Microsoft に通知する方法です。 その問題に関するサポート ログは、Microsoft によって管理および制御される Azure Storage アカウントに自動的にアップロードされます。 Microsoft サポートと Microsoft のエンジニアは、これらのサポート ログを確認して、お客様の問題を解決するための最適な措置を決定します。    

> [!NOTE]
> これらのログは、デバッグ目的、および問題が発生した場合にお客様にサポートを提供するためにのみ使用されます。 


## <a name="enabling-proactive-log-collection"></a>事前ログ収集を有効にする

事前ログ収集は、ローカル UI を使用してデバイスをアクティブ化する際に有効にすることができます。 

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。
2. **[アクティブ化]** タイルで、 **[アクティブにする]** を選択します。 

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. **[アクティブ化]** ウィンドウで次の操作を実行します。
    1. [Azure Stack Edge Pro R のアクティブ化キーの取得](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)に関するセクションの説明に従って取得した **アクティブ化キー** を入力します。

    1. デバイスの正常性状態に基づいて Microsoft がログを収集できるようにするために、プロアクティブなログ収集を有効にすることができます。 この方法で収集されたログは、Azure Storage アカウントにアップロードされます。
    
    1. **[適用]** を選択します。

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>事前ログ収集インジケーター

事前ログ収集を有効にすると、次のいずれかのイベントがデバイスで検出されたときに、ログが自動的にアップロードされます。  


|アラート/エラー/条件  |説明  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 整合サービスは異常です。         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge エージェントが実行されていません。         |
|UpdateInstallFailedEvent | 更新プログラムをインストールできませんでした。        |

 
Microsoft は、前述の一覧に新しいイベントを今後も追加します。 新しいイベントについては、追加の同意は必要ありません。 最新の事前ログ収集インジケーターについては、このページを参照してください。    
 

## <a name="other-log-collection-methods"></a>その他のログ収集方法

検出された特定の問題に関連する特定のログを収集する事前ログ収集以外に、システムの正常性と動作についてより深く理解できる他のログ収集もあります。 通常、これらの他のログ収集は、サポート リクエスト中に実行することも、デバイスが提供するテレメトリ データに基づいて Microsoft によってトリガーすることもできます。  

## <a name="handling-data"></a>データの処理

お客様が事前ログ収集を有効にした場合、お客様は、ここで説明されているように Azure Stack Edge デバイスからのログを Microsoft が収集することに同意するものとします。 また、お客様は Microsoft によって管理および制御されている Azure ストレージ アカウントに、これらのログをアップロードして保持することについても承認し、同意するものとします。

Microsoft はこのデータを、システムの正常性と問題のトラブルシューティングにのみ使用します。 お客様の同意なしに、マーケティング、広告、その他の商業目的でデータが使用されることはありません。 

Microsoft が収集するデータは、Microsoft 標準のプライバシーに関する声明に従って処理されます。 お客様が事前ログ収集の同意を取り消す場合、取り消し前の同意によって収集されたデータは影響を受けません。

## <a name="next-steps"></a>次のステップ

[サポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)方法について説明します。