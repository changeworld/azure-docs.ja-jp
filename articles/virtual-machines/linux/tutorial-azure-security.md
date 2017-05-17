---
title: "Azure Security Center を使って Linux VM のセキュリティを確保する | Microsoft Docs"
description: "チュートリアル - Azure Security Center を使って VM のセキュリティを確保する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4c680ee63e1c2d8e858c725adc42bbcbc49e4045
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Azure Security Center を使って VM のセキュリティを監視する

Azure Security Center は、Azure リソースの構成をセキュリティ面から詳しく確認できるサービスです。 また、セキュリティの統合監視機能により、他の手段では見落としてしまうような脅威を検出することもできます。 このチュートリアルでは、Azure Security Center の概要を簡単に紹介したうえで、Azure 仮想マシンに対して使用する方法を説明します。   

## <a name="security-center-overview"></a>Security Center の概要

Azure Security Center は、VM の構成で問題となりうる箇所や、VM を標的としたセキュリティ面の脅威の危険性を特定できるサービスです。 具体的には、ネットワーク セキュリティ グループが関連付けられていない VM、暗号化されていないディスクが存在する VM、RDP 経由でブルート フォース攻撃を受けている VM などを特定できます。 このような情報は、Azure Security Center のダッシュボードに、グラフを使って見やすく表示されます。

Azure Security Center のダッシュボードにアクセスするには、Azure Portal の左側のナビゲーション ウィンドウで **[Security Center]** をクリックします。 ダッシュボードには、リソースの正常性、セキュリティ アラート、構成の推奨事項の概要が表示されます。 このダッシュボードでは、Azure 環境のセキュリティの正常性、現在の推奨事項の数、脅威アラートの現在の状態を確認できます。 概要を示したグラフはそれぞれ展開することができます。展開すると、その分野に関する詳しい情報が表示されます。

![ASC ダッシュボード](./media/tutorial-azure-security/asc-dash.png)

Azure Security Center は、単にデータを探索するだけにとどまらず、検出した問題について推奨事項を提示します。 たとえば、ネットワーク セキュリティ グループが関連付けられていない状態で VM をデプロイした場合には、その問題を修復するための手順も含めた推奨事項が作成されます。 提示される推奨事項ではほかにも、Azure Security Center を離れることなく自動で修復を実行する機能が用意されています。  

![推奨事項](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>データ収集を構成する

VM のセキュリティ構成を表示するためには、まず Azure Security Center のデータ収集を構成する必要があります。 具体的には、データ収集を有効にするほか、収集したデータを保持するための Azure ストレージ アカウントを作成する必要があります。 

1. Azure Security Center のダッシュボードで、**[セキュリティ ポリシー]** をクリックして、サブスクリプションを選択します。 
2. **[データ収集]** で *[オン]* を選択します。
3. **[ストレージ アカウントの選択]** をクリックして、新しいストレージ アカウントを作成します。 完了したら、 **[OK]** を選択します。
4. **[セキュリティ ポリシー]** ブレードで、**[保存]** をクリックします。 

この操作が完了すると、すべての仮想マシンに Azure Security Center データ収集エージェントがインストールされ、データの収集が始まります。 

## <a name="configure-security-policy"></a>セキュリティ ポリシーを構成する

セキュリティ ポリシーとは、セキュリティ ポリシーの項目を定義したものを指します。このポリシーで定義した項目についてデータが収集され、推奨事項が提示されます。 既定では、ポリシーの項目すべてについて Azure リソースが評価されます。 ポリシーの個々の項目は、全 Azure リソースを対象にグローバルに無効にすることも、リソース グループ単位で無効にすることもできます。 この構成を使えば、さまざまな Azure リソースに対して別々のセキュリティ ポリシーを適用できます。 Azure Security Center のセキュリティ ポリシーに関する詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](../../security-center/security-center-policies.md)」を参照してください。 

全 Azure リソースにセキュリティ ポリシー を構成する方法は、次のとおりです。

1. Azure Security Center のダッシュボードで、**[セキュリティ ポリシー]** をクリックして、サブスクリプションを選択します。 
2. **[Prevention policy (防止ポリシー)]** をクリックします。
3. Azure リソースすべてに適用が必要なポリシー項目を有効または無効にします。
4. 完了したら、**[OK]** をクリックします。
5. **[セキュリティ ポリシー]** ブレードで、**[保存]** をクリックします。 

特定のリソース グループを対象にポリシーを構成する場合も、基本的な手順は同じです。ただし、[セキュリティ ポリシー] ブレードではサブスクリプションではなく、リソース グループを選択してください。 ポリシーを構成するときには、**[継承]** で *[固有]* を選択します。 特定のリソース グループについてデータ収集を無効にする場合には、その構成もここで行なえます。

以下の例では、*myResoureGroup* という名前のリソース グループを対象に固有のポリシーを作成しています。 このポリシーでは、ディスクの暗号化と Web アプリケーション ファイアウォールに関する推奨事項の提示の 2 つがどちらも無効になっています。

![固有のポリシー](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>VM の構成の正常性を表示する

データの収集を有効にしてセキュリティ ポリシーを構成すると、Azure Security Center がアラートの生成と推奨事項の提示を開始します。 VM がデプロイされ、データ収集エージェントがインストールされるたびに、Azure Security Center に新しい VM のデータが収集されていきます。 VM の構成の正常性に関する詳細については、「[Azure Security Center での仮想マシンの保護](../../security-center/security-center-virtual-machine-recommendations.md)」を参照してください。 

データが収集されると、各 VM および関連する Azure リソースのリソース正常性が集計され、見やすいグラフの形で表示されます。 リソースの正常性を確認するには、Azure Security Center のダッシュボードに戻ります。 **[Resource security health (リソースのセキュリティの正常性)]** の **[コンピューティング]** をクリックします。 最後に、**[コンピューティング]** ブレードで **[仮想マシン]** をクリックします。 このビューでは、VM すべてについて構成の状態の概要を確認できます。

![コンピューティングの正常性](./media/tutorial-azure-security/compute-health.png)

各 VM を選択すると、その VM に関する推奨事項がすべて表示されます。 推奨事項については、このチュートリアルの次のセクションで詳しく説明します。

## <a name="remediate-configuration-issues"></a>構成の問題を修復する

Azure Security Center が構成データの収集を開始した後は、構成されているセキュリティ ポリシーに基づいて推奨事項が提示されます。 たとえば、ネットワーク セキュリティ グループが関連付けられていない状態で VM を構成していた場合には、ネットワーク セキュリティ グループの作成を促す内容の推奨事項が提示されます。 推奨事項の一覧を表示する方法は、次のとおりです。 

1. Azure Security Center のダッシュボードで **[推奨事項]** をクリックします。
3. 特定の推奨事項を選択すると、その推奨事項が適用されるリソースの一覧を示したブレードが表示されます。
4. 問題を修復するリソースを選択します。
5. 問題の修復手順については、画面に表示されている指示に従います。 

Azure Security Center では多くの場合、Azure Security Center を離れることなくその場で問題を修復するための手順が表示されます。 たとえば、次の例では、受信規則に一切制限がない NSG が検出されています。 この推奨事項では、**[編集]** ボタンを選択できます。このボタンを押すと、問題となっている規則を変更するうえで必要な UI が表示されます。 

![推奨事項](./media/tutorial-azure-security/remediation.png)

推奨事項の内容に従って問題を修復すると、解決済みとしてマークされます。 

## <a name="view-detected-threats"></a>検出された脅威を表示する

Azure Security Center には、リソースの構成に関する推奨事項を提示するだけでなく、脅威の検出に関するアラートを表示する機能が備わっています。 このセキュリティ アラート機能は、各 VM、Azure のネットワーク ログ、および接続されているパートナー ソリューションから収集されるデータを集計し、Azure リソースに対するセキュリティ面の脅威を検出するものです。 Azure Security Center の脅威検出機能に関する詳細については、「[Azure Security Center の検出機能](../../security-center/security-center-detection-capabilities.md)」を参照してください。

セキュリティ アラート機能を使用するには、Azure Security Center の価格レベルを *Free* から *Standard* に変更する必要があります。 価格レベルを Standard に変更した場合、30 日間は**無料で試用**できます。 価格レベルを変更する方法は、次のとおりです。  

1. Azure Security Center のダッシュボードで、**[セキュリティ ポリシー]** をクリックして、サブスクリプションを選択します。
2. **[価格レベル]** をクリックします。
3. 新しいレベルを選択し、**[選択]** をクリックします。
5. **[セキュリティ ポリシー]** ブレードで、**[保存]** をクリックします。 

この機能を有効にすると、セキュリティ面の脅威が検出されるたびにセキュリティ アラートのグラフにデータが反映されるようになります。

![セキュリティ アラート](./media/tutorial-azure-security/security-alerts.png)

アラートを選択すると、脅威の詳細、検出時点、脅威の試行回数、推奨される修復方法などの情報が表示されます。 この例では、RDP 経由のブルート フォース攻撃が検出されており、試行に 294 回失敗したことがわかるほか、推奨される解決方法が表示されています。

![RDP 攻撃](./media/tutorial-azure-security/rdp-attack.png)
