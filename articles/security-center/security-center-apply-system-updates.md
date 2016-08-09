<properties
   pageTitle="Azure Security Center でシステムの更新プログラムを適用する | Microsoft Azure"
   description="このドキュメントでは、Azure Security Center の推奨事項である ";**システムの更新プログラムを適用する**"; と ";**システムの更新後に再起動する**"; を実装する方法について説明します。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Azure Security Center でシステムの更新プログラムを適用する

Azure Security Center では、オペレーティング システムに不足している更新プログラムがないかどうかを確認するために、Windows および Linux 仮想マシン (VM) の監視が毎日行われています。Security Center は、Windows VM に構成されているサービスに応じて、Windows Update または Windows Server Update Services (WSUS) から利用可能なセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。また、Linux システムにおける最新の更新プログラムについても確認します。VM でシステムの更新プログラムが不足している場合は、それらを適用するよう Security Center による推奨が行われます。

> [AZURE.NOTE] このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[システムの更新プログラムを適用する]** を選択します。![システムの更新の適用][1]

2. **[システムの更新プログラムを適用する]** ブレードが開き、システムの更新が行われていない VM の一覧が表示されます。VM を選択します。![Select a VM][2]

3. ブレードが開き、選択した VM に不足している更新プログラムの一覧が表示されます。システムの更新プログラムを選択します。この例では、KB3156016 を選択します。![Missing security updates][3]

4. **セキュリティ更新プログラム**のブレードの手順に従い、不足している更新プログラムを適用します。![Security update][4]

## システムの更新後に再起動する

5. **[推奨事項]** ブレードに戻ります。システムの更新プログラムを適用した後は、**[システムの更新後に再起動する]** という新しいエントリが生成されています。このエントリは、システムの更新プログラムの適用プロセスを完了するために、VM の再起動が必要であることを示しています。![Reboot after system updates][5]

6. **[システムの更新後に再起動する]** を選択します。これにより、**[システムの更新を完了するために、再起動は保留中です]** ブレードが開き、システムの更新プログラムの適用プロセスを完了するために再起動が必要な VM の一覧が表示されます。![Restart pending][6]

Azure から VM を再起動して、プロセスを完了します。

## 関連項目

セキュリティ センターの詳細については、次を参照してください。

- [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- [Azure Security Center でのセキュリティ正常性の監視](security-center-monitoring.md) -- Azure リソースの正常性を監視する方法について説明しています。
- [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) - セキュリティの警告の管理と対応の方法について説明しています。
- [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- [Azure Security Center に関する FAQ](security-center-faq.md) -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]: ./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

<!---HONumber=AcomDC_0727_2016-->