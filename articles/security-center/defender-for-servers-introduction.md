---
title: Microsoft Defender for servers - 利点と機能
description: Microsoft Defender for servers の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 121809ab32db6811eb6755c857f13a3469cbcfa0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061050"
---
# <a name="introduction-to-microsoft-defender-for-servers"></a>Microsoft Defender for servers の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for servers によって、お使いの Windows マシンと Linux マシンを対象とした脅威検出および高度な防御が追加されます。

Windows の場合、Microsoft Defender for Cloud は Azure サービスと統合され、Windows ベースのマシンを監視および保護します。 Defender for Cloud は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

Linux の場合、Defender for Cloud は auditd (最も一般的な Linux 監査フレームワークの 1 つ) を使用して、Linux マシンから監査レコードを収集します。


## <a name="what-are-the-benefits-of-microsoft-defender-for-servers"></a>Microsoft Defender for servers の利点

Microsoft Defender for servers には、次のような脅威検出および保護機能が用意されています。

- **Microsoft Defender for Endpoint の統合ライセンス** - Microsoft Defender for servers には、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) が含まれています。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。 詳細については、[エンドポイントの保護](integration-defender-for-endpoint.md)に関する記事をご覧ください。

    Defender for Endpoint で脅威が検出されると、アラートがトリガーされます。 このアラートは Defender for Cloud に表示されます。 Defender for Cloud から、Defender for Endpoint コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることもできます。 Microsoft Defender for Endpoint の詳細をご確認ください。

    > [!IMPORTANT]
    > Defender for Cloud と Microsoft Defender for Endpoint の統合は、既定で有効になっています。 そのため、Microsoft Defender for servers を有効にする際、脆弱性、インストールされているソフトウェア、エンドポイントのアラートに関連する Microsoft Defender for Endpoint データへの Defender for Cloud によるアクセスに同意します。
    >
    > 現在、このセンサーは、プレビュー段階で Linux マシン用に提供されています。 詳細については、「[Defender for Cloud の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)」を参照してください。

- **マシンの脆弱性評価ツール** - Microsoft Defender for servers には、マシンに対する脆弱性の検出と管理のツールが含まれています。 Defender for Cloud の [設定] ページから、これらのツールのうち、マシンに配置するものを選択でき、検出された脆弱性はセキュリティに関する推奨事項で示されます。

    - **Microsoft 脅威と脆弱性の管理** - Microsoft Defender for Endpoint を使用して、リアルタイムで脆弱性と構成の誤りを発見します。追加のエージェントや定期的なスキャンは必要ありません。 [脅威と脆弱性の管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)によって、脅威の情勢、組織での検出、脆弱なデバイス上の機密情報、ビジネス事情に基づき、脆弱性の優先順位が付けられます。 詳細については、「[Microsoft Defender for Endpoint の脅威と脆弱性の管理を使用して弱点を調査する](deploy-vulnerability-assessment-tvm.md)」を参照してください

    - **Qualys を使用した脆弱性スキャナー** - Qualys のスキャナーは、Azure とハイブリッドの仮想マシンの脆弱性をリアルタイムで特定するための主要なツールの 1 つです。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Defender for Cloud 内でシームレスに処理されます。 詳細については、[Azure およびハイブリッドのマシンに対する Defender for Cloud の統合された Qualys スキャナー](deploy-vulnerability-assessment-vm.md)に関する記事を参照してください。

- **Just-In-Time (JIT) 仮想マシン (VM) アクセス** - 脅威アクターは、RDP や SSH などの、開いている管理ポートがあるアクセス可能なマシンを積極的に探しています。 すべての仮想マシンは、攻撃の対象となる可能性があります。 VM への侵害が成功すると、これは環境内のリソースをさらに攻撃するためのエントリ ポイントとして使用されます。

    Microsoft Defender for servers を有効にすると、Just-In-Time VM アクセスを使用して、お使いの VM への受信トラフィックをロックダウンできます。これにより、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。 詳細については、[JIT VM アクセス](just-in-time-access-overview.md)に関する記事をご覧ください。

- **ファイルの整合性の監視 (FIM)** - ファイルの整合性の監視 (FIM) は変更の監視とも呼ばれ、オペレーティング システムやアプリケーション ソフトウェアなどのファイルとレジストリを調べて、攻撃の兆候となる変更を確認します。 比較方式を使用して、ファイルの現在の状態がファイルの前回のスキャンと異なっているかどうかが判別されます。 この比較を使用して、有効なものであれ、疑わしいものであれ、ファイルに変更が加えられていないかを判別できます。

    Microsoft Defender for servers を有効にすると、FIM を使用して、Windows ファイル、Windows レジストリ、および Linux ファイルの整合性を検証できます。 詳細については、「[Microsoft Defender for Cloud でのファイルの整合性の監視](file-integrity-monitoring-overview.md)」を参照してください。

