<properties
   pageTitle="Linux 仮想マシン (VM) への SAP NetWeaver の導入 – デプロイ ガイド | Microsoft Azure"
   description="Linux 仮想マシン (VM) 上の SAP NetWeaver – デプロイ ガイド"
   services="virtual-machines-linux,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="sedusch"/>

# Azure Virtual Machines (VM) への SAP NetWeaver の導入 – デプロイ ガイド

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "Linux 仮想マシン (VM) 上の SAP NetWeaver – DBMS デプロイ ガイド"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "VM と VHD のキャッシング"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "ソフトウェア RAID"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure ストレージ"
[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "RDBMS デプロイの構造"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Azure VM の高可用性と障害復旧"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 以降"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 以前のリリース"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Microsoft Azure Marketplace からの SQL Server イメージの使用"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Azure での 一般的な SAP 用 SQL Server の概要"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "SQL Server RDBMS の詳細"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "ストレージの構成"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "バックアップと復元"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "バックアップと復元のパフォーマンスに関する考慮事項"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "その他"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-linux-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-linux-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-linux-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-linux-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-linux-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-linux-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-linux-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-linux-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-linux-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "Linux 仮想マシン (VM) 上の SAP NetWeaver – デプロイ ガイド"
[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "SAP リソース"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "カスタム イメージを使用して VM をデプロイする"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Azure PowerShell コマンドレットのデプロイ"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "SAP 関連の PowerShell コマンドレットのダウンロードとインポート"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "オンプレミス ドメインへの VM の参加 (Windows のみ)"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Azure VM エージェントのダウンロード、インストール、有効化"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Azure Enhanced Monitoring Extension for SAP の構成"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Azure Enhanced Monitoring for SAP の適合性チェック"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Azure 監視インフラストラクチャ構成のヘルス チェック"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "SAP 用 Azure 監視インフラストラクチャのトラブルシューティング"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "監視の構成"
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "プロキシの構成"
[deployment-guide-figure-100]: ./media/virtual-machines-linux-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-linux-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-linux-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-linux-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-linux-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-linux-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-linux-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-linux-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-linux-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-linux-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-linux-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-linux-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-linux-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-linux-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-linux-sap-get-started.md
[getting-started-dbms]: virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-linux-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-linux-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "Linux 仮想マシン (VM) 上の SAP NetWeaver – 計画および実装ガイド"
[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "リソース"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "SAP アプリケーション サーバーの高可用性"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "SAP インスタンスでの自動開始の使用"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "クラウドのみ - オンプレミスの顧客ネットワークとの依存関係を持たない仮想マシンの Azure へのデプロイ"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "クロス プレミス - オンプレミス ネットワークに完全に統合されることを要件とする 1 つまたは複数の SAP VM の Azure へのデプロイ"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Azure リージョン"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "障害ドメイン"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "アップグレード ドメイン"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Azure の可用性セット"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Microsoft Azure 仮想マシンの概念"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure Premium Storage"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "顧客固有のイメージを使用する VM のデプロイ"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "SAP 用の顧客固有のイメージを使用する VM のデプロイの準備"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Azure 用の VM と SAP の準備"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Azure ディスクと Azure イメージの違い"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "オンプレミスから Azure への VHD のアップロード"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Azure Storage アカウント間でのディスクのコピー"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "SAP デプロイ用の VM/VHD 構造"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "アタッチされたディスクに対する自動マウントの設定"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "単一の VM と SAP NetWeaver のデモ/トレーニング シナリオ"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "SAP インスタンスのクラウド専用のデプロイの概念"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Azure Monitoring Solution for SAP"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure Premium Storage"

[planning-guide-figure-100]: ./media/virtual-machines-linux-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-linux-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-linux-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-linux-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-linux-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-linux-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-linux-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-linux-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-linux-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-linux-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-linux-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-linux-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-linux-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-linux-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-linux-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-linux-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-linux-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-linux-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-linux-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-linux-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-linux-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-linux-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-linux-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Microsoft Azure のネットワーク"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "ストレージ: Microsoft Azure Storage とデータ ディスク"

[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "SAP 製品の可用性マトリックス"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-cross-premises-options.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイ モデル。

Microsoft Azure を使用すると、企業は時間のかかる調達サイクルを省略し、最短時間でコンピューティングとストレージのリソースを取得することができます。Azure Virtual Machines により、企業は SAP NetWeaver ベースのアプリケーションのような従来のアプリケーションを Azure にデプロイし、追加のオンプレミスのリソースを必要とせずに信頼性と可用性を高めることができます。また、Microsoft Azure ではクロス プレミス接続もサポートしており、これにより企業は Azure Virtual Machines を自社のオンプレミス ドメイン、プライベート クラウド、および SAP システム ランドス ケープに積極的に統合できます。

このホワイト ペーパーでは、SAP NetWeaver ベースのアプリケーションのデプロイ用の Azure 仮想マシンを準備する手順について説明します。[計画および実装ガイド][planning-guide]に記載されている情報を理解していることを前提とします。理解していない場合は、まずその個別ドキュメントを参照してください。

特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Notes を補足する内容となっています。

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## はじめに
世界中の多くの企業が、最も著名な SAP Business Suite、SAP NetWeaver をベースとするアプリケーションを使用して、ミッション クリティカルなビジネス プロセスを実行しています。したがって、システム正常性は重要な資産であり、パフォーマンスの問題などの誤作動が発生した場合に企業サポートを提供できることが重要な要件になります。Microsoft Azure では、すべてのビジネス クリティカルなアプリケーションのサポート要件に対応する優れたプラットフォームの実装を提供します。このガイドは、Azure Marketplace から取得したか、または顧客固有のイメージを使用しているかなどの仮想マシンの作成方法を問わずに企業サポートを提供できるように、SAP ソフトウェアのデプロイを対象とした Microsoft Azure 仮想マシンを構成できるようにします。次に、必要なすべての設定手順について詳しく説明します。

## 前提条件とリソース
### 前提条件
開始する前に、次の章で説明されている前提条件が満たされていることを確認してください。

#### ローカル パーソナル コンピューター
SAP ソフトウェアのデプロイ用の Azure の仮想マシンの設定は、いくつかの手順から構成されます。Windows VM または Linux VM を管理するには、PowerShell スクリプトと Microsoft Azure ポータルを使用する必要があります。そのため、Windows 7 以降を実行しているローカル パーソナル コンピューターが必要です。Linux VM を管理し、このタスク用でのみ Linux コンピューターを使用する場合は、Azure コマンド ライン インターフェイス (Azure CLI) を使用することもできます。

#### インターネット接続
必要なツールとスクリプトをダウンロードして実行するには、インターネット接続が必要です。また、Azure Enhanced Monitoring Extension を実行している Microsoft Azure 仮想マシンには、インターネットへのアクセスが必要です。この Azure VM が Azure 仮想ネットワークまたはオンプレミス ドメインの一部である場合、[「プロキシの構成」][deployment-guide-configure-proxy]の章の説明に従って、関連するプロキシ設定を行います。

#### Microsoft Azure サブスクリプション
Azure アカウントが既に存在しており、対応するログオン資格情報が認識されています。

#### トポロジに関する考慮事項とネットワーク
Azure における SAP デプロイのトポロジとアーキテクチャを定義する必要があります。関連するアーキテクチャは次のとおりです。

* 使用する Microsoft Azure Storage アカウント
* SAP システムをデプロイする仮想ネットワーク
* SAP システムをデプロイするリソース グループ
* SAP システムをデプロイする Azure リージョン
* SAP 構成 (2 層または 3 層)
* VM のサイズ、VM にマウントする追加の VHD の数
* SAP Transport and Correction システムの構成

Azure Storage アカウントまたは Azure 仮想ネットワークを別個に作成および構成しておく必要があります。作成および構成の方法は、 [計画および実装ガイド][planning-guide]で説明されています。

#### SAP のサイズ設定
* SAP Quicksizer を使用するなどして、予想される SAP ワークロードを決定し、SAPS 番号を把握します。
* SAP システムの必要な CPU リソースとメモリ使用量を把握する必要があります。
* 1 秒あたりの必要な I/O 操作を把握する必要があります。
* Azure での異なる VM 間で結果として生じる通信で必要なネットワーク帯域幅を把握します。
* オンプレミス資産と、Azure によってデプロイされた SAP システムの間で必要なネットワーク帯域幅を把握します。

#### リソース グループ
リソース グループは、同じライフ サイクル (同時に作成と削除が行われるなど) のすべてのリソースを含むという新しい概念です。リソース グループの詳細については、[この記事][resource-group-overview]をご覧ください。

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP リソース
構成作業では、次のリソースが必要です。

* 次の情報が記載されている SAP ノート [1928533]
	* SAP ソフトウェアのデプロイでサポートされる Azure 仮想マシン サイズの一覧
	* Azure 仮想マシン サイズごとの容量に関する重要な情報
	* サポートされる SAP ソフトウェアと OS および DB の組み合わせ
* Microsoft Azure に SAP ソフトウェアをデプロイする場合に SAP でサポートされる前提条件の一覧が記載されている SAP ノート [2015553]
* Enhanced Azure Monitoring for SAP に関するその他のトラブルシューティング情報が記載されている SAP ノート [1999351]
* Microsoft Azure 上の SAP で利用できるすべての監視メトリックに関する詳細情報が記載されている SAP ノート [2178632]
* 新しい Azure Resource Manager でデプロイする場合の、Microsoft Azure 上の Windows 用 SAP Host Agent の必要なバージョンが記載されている SAP Note [1409604]
* 新しい Azure Resource Manager でデプロイする場合の、Microsoft Azure 上の Linux 用 SAP Host Agent の必要なバージョンが記載されている SAP Note [2191498]
* Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されている SAP ノート [2243692]
* SUSE LINUX Enterprise Server 12 に関する一般情報が記載されている SAP ノート [1984787]
* Red Hat Enterprise Linux 7.x に関する一般情報が記載されている SAP ノート [2002167]
* Linux 用のすべての必要な SAP ノートが記載されている [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)
* [Azure PowerShell][azure-ps] の一部である SAP 固有の PowerShell コマンドレット
* [Azure CLI][azure-cli] の一部である SAP 固有の Azure CLI
* [Microsoft Azure ポータル:][azure-portal]

[コメント]: <> (MSSedusch TODO SAP ノート 1409604 の SAP Host Agent の ARM パッチ レベルを追加してください)
 
次のガイドでは、Microsoft Azure 上の SAP のトピックについて説明します。

* [Azure Virtual Machines (VM) への SAP NetWeaver の導入 – 計画および実装ガイド][planning-guide]
* [Azure Virtual Machines (VM) への SAP NetWeaver の導入 – デプロイ ガイド (本書)][deployment-guide]
* [Azure Virtual Machines (VM) への SAP NetWeaver の導入 – DBMS デプロイ ガイド][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ
この章では、さまざまなデプロイ方法と、各デプロイ タイプの手順を学習します。

### SAP 用 VM をデプロイする
Microsoft Azure では、VM および関連付けられているディスクをデプロイする方法が複数用意されています。したがって、VM の準備はデプロイの方法によって異なる場合があるため、その違いを理解しておくことが非常に重要です。通常は、次のシナリオを検討します。

#### Azure Marketplace から VM をデプロイする
Microsoft またはサード パーティが提供するイメージを Azure Marketplace から取得して VM をデプロイします。Microsoft Azure で VM をデプロイした後は、オンプレミス環境の場合と同じガイドラインおよびツールに従って VM 内に SAP ソフトウェアをインストールします。Azure VM 内に SAP ソフトウェアをインストールする場合、SAP と Microsoft は、SAP インストール メディアを Azure VHD にアップロードして保存するか、すべての必要な SAP インストール メディアを格納した「ファイル サーバー」として機能する Azure VM を作成することをお勧めします。

[コメント]: <> (MSSedusch TODO ファイル サーバーまたは VHD などのファイル管理を推奨する必要があるのはなぜですか? オンプレミスとそれほど異なりますか?)

詳細については、[「シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする」][deployment-guide-3.2]の章を参照してください。

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>カスタム イメージを使用して VM をデプロイする
OS または DBMS のバージョンに関連する固有のパッチ要件により、Azure Marketplace で提供されるイメージは、ニーズに適さない場合があります。そのため、後で繰り返しデプロイできる、独自の「プライベート」OS/DB の VM イメージを使用して VM を作成しなければならない場合があります。プライベート イメージを作成する手順は、Windows イメージと Linux イメージによって異なります。

___

> ![Windows][Logo_Windows] Windows
>
> 複数の仮想マシンをデプロイするために使用できる Windows イメージを準備するには、Windows 設定 (Windows SID やホスト名など) をオンプレミス VM で抽象化/汎用化する必要があります。これを行うには、<https://technet.microsoft.com/library/cc721940.aspx> で説明されている sysprep を使用 します。
>
> ![Linux][Logo_Linux] Linux
>
> 複数の仮想マシンをデプロイするために使用できる Linux イメージを準備するには、一部の Linux 設定をオンプレミス VM で抽象化/汎用化する必要があります。これを行うには、[この記事][virtual-machines-linux-capture-image]または[この記事][virtual-machines-linux-agent-user-guide-command-line-options]で説明されている waagent -deprovision を使用します。

___

SAP Software Provision Manager を使用して新しい SAP システムをインストールし、仮想マシンに関連付けられている VHS からデータベース バックアップを復元するか、DBMS でサポートされている場合は Azure Storage からデータベース バックアップを直接復元してデータベースのコンテンツを設定できます ([DBMS デプロイメント ガイド][dbms-guide]を参照してください)。使用しているオンプレミス VM (特に 2 層システム) に SAP システムを既にインストールしている場合、SAP Software Provisioning Manager でサポートされているシステム名の変更手順に従って、Azure VM のデプロイ後に SAP システムの設定を調整できます (SAP ノート [1619720])。インストールしていない場合は、Azure VM のデプロイ後に SAP ソフトウェアをインストールできます。

詳細については、「[シナリオ 2: SAP 用カスタム イメージを使用して VM をデプロイする][deployment-guide-3.3]」の章を参照してください。

#### 汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する
オンプレミスから Microsoft Azure に特定の SAP システムを移動することを計画します。これは、OS、SAP バイナリ、最終的な DBMS バイナリを格納している VHD と、Microsoft Azure への DBMS のデータおよびログ ファイルを格納している VHD をアップロードすることで行うことができます。「[カスタム イメージを使用して VM をデプロイする][deployment-guide-3.1.2]」の章で説明したシナリオとは対照的に、ホスト名、SAP SID、および SAP ユーザー アカウントがオンプレミス環境で構成されているため、Azure VM でそれらを保持します。そのため、オペレーティング システムを汎用化する必要はありません。これは、オンプレミスで実行される SAP ランドス ケープと Microsoft Azure で実行される SAP ランドスケープが存在するクロス プレミスのシナリオではほとんどの場合適用されます。

詳細については、「[シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する][deployment-guide-3.4]」の章を参照してください。

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする
Microsoft Azure では、Windows Server とさまざまな Linux ディストリビューションの標準的な OS イメージを提供する Azure Marketplace から VM インスタンスをデプロイすることができます。SQL Server などの DBMS SKU を含むイメージもデプロイできます。DMBS SKU を含むイメージの詳細については、[DBMS デプロイメント ガイド][dbms-guide]を参照してください。

Azure Marketplace から VM をデプロイするための SAP 固有の手順は次のようになります。

![Azure Marketplace の VM イメージを使用した SAP システムの VM デプロイのフローチャート][deployment-guide-figure-100]

フローチャートに従って、次の手順を実行する必要があります。

#### Azure ポータルを使用した仮想マシンの作成
Azure Marketplace のイメージを使用して新しい仮想マシンを作成する最も簡単な方法は、Azure ポータルを使用することです。<https://portal.azure.com/#create> に移動します。デプロイするオペレーティング システムの種類 (Windows、SUSE、RHEL など) を検索フィールド入力し、バージョンを選択します。デプロイメント モデルとして「Azure Resource Manager」を選択し、[作成] クリックします。

ウィザードでは、仮想マシンの作成で必要なパラメーターと、ネットワーク インターフェイスやストレージ アカウントなどのすべての必要なリソースが案内されます。次のようなパラメーターがあります。

1. 基本
    1. 名前: リソースの名前 (仮想マシン名など)。
    1. ユーザー名とパスワード、SSH 公開キー: プロビジョニング時に作成されるユーザーのユーザー名とパスワードを入力します。Linux 仮想マシンの場合、SSH を使用してコンピューターにログインする場合に使用する SSH 公開キーも入力します。
    1. サブスクリプション: 新しい仮想マシンのプロビジョニングに使用するサブスクリプションを選択します。
    1. リソース グループ: リソース グループの名前です。新しいグループまたは既に存在するリソース グループの名前を挿入できます。
    1. 場所: 新しい仮想マシンをデプロイする場所を選択します。仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を選択してください。詳細については、[計画ガイド][planning-guide]の「[Microsoft Azure ネットワーク][planning-guide-microsoft-azure-networking]」の章を参照してください。
1. サイズ: サポートされている VM の種類の一覧については、SAP ノート [1928533] を参照してください。Premium Storage を使用する場合は、適切な種類を選択していることを確認してください。Premium Storage ではすべての VM の種類はサポートしていません。詳細については、[計画ガイド][planning-guide]の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」と「[Azure Premium Storage][planning-guide-azure-premium-storage]」を参照してください。
1. [設定]
    1. ストレージ アカウント: 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。さまざまなストレージ タイプの詳細については、[DBMS ガイド][dbms-guide]の「[Microsoft Azure Storage][dbms-guide-2.3]」の章を参照してください。すべてのストレージ タイプが SAP アプリケーションの実行でサポートされるわけではない点に注意してください。
    1. 仮想ネットワークとサブネット: 仮想マシンをイントラネットに統合する場合、オンプレミス ネットワークに接続する仮想マシンを選択します。
    1. パブリック IP アドレス: 使用するパブリック IP アドレスを選択するか、新しいパブリック IP アドレスを作成するためのパラメーターを入力します。パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスすることができます。仮想マシンへのアクセスをフィルター処理するには、ネットワーク セキュリティ グループも作成してください。
    1. ネットワーク セキュリティ グループ: 詳細については、「[ネットワーク セキュリティ グループ (NSG) について][virtual-networks-nsg]」を参照してください。
    1. 監視: 診断設定を無効にできます。「[監視の構成][deployment-guide-configure-monitoring-scenario-1]」の章の説明に従って、Azure Enhanced Monitoring を有効にするコマンドを実行すると、監視が自動的に有効になります。
    1. 可用性: 可用性セットを選択するか、新しい可用性セットを作成するパラメーターを入力します。詳細については、「[Azure 可用性セット][planning-guide-3.2.3]」の章を参照してください。
1. 概要: [概要] ページに表示される情報を検証し、[OK] をクリックします。

ウィザードを完了すると、選択したリソース グループに仮想マシンがデプロイされます。

#### テンプレートを使用した仮想マシンの作成
[azure-quickstart-templates github repository][azure-quickstart-templates-github] で公開されている SAP テンプレートを使用してデプロイメントを作成することもできます。また、[Azure ポータル][virtual-machines-windows-tutorial]、[PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]、[Azure CLI][virtual-machines-linux-tutorial] を使用して手動で仮想マシンを作成することもできます。

* 1 台のみの仮想マシンを使用して 2 層システムを作成する場合、[2 層構成 (仮想マシン 1 台のみ) のテンプレート][sap-templates-2-tier-marketplace-image]を使用します。
* 複数の仮想マシンを使用して 3 層システムを作成する場合、[3 層構成 (仮想マシン 1 台のみ) のテンプレート][sap-templates-3-tier-marketplace-image]を使用します。

上記のテンプレートのいずれかを開くと、Azure ポータルの [パラメーターの編集] パネルが表示されます。次の情報を入力します。

* **sapSystemId**: SAP システム ID
* **osType**: Windows Server 2012 R2、SLES 12 または RHEL 7.2 などのデプロイするオペレーティング システム
    * 一覧には、Microsoft Azure 上の SAP によってサポートされているバージョンのみが表示されます。
* **sapSystemSize**: SAP システムのサイズ
    * 新しいシステムで提供する SAPS の量。システムで必要な SAPS の量が不明な場合、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
* **systemAvailability**: (3 層テンプレートのみ) システムの可用性
    * HA インストールに適した構成の HA を選択します。ASCS 用の2 つのデータベース サーバーと 2 つのサーバーが作成されます。
* storageType: (2 層テンプレートのみ) 使用するストレージの種類
    * 大規模なシステムの場合、Premium Storage の使用を強くお勧めします。さまざまなストレージの種類の詳細については、
        * [DBMS ガイド][dbms-guide]の [Microsoft Azure Storage][dbms-guide-2.3] を参照してください。
        * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
        * [Microsoft Azure Storage の概要][storage-introduction]
* **adminUsername** と **adminPassword**: ユーザー名とパスワード
    * コンピューターへのログオンで使用できる新しいユーザーが作成されます。
* **newOrExistingSubnet**: 新しい仮想ネットワークまたはサブネットを作成するか、既存のサブネットを使用するかを決定します。オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、既存のものを選択します。
* **subnetId**: 仮想マシンを接続するサブネットの ID。仮想マシンをオンプレミス ネットワークに接続する VPN または Expressroute 仮想ネットワークのサブネットを選択します。通常、ID は /subscriptions/`<subscription id`>/resourcegroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> のようになります。

すべてのパラメーターを入力したら、サブスクリプションおよび使用するリソース グループを選択します。既存のリソース グループを選択するか、ドロップダウン メニューで [+ 新規] を選択して新しいリソース グループを作成することができます。新しいリソース グループを作成する場合は、リソース グループと仮想マシンを作成するリージョンも選択する必要があります。

法律条項を確認し、同意して [作成] をクリックします。

Azure Marketplace のイメージを使用する場合、既定では Azure VM エージェントがデプロイされることに注意してください。

#### プロキシ設定の構成
オンプレミス ネットワークの構成によっては、仮想マシンが VPN または Expressroute を介してオンプレミス ネットワークに接続している場合、仮想マシンでプロキシを構成しなければならない場合があります。構成しないと、仮想マシンがインターネットにアクセスできない場合があり、必要な拡張機能のダウンロードや監視データの収集ができません。このドキュメントの「[プロキシの構成][deployment-guide-configure-proxy]」の章を参照してください。

#### ドメインへの参加 (Windows のみ)
Azure でのデプロイメントが Azure サイト間または Expressroute を介してオンプレミスの AD または DNS に接続している場合 ([計画および実装ガイド][planning-guide]ではクロス プレミスとも呼ばれる)、オンプレミス ドメインに VM を参加させる必要があります。この手順に関する考慮事項については、このドキュメントの「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」を参照してください。

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>監視の構成
このドキュメントの「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の説明に従って、Azure Enhanced Monitoring Extension for SAP を構成します。

このドキュメントの「[SAP リソース][deployment-guide-2.2]」の章で示されているリソースで、SAP カーネルおよび SAP Host Agent の必要な最小バージョンについて SAP Monitoring の前提条件を確認してください。

#### 監視の確認
「[Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」で説明されているとおりに監視が機能しているかどうかを確認します。

#### デプロイ後の手順
VM を作成してデプロイした後は、すべての必要なソフトウェア コンポーネントを VM にインストールします。この種類の VM のデプロイでは、他の VM 内の Microsoft Azure で既に利用可能か、または接続可能なディスクとしてな利用可能なインストール対象のソフトウェアが必要です。または、オンプレミスの資産への接続 (共有のインストール) を指定するクロス プレミスのシナリオを検討します。

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする
[計画および実装ガイド][planning-guide]の詳細な手順で説明されているように、カスタム イメージを準備して作成し、そのイメージを使用して複数の新しい VM を作成することができます。フローチャートでの手順は次のようになります。
 
![Private Marketplace の VM イメージを使用した SAP システムの VM デプロイのフローチャート][deployment-guide-figure-300]

フローチャートに従って、次の手順を実行する必要があります。

#### 仮想マシンの作成
Azure ポータルからプライベート OS イメージを使用してデプロイメントを作成する場合、[azure-quickstart-templates github repository][azure-quickstart-templates-github] で公開されている SAP テンプレートのうちの 1 つを使用します。[PowerShell][virtual-machines-upload-image-windows-resource-manager] を使用して、手動で仮想マシンを作成することもできます。

* 1 台のみの仮想マシンと固有の OS イメージを使用して 2 層システムを作成する場合、[2 層構成 (仮想マシン 1 台のみ) のテンプレート][sap-templates-2-tier-user-image]を使用します。
* 複数の仮想マシンと固有の OS イメージを使用して 3 層システムを作成する場合、[3 層構成 (複数の仮想マシン) のテンプレート][sap-templates-3-tier-user-image]を使用します。

上記のテンプレートのいずれかを開くと、Azure ポータルの [パラメーターの編集] パネルが表示されます。次の情報を入力します。

* **sapSystemId**: SAP システム ID
* **osType**: デプロイするオペレーティング システムの種類 (Windows または Linux)
* **sapSystemSize**: SAP システムのサイズ
    * 新しいシステムで提供する SAPS の量。システムで必要な SAPS の量が不明な場合、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
* **systemAvailability**: (3 層テンプレートのみ) システムの可用性
    * HA インストールに適した構成の HA を選択します。ASCS 用の2 つのデータベース サーバーと 2 つのサーバーが作成されます。
* **storageType**: (2 層テンプレートのみ) 使用するストレージの種類
    * 大規模なシステムの場合、Premium Storage の使用を強くお勧めします。さまざまなストレージの種類の詳細については、
        * [DBMS ガイド][dbms-guide]の [Microsoft Azure Storage][dbms-guide-2.3] を参照してください。
        * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
        * [Microsoft Azure Storage の概要][storage-introduction]
* **adminUsername** と **adminPassword**: ユーザー名とパスワード
    * コンピューターへのログオンで使用できる新しいユーザーが作成されます。
* **userImageVhdUri**: プライベート OS イメージ VHD の URI (例: https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd)
* **userImageStorageAccount**: プライベート OS イメージを保存するストレージ アカウントの名前 (例: 上記の URI の例の `<accountname`>)
* **newOrExistingSubnet**: 新しい仮想ネットワークまたはサブネットを作成するか、既存のサブネットを使用するかを決定します。オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、既存のものを選択します。
* **subnetId**: 仮想マシンを接続するサブネットの ID。仮想マシンをオンプレミス ネットワークに接続する VPN または Expressroute 仮想ネットワークのサブネットを選択します。通常、ID は /subscriptions/`<subscription id`>/resourcegroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> のようになります。

すべてのパラメーターを入力したら、サブスクリプションおよび使用するリソース グループを選択します。既存のリソース グループを選択するか、ドロップダウン メニューで [+ 新規] を選択して新しいリソース グループを作成することができます。新しいリソース グループを作成する場合は、リソース グループと仮想マシンを作成するリージョンも選択する必要があります。

法律条項を確認し、同意して [作成] をクリックします。

#### VM エージェントのインストール (Linux のみ)
上記のテンプレートを使用する場合、Linux エージェントをユーザー イメージにインストールしておく必要があります。17 以下の場合、デプロイは失敗します。このドキュメントの「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の章の説明に従って、VM エージェントをユーザー イメージにダウンロードしてインストールします。上記のテンプレートを使用しない場合、後で VM エージェントをインストールすることもできます。

#### ドメインへの参加 (Windows のみ)
Azure でのデプロイメントが Azure サイト間または Expressroute を介してオンプレミスの AD または DNS に接続している場合 ([計画および実装ガイド][planning-guide]ではクロス プレミスとも呼ばれる)、オンプレミス ドメインに VM を参加させる必要があります。この手順に関する考慮事項については、このドキュメントの「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」を参照してください。

#### プロキシ設定の構成
オンプレミス ネットワークの構成によっては、仮想マシンが VPN または Expressroute を介してオンプレミス ネットワークに接続している場合、仮想マシンでプロキシを構成しなければならない場合があります。構成しないと、仮想マシンがインターネットにアクセスできない場合があり、必要な拡張機能のダウンロードや監視データの収集ができません。このドキュメントの「[プロキシの構成][deployment-guide-configure-proxy]」の章を参照してください。

#### 監視の構成
このドキュメントの「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の説明に従って、 Azure Monitoring Extension for SAP を構成します。このドキュメントの「[SAP リソース][deployment-guide-2.2]」の章で示されているリソースで、SAP カーネルおよび SAP Host Agent の必要な最小バージョンについて SAP Monitoring の前提条件を確認してください。

#### 監視の確認
「[Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」で説明されているとおりに監視が機能しているかどうかを確認します。

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する
このシナリオでは、現在の形でオンプレミスから Azure への移動のみを行う SAP システムのケースを取り上げます。つまり、Windows または Linux のホスト名、SAP SID などは変更されません。このシナリオでは、VHD はデプロイ中にイメージとして参照されませんが、OS ディスクとして直接使用されます。デプロイに関しては、デプロイ中にVM エージェントを自動的にインストールすることができないため、前述の 2 つのシナリオとは異なります。そのため、Azure VM エージェントを Microsoft からダウンロードし、後で VM 内で手動でインストールして有効にする必要があります。タスクが成功したら、続けて SAP Host Monitoring Azure Extension の初期化と構成を行うことができます。Azure VM エージェントの機能の詳細については、次の記事を参照してください。

[コメント]: <> (MSSedusch TODO 以下の Windows リンクを更新してください)

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide]

___

さまざまな手順のワークフローは次のようになります。
 
![VM ディスクを使用した SAP システムの VM デプロイのフローチャート][deployment-guide-figure-400]

ディスクを既にアップロードし、Azure で定義していると仮定して ([計画および実装ガイド][planning-guide]を参照)、次の手順に従います。

#### 仮想マシンの作成
Azure ポータルからプライベート OS ディスクを使用してデプロイメントを作成する場合、[azure-quickstart-templates github repository][azure-quickstart-templates-github] で公開されている SAP テンプレートを使用します。PowerShell または Azure CLI を使用して、手動で仮想マシンを作成することもできます。

* [2 層構成 (仮想マシン 1 台のみ) のテンプレート][sap-templates-2-tier-os-disk]
    * 仮想マシンを 1 台のみ使用して 2 層システムを作成する場合は、このテンプレートを使用します。

上記のテンプレートを開くと、Azure ポータルの [パラメーターの編集] パネルが表示されます。次の情報を入力します。

* **sapSystemId**: SAP システム ID
* **osType**: デプロイするオペレーティング システムの種類 (Windows または Linux)
* **sapSystemSize**: SAP システムのサイズ
    * 新しいシステムで提供する SAPS の量。システムで必要な SAPS の量が不明な場合、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
* **storageType**: (2 層テンプレートのみ) 使用するストレージの種類
    * 大規模なシステムの場合、Premium Storage の使用を強くお勧めします。さまざまなストレージの種類の詳細については、
        * [DBMS ガイド][dbms-guide]の [Microsoft Azure Storage][dbms-guide-2.3] を参照してください。
        * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
        * [Microsoft Azure Storage の概要][storage-introduction]
* **osDiskVhdUri**: プライベート OS ディスクの URI (例: https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd)
* **newOrExistingSubnet**: 新しい仮想ネットワークまたはサブネットを作成するか、既存のサブネットを使用するかを決定します。オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、既存のものを選択します。
* **subnetId**: 仮想マシンを接続するサブネットの ID。仮想マシンをオンプレミス ネットワークに接続する VPN または Expressroute 仮想ネットワークのサブネットを選択します。通常、ID は /subscriptions/`<subscription id`>/resourcegroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> のようになります。

すべてのパラメーターを入力したら、サブスクリプションおよび使用するリソース グループを選択します。既存のリソース グループを選択するか、ドロップダウン メニューで [+ 新規] を選択して新しいリソース グループを作成することができます。新しいリソース グループを作成する場合は、リソース グループと仮想マシンを作成するリージョンも選択する必要があります。

法律条項を確認し、同意して [作成] をクリックします。

#### VM エージェントのインストール
上記のテンプレートを使用する場合、Linux エージェントを OS ディスクにインストールしておく必要があります。17 以下の場合、デプロイは失敗します。このドキュメントの「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の章の説明に従って、VM エージェントを VM にダウンロードしてインストールします。

上記のテンプレートを使用しない場合、後で VM エージェントをインストールすることもできます。

#### ドメインへの参加 (Windows のみ)
Azure でのデプロイメントが Azure サイト間または Expressroute を介してオンプレミスの AD または DNS に接続している場合 ([計画および実装ガイド][planning-guide]ではクロス プレミスとも呼ばれる)、オンプレミス ドメインに VM を参加させる必要があります。この手順に関する考慮事項については、このドキュメントの「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」を参照してください。

#### プロキシ設定の構成
オンプレミス ネットワークの構成によっては、仮想マシンが VPN または Expressroute を介してオンプレミス ネットワークに接続している場合、仮想マシンでプロキシを構成しなければならない場合があります。構成しないと、仮想マシンがインターネットにアクセスできない場合があり、必要な拡張機能のダウンロードや監視データの収集ができません。このドキュメントの「[プロキシの構成][deployment-guide-configure-proxy]」の章を参照してください。

#### 監視の構成
このドキュメントの「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の説明に従って、Azure Enhanced Monitoring Extension for SAP を構成します。

このドキュメントの「[SAP リソース][deployment-guide-2.2]」の章で示されているリソースで、SAP カーネルおよび SAP Host Agent の必要な最小バージョンについて SAP Monitoring の前提条件を確認してください。

#### 監視の確認
「[Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」で説明されているとおりに監視が機能しているかどうかを確認します。

### シナリオ 4: SAP の監視の構成を更新する
監視の構成を更新しなければならない場合があります。

* MS と SAP の共同チームは、監視機能を拡張し、カウンターの追加と一部のカウンターの削除を決定しました。
* Microsoft では、監視データを提供する、基になる Azure インフラストラクチャの新しいバージョンを提供しており、Azure Enhanced Monitoring Extension for SAP はこれらの変更に対応しています。
* Azure VM にマウントする VHD を追加するか、VHD を削除します。この場合、ストレージ関連データのコレクションを更新する必要があります。エンドポイントの追加または削除、または VM への IP アドレスの割り当てによって構成を変更する場合、この変更は監視の構成には影響しません。
* A5 から別の VM のサイズなど、Azure VM のサイズを変更します。
* Azure VM に新しいネットワーク インターフェイスを追加します。

監視の構成を更新には、次の手順に従います。

* このドキュメントの「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の手順に従って、監視インフラストラクチャを更新します。この章で説明されるスクリプトを再実行すると、監視の構成をデプロイすることが検出されて、必要な変更が監視の構成に対して実行されます。

___

> ![Windows][Logo_Windows] Windows
>
> Azure VM エージェントの更新では、ユーザーが操作を行う必要はありません。VM エージェントは自動的にそれ自体を更新し、VM の再起動を必要としません。
>
> ![Linux][Logo_Linux] Linux
>
> Azure Linux エージェントを更新するには、[この記事][virtual-machines-linux-update-agent]の手順に従ってください。

___

## 単一のデプロイの詳細手順

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell コマンドレットのデプロイ
* <https://azure.microsoft.com/downloads/> に移動します
* 「コマンド ライン ツール」セクションには「Windows PowerShell」というセクションがあります。[インストール] リンクをクリックします。
* Microsoft ダウンロード マネージャーで、.exe で終わる行項目がポップアップで表示されます。[実行] オプションを選択します。
* Microsoft Web プラットフォーム インストーラーを実行するかどうかを確認するポップアップが表示されます。[はい] を押します。
* 次のような画面が表示されます。
 
![Azure PowerShell コマンドレットのインストール画面][deployment-guide-figure-500] <a name="figure-5"></a>

* [インストール] を押して、使用許諾契約書に同意します。

PowerShell コマンドレットが更新されているかどうかをこまめに確認してください。通常、毎月の更新プログラムがあります。これを行う最も簡単な方法は、[この][deployment-guide-figure-5]図のインストール画面まで、上記のインストール手順を実行することです。この画面には、実際のリリース番号と、コマンドレットのリリース日が表示されます。SAP ノート [1928533] または [2015553] に明記されていない限り、最新バージョンの Azure PowerShell コマンドレットを使用することをお勧めします。

PS コマンドを使用して、デスクトップ/ラップトップに現在インストールされている Azure コマンドレットのバージョンを確認することができます。

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

結果は[この][deployment-guide-figure-6]図のように表示されます。

![Azure PS コマンドレットのバージョン チェックの結果][deployment-guide-figure-600] <a name="figure-6"></a>

デスクトップ/ラップトップにインストールされている Azure コマンドレットのバージョンが最新のバージョンの場合、Microsoft Web Platform Installer を起動した後の最初の画面は、[この][deployment-guide-figure-5]図で示される画面とは外観が若干異なります。

次の[図][deployment-guide-figure-7]の赤い円の部分に注意してください。
 
![Azure PS コマンドレットの最新版がインストールされていることを示す Azure PowerShell コマンドレットのインストール画面][deployment-guide-figure-700] <a name="figure-7"></a>

画面が[上記][deployment-guide-figure-7]のように、最新版の Azure コマンドレットが既にインストールされていることを示す場合、インストールを続行する必要はありません。この場合、この段階でインストールを「終了」することができます。

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI のデプロイ
* <https://azure.microsoft.com/downloads/> に移動します
* 「コマンド ライン ツール」セクションには「Azure コマンド ライン インターフェイス」というセクションがあります。使用しているオペレーティング システムのインストール リンクをクリックします。

Azure CLI が更新されているかどうかをこまめに確認してください。通常、毎月の更新プログラムがあります。これを行う最も簡単な方法は、前述のインストール手順を実行することです。

次のコマンドを使用して、デスクトップ/ラップトップに現在インストールされている Azure CLI のバージョンを確認することができます。

```
azure --version
```

結果は[この][deployment-guide-figure-azure-cli-version]図のように表示されます。

![Azure CLI のバージョン チェックの結果][deployment-guide-figure-760] <a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>オンプレミス ドメインへの VM の参加 (Windows のみ)
オンプレミスの AD および DNS が Azure に拡張されるクロス プレミス シナリオに SAP VM をデプロイする場合、VM がオンプレミス ドメインに参加している必要があります。オンプレミス ドメインに VM を参加させるための詳細な手順と、オンプレミス ドメインのメンバーになるために必要な追加ソフトウェアは、ユーザーによって異なります。通常、オンプレミス ドメインに VM を参加させるということは、マルウェア対策ソフトウェアや、バックアップまたは監視ソフトウェアのさまざまなエージェントなどの追加ソフトウェアをインストールすることを意味します。

さらに、ゲスト VM の Windows ローカル システム アカウント (S-1-5-18) にもプロキシ設定が存在するドメインに参加すると、インターネット プロキシ設定が強制されるケースも確認する必要があります。最も簡単なのは、ドメイン内のシステムに適用されるドメイン グループ ポリシーを使用してプロキシを強制することです。

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM エージェントのダウンロード、インストール、有効化
汎用化されていない (Windows 用に sysprep されていないなど) OS イメージから SAP の VM をデプロイする場合、次の手順が必要です。Azure Marketplace からデプロイした仮想マシンの場合、エージェントをインストールする必要はありません。これらのイメージには Azure エージェントが含まれています。

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Azure VM エージェントのダウンロード:
	* Azure VM エージェント インストーラー パッケージを <https://go.microsoft.com/fwlink/?LinkId=394789> からダウンロードします。
	* ラップトップまたはサーバーのローカルに VM エージェント MSI パッケージを保存します。
* Azure VM エージェントのインストール:
	* ターミナル サービス (RDP) を使用して、デプロイした Azure VM に接続します。
	* VM で Windows エクスプ ローラー ウィンドウを開き、VM エージェントの MSI ファイルがあるディレクトリを開きます。
	* Azure VM エージェント インストーラー MSI ファイルをローカルのラップトップ/サーバーから、VM 内の VM エージェントのターゲット ディレクトリにドラッグ アンド ドロップします。
	* VM 内で MSI ファイルをダブルクリックします。
	* オンプレミス ドメインに参加している VM の場合、「[プロキシの構成][deployment-guide-configure-proxy]」の章の記載だけでなく、最終的なインターネット プロキシ設定を VM 内の Windows ローカル システム アカウント (S-1-5-18) に適用してください。VM エージェントはこのコンテキストで実行されて、Azure に接続できる必要があります。

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
次のコマンドを使用して Linux の VM エージェントをインストールしてください。

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>プロキシの構成
プロキシを構成する手順は、Windows と Linux 間で異なります。

#### Windows
インターネットにアクセスするには、これらの設定がローカル システム アカウントでも有効である必要があります。プロキシ設定がグループ ポリシーで設定されていない場合、次の手順に従って LocalSystem アカウントのプロキシ設定を構成できます。

1.	gpedit.msc を開きます。
1.	[コンピューターの構成]、[管理用テンプレート]、[Windows コンポーネント]、[Internet Explorer] の順にクリックし、[マシンごとにプロキシ設定を行う (ユーザーごとではなく)] を有効にします。
1.	コントロール パネルを開き、[ネットワークとインターネット]、[インターネット オプション] の順にクリックします。
1.	[接続] タブを開き、[LAN の設定] をクリックします。
1.	[設定を自動的に検出する] を無効にします。
1.	[LAN にプロキシ サーバーを使用する] を有効にして、プロキシのホストとポートを入力します。

#### Linux
Microsoft Azure ゲスト エージェントの構成ファイル /etc/waagent.conf で適切なプロキシを構成します。次のパラメーターを設定する必要があります。

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

構成を変更したら、次のコマンドを使用してエージェントを再起動します。

```
sudo service waagent restart
```

/etc/waagent.conf のプロキシ設定は、必要な VM 拡張機能にも適用されます。Azure リポジトリを使用する場合は、これらのリポジトリへのトラフィックがオンプレミスのイントラネットを経由しないようにしてください。ユーザー定義のルートを作成して強制トンネリングを有効にする場合は、サイト間接続を経由せず、直接インターネットにリポジトリへのトラフィックをルーティングするルートを追加してください。

- **SLES**: /etc/regionserverclnt.cfg にリストされている IP アドレスのルートも追加する必要があります。下記のスクリーンショットは例を示します。

- **RHEL**: /etc/yum.repos.d/rhui-load-balancers のリストにあるホストの IP アドレスのルートも追加する必要があります。下記のスクリーンショットは例を示します。

ユーザー定義のルートの詳細については、[この記事][virtual-networks-udr-overview]を参照してください。

![強制トンネリング][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Azure Enhanced Monitoring Extension for SAP の構成
「[Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]」の章の説明に従って VM を準備すると、Azure VM エージェントがコンピューターにインストールされます。次の重要な手順は、Microsoft Azure のグローバル データセンターの Azure 拡張リポジトリで入手できる Azure Enhanced Monitoring Extension for SAP をデプロイすることです。詳細については、[計画および実装ガイド][planning-guide-9.1]を参照してください。

Azure PowerShell または Azure CLI を使用して、Azure Enhanced Monitoring Extension for SAP のインストールおよび構成を行うことができます。Windows コンピューターを使用して Windows VM または Linux VM に拡張機能をインストールする場合、「[Azure PowerShell][deployment-guide-4.5.1]」の章を参照してください。Linux デスクトップを使用して Linux VM に拡張機能をインストールする場合、[Azure CLI][deployment-guide-4.5.2] を参照してください。

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux VM および Windows VM の Azure PowerShell
Azure Enhanced Monitoring Extension for SAP をインストールするタスクを実行するには、次の手順を実行します。

* 最新バージョンの Microsoft Azure PowerShell コマンドレットをインストールしていることを確認します。このドキュメントの「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」の章を参照してください。
* 次の PowerShell コマンドレットを実行します。使用可能な環境の一覧を表示するには、Get AzureRmEnvironment コマンドレットを実行します。パブリック Azure を使用する場合、環境は AzureCloud を選択します。中国で Azure を使用する場合、AzureChinaCloud を選択します。

```powershell
	$env = Get-AzureRmEnvironment -Name <name of the environment>
	Login-AzureRmAccount -Environment $env
	Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

アカウント データと Azure の仮想マシンを指定すると、スクリプトによって必要な拡張機能をデプロイされて、必要な機能が有効になります。これには数分かかる場合があります。Set-AzureRmVMAEMExtension の詳細については、[この MSDN の記事][msdn-set-azurermvmaemextension]をお読みください。
  
![SAP 固有の Azure のコマンドレット Set-AzureRmVMAEMExtension の正常実行の結果画面][deployment-guide-figure-900]

Set-AzureRmVMAEMExtension の実行が成功すると、SAP のホスト監視機能の構成に必要なすべての手順が実行されます。

スクリプトの出力は次のようになります。

* ベース VHD (オペレーティング システムを含む) と、VM にマウントされているその他のすべての VHD の監視構成が構成されたことの確認。
* 次の 2 つのメッセージで、特定のストレージ アカウントのストレージ メトリックの構成が確認されます。
* 出力の 1 つの行には、監視構成の実際の更新状態が表示されます。
* 別のメッセージでは、構成がデプロイまたは更新されたことが確認されます。
* 出力の最後の行は、監視構成のテストが可能かどうかを示す情報です。
* Azure Enhanced Monitoring のすべての手順が正常に実行されたこと、および Azure インフラストラクチャが必要なデータを提供することは確認するには、このドキュメントの「[Azure Enhanced Monitoring for SAP の適合性チェック][deployment-guide-5.1]」の章の説明に従って、Azure Enhanced Monitoring Extension for SAP の適合性チェックを実行してください。
* この作業を続行する場合、Azure 診断が関連データを収集するまで 15 ~ 30 分かかります。

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux VM 向け Azure CLI

Azure Enhanced Monitoring Extension for SAP をインストールするタスクを実行するには、次の手順を実行します。

1. [こちらの記事][azure-cli]の説明に従って Azure CLI をインストールします。
1. Azure のアカウントを使用してログインします。

    ```
    azure login
    ```
1. Azure Resource Manager モードに切り替えます。

    ```
    azure config mode arm
    ```
1. Azure Enhanced Monitoring を有効にします。

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Azure Enhanced Monitoring が Azure Linux VM 上で有効になっていることを確認します。/var/lib/AzureEnhancedMonitor/PerfCounters ファイルが存在することを確認します。このファイルが存在する場合、次のコマンドを使用して、AEM により収集された情報を表示します。

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    次のような出力が表示されます。
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング
Azure VM を配置し、関連する Azure 監視インフラストラクチャを設定した後は、Azure Enhanced Monitoring のすべてのコンポーネントが適切に機能しているかどうかを確認します。

したがって、「[Azure Enhanced Monitoring for SAP の適合性チェック][deployment-guide-5.1]」の章の説明に従って、Azure Enhanced Monitoring Extension for SAP の適合性チェックを実行します。このチェックの結果が良好で、すべての関連するパフォーマンス カウンターを取得する場合、Azure の監視が正常に設定されています。この場合は、このドキュメントの「[SAP リソース][deployment-guide-2.2]」の章に示されている SAP ノートの説明に従って、SAP Host Agent のインストールに進みます。適合性チェックの結果が、カウンターが見つからないことを示す場合、「[Azure 監視インフラストラクチャ構成のヘルス チェック][deployment-guide-5.2]」の章の説明に従って、Azure 監視インフラストラクチャのヘルス チェックを実行します。Azure 監視設定に問題が発生した場合、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」の章で、トラブルシューティングに関するヘルプを参照します。

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Azure Enhanced Monitoring for SAP の適合性チェック
このチェックを実行して、SAP アプリケーション内に表示されるメトリックが、基になる Azure 監視インフラストラクチャによって完全に指定されていることを確認します。

#### Windows VM での適合性チェックの実行
適合性チェックを実行するには、Azure 仮想マシンにログオンし (管理者アカウントは必要ありません)、次の手順を実行します。

* Windows コマンド プロンプトを開き、Azure Monitoring Extension for SAP のインストール フォルダー C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop に移動します。

上記の監視の拡張機能のパスに含まれるバージョン部分は異なる場合があります。インストール フォルダーに監視の拡張機能のバージョンのフォルダーが複数表示される場合、Windows サービス「AzureEnhancedMonitoring」の構成を確認し、「実行ファイルのパス」に表示されているフォルダーに切り替えます。
 
![Azure Enhanced Monitoring Extension for SAP を実行しているサービスのプロパティ][deployment-guide-figure-1000]

* パラメーターを指定せずにコマンド ウィンドウで azperflib.exe を実行します。

> [AZURE.NOTE] azperflib.exe はループで実行されて、収集したカウンターを 60 秒ごとに更新します。ループを終了するには、コマンド ウィンドウを閉じます。

Azure Enhanced Monitoring Extension がインストールされていない場合、または「AzureEnhancedMonitoring」サービスが実行されていない場合は、拡張機能が正しく構成されていません。この場合は、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」の章で説明されている拡張機能を再デプロイするための詳細な手順に従ってください。

##### azperflib.exe の出力の確認
azperflib.exe の出力では、SAP 用に設定されたすべての Azure パフォーマンス カウンターが表示されます。収集されたカウンターの一覧の下部には、Azure 監視の状態を示すヘルス インジケーターと概要が表示されます。
 
![azperflib.exe の実行によるヘルス チェックの、問題がないことを示す出力][deployment-guide-figure-1100] <a name="figure-11"></a>

空としてレポートされる「カウンター合計」の数の出力に返された結果と、[上記][deployment-guide-figure-11]の図の上部に示される「ヘルス チェック」の結果を確認します。

結果の値は次のように解釈できます。

| azperflib.exe の結果の値 | Azure 監視の適合性ステータス |
| ------------------------------|----------------------------------- |
| **カウンター合計: 空** | 次の 2 つの Azure ストレージ カウンターは空である場合があります: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>他のすべてのカウンターには値が含まれている必要があります。 |
| **ヘルス チェック** | 返されたステータスが [OK] の場合のみ、[OK] と表示されます。 |

azperflib.exe の戻り値のいずれかが、設定されているすべてのカウンターが正しく返されることを示さない場合は、「[Azure 監視インフラストラクチャ構成のヘルス チェック][deployment-guide-5.2]」の章で説明されている Azure 監視インフラストラクチャ構成のヘルス チェックの手順に従います。

#### Linux VM での適合性チェックの実行
適合性チェックを実行するには、SSH を使用して Azure 仮想マシンにログオンし、次の手順を実行します。

* Azure Enhanced Monitoring Extension の出力を確認します。
    * more /var/lib/AzureEnhancedMonitor/PerfCounters
        * パフォーマンス カウンターの一覧が表示されます。ファイルが空であってはなりません。
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
        * エラーがない 1 行が返されます (例: 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;)。
    * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * 空であるか、存在しない必要があります。
* 上記の最初のチェックが正常に実行されなかった場合は、次の追加テストを実行します。
    * waagent がインストールおよび開始されていることを確認します。
        * sudo ls -al /var/lib/waagent/
            * waagent ディレクトリの内容が一覧表示されます。
        * ps -ax | grep waagent
            * 「python /usr/sbin/waagent -daemon」のようなエントリが 1 つ表示されます。
    * Linux Diagnostic Extension がインストールおよび開始されていることを確認します。
        * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
            * Linux Diagnostic Extension ディレクトリの内容が一覧表示されます。
        * ps -ax | grep diagnostic
            * 「python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon」のようなエントリが 1 つ表示されます。
    * Azure Enhanced Monitoring Extension がインストールおよび開始されていることを確認します。
        * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
            * Azure Enhanced Monitoring Extension ディレクトリの内容が一覧表示されます。
        * ps -ax | grep AzureEnhanced
            * 「python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon」のようなエントリが 1 つ表示されます。
* SAP ノート [1031096] の説明に従って SAP Host Agent をインストールし、saposcol の出力を確認します。
    * /usr/sap/hostctrl/exe/saposcol -d を実行します。
    * dump ccm を実行します。
    * メトリック「Virtualization\_Configuration\\Enhanced Monitoring Access」が true であるかどうかを確認します。
* 既にインストールされている SAP NetWeaver ABAP アプリケーション サーバーがある場合は、トランザクション ST06 を開き、Enhanced Monitoring が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合は、「[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」の章で説明されている拡張機能を再デプロイするための詳細な手順に従ってください。

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Azure 監視インフラストラクチャ構成のヘルス チェック
一部の監視データが前述の「[Azure Enhanced Monitoring for SAP の適合性チェック][deployment-guide-5.1]」のテストで示されるとおりに正しく配信されない場合、Test-AzureRmVMAEMExtension コマンドレットを実行し、Azure 監視インフラストラクチャおよび Monitoring Extension for SAP の現在の構成が正しいかどうかをテストします。

監視構成をテストするには、次のシーケンスを実行します。

* このドキュメントの「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」の章の説明に従って Microsoft Azure PowerShell の最新バージョンをインストールしていることを確認します。
* 次の PowerShell コマンドレットを実行します。使用可能な環境の一覧を表示するには、Get AzureRmEnvironment コマンドレットを実行します。パブリック Azure を使用する場合、環境は AzureCloud を選択します。中国で Azure を使用する場合、AzureChinaCloud を選択します。

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* アカウント データと Azure 仮想マシンを指定すると、選択した仮想マシンの構成がスクリプトによってテストされます。

 
![SAP 固有の Azure コマンドレット Test-VMConfigForSAP\_GUI の入力画面][deployment-guide-figure-1200]

アカウントと Azure 仮想マシンの情報を入力すると、選択した仮想マシンの構成がスクリプトによってテストされます。
 
![SAP 用 Azure 監視インフラストラクチャのテスト成功の出力][deployment-guide-figure-1300]

すべてのチェックが OK とマークされていることを確認します。一部のチェックが OK でない場合、このドキュメントの「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章で説明されている更新コマンドレットを実行してください。15 分間待ってから、「[Azure Enhanced Monitoring for SAP の適合性チェック][deployment-guide-5.1]」と「[Azure 監視インフラストラクチャ構成のヘルス チェック][deployment-guide-5.2]」の章の説明に従って、もう一度チェックを実行します。チェックで一部またはすべてのカウンターの問題が見つかった場合、[SAP 用 Azure 監視インフラストラクチャのトラブルシューティング][deployment-guide-5.3]」の章に進みます。

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>SAP 用 Azure 監視インフラストラクチャのトラブルシューティング

#### ![Windows][Logo_Windows] Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックの収集は、Windows サービス「AzureEnhancedMonitoring」によって行われます。サービスが正しくインストールされていない場合、または VM で実行されていない場合は、パフォーマンス メトリックをまったく収集できません。

##### Azure Enhanced Monitoring Extension のインストール ディレクトリが空である 

###### 問題
インストール ディレクトリ C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop が空です。

###### 解決策
拡張機能がインストールされていません。プロキシの問題 (前述) かどうかを確認してください。コンピューターの再起動か構成スクリプト Set-AzureRmVMAEMExtension の再実行またはその両方を実行する必要があります。

##### Azure Enhanced Monitoring のサービスが存在しない 

###### 問題
Windows サービス「AzureEnhancedMonitoring」が存在しません。azperflib.exe: azperlib.exe の出力には、[次の図][deployment-guide-figure-14]のようなエラーが返されます。
 
![Azure Enhanced Monitoring Extension for SAP のサービスが実行されていないことを示す azperflib.exe の実行][deployment-guide-figure-1400] <a name="figure-14"></a>

###### 解決策
[上の図で][deployment-guide-figure-14]示されるようにサービスが存在しない場合、Azure Monitoring Extension for SAP が正しくインストールされていません。「[Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]」の章のデプロイ シナリオで説明されている手順に従って、拡張機能を再デプロイします。

拡張機能のデプロイ後、Azure パフォーマンス カウンターが 1 時間後に Azure VM 内で提供されるかどうかをもう一度確認します。

##### Azure Enhanced Monitoring のサービスは存在するが、起動に失敗する 

###### 問題
Windows サービス「AzureEnhancedMonitoring」が存在し、有効になっていても、起動に失敗します。詳細については、アプリケーション イベント ログを確認します。

###### 解決策
構成が不適切です。「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の説明に従って、VM の監視の拡張機能を再度有効にします。

#### ![Windows][Logo_Windows] 一部の Azure パフォーマンス カウンターが見つからない
Azure のパフォーマンス メトリックの収集は、複数のソースからデータを取得するWindows サービス「AzureEnhancedMonitoring」によって行われます。構成データの一部がローカルで収集されて、パフォーマンス メトリックが Azure 診断から読み取られ、ストレージ カウンターがストレージ サブスクリプション レベルでログ記録から使用されます。

SAP ノート [1999351] を使用したトラブルシューティングで問題が解決しない場合、構成スクリプト Set-AzureRmVMAEMExtension を再実行してください。ストレージ分析または診断カウンターは、有効になった直後には作成されない場合があるため、1 時間待機しなくてはならない場合があります。問題が解決しない場合は、BC-OP-NT-AZR コンポーネントで SAP カスタマー サポート メッセージを開きます。

#### ![Linux][Logo_Linux] Azure パフォーマンス カウンターまったく表示されない

Azure のパフォーマンス メトリックの収集は、デーモンによって行われます。デーモンが実行されていない場合、パフォーマンス メトリックがまったく収集されません。

##### Azure Enhanced Monitoring Extension のインストール ディレクトリが空である 

###### 問題
ディレクトリ /var/lib/waagent/ に Azure Enhanced Monitoring Extension のサブディレクトリがありません。

###### 解決策
拡張機能がインストールされていません。プロキシの問題 (前述) かどうかを確認してください。コンピューターの再起動か構成スクリプト Set-AzureRmVMAEMExtension の再実行またはその両方を実行する必要があります。

#### ![Linux][Logo_Linux] 一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックの収集は、複数のソースからデータを取得するデーモンによって行われます。構成データの一部がローカルで収集されて、パフォーマンス メトリックが Azure 診断から読み取られ、ストレージ カウンターがストレージ サブスクリプション レベルでログ記録から使用されます。

既知の問題に関する完全な最新の一覧については、Enhanced Azure Monitoring for SAP に関するその他のトラブルシューティング情報が記載されている SAP ノート [1999351] を参照してください。

SAP ノート [1999351] を使用したトラブルシューティングで問題が解決しない場合、「[Azure Enhanced Monitoring Extension for SAP の構成][deployment-guide-4.5]」の章の説明に従って、構成スクリプト Set-AzureRmVMAEMExtension を再実行してください。ストレージ分析または診断カウンターは、有効になった直後には作成されない場合があるため、1 時間待機しなくてはならない場合があります。問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

<!---HONumber=AcomDC_0727_2016-->