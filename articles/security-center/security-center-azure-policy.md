---
title: "Azure Security Center のセキュリティ ポリシーを Azure Policy に統合する | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center のセキュリティ ポリシーを Azure Policy に統合するための構成方法について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>Azure Policy によって提供される Security Center のセキュリティ ポリシーの設定
このドキュメントでは、Azure Policy によって提供される Security Center のセキュリティ ポリシーを構成するうえで必要な手順について詳しく説明します。 


## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 そのポリシーを Security Center で編集するか、[Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) を使って新しいポリシー定義を作成したり、管理グループ (組織全体のこともあれば、その中の一部署などのこともあります) にポリシーを割り当てたりすることによって、ポリシーのコンプライアンスを監視することができます。

> [!NOTE]
> Azure Policy は現在、制限付きのプレビュー段階です。 参加するには、[こちら](https://aka.ms/getpolicy)をクリックしてください。 Azure ポリシーの詳細については、「[Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (コンプライアンス強化のためのポリシーの作成と管理)」を参照してください。

## <a name="edit-security-policies"></a>セキュリティ ポリシーの編集
Security Center では、各 Azure サブスクリプションの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、そのサブスクリプションまたはそれが含まれる管理グループの所有者、共同作成者、セキュリティ管理者のいずれかである必要があります。 Azure Portal にサインインし、次の手順に従って Security Center でセキュリティ ポリシーを表示します。

1. **Security Center** ダッシュボードの **[全般]** で、**[セキュリティ ポリシー]** をクリックします。
2. セキュリティ ポリシーを有効にするサブスクリプションを選択します。

    ![ポリシー管理](./media/security-center-policies/security-center-policies-fig10.png)

3. **[ポリシー コンポーネント]** セクションで、**[セキュリティ ポリシー]** をクリックします。

    ![ポリシー コンポーネント](./media/security-center-policies/security-center-policies-fig12.png)

4. これが、Azure Policy によって Security Center に割り当てられる既定のポリシーです。 **[POLICIES AND PARAMETERS]\(ポリシーとパラメーター\)** にある項目を削除したり、**[利用可能なオプション]** にある他のポリシー定義を追加したりできます。 そのためには、定義の名前の横にあるプラス記号をクリックします。

    ![ポリシーの定義](./media/security-center-policies/security-center-policies-fig11.png)

5. ポリシーに関して詳しい説明が必要な場合には、ポリシーをクリックすると別のページが開き、その詳細と[ポリシー定義](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure)の構造を備えた JSON コードが表示されます。

    ![json](./media/security-center-policies/security-center-policies-fig14.png)

6. 編集が終了したら、**[保存]** をクリックします。


## <a name="available-security-policy-definitions"></a>利用可能なセキュリティ ポリシーの定義

既定のセキュリティ ポリシーで利用可能なポリシーの定義については、次の表を参照してください。 

| [ポリシー] | 状態がオンの場合 |
| --- | --- |
| システムの更新プログラム |Windows Update または Windows Server Update Services から、利用できる毎日のセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 取得される一覧は、その仮想マシンで構成されているサービスによって異なります。この一覧によって、不足している更新プログラムを適用するよう推奨されます。 Linux システムの場合、ポリシーはディストリビューション提供のパッケージ管理システムを利用し、使用できる更新プログラムが含まれているパッケージを特定します。 また、[Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md) 仮想マシンのセキュリティ更新プログラムと重要な更新プログラムについても確認します。 |
| OS の脆弱性 |オペレーティング システム構成を毎日分析し、攻撃に対する仮想マシンの脆弱性を引き起こすおそれのある問題を特定します。 また、このポリシーによって、これらの脆弱性に対応するための構成の変更が推奨されます。 監視対象のこの特定の構成に関する詳細は、 [推奨される基準の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) を参照してください。 (現時点では、Windows Server 2016 は完全にはサポートされていません。) |
| エンドポイント保護 |ウイルス、スパイウェア、およびその他の悪意のあるソフトウェアを特定して削除するため、すべての Windows 仮想マシンにエンドポイント保護をプロビジョニングすることをお勧めします。 |
| ディスクの暗号化 |静止データの保護を強化するために、ディスク暗号化はすべての仮想マシンで有効にすることをお勧めします。 |
| ネットワーク セキュリティ グループ |パブリック エンドポイントがある VM への受信トラフィックと送信トラフィックを制御するように、 [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) を構成することを推奨します。 サブネットで構成されたネットワーク セキュリティ グループは、他に指定しない限り、すべての仮想マシンのネットワーク インターフェイスによって継承されます。 このポリシーは、ネットワーク セキュリティ グループが構成されていることをチェックするほか、受信セキュリティ規則を評価して、受信トラフィックを許可する規則を特定します。 |
| Web アプリケーション ファイアウォール |次のいずれかにあてはまる場合に、Web アプリケーション ファイアウォールを仮想マシンにプロビジョニングすることを推奨します。 </br></br>[インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) が使用されていて、関連するネットワーク セキュリティ グループの受信セキュリティ規則が、ポート 80/443 にアクセスできるように構成されている。</br></br>負荷分散された IP が使用されていて、関連する負荷分散規則と受信ネットワーク アドレス変換 (NAT) 規則が、ポート 80/443 にアクセスできるように構成されている  (詳細については、[Azure Resource Manager によるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)に関するページを参照してください)。 |
| 次世代のファイアウォール |Azure に組み込まれているネットワーク セキュリティ グループの適用範囲外までネットワーク保護を拡張します。 次世代ファイアウォールの利用が推奨されるデプロイが Security Center によって検出されると、仮想アプライアンスをプロビジョニングできるようになります。 |
| SQL 監査と脅威検出 |コンプライアンスのために、また高度な脅威検出の調査のために、Azure データベースへのアクセスの監査を有効にすることを推奨します。 |
| SQL の暗号化 |Azure SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに対し、REST での暗号化を有効にすることを推奨します。 セキュリティ侵害を受けた場合でも、データが読み取られることはありません。 |
| 脆弱性評価 |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| ストレージ暗号化 |現在この機能は、Azure BLOB と Azure Files で利用できます。 [Storage サービスの暗号化] を有効にした後、暗号化されるのは新しいデータのみであり、このストレージ アカウントにある既存のファイルは暗号化されません。 |
| JIT ネットワーク アクセス |ジャスト イン タイムが有効になっている場合、Security Center では NSG ルールの作成により Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックをロックダウンする必要があるポートを選択します。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」を参照してください。 |


## <a name="next-step"></a>次のステップ
このドキュメントでは、セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)。 Azure Security Center を導入するための設計上の考慮事項を計画し、理解する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)。 セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