- **適応型アプリケーション制御 (AAC)** - 適応型アプリケーション制御は、マシンの既知の安全なアプリケーションの許可リストを定義するための、インテリジェントで自動化されたソリューションです。

    適応型アプリケーション制御を有効にして構成した場合、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されます。 詳細については、「[適応型アプリケーション制御を使用して、マシンの攻撃対象領域を減らす](adaptive-application-controls.md)」をご覧ください。

- **アダプティブ ネットワークのセキュリティ強化機能 (ANH)** - ネットワーク セキュリティ グループ (NSG) を適用してリソースとの間でやり取りされるトラフィックをフィルター処理することにより、ネットワーク セキュリティ ポスチャを向上させることができます。 ただし、NSG を通過する実際のトラフィックが、定義される NSG ルールのサブセットとなる場合もあります。 このような場合は、実際のトラフィック パターンに基づいて NSG ルールを強化することによって、セキュリティ ポスチャをさらに向上させることができます。

    アダプティブ ネットワークのセキュリティ強化機能によって、NSG ルールをさらに強化するための推奨事項が提供されます。 実際のトラフィック、既知の信頼された構成、脅威インテリジェンス、および侵害に関するその他のインジケーターを考慮する機械学習アルゴリズムを使用し、特定の IP/ポート タプルからのトラフィックのみを許可する推奨事項を提示します。 詳細については、「[アダプティブ ネットワークのセキュリティ強化により、ネットワークのセキュリティ体制を向上させる](adaptive-network-hardening.md)」をご覧ください。


- **Docker ホストの強化機能** - Microsoft Defender for Cloud では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Defender for Cloud によって、こうしたコンテナーの構成が継続的に評価されます。 その後、Center for Internet Security (CIS) Docker Benchmark と比較されます。 Defender for Cloud には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 詳細については、「[Docker ホストのセキュリティを強化する](harden-docker-hosts.md)」をご覧ください。

- **ファイルレス攻撃の検出** - ファイルレス攻撃は、ディスクベースのスキャン手法による検出を避けるために、悪意のあるペイロードをメモリに挿入します。 侵害されたプロセスのメモリ内に存続する攻撃者のペイロードにより、さまざまな悪意のあるアクティビティが実行されます。

  自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションでは、実行時にマシンが定期的にスキャンされて、プロセスのメモリから分析情報が直接抽出されます。 特定の分析情報には、次のものの識別が含まれます。 

  - よく知られているツールキットと暗号化マイニング ソフトウェア 

  - シェルコード。通常、ソフトウェアの脆弱性の悪用でペイロードとして使用される小さなコードです。

  - プロセスのメモリ内に挿入された悪意のある実行可能ファイル

  ファイルレス攻撃の検出では、ネットワーク アクティビティなどのプロセス メタデータの記述を含む詳細なセキュリティ アラートが生成されます。 これらの詳細により、アラートのトリアージ、関連付け、およびダウンストリームの応答時間が短縮されます。 このアプローチにより、イベントベースの EDR ソリューションが補完され、検出範囲が拡大します。

  ファイルレス攻撃の検出アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

- **Linux auditd アラートと Log Analytics エージェントの統合 (Linux のみ)** - auditd システムは、システム コールの監視を担当するカーネル レベルのサブシステムで構成されます。 指定されたルール セットによってそれらのフィルター処理が行われ、それらに対するメッセージがソケットに書き込まれます。 Defender for Cloud は、Log Analytics エージェント内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。

    Linux 用 Log Analytics エージェントによって、auditd レコードが収集され、それらがエンリッチされてイベントに集約されます。 Defender for Cloud では、Linux のシグナルを使用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析が継続的に追加されています。 Windows の機能と同様に、これらの分析には、不審なプロセス、不審なサインインの試行、カーネル モジュールの読み込み、その他のアクティビティが含まれます。 これらのアクティビティは、マシンが攻撃を受けているか、侵害されたことを示している可能性があります。  

    Linux アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-linux)に関するページを参照してください。


## <a name="simulating-alerts"></a>アラートのシミュレーション

アラートをシミュレートするには、次のいずれかのプレイブックをダウンロードします。

- Windows の場合: [Microsoft Defender for Cloud プレイブック: セキュリティ アラート](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux の場合: [Microsoft Defender for Cloud プレイブック: Linux の検出](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)




## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Defender for servers について説明しました。 

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

関連資料については、次のページを参照してください。

- アラートが Defender for Cloud によって生成されたか、別のセキュリティ製品から Defender for Cloud によって受信されたかにかかわらず、アラートをエクスポートできます。 アラートを Microsoft Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。
