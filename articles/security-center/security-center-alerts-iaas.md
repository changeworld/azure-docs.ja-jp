---
title: Azure Security Center での VM とサーバーの脅威検出 | Microsoft Docs
description: このトピックでは、Azure Security Center で使用可能な VM および サーバーのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754311"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure Security Center での VM とサーバーの脅威検出

このトピックでは、次のオペレーティング システムを使用する VM およびサーバーで使用可能なさまざまな種類の検出方法およびアラートを示します。 サポートされているバージョンの一覧については、「[Azure Security Center でサポートされている機能とプラットフォーム](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)」をご覧ください。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center は Azure サービスと統合し、Windows ベースのマシンの監視と保護が行われます。 Security Center は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

* **Microsoft Defender ATP** <a name="windows-atp"></a> -  Security Center は、Microsoft Defender Advanced Threat Protection (ATP) との統合により、そのクラウド ワークロード保護プラットフォームを拡張します。 これにより、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

    > [!NOTE]
    > Microsoft Defender ATP センサーは、Security Center を使用する Windows サーバーで自動的に有効になります。

    Microsoft Defender ATP で脅威が検出されると、アラートがトリガーされます。 アラートは、[Security Center] ダッシュボードに表示されます。 ダッシュボードからは、Microsoft Defender ATP コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることができます。 Microsoft Defender ATP の詳細については、「[Microsoft Defender ATP サービスに対するサーバーのオンボード](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)」をご覧ください。

* **クラッシュ ダンプ分析** <a name="windows-dump"></a> - ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。

    クラッシュは、マルウェアが原因か、マルウェアが含まれている可能性があります。 セキュリティ製品によって検出されないようにするために、さまざまな形式のマルウェアがファイルレス攻撃を使用して、ディスクへの書き込みやディスクに書き込まれたソフトウェア コンポーネントの暗号化を回避します。 この種の攻撃は、従来のディスク ベースのアプローチを使用して検出することは困難です。

    しかし、メモリ分析を使用すると、この種の攻撃を検出できます。 クラッシュ ダンプでメモリを分析すると、Security Center では、攻撃で使用されている手法を検出できます。 たとえば、攻撃では、ソフトウェアの脆弱性の悪用、機密データへのアクセス、侵害されたコンピューターでの不正な保持が試みられている可能性があります。 Security Center では、ホストへのパフォーマンスへの影響を最小限に抑えながら、これが行われます。

    クラッシュ ダンプ分析アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

* **ファイルレス攻撃の検出** <a name="windows-fileless"></a> - エンドポイントを対象とするファイルレス攻撃は一般的です。 ファイルレス攻撃は、検出を回避するために悪意のあるペイロードをメモリに挿入します。 侵害されたプロセスのメモリ内に存続する攻撃者のペイロードにより、さまざまな悪意のあるアクティビティが実行されます。

    自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションでは、実行時にマシンが定期的にスキャンされて、セキュリティ クリティカルなプロセスのメモリから分析情報が直接抽出されます。

    これは、悪用、コード インジェクション、および悪意のあるペイロードの実行の証拠を見つけます。 ファイルレス攻撃の検出により、アラートのトリアージ、相関関係、およびダウン ストリームの応答時間を高速化するための詳細なセキュリティ アラートが生成されます。 このアプローチにより、イベント ベースの EDR ソリューションが補完され、検出範囲が拡大します。

    ファイルレス攻撃の検出アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

> [!NOTE]
> Windows のアラートをシミュレートするには、「[Azure Security Center プレイブック:セキュリティ アラート](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)」をダウンロードします。

## Linux <a name="linux-machines"></a>

Security Center では、**auditd** (最も一般的な Linux 監査フレームワークの 1 つ) を使用して Linux マシンから監査レコードが収集されます。 auditd はメインライン カーネルに存在します。 

* **Linux auditd アラートと Microsoft Monitoring Agent (MMA) の統合** <a name="linux-auditd"></a> - auditd システムは、システム コールの監視を担当するカーネル レベルのサブシステムで構成されます。 指定されたルール セットによってそれらのフィルター処理が行われ、それらに対するメッセージがソケットに書き込まれます。 Security Center は、Microsoft Monitoring Agent (MMA) 内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。  

    Linux の MMA エージェントを使用して、auditd レコードの収集、拡充、およびイベントへの集約が行われます。 Security Center では、Linux のシグナルを使用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析が、継続的に追加されています。 Windows の機能と同様に、これらの分析には、不審なプロセス、不審なサインインの試行、カーネル モジュールの読み込み、その他のアクティビティが含まれます。 これらのアクティビティは、マシンが攻撃を受けているか、侵害されたことを示している可能性があります。  

    以下に、攻撃ライフ サイクルのさまざまなステージにまたがる分析の例をいくつか示します。

    Linux アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-linux)に関するページを参照してください。

> [!NOTE]
> Linux のアラートをシミュレートするには、「[Azure Security Center プレイブック:Linux の検出](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)」をダウンロードします。

 
 ## <a name="next-steps"></a>次のステップ

Security Center の検出の例と詳細については、以下をご覧ください。

* [Azure Security Center によるサイバー攻撃の自動検出のしくみ](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center で管理ツールを使用して脆弱性を検出する方法](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Azure Security Center を活用して、侵害された Linux マシンの攻撃を検出する ](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center で Linux の新たな脆弱性を検出可能](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)