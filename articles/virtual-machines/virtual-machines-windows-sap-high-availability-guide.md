<properties
   pageTitle="Windows 仮想マシン (VM) 上の SAP NetWeaver - 高可用性ガイド | Microsoft Azure"
   description="Windows 仮想マシン (VM) 上の SAP NetWeaver - 高可用性ガイド"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# Windows 仮想マシン (VM) 上の SAP NetWeaver - 高可用性ガイド

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

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "Windows 仮想マシン (VM) 上の SAP NetWeaver – DBMS デプロイメント ガイド"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "VM と VHD のキャッシング"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "ソフトウェア RAID"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure ストレージ"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "RDBMS デプロイの構造"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Azure VM の高可用性と障害復旧"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 以降"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 以前のリリース"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Microsoft Azure Marketplace からの SQL Server イメージの使用"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Azure での一般的な SAP 用 SQL Server の概要"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "SQL Server RDBMS の詳細"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "ストレージの構成"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "バックアップと復元"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "バックアップと復元のパフォーマンスに関する考慮事項"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "その他"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "Windows 仮想マシン (VM) 上の SAP NetWeaver – デプロイメント ガイド"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "SAP リソース"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "カスタム イメージを使用して VM をデプロイする"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Azure PowerShell コマンドレットのデプロイ"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "SAP 関連の PowerShell コマンドレットのダウンロードとインポート"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "オンプレミス ドメインへの VM の参加 (Windows のみ)"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Azure VM エージェントのダウンロード、インストール、有効化"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Azure Enhanced Monitoring Extension for SAP の構成"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Azure Enhanced Monitoring for SAP の適合性チェック"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Azure 監視インフラストラクチャ構成のヘルス チェック"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "SAP 用 Azure 監視インフラストラクチャのトラブルシューティング"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "監視の構成"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "プロキシの構成"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Azure 上の SAP のエンド ツー エンド監視設定の確認とトラブルシューティング"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "Windows 仮想マシン (VM) 上の SAP NetWeaver – 計画および実装ガイド"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "リソース"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Azure Virtual Machines で実行する SAP NetWeaver の高可用性 (HA) と 障害復旧 (DR)"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "SAP アプリケーション サーバーの高可用性"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "SAP インスタンスでの自動開始の使用"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "クラウドのみ - オンプレミスの顧客ネットワークとの依存関係を持たない仮想マシンの Azure へのデプロイ"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "クロス プレミス - オンプレミス ネットワークに完全に統合されることを要件とする 1 つまたは複数の SAP VM の Azure へのデプロイ"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Azure リージョン"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "障害ドメイン"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "アップグレード ドメイン"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Azure の可用性セット"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Microsoft Azure 仮想マシンの概念"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure Premium Storage"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "顧客固有のイメージを使用する VM のデプロイ"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "SAP 用の顧客固有のイメージを使用する VM のデプロイの準備"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Azure 用の VM と SAP の準備"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Azure ディスクと Azure イメージの違い"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "オンプレミスから Azure への VHD のアップロード"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Azure Storage アカウント間でのディスクのコピー"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "SAP デプロイ用の VM/VHD 構造"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "アタッチされたディスクに対する自動マウントの設定"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "単一の VM と SAP NetWeaver のデモ/トレーニング シナリオ"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "SAP インスタンスのクラウド専用のデプロイの概念"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Azure Monitoring Solution for SAP"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure Premium Storage"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Microsoft Azure のネットワーク"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "ストレージ: Microsoft Azure Storage とデータ ディスク"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "Windows 仮想マシン (VM) 上の SAP NetWeaver - 高可用性ガイド"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "前提条件"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "リソース"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Azure Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの SAP HA の違い"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "リソース グループ"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "クラシック デプロイメント モデルと比較した Azure Resource Manager デプロイメント モデルでのクラスタリング"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "Windows Server フェールオーバー クラスタリング (WSFC)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "クォーラム モード"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "オンプレミスの Windows フェールオーバー クラスター"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "共有記憶域"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "ネットワーク/名前解決"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Microsoft Azure での Windows フェールオーバー クラスター"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "SIOS DataKeeper を使用した Microsoft Azure 上の共有ディスク"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Microsoft Azure での名前解決"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Azure IaaS での SAP NetWeaver の高可用性"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "SAP アプリケーション サーバーの高可用性"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "SAP (A)SCS インスタンスの高可用性"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Azure の Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスの高可用性"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "DBMS インスタンスの高可用性"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "エンド ツー エンドの HA デプロイメントの考えられるシナリオ"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "インフラストラクチャの準備"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "運用環境で使用するための企業ネットワーク接続 (クロスプレミス) での VM のデプロイ"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "テスト/デモを目的とした SAP インスタンスのクラウドのみのデプロイ"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Azure Virtual Network"
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "DNS IP アドレス"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスのホスト名と静的 IP アドレス"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "SAP VM の静的 IP アドレスの設定"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "内部ロード バランサー (ILB) の静的 IP アドレスの設定"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則の変更"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "ドメインへの Windows マシンの追加"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "SAP ASCS/SCS インスタンスで使用する両方のクラスター ノードでのレジストリ エントリの追加"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスターのセットアップ"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "クラスター構成内のクラスター ノードの収集"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "クラスターのファイル共有監視の構成"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "ファイル共有の作成"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの構成"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Microsoft .NET Framework 3.5 の機能の追加"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "SIOS DataKeeper のインストール"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "SIOS DataKeeper のセットアップ"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "SAP NetWeaver システムのインストール"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "高可用性 ASCS/SCS インスタンスでの SAP のインストール"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "クラスター化された SAP ASCS/SCS の仮想ホスト名の作成"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "最初の SAP クラスター ノードのインストール"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "ASCS/SCS インスタンスの SAP プロファイルの変更"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "プローブ ポートの追加"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "データベース インスタンスのインストール"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "2 番目のクラスター ノードのインストール"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "SAP ERS インスタンスの Windows サービスのスタートアップの種類の変更"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "SAP プライマリ アプリケーション サーバー (PAS) のインストール"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "SAP 追加アプリケーション サーバー (AAS) のインストール"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションのテスト"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "出発点 - SAP ASCS/SCS インスタンスがクラスター ノード A で実行されている"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "ノード A からノード B へのフェールオーバー プロセス"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "最終結果 - SAP ASCS/SCS インスタンスがクラスター ノード B で実行されている"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


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
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Microsoft Azure を使用すると、企業は時間のかかる調達サイクルを省略し、コンピューティング、ストレージ、ネットワークの各リソースを最小限の時間で取得できます。Azure Virtual Machines により、企業は SAP NetWeaver ベースのアプリケーション (ABAP、Java、ABAP + Java スタック) のような従来のアプリケーションを Azure にデプロイし、オンプレミスの追加のリソースを必要とせずにアプリケーションの信頼性と可用性を高めることができます。また、Azure Virtual Machines ではクロスプレミス接続もサポートしているので、企業は Azure Virtual Machines を自社のオンプレミス ドメイン、プライベート クラウド、SAP システム ランドスケープに積極的に統合できます。


このドキュメントでは、新しい Azure Resource Manager デプロイメント モデルで新しい方法を使用して、高可用性 SAP システムを Azure にデプロイするために必要なすべての手順について詳しく説明します。このガイドで説明する主な手順は次のとおりです。


- 適切な SAP インストール ガイドと SAP Notes を見つける。これらは、「[リソース][sap-ha-guide-2]」に記載されています。  
  特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Notes を補足する内容となっています。

- 現在の Azure クラシック デプロイメント モデルとこの新しい Azure Resource Manager デプロイメント モデルの違いを理解する。

- Azure デプロイメントに適したモデルを選択できるように、Windows Server フェールオーバー クラスター (WSFC) のクォーラム モードを理解する。

- Azure における Windows Server フェールオーバー クラスター (WSFC) の共有記憶域を理解する。

- SAP ASCS/SCS や DBMS のように SAP の単一障害点となるコンポーネントと、SAP アプリケーション サーバーなどの冗長コンポーネントを Azure で保護する方法を理解する。

- プラットフォームとして Microsoft Azure を使用し、新しい Azure Resource Manager を使用して、Windows フェールオーバー クラスター (WSFC) での高可用性 (HA) SAP システムのインストールと構成を段階的に進める。

- オンプレミス デプロイメントでは不要であり、Azure の WSFC では必要となる追加の手順。


デプロイと構成を簡素化するために、新しい SAP 3 層 HA Azure Resource Manager テンプレートを使用します。これらのテンプレートでは、高可用性 SAP システムに必要となる完全なインフラストラクチャのデプロイを自動化し、SAP システムの目的の SAPS サイズ設定をサポートします。

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>前提条件

作業を開始する前に、以降の章で説明する前提条件を満たしており、「リソース」に記載されているすべてのリソースをチェック済みであることを確認してください。

3 層 SAP NetWeaver 用 Azure Resource Manager テンプレート ([https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)) を使用します。

SAP Azure Resource Manager テンプレートの概要については、[https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) をご覧ください。


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>リソース

Azure での SAP デプロイのトピックを記載した次の追加ガイドが提供されています。

- [Windows 仮想マシン (VM) 上の SAP NetWeaver – 計画および実装ガイド][planning-guide]
- [Windows 仮想マシン (VM) 上の SAP NetWeaver – デプロイメント ガイド][deployment-guide]
- [Windows 仮想マシン (VM) 上の SAP NetWeaver – DBMS デプロイメント ガイド][dbms-guide]
- [Windows 仮想マシン (VM) 上の SAP NetWeaver - 高可用性ガイド (本書)][sap-ha-guide]


> [AZURE.NOTE] 可能な限り、参照する SAP インストール ガイドへのリンクが設定されています ([SAP インストール ガイド][sap-installation-guides]をご覧ください)。前提条件やインストール プロセスについては、必ず SAP NetWeaver のインストール ガイドをよく読んでください。Microsoft Azure Virtual Machine にインストールされた SAP NetWeaver ベースのシステムに固有のタスクについて説明しているのは、このドキュメントだけです。

次の SAP Notes は、SAP on Azure のトピックに関連します。


| Note 番号 | タイトル                                                    
| ------------- |----------------------------------------------------------
| [1928533] | SAP Applications on Azure: Supported Products and Sizing (Azure 上の SAP アプリケーション: サポートされる製品とサイズ変更) 
| [2015553] | SAP on Microsoft Azure: Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件)         
| [1999351] | Enhanced Azure Monitoring for SAP (Azure Monitoring for SAP の強化)                        
| [2178632] | Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック)        
| [1999351] | Virtualization on Windows: Enhanced Monitoring (Windows での仮想化: 拡張された監視機能)           


Azure サブスクリプションの一般的な既定の制限事項と最大の制限事項については、[こちらの記事][azure-subscription-service-limits-subscription]に記載されています。

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの SAP HA の違い 

> [AZURE.NOTE] クラシック デプロイメント モデルは、Azure サービス管理 (ASM) モデルとも呼ばれます。

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>リソース グループ
リソース グループは新しい概念です。リソース グループには、同じライフ サイクル (同時に作成され、削除されるなど) のすべてのリソースが含まれます。リソース グループの詳細については、この記事をご覧ください。

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>クラシック デプロイメント モデルと比較した Azure Resource Manager デプロイメント モデルでのクラスタリング 

新しい Azure Resource Manager モデルは、HA に対応するクラシック デプロイメント モデルと次の点が異なります。

- Azure 内部ロード バランサー (ILB) を使用するためにクラウド サービスは不要

古い Azure クラシック モデルを引き続き使用する場合は、Azure における SAP NetWeaver に関するホワイト ペーパー「[Clustering SAP ASCS/SCS Instances using Windows Server Failover Cluster on Azure with SIOS DataKeeper (Azure 上の Windows Server フェールオーバー クラスターと SIOS DataKeeper を使用した SAP ASCS/SCS インスタンスのクラスタリング)](http://go.microsoft.com/fwlink/?LinkId=613056)」に記載されている手順に従う必要があります。

> [AZURE.NOTE] SAP インストールには、新しい Azure Resource Manager デプロイメント モデルを使用することを強くお勧めします。クラシック デプロイメント モデルに比べ、多くのメリットがもたらされるためです。詳細については、[こちらの記事][virtual-machines-azure-resource-manager-architecture-benefits-arm]をご覧ください。


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server フェールオーバー クラスタリング (WSFC) 

Microsoft WSFC は、Windows における高可用性 SAP ASCS/SCS インストールと DBMS の技術基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) のグループです。ノードで障害が発生した場合、WSFC は、定義済みのアプリケーションやサービスを提供できるようにするために、正常なクラスターを引き続き維持すると同時に、発生する可能性のある障害の数を特定する必要があります。これを実現するために、さまざまなクォーラム モードが用意されています。
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>クォーラム モード

WSFC では、次の 4 種類のクォーラム モードを使用できます。

- **ノード マジョリティ:** 各ノードが投票できます。クラスターは、投票数が過半数の場合にのみ機能します。このオプションは、ノード数が奇数の場合に推奨されます。たとえば、7 ノード クラスターの 3 ノードで障害が発生する可能性があっても、過半数には達するので、クラスターは引き続き稼働します。

- **ノードおよびディスク マジョリティ:** 各ノードとクラスター記憶域の指定されたディスク ("ディスク監視") が投票できます。ただし、これらが使用可能であり、通信している必要があります。クラスターは、投票数が過半数の場合にのみ機能します。このモードは、ノード数が偶数のクラスター環境で有効です。半数のノードとディスクがオンラインであれば、クラスターは正常な状態に維持されます。

- **ノードおよびファイル共有マジョリティ:** 各ノードと管理者が作成した指定されたファイル共有 (ファイル共有監視) が投票できます。これらが使用可能であり、通信しているかどうかは問いません。クラスターは、投票数が過半数の場合にのみ機能します。このモードはノード数が偶数のクラスター環境で有効であり、"ノードおよびディスク マジョリティ" モードに似ていますが、監視ディスクの代わりに監視用ファイル共有を使用します。実装は簡単ですが、ファイル共有自体が高可用性でない場合、単一障害点になる可能性があります。

- **非マジョリティ: ディスクのみ:** 1 つのノードが使用可能であり、クラスター記憶域内の特定のディスクと通信していれば、クラスターはクォーラムを獲得します。また、そのディスクと通信しているノードだけがクラスターに参加できます。このモデルは推奨されません。 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>オンプレミスの Windows フェールオーバー クラスター

この例では、クラスターは 2 つのノードで構成されています。ノード間のネットワーク接続に障害が発生したときに、どちらのノードも引き続き稼働している場合、クラスターのアプリケーションとサービスを提供し続ける必要があるノードを明確にする必要があります。クォーラム ディスクまたはファイル共有がこの目的を果たします。クォーラム ディスクまたはファイル共有にアクセスできるノードがサービスのアクセシビリティを確保します。

この例では、2 ノード クラスターを使用しています。そのため、ここではノードおよびファイル共有クォーラム モードを選択しました。ノードおよびディスク マジョリティも有効な選択肢です。運用環境では、代わりにクォーラム ディスクを使用し、ネットワークおよびストレージ システム テクノロジを使用してクォーラム ディスクを高可用性にすることをお勧めします。

![図 1: Azure 上の SAP ASCS/SCS の Windows Server フェールオーバー クラスター構成案][sap-ha-guide-figure-1000]

"_**図 1:** Azure 上の SAP ASCS/SCS の Windows Server フェールオーバー クラスター構成案_"

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>共有記憶域

上の図は、2 つのノードを持つ共有記憶域クラスターを示しています。オンプレミスの共有記憶域クラスターには、クラスター内のすべてのノードに認識される共有記憶域があります。ロック メカニズムがデータの破損を防ぎます。また、別のノードで障害が発生した場合、すべてのノードが検出できます。一方のノードで障害が発生すると、もう一方のノードがストレージ リソースの所有権を取得し、サービスの可用性を確保します。

> [AZURE.NOTE] SQL Server のような DBMS を使用して高可用性を実現する場合、共有ディスクは不要です。SQL Server AlwaysOn は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。そのため、Windows クラスター構成に共有ディスクは不要です。

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>ネットワーク/名前解決

クラスターには、仮想 IP アドレスと DNS サーバーによって提供される仮想ホスト名を使用して到達できます。オンプレミスのノードと DNS サーバーは、複数の IP アドレスを処理できます。

標準的なセットアップでは、次のような複数のネットワーク接続が使用されます。

- 記憶域への専用接続
- クラスター内部のハートビート用ネットワーク接続
- クライアントがクラスターへの接続に使用するパブリック ネットワーク



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Microsoft Azure での Windows フェールオーバー クラスター

ベア メタル デプロイメントやプライベート クラウド デプロイメントと比較すると、Microsoft Azure Virtual Machines では WSFC を構成する際に追加の手順が必要となります。共有クラスター ディスクを構築するには、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名が必要になります。

Microsoft Azure 上で SAP HA Central Services クラスターを構築する際に必要となる追加の概念と手順について以下に説明します。これらの手順では、サード パーティ製ツールの SIOS DataKeeper をセットアップし、Azure 内部ロード バランサーを構成する方法を示します。これらのツールにより、Microsoft Azure でファイル共有監視を含む Windows フェールオーバー クラスターを作成できます。


![図 2: 共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスター構成のスキーマ][sap-ha-guide-figure-1001]

"_**図 2:** 共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスター構成のスキーマ_"


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>SIOS DataKeeper を使用した Microsoft Azure 上の共有ディスク

2016 年 9 月の時点で、Microsoft Azure は共有記憶域クラスターを作成するための共有記憶域は提供していません。ただし、高可用性 SAP ASCS/SCS インスタンスにはクラスター共有記憶域が必要になります。

サード パーティ製ソフトウェアの SIOS DataKeeper Cluster Edition を使用すると、クラスター共有記憶域をシミュレートするミラー化された記憶域を作成できます。SIOS ソリューションは、リアルタイムの同期データ レプリケーションを実現します。クラスターの共有ディスク リソースを作成する方法は次のとおりです。

- Windows クラスター構成に含まれる各 VM に接続された Azure VHD を追加する。
- 両方の VM ノードで SIOS DataKeeper Cluster Edition を実行する。
- 追加の VHD 接続されたボリュームの内容を、ソース VM からターゲット VM の追加の VHD 接続されたボリュームにミラー化するように SIOS DataKeeper Cluster Edition を構成する。SIOS DataKeeper は、ソースとターゲットのローカル ボリュームを抽象化し、1 つの共有ディスクとして Windows フェールオーバー クラスターに提示します。

SIOS DataKeeper 製品の詳細については、[http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/) をご覧ください。

 ![図 2: SIOS DataKeeper を使用した Azure の Windows Server フェールオーバー クラスター構成のスキーマ][sap-ha-guide-figure-1002]

"_**図 3:** SIOS DataKeeper を使用した Azure の Windows Server フェールオーバー クラスター構成のスキーマ_"

> [AZURE.NOTE] SQL Server のような DBMS を使用して高可用性を実現する場合、共有ディスクは不要です。SQL Server AlwaysOn は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。そのため、Windows クラスター構成に共有ディスクは不要です。

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Microsoft Azure での名前解決

Microsoft Azure クラウド プラットフォームには、仮想 IP アドレス (Floating IP など) を構成する機能は用意されていません。そのため、クラウド内のクラスター リソースに到達するための仮想 IP アドレスを設定する別のソリューションが必要となります。Azure には、内部ロード バランサー (ILB) が用意されています。ILB により、クラスターの仮想 IP アドレス経由でクラスターに到達できます。そのため、クラスター ノードを含むリソース グループに ILB をデプロイする必要があります。デプロイ後、ILB のプローブ ポートで必要なすべてのポート フォワーディング規則を構成する必要があります。クライアントは仮想ホスト名を使用して接続できます。DNS サーバーがクラスター IP アドレスを解決し、ILB がクラスターのアクティブ ノードへの転送を処理します。

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>Azure IaaS での SAP NetWeaver の高可用性

「[Windows 仮想マシン (VM) 上の SAP NetWeaver – 計画および実装ガイド][planning-guide-11]」の Azure 仮想マシン (VM) 上の SAP NetWeaver の HA に関する章で既に説明したように、SAP アプリケーションの高可用性 (SAP ソフトウェア コンポーネントの HA など) を実現するには、次のコンポーネントを保護する必要があります。

- SAP アプリケーション サーバー
- SAP ASCS/SCS インスタンス
- DBMS サーバー

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>SAP アプリケーション サーバーの高可用性

SAP アプリケーション サーバー/ダイアログ インスタンスの場合、通常、特定の高可用性ソリューションは不要です。高可用性は冗長性によって実現されるため、さまざまな Azure 仮想マシンで複数のダイアログ インスタンスを構成します。2 つの Azure VM に少なくとも 2 つの SAP アプリケーション インスタンスをインストールする必要があります。

![図 4: SAP アプリケーション サーバーの HA][sap-ha-guide-figure-2000]

"_**図 4:** SAP アプリケーション サーバーの HA_"


SAP アプリケーション サーバーをホストするすべての仮想マシンを、同じ **Azure 可用性セット**に配置する必要があります。Azure 可用性セットでは次のことが実現されます。

- すべての VM が同じアップグレード ドメインに含まれているので、たとえば、計画済みメンテナンスのためのダウンタイム中に VM が同時に更新されるのを回避できます。
- すべての VM が同じ障害ドメインに含まれるようになるので、たとえば、すべての VM の可用性に影響を及ぼす可能性のある単一障害点を回避するように VM をデプロイできます。

詳細については、「[仮想マシンの可用性管理][virtual-machines-manage-availability]」をご覧ください。

Azure ストレージ アカウントが単一障害点になる可能性があるため、少なくとも 2 つの VM を少なくとも 2 つの Azure ストレージ アカウントに分散させることが重要です。SAP ダイアログ インスタンスを実行する VM をそれぞれ別のストレージ アカウントにデプロイするのが理想的です。

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>SAP (A)SCS インスタンスの高可用性 

![図 5: SAP ASCS/SCS インスタンス の HA][sap-ha-guide-figure-2001]

"_**図 5:** SAP ASCS/SCS インスタンス の HA_"


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Azure の Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスの高可用性

ベア メタル デプロイメントやプライベート クラウド デプロイメントと比較すると、Microsoft Azure Virtual Machines では WSFC を構成する際に追加の手順が必要となります。Windows フェールオーバー クラスターを構築するには、SAP ASCS/SCS インスタンスのクラスタリング用に、共有クラスター ディスク、複数の IP アドレスと仮想ホスト名、Azure 内部ロード バランサー (ILB) が必要となります。

これについては、このドキュメントで後ほど詳しく説明します。

![図 6: SIOS DataKeeper を使用した Azure の SAP ASCS/SCS 用 Windows Server フェールオーバー クラスター構成のスキーマ][sap-ha-guide-figure-1002]

"_**図 6:** SIOS DataKeeper を使用した Azure の SAP ASCS/SCS 用 Windows Server フェールオーバー クラスター構成のスキーマ_"


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>DBMS インスタンスの高可用性

DBMS は SAP システムの SPOF でもあるため、HA ソリューションを使用して保護する必要があります。Windows Sever フェールオーバー クラスターと Azure 内部ロードバランサーを使用した、Azure の SQL Server AlwaysOn HA ソリューションの例を次に示します。SQL Server AlwaysOn は、独自の DBMS レプリケーションを使用して、DBMS のデータとログ ファイルをレプリケートします。そのため、設定全体を簡素化するクラスター共有ディスクは不要です。


![図 7: SAP DBMS サーバーの HA - SQL Server AlwaysOn の HA 設定の例][sap-ha-guide-figure-2003]

"_**図 7:** SAP DBMS サーバーの HA - SQL Server AlwaysOn の HA 設定の例_"


このドキュメントでは、DBMS のクラスタリングは取り上げていません。

Azure Resource Manager デプロイメント モデルを使用した Azure での SQL Server のクラスタリングの詳細については、次の記事をご覧ください。

- [Azure VM での AlwaysOn 可用性グループの手動構成 - Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>エンド ツー エンドの HA デプロイメントの考えられるシナリオ

Azure における完全な SAP NetWeaver HA アーキテクチャの例を次に示します。この例では、SAP ASCS/SCS インスタンスに専用クラスターを使用し、DBMS にも別の専用クラスターを使用しています。

![図 8: SAP HA アーキテクチャ テンプレート 1 - ASCS/SCS の専用クラスターと DBMS インスタンスの専用クラスター][sap-ha-guide-figure-2004]

"_**図 8:** SAP HA アーキテクチャ テンプレート 1 - ASCS/SCS の専用クラスターと DBMS インスタンスの専用クラスター_"

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>インフラストラクチャの準備

SAP に必要なリソースのデプロイを簡素化するために、SAP 用 Azure Resource Manager テンプレートが開発されました。

これらの 3 層テンプレートでは、次のような高可用性シナリオもサポートします。

- **アーキテクチャ テンプレート 1** -2 つのクラスターが含まれます。クラスターは、SAP の単一障害点である SAP ASCS/SCS と DBMS にそれぞれ対応します。

シナリオ 1 の Azure Resource Manager テンプレートは以下で入手できます。

- Azure Marketplace イメージ: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- カスタム イメージ: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

SAP 3 層 Marketplace イメージをクリックすると、Azure Portal に次の UI が表示されます。

![図 9: SAP HA の Azure Resource Manager パラメーターを指定する][sap-ha-guide-figure-3000]

"_**図 9:** SAP HA の Azure Resource Manager パラメーターを指定する_"


[SYSTEMAVAILABILITY] オプションの **[HA]** を必ず選択します。

テンプレートでは以下が作成されます。

- 必要なすべての **VM**:
    - SAP アプリケーション サーバー VM: `<SAPSystemSID>-di-<Number>`
    - ASCS/SCS クラスター VM: `<SAPSystemSID>-ascs-<Number>`
    - DBMS クラスター: `<SAPSystemSID>-db-<Number>`
- **IP アドレスが関連付けられた、すべての VM のネットワーク カード**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Azure Storage アカウントについて**
- 次の**可用性グループ**:
    - SAP アプリケーション サーバー VM: `<SAPSystemSID>-avset-di`
    - SAP ASCS/SCS クラスター VM: `<SAPSystemSID>-avset-ascs`
    - DBMS クラスター VM: `<SAPSystemSID>-avset-db`
- ASCS/SCS インスタンス用のすべてのポートと IP アドレスを持つ **Azure 内部ロード バランサー (ILB)**: `<SAPSystemSID>-lb-ascs`
-	SQL Server DBMS 用のすべてのポートと IP アドレスを持つ **Azure 内部ロード バランサー (ILB)**: `<SAPSystemSID>-lb-db`
- **ネットワーク セキュリティ グループ**: `<SAPSystemSID>-ascs-0` VM に対して外部 RDP ポートを開いた `<SAPSystemSID>-nsg-ascs-0`


> [AZURE.NOTE]  ネットワーク カードと Azure ILB のすべての IP アドレスは、最初は**動的**として作成されます。このドキュメントで後述するように、これらを**静的** IP アドレスに変更する必要があります。


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>運用環境で使用するための企業ネットワーク接続 (クロスプレミス) での VM のデプロイ

運用 SAP システムの場合、Azure サイト間 (VPN) または Azure ExpressRoute を使用して、[企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2] で Azure VM をデプロイします。

> [AZURE.NOTE]  この場合、Azure VNET とサブネットが既に作成され、準備されています。


**[NEWOREXISTINGSUBNET]** フィールドで [existing] を選択します。

**[SUBNETID]** フィールドに、Azure VM のデプロイ先となる準備済みの Azure ネットワーク SubnetID の完全な文字列を追加する必要があります。

次の PowerShell コマンドを使用すると、すべての Azure ネットワーク サブネットのリストを取得できます。

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


**SUBNETID** は ID フィールドに表示されます。

すべての **SUBNETID** のリストを取得するには、次の PowerShell コマンドを使用します。

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID** は次のようになります。

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>テスト/デモを目的とした SAP インスタンスのクラウドのみのデプロイ

クラウドのみのデプロイメント モデルで HA SAP システムをデプロイすることもできます。

このデプロイは、主にデモ ユース ケースに適していますが、運用ユース ケースには適していません。

[NEWOREXISTINGSUBNET] フィールドで _**[new]**_ を選択します。[SUBNETID] フィールドは、**空白**のままにしておきます。

Azure VNET とサブネットは、SAP Azure Resource Manager テンプレートによって自動的に作成されます。

> [AZURE.NOTE] さらに、同じ VNET に AD/DNS 専用の VM を少なくとも 1 つはデプロイする必要があります。これらの VM はテンプレートでは作成されません。


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure Virtual Network

このガイドの例では、Azure VNET のアドレス空間は 10.0.0.0/16 です。アドレス範囲が 10.0.0.0/24 である "_**Subnet**_" というサブネットが 1 つあります。すべての VM と ILB がこの VNET にデプロイされています。
  
> [AZURE.NOTE] ゲスト内でネットワーク設定 (IP アドレス、DNS サーバー、サブネットなど) を変更しないでください。ネットワーク設定はすべて Azure から行い、DHCP サービスによって反映されます。

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP アドレス

VNET の **[DNS サーバー]** オプションが **[カスタム DNS]** に設定されていることを確認します。次のように構成します。

- **[企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2]**: オンプレミス DNS サーバーの IP アドレスを追加します。オンプレミス DNS サーバーは、Azure で実行されている VM に拡張できます。この場合、DNS サービスを実行するように構成されている Azure VM の IP アドレスを追加できます。

-	**[クラウドのみのデプロイ][planning-guide-2.1]**: 同じ VNET に追加の VM をデプロイします。この VM は DNS サーバーとして機能します。DNS サービスを実行するように構成されている Azure VM の IP アドレスを追加します。


![図 10: Azure VNET の DNS サーバーを構成する][sap-ha-guide-figure-3001]

"_**図 10:** Azure VNET の DNS サーバーを構成する_"

> [AZURE.NOTE] DNS サーバーの IP アドレスを変更した場合は、変更を適用して新しい DNS サーバーに反映するために、Azure VM を再起動する必要があります。このガイドの例では、DNS サービスは次の Windows VM にインストールされ、構成されています。



| VM ロール | VM ホスト名 | ネットワーク カード名 | 静的 IP アドレス  
| ---------------|-------------|--------------------|-------------------
| 最初の DNS サーバー | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 2 番目の DNS サーバー | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスのホスト名と静的 IP アドレス

オンプレミスと同様に、次の予約済みのホスト名と IP アドレスが必要です。

| 仮想ホスト名の役割 | 仮想ホスト名 | 仮想静的 IP アドレス 
| ----------------------------------------------------------------------------|------------------|---------------------------
| SAP ASCS/SCS の最初のクラスターの仮想ホスト名 (クラスターの管理に使用) | pr1-ascs-vir | 10\.0.0.42                 
| SAP ASCS/SCS **インスタンス**の仮想ホスト名 | pr1-ascs-sap | `10.0.0.43`             
| SAP DBMS の2 番目のクラスターの仮想ホスト名 (クラスターの管理に使用) | pr1-dbms-vir | 10\.0.0.32                 
 

仮想ホスト名 _**pr1-ascs-vir**_ と _**pr1-dbms-vir**_ および関連付けられた IP アドレスは、クラスター自体の管理に使用されます。「[クラスター構成内のクラスター ノードの収集][sap-ha-guide-8.12.1]」で説明するように、これらはクラスターの作成時に作成されます。

他の 2 つの仮想ホスト名 _**pr1-ascs-sap**_ と _**pr1-dbms-sap**_ および関連付けられた IP アドレスは、クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスで使用されます。「[クラスター化された SAP ASCS/SCS の仮想ホスト名の作成][sap-ha-guide-9.1.1]」で説明するように、これらは DNS サーバー上で手動で作成できます。

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>SAP VM の静的 IP アドレスの設定

クラスタリング用の仮想マシンをデプロイしたら、すべての VM の静的 IP アドレスを設定する必要があります。これはゲスト OS 内では実行できません。Azure Virtual Network 構成内で構成する必要があります。

これを実行する方法の 1 つとして、Azure Portal を使用します。Azure Portal で次の場所に移動します。

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

[割り当て] フィールドを **[動的]** から **[静的]** に変更し、目的の **IP アドレス**を入力します。

> [AZURE.NOTE] ネットワーク カードの IP アドレスを変更した場合は、変更を適用するために Azure VM を再起動する必要があります。


![図 11: 各 VM のネットワーク カードの静的 IP アドレスを構成する][sap-ha-guide-figure-3002]

"_**図 11:** 各 VM のネットワーク カードの静的 IP アドレスを構成する_"

AD/DNS サービスに使用する VM も含め、すべての VM のすべてのネットワーク インターフェイスに対してこの手順を繰り返します。

このガイドの例では、次の VM と静的 IP アドレスを使用しています。

| VM ロール | VM ホスト名 | ネットワーク カード名 | 静的 IP アドレス  
| ----------------------------------------|--------------|--------------------|-------------------
| 最初の SAP アプリケーション サーバー | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 2 番目の SAP アプリケーション サーバー | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| 最後の SAP アプリケーション サーバー | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| ASCS/SCS インスタンスの最初のクラスター ノード | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| ASCS/SCS インスタンスの 2 番目のクラスター ノード | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| DBMS インスタンスの最初のクラスター ノード | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| DBMS インスタンスの 2 番目のクラスター ノード | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>内部ロード バランサー (ILB) の静的 IP アドレスの設定

SAP Azure Resource Manager テンプレートでは、SAP ASCS/SCS インスタンス クラスターと DBMS クラスターで使用する Azure 内部ロード バランサー (ILB) が作成されます。

初期デプロイでは、ILB の IP アドレスが**動的**に設定されます。この IP アドレスを**静的**に変更することが重要です。

このガイドの例では、次の静的 IP アドレスが割り当てられた 2 つの Azure ILB を使用しています。

| Azure ILB の役割 | Azure ILB 名 | 静的 IP アドレス 
| ---------------------------|----------------|-------------------
| SAP ASCS/SCS インスタンスの ILB | pr1-lb-ascs | `10.0.0.43`         
| SAP DBMS の ILB | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**SAP ASCS/SCS の仮想ホスト名の IP アドレスは、SAP ASCS/SCS の Azure Load Balancer pr1-lb-ascs の IP アドレスと同じであり、** **DBMS の仮想名の IP アドレスは、DBMS の Azure Load Balancer pr1-lb-dbms の IP アドレスと同じです。**

このガイドの例では、内部ロード バランサー _pr1-lb-ascs_ の IP アドレスを、SAP ASCS/SCS インスタンスの仮想ホスト名の IP アドレス (`10.0.0.43`) に設定します。

![図 12: SAP ASCS/SCS インスタンスの内部ロード バランサー (ILB) の静的 IP アドレスを設定する][sap-ha-guide-figure-3003]

"_**図 12:** SAP ASCS/SCS インスタンスの内部ロード バランサー (ILB) の静的 IP アドレスを設定する_"

同様に、Load Balancer _pr1-lb-dbms_ の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレス (このガイドの例では 10.0.0.33) に設定します。

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則

既定では、SAP Azure Resource Manager テンプレートによって、次のインスタンスに必要なすべてのポートが作成されます。

- 既定のインスタンス番号が **00** の ABAP ASCS インスタンス
- 既定のインスタンス番号が **01** の Java SCS インスタンス

そのため、SAP ASCS/SCS インスタンスのインストール時に、ABAP ASCS インスタンスと Java SCS インスタンスにこれらの既定のインスタンス番号 (00 と 01) を使用する必要があります。

次の Azure ILB エンドポイントが必要であり、SAP NetWeaver ABAP ASCS ポート用に作成されます。


| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 00 の ASCS インスタンス) (インスタンス番号が 10 の ERS) の具体的なポート  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| エンキュー サーバー/_lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| ABAP メッセージ サーバー/_lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| 内部 ABAP メッセージ/_lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| メッセージ サーバー HTTP/_Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| SAP 起動サービス ASCS HTTP/_Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| SAP 起動サービス ASCS HTTPS/_Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| エンキュー レプリケーション/_Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| SAP 起動サービス ERS HTTP _Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| SAP 起動サービス ERS HTTP _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| ファイル共有 _Lbrule445_ | | 445                                                                      

"_**表 1:** SAP NetWeaver ABAP ASCS インスタンスのポート番号_"


次の Azure ILB エンドポイントが必要であり、SAP NetWeaver Java SCS ポート用に作成する必要があります。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 01 の SCS インスタンス) (インスタンス番号が 11 の ERS) の具体的なポート  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| エンキュー サーバー/_lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| ゲートウェイ サーバー/_lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Java メッセージ サーバー/_lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| メッセージ サーバー HTTP/_Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| SAP 起動サービス SCS HTTP/_Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| SAP 起動サービス SCS HTTPS/_Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| エンキュー レプリケーション/_Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| SAP 起動サービス ERS HTTP _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| SAP起動サービス ERS HTTP _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| ファイル共有 _Lbrule445_ | | 445                                                                      

"_**表 2:** SAP NetWeaver Java SCS インスタンスのポート番号_"


![図 13: Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則][sap-ha-guide-figure-3004]

"_**図 13:** Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則_"

同様に、Load Balancer _**pr1-lb-dbms**_ の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレス (このガイドの例では _**10.0.0.33**_) に設定します。

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則の変更

SAP ASCS/SCS インスタンスに別のインスタンス番号を使用する場合は、それらのポートの名前と値を更新する必要があります。

これを更新する方法の 1 つとして Azure Portal を使用します。

`<SID>-lb-ascs load balancer -> Load Balancing Rules` に移動します

SAP ASCS/SCS インスタンスに属するすべての負荷分散規則について、以下を変更します。

- 名前
- ポート
- バックエンド ポート

たとえば、既定の ASCS インスタンス番号を 00 から 31 に変更する場合は、"_**表 1:** SAP NetWeaver ABAP ASCS インスタンスのポート番号_" に示すすべてのポートに対して変更を行う必要があります。

ポート _lbrule3200_ の更新の例を次に示します。

![図 14: Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則を変更する][sap-ha-guide-figure-3005]

"_**図 14:** Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則を変更する_"


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>ドメインへの Windows マシンの追加

静的 IP アドレスを VM に割り当てたら、VM をドメインに追加します。

![図 15: VM をドメインに追加する][sap-ha-guide-figure-3006]

"_**図 15:** VM をドメインに追加する_"


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>SAP ASCS/SCS インスタンスで使用する両方のクラスター ノードでのレジストリ エントリの追加

Azure 内部ロード バランサーなどの Azure Load Balancer は、接続が一定時間アイドル状態になると接続を閉じます (アイドル タイムアウト)。一方、ダイアログ インスタンスの SAP ワーク プロセスは、最初のエンキュー/デキュー要求の送信が必要になるとすぐに、SAP エンキュー プロセスへの接続を開きます。通常、これらの接続は、ワーク プロセスまたはエンキュー プロセスが再起動するまで確立されたままになります。ただし、接続がしばらくの間アイドル状態になると、Azure ILB が接続を閉じます。エンキュー プロセスへの接続が存在しない場合、SAP ワーク プロセスが接続を再確立するため、これはそれほど問題にはなりません。ただし、これらのアクティビティは SAP プロセスの開発者トレースで文書化されるので、実際にはこれといった理由がなくても、大量のコンテンツがトレースに作成されることになります。そのため、両方のクラスター ノードで TCP/IP の `KeepAliveTime` と `KeepAliveInterval` を変更することをお勧めします。TCP/IP パラメーターの変更は、このドキュメントで後述する SAP プロファイル パラメーターと組み合わせる必要があります。

SAP ASCS/SCS の両方の Windows クラスター ノードで、次の Windows レジストリ エントリを追加します。

| パス | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 変数名 | `KeepAliveTime`                                              
| 変数の型 | REG\_DWORD (Decimal)                                        
| 値 | 120000                                                     
| ドキュメントへのリンク | [https://technet.microsoft.com/ja-JP/library/cc957549.aspx](https://technet.microsoft.com/ja-JP/library/cc957549.aspx) 


"_**表 3:** 変更する最初の TCP/IP パラメーター_"


| パス | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 変数名 | `KeepAliveInterval`                                          
| 変数の型 | REG\_DWORD (Decimal)                                        
| 値 | 120000                                                     
| ドキュメントへのリンク | [https://technet.microsoft.com/ja-JP/library/cc957548.aspx](https://technet.microsoft.com/ja-JP/library/cc957548.aspx)


"_**表 4:** 変更する 2 番目の TCP/IP パラメーター_"

変更を適用するために、**両方のクラスター ノードを再起動**します。

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスターのセットアップ

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>クラスター構成内のクラスター ノードの収集

まず、両方のクラスター ノードにフェールオーバー クラスタリング機能を追加します。これは、**役割と機能の追加ウィザード**で実行されるので、これ以上の説明は不要です。

次に、Windows フェールオーバー クラスター マネージャーを使用して、フェールオーバー クラスターをセットアップします。

フェールオーバー クラスター マネージャー MMC で、[クラスターの作成] をクリックし、最初のクラスター ノード A の名前 (例: _**pr1-ascs-0**_) だけを追加します。2 番目のノードはまだ追加しないでください。これは後の手順で追加します。

![図 16: フェールオーバー クラスター構成を追加するための最初の手順 - クラスター ノードとして使用する最初のノードのサーバー/VM 名を追加する][sap-ha-guide-figure-3007]

"_**図 16:** フェールオーバー クラスター構成を追加するための最初の手順 - クラスター ノードとして使用する最初のノードのサーバー/VM 名を追加する_"

次の手順では、クラスターのネットワーク名 (仮想ホスト名) を要求されます。

![図 17: フェールオーバー クラスター構成を追加するための 2 番目の手順 - クラスターの名前を定義する][sap-ha-guide-figure-3008]

"_**図 17:** フェールオーバー クラスター構成を追加するための 2 番目の手順 - クラスターの名前を定義する_"


クラスターが作成されると、クラスター検証テストが実行されます。

![図 18: フェールオーバー クラスター構成を追加するための最後の手順 - クラスター検証チェックが実行される][sap-ha-guide-figure-3009]

"_**図 18:** フェールオーバー クラスター構成を追加するための最後の手順 - クラスター検証チェックが実行される_"


![図 19: フェールオーバー クラスター構成を追加するための最後の手順 - クラスター検証チェックでクォーラム ディスクが見つからないことに関する警告が表示される][sap-ha-guide-figure-3010]

"_**図 19:** フェールオーバー クラスター構成を追加するための最後の手順 - クラスター検証チェックでクォーラム ディスクが見つからないことに関する警告が表示される_"

ディスクに関する警告は、この段階では無視して構いません。後で、SIOS 共有ディスクと共にファイル共有監視を追加します。この段階では、クォーラムは重要ではありません。

![図 20: IP アドレスが割り当てられたクラスター コア リソースが定義されている - ただし、新しい IP アドレスが必要][sap-ha-guide-figure-3011]

"_**図 20:** IP アドレスが割り当てられたクラスター コア リソースが定義されている - ただし、新しい IP アドレスが必要_"


サーバーの IP アドレスが VM ノードの 1 つを指しているため、クラスターが起動できません。コア クラスター サービスの IP アドレスを変更する必要があります。

たとえば、クラスターの仮想ホスト名 _**pr1-ascs-vir**_ の IP アドレス (このガイドの例では _**10.0.0.42**_) を割り当てる必要があります。これは、次に示すように、コア クラスター サービスの IP リソースのプロパティ ページで行います。

![図 21: [プロパティ] を使用して正しい IP アドレスに変更する][sap-ha-guide-figure-3012]

"_**図 21:** [プロパティ] を使用して正しい IP アドレスに変更する_"


![図 22: このクラスター用に予約された IP アドレスを割り当てる][sap-ha-guide-figure-3013]

"_**図 22:** このクラスター用に予約された IP アドレスを割り当てる_"

IP アドレスを変更すると、クラスターの仮想ホスト名がオンラインになります。

![図 23: 正しい IP アドレスでコア クラスター サービスが起動して実行されている][sap-ha-guide-figure-3014]

"_**図 23:** 正しい IP アドレスでコア クラスター サービスが起動して実行されている_"

コア クラスター サービスが起動して実行されているので、2 番目のクラスター ノードを追加できます。

![図 24: 2 番目のクラスター ノードを追加する][sap-ha-guide-figure-3015]

"_**図 24:** 2 番目のクラスター ノードを追加する_"

![図 25: 2 番目のクラスター ノードのホスト名 (例: pr1-ascs-1) を追加する][sap-ha-guide-figure-3016]

"_**図 25:** 2 番目のクラスター ノードのホスト名 (例: pr1-ascs-1) を追加する_"

![図 26: チェック ボックスはオンにしない][sap-ha-guide-figure-3017]

"_**図 26:** チェック ボックスはオンにしない_"

> [AZURE.NOTE]  
[使用可能な記憶域をすべてクラスターに追加する] チェック ボックスがオンに**なっていない**ことを確認してください。

![図 27: ここでもディスク クォーラムに関する警告を無視する][sap-ha-guide-figure-3018]

"_**図 27:** ここでもディスク クォーラムに関する警告を無視する_"

クォーラムとディスクに関する警告は無視して構いません。「**[SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール][sap-ha-guide-8.12.3]**」で説明するように、クォーラムと共有ディスクの設定は後で構成します。

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>クラスターのファイル共有監視の構成

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>ファイル共有の作成

クォーラム ディスクではなく、ファイル共有監視を選択します。このオプションは、SIOS DataKeeper でサポートされています。

このドキュメントの図で使用されている構成では、Azure で実行されている、_**domcontr-0**_ という AD/DNS サーバーでファイル共有監視を構成しています。(サイト間または ExpressRoute を使用して) Azure への VPN 接続を構成したので、AD/DNS はオンプレミスに存在します。そのため、ファイル共有監視の実行には適していません。

> [AZURE.NOTE] AD/DNS がオンプレミスでのみ実行されている場合は、オンプレミスで実行されている AD/DNS の Windows OS でファイル共有監視を構成しないでください。Azure で実行されているクラスター ノードとオンプレミスの AD/DNS 間のネットワーク待ち時間が長すぎることが原因で、接続の問題が発生すると考えられるためです。ファイル共有監視は、クラスター ノードの近くで実行されている Azure Windows VM で構成する必要があります。

クォーラム ドライブには、1024 MB 以上の空き領域が必要です。2048 MB が推奨されます。

まず、クラスター名オブジェクトを追加します。

![図 28: クラスター名オブジェクトの共有に対するアクセス許可を割り当てる][sap-ha-guide-figure-3019]

"_**図 28:** クラスター名オブジェクトの共有に対するアクセス許可を割り当てる_"

アクセス許可には、クラスター名オブジェクト (このガイドの例では _**pr1-ascs-vir$**_) の共有内のデータを変更する権限を必ず含めます。クラスター名オブジェクトを上記の一覧に追加するには、**[追加]** をクリックし、次に示すように、コンピューター オブジェクトもチェックできるようにフィルターを変更する必要があります。

![図 29: オブジェクトの種類を変更してコンピューター オブジェクトも含める][sap-ha-guide-figure-3020]

"_**図 29:** オブジェクトの種類を変更してコンピューター オブジェクトも含める_"

![図 30: コンピューター オブジェクトのボックスをオンにする][sap-ha-guide-figure-3021]

"_**図 30:** コンピューター オブジェクトのボックスをオンにする_"

その後、図 29 に示すように、クラスター名オブジェクトを入力します。これでレコードが作成されたので、図 28 に示すようにアクセス許可を変更できます。

次に、共有の [セキュリティ] タブを使用し、クラスター名オブジェクトのさらに細かいアクセス許可を定義します。

![図 31: ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する][sap-ha-guide-figure-3022]

"_**図 31:** ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する_"

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの構成

次に、フェールオーバー クラスター マネージャーを使用して、クラスター構成をファイル共有監視に変更します。

![図 32: ここに示すように、クラスター クォーラム設定の構成ウィザードを呼び出す][sap-ha-guide-figure-3023]

"_**図 32:** ここに示すように、クラスター クォーラム設定の構成ウィザードを呼び出す_"

![図 33: 各種クォーラム構成の選択画面][sap-ha-guide-figure-3024]

"_**図 33:** 各種クォーラム構成の選択画面_"

この選択では、_**[クォーラム監視を選択する]**_ を選択する必要があります。

![図 34: ファイル共有監視の選択画面][sap-ha-guide-figure-3025]

"_**図 34:** ファイル共有監視の選択画面_"

ここでは、_**[ファイル共有監視を構成する]**_ を選択する必要があります。

![図 35: 監視の共有のファイル共有の場所を定義する][sap-ha-guide-figure-3026]

"_**図 35:** 監視の共有のファイル共有の場所を定義する_"


ファイル共有の UNC パスを入力します (この例では `\\domcontr-0\FSW`)。

[次へ] をクリックすると、実行する変更の一覧が表示されます。内容を確認し、[次へ] をクリックしてクラスター構成を変更します。

![図 36: クラスターの正常な再構成を示す画面][sap-ha-guide-figure-3027]

"_**図 36:** クラスターの正常な再構成を示す画面_"

この最後の手順で、クラスター構成が正常に再構成されます。

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール

Azure で動作中の Windows Server フェールオーバー クラスター構成があるのが現在の状態です。ただし、このクラスター構成には、共有ディスク リソースがまだありません。SAP ASCS/SCS をインストールするには、共有ディスク リソースが必要です。ここで SIOS DataKeeper Cluster Edition が関与します。Azure では必要な機能を備えた共有ディスク リソースを作成することができないため、SIOS DataKeeper などを利用してこの機能を提供する必要があります。

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Microsoft .NET Framework 3.5 の機能の追加

Windows Server の最新のリリースでは、Microsoft .NET Framework 3.5 が自動的に有効化されることもインストールされることもありません。しかし、SIOS DataKeeper には、この製品をインストールするすべてのノードに .NET Framework が必要です。そのため、各種 VM のすべてのゲスト OS に .NET Framework 3.5 をインストールする必要があります。

.NET Framework 3.5 は 2 とおりの方法で追加できます。1 つ目の方法では、次に示すように Windows の **[役割と機能の追加]** を使用します。

![図 37: 役割と機能の追加ウィザードを使用して .Net Framework 3.5 をインストールする][sap-ha-guide-figure-3028]

"_**図 37:** 役割と機能の追加ウィザードを使用して .Net Framework 3.5 をインストールする_"

![図 38: 役割と機能の追加ウィザードを使用した .Net Framework 3.5 のインストールの進行状況バー][sap-ha-guide-figure-3029]

"_**図 38:** 役割と機能の追加ウィザードを使用した .Net Framework 3.5 のインストールの進行状況バー_"

.Net Framework 3.5 の機能を有効にする 2 つ目の方法では、コマンド ライン ツールの _**dism.exe**_ を使用します。この種のインストールでは、Windows インストール メディアの "sxs" ディレクトリにアクセスできる必要があります。管理者特権のコマンド ライン ウィンドウで、次のコマンドを実行する必要があります。

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper のインストール

SIOS DataKeeper Cluster Edition のインストールを見ていきましょう。SIOS DataKeeper Cluster Edition は、クラスターの 2 つのノードにそれぞれインストールする必要があります。SIOS DataKeeper では、同期されたミラーを作成し、クラスター共有記憶域をシミュレートすることで、仮想共有記憶域を作成できます。

SIOS ソフトウェアをインストールする前に、ドメイン ユーザー _**DataKeeperSvc**_ を作成する必要があります。

> [AZURE.NOTE] この _**DataKeeperSvc**_ ユーザーを、両方のクラスター ノードの**ローカルの Administrators** グループに追加します。
  
SIOS ソフトウェアを両方のクラスター ノードにインストールします。

![SIOS インストーラー][sap-ha-guide-figure-3030]

![図 39: SIOS DataKeeper のインストールの最初の画面][sap-ha-guide-figure-3031]

"_**図 39:** SIOS DataKeeper のインストールの最初の画面_"

![図 40: DataKeeper からサービスを無効にすることが通知される][sap-ha-guide-figure-3032]

"_**図 40:** DataKeeper からサービスを無効にすることが通知される_"

図 40 のポップアップが表示されたら、_**[はい]**_ を選択します。

![図 41: SIOS DataKeeper のユーザー選択][sap-ha-guide-figure-3033]

"_**図 41:** SIOS DataKeeper のユーザー選択_"


上の画面では、_**[Domain or Server account (ドメインまたはサーバー アカウント)]**_ を選択することをお勧めします。

![図 42: SIOS DataKeeper のインストールでドメイン ユーザーとパスワードを指定する][sap-ha-guide-figure-3034]

"_**図 42:** SIOS DataKeeper のインストールでドメイン ユーザーとパスワードを指定する_"

SIOS DataKeeper 用に作成したドメイン アカウントとそのアカウントのパスワードを指定します。

![図 43: SIOS DataKeeper ライセンスを提供する][sap-ha-guide-figure-3035]

"_**図 43:** SIOS DataKeeper ライセンスを提供する_"

図 43 に示すように、SIOS DataKeeper のライセンス キーをインストールします。インストールの最後に、**VM の再起動**を要求されます。

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper のセットアップ

両方のノードに SIOS DataKeeper をインストールしたら、構成を開始する必要があります。構成の目的は、各 VM に接続された追加の VHD 間で同期データ レプリケーションを実現することです。以下の手順は、両方のノードでの構成を示しています。

![図 44: DataKeeper の管理および構成ツール][sap-ha-guide-figure-3036]

"_**図 44:** DataKeeper の管理および構成ツール_"


DataKeeper の管理および構成ツールを起動し、_**[Connect Server (サーバーへの接続)]**_ リンク (上の図の赤で囲まれている部分) をクリックします。

![図 45: 最初のノードの名前または TCP/IP アドレスを挿入し、次に 2 番目のノードの名前または TCP/IP アドレスを挿入すると、管理ツールが接続される][sap-ha-guide-figure-3037]

"_**図 45:** 最初のノードの名前または TCP/IP アドレスを挿入し、次に 2 番目のノードの名前または TCP/IP アドレスを挿入すると、管理ツールが接続される_"

次に、2 つのノード間のレプリケーション ジョブを作成します。

![図 46: レプリケーション ジョブを作成する][sap-ha-guide-figure-3038]

"_**図 46:** レプリケーション ジョブを作成する_"

ウィザードに従ってプロセスを進めます。

![図 47: レプリケーション ジョブの名前を定義する][sap-ha-guide-figure-3039]

"_**図 47:** レプリケーション ジョブの名前を定義する_"

![図 48: 現在のソース ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3040]

"_**図 48:** 現在のソース ノードとして使用するノードの基本データを定義する_"

まず、ソース ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する必要があります。次に、ターゲット ノードを定義します。ここでも、ターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する必要があります。

![図 49: 現在のターゲット ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3041]

"_**図 49:** 現在のターゲット ノードとして使用するノードの基本データを定義する_"

次に、適用する圧縮アルゴリズムを定義します。ここでは、レプリケーション ストリームを圧縮することをお勧めします。特に再同期の状況では、レプリケーション ストリームを圧縮すると、再同期時間が大幅に短縮されます。圧縮では、VM の CPU および RAM リソースが利用されることに注意してください。したがって、圧縮率が高いほど、CPU 使用率が増加します。この設定は後で調整して変更できます。

確認する必要があるもう 1 つの設定は、レプリケーションを非同期と同期のどちらで実行するかです。**SAP ASCS/SCS 構成を保護するために、同期レプリケーションの設定が必須となります。**

![図 50: レプリケーションの詳細を定義する][sap-ha-guide-figure-3042]

"_**図 50:** レプリケーションの詳細を定義する_"

最後の手順では、レプリケーション ジョブによってレプリケートされたボリュームを、WSFC クラスター構成に共有ディスクとして示すかどうかを定義します。SAP ASCS/SCS 構成では、Windows クラスターが、レプリケートされたボリュームを、クラスター ボリュームとして使用できる共有ディスクとして認識するように、[はい] を選択する必要があります。

![図 51: [はい] をクリックして、レプリケートされたボリュームをクラスター ボリュームとして有効にする][sap-ha-guide-figure-3043]

"_**図 51:** [はい] をクリックして、レプリケートされたボリュームをクラスター ボリュームとして有効にする_"

作成が完了すると、DataKeeper の管理ツールにレプリケーション ジョブがアクティブとして表示されます。

![図 52: SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている][sap-ha-guide-figure-3044]

"_**図 52:** SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている_"

結果として、次に示すように、Windows フェールオーバー クラスター マネージャーにディスクを DataKeeper ディスクとして表示できるようになります。

![図 53: DataKeeper によってレプリケートされたディスクがフェールオーバー クラスター マネージャーに表示される][sap-ha-guide-figure-3045]

"_**図 53:** DataKeeper によってレプリケートされたディスクがフェールオーバー クラスター マネージャーに表示される_"


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>SAP NetWeaver システムのインストール

DBMS のセットアップは、使用している DBMS システムによって異なるため、ここでは説明しません。ただし、さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。例: SQL Server の AlwaysOn またはデータベース ミラーリングや、Oracle の Oracle Data Guard などがあります。このドキュメントで使用するシナリオ例では、DBMS の保護も行いませんでした。

Azure でこのようなクラスター化された SAP ASCS/SCS 構成と対話する各種 DBMS に関する特別な考慮事項もありません。

> [AZURE.NOTE]  
SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。最大の違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。SAP Java システムでは 1 つの SCS インスタンスを使用し、SAP ABAP + Java システムでは同じ Microsoft フェールオーバー クラスター グループで実行されている ASCS インスタンスと SCS インスタンスを 1 つずつ使用します。各 SAP NetWeaver インストール スタックのインストールの違いは明確に示されます。他の部分はすべて同じと見なされます。

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>高可用性 ASCS/SCS インスタンスでの SAP のインストール

> [AZURE.NOTE]  
DataKeeper のミラー化されたボリュームにページ ファイルを配置しないでください。これは DataKeeper でサポートされていません。ページ ファイルは、Azure に VM をデプロイしたときに既に配置されている Azure VM の一時 D:\\ ドライブにそのまま残しておくことができます。Windows ページ ファイルが Azure VM の D:\\ ドライブに配置されていない場合は、このドライブに配置し直してください。

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>クラスター化された SAP ASCS/SCS の仮想ホスト名の作成

まず、ASCS/SCS インスタンスの仮想ホスト名の必要な DNS エントリを作成します。この作業に使用するツールは、Windows DNS マネージャーです。仮想ホスト名に加え、仮想ホスト名に割り当てる IP アドレスも定義する必要があります。

> [AZURE.NOTE]  
**ASCS/SCS インスタンスの仮想ホスト名に割り当てる IP アドレスは、Azure Load Balancer (`<sid>-lb-ascs`) に割り当てた IP アドレスと同じである必要があることに留意してください。SAP ASCS/SCS の仮想ホスト名 `(pr1-ascs-sap)` の IP アドレスは、Azure Load Balancer `(pr1-lb-ascs)` の IP アドレスと同じです。**

これは、Azure 上の 1 つの Windows Server フェールオーバー クラスターで実行できる SAP フェールオーバー クラスターの役割は 1 つだけであることも意味します (例: ABAP システムでは 1 つの ASCS インスタンス、Java システムでは 1 つの SCS インスタンス、ABAP + Java では ASCS インスタンスと SCS インスタンスが 1 つずつ)。

> [AZURE.NOTE] SAP インストール ガイドで説明する Multi-SID クラスタリング ([SAP インストール ガイド][sap-installation-guides]を参照) は、現在、Azure では機能しません。

![図 54: SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046]

"_**図 54:** SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_"

次の図に示すように、DNS マネージャーでは、このエントリはドメインの下に表示されます。

![図 55: SAP ASCS/SCS クラスター構成の一覧に表示された新しい仮想名と TCP/IP アドレス][sap-ha-guide-figure-3047]

"_**図 55:** SAP ASCS/SCS クラスター構成の一覧に表示された新しい仮想名と TCP/IP アドレス_"

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>最初の SAP クラスター ノードのインストール

最初の ASCS/SCS クラスター ノードのインストールは、SAP インストール ドキュメントに記載されている方法とほとんど違いはありません。
- クラスター ノード A (このガイドの例では、_**pr1-ascs-0**_ ホスト) で [First Cluster Node (最初のクラスター ノード)] オプションを実行します。

Azure 内部ロード バランサーの既定のポートを維持する場合は、以下を選択します。

- **ABAP システムの場合** - **ASCS** インスタンス番号 **00**
- **Java システムの場合** - **SCS** インスタンス番号 **01**
- **ABAP + JAVA システムの場合** - **ASCS** インスタンス番号 **00** と **SCS** インスタンス番号 **01**

他のインスタンス番号 (ABAP ASCS インスタンスでは 00 以外、Java SCS インスタンスでは 01 以外) を使用する場合は、「**[Azure 内部ロード バランサー (ILB) の既定の ASCS/SCS 負荷分散規則の変更][sap-ha-guide-8.9]**」の説明に従って、まず Azure ILB の既定の負荷分散規則を変更する必要があります。

この手順の完了後、通常の SAP インストール ドキュメントには記載されていないいくつかの手順を実行する必要があります。

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>ASCS/SCS インスタンスの SAP プロファイルの変更

新しいプロファイル パラメーターを追加する必要があります。このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続を閉じるのを防ぐことができます。このドキュメントの「**[SAP ASCS/SCS インスタンスで使用する両方のクラスター ノードでのレジストリ エントリの追加][sap-ha-guide-8.11]**」で、問題のシナリオについて説明しました。そのセクションで、一部の基本的な TCP/IP 接続パラメーターにも 2 つの変更を加えました。もう 1 つの手順として、接続が Azure ILB のアイドルしきい値に達しないように、**keep\_alive** 信号を送信するようエンキュー サーバーを構成する必要があります。そのために、次のプロファイル パラメーターを

```
enque/encni/set_so_keepalive = true
```

SAP ASCS/SCS インスタンス プロファイルに追加します。このガイドの例では、パスは次のようになります。

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

SAP SCS インスタンス プロファイルの対応するパスは次のようになります。

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>プローブ ポートの追加

クラスター構成全体を Azure Load Balancer と連携させるために、内部ロード バランサーのプローブ機能を活用する必要があります。通常、Azure 内部ロード バランサーは、参加している仮想マシン間で受信ワークロードのバランスを取り、ワークロードを均等に分散させます。ただし、一方のインスタンスだけがアクティブであり、もう一方はパッシブでワークロードを受け入れることができないため、このようなクラスター構成ではこれは機能しません。Azure 内部ロード バランサーがアクティブ インスタンスにのみ処理を割り当てる構成を可能にするために、プローブ機能が作成されました。この機能により、内部ロード バランサーはどのインスタンスがアクティブかを確認した後、ワークロードでそのインスタンスだけを対象にすることができます。まず、クラスター構成に関与する VM のいずれかで次の PowerShell コマンドを実行して、現在の _**ProbePort**_ 設定を確認しましょう。

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![図 56: クラスター構成のプローブ ポートは既定で 0 に設定されている][sap-ha-guide-figure-3048]

"_**図 56:** クラスター構成のプローブ ポートは既定で 0 に設定されている_"

既定では、プローブ ポート番号は 0 に設定されています。この構成を機能させるには、ポートを定義する必要があります。ここでは、プローブ ポート _**62300**_ を使用する必要があります。SAP Azure Resource Manager テンプレートでこのポート番号が定義されているためです。このポート番号の割り当ては、次の 2 つのコマンドを使用して実行できます。

まず、SAP 仮想ホスト名のクラスター リソース _**SAP WAC IP**_ を取得します。

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

次に、プローブ ポートを 62300 に設定します。

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

変更を有効にするために、_**SAP PR1**_ クラスターの役割を一度停止してから起動する必要があります。

_**SAP PR1**_ クラスターの役割がオンラインになったら、_**ProbePort**_ が新しい値に設定されていることを確認します。

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![図 57: 変更後のクラスターのプローブ ポート][sap-ha-guide-figure-3049]

"_**図 57:** 変更後のクラスターのプローブ ポート_"

_**ProbePort**_ が _**62300**_ に設定されていることがわかります。これで、_**ascsha-dbas**_ などの他のホストから、ファイル共有 _**\\\ascsha-clsap\\sapmnt**_ にアクセスできるようになりました。

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> データベース インスタンスのインストール

データベース インスタンスのインストールは、SAP インストール ドキュメントに記載されているプロセスとまったく同じです。したがって、ここでは説明しません。

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>2 番目のクラスター ノードのインストール

2 番目のクラスター ノードのインストールも、SAP インストール ドキュメントに記載されているものと同じです。したがって、2 番目のクラスター ノードのインストールについては、インストール ガイドの手順に従ってください。

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>SAP ERS インスタンスの Windows サービスのスタートアップの種類の変更

両方のクラスター ノードで、SAP ERS Windows サービスのスタートアップの種類を _**[自動 (遅延開始)]**_ に変更します。

![図 58: SAP ERS インスタンスのサービスの種類を遅延自動に変更する][sap-ha-guide-figure-3050]

"_**図 58:** SAP ERS インスタンスのサービスの種類を遅延自動に変更する_"

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>SAP プライマリ アプリケーション サーバー (PAS) のインストール

PAS のホストとして指定されている VM で、プライマリ アプリケーション サーバー インスタンス `<sid>-di-0` のインストールを実行します。Azure または DataKeeper の仕様に対する依存関係はありません。

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>SAP 追加アプリケーション サーバー (AAS) のインストール

SAP アプリケーション サーバーのホストとして指定されているすべての VM (例: `<sid>-di-1` ～ `<sid>-di-<n>`) で、SAP 追加アプリケーション サーバーのインストールを実行します。

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションのテスト

"_**フェールオーバー クラスター マネージャー**_" と SIOS DataKeeper UI を使用して、SAP ASCS/SCS インスタンスのフェールオーバーと SIOS ディスク レプリケーションを簡単にテストおよび監視できます。

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>出発点 - SAP ASCS/SCS インスタンスがクラスター ノード A で実行されている

_**SAP WAC**_ クラスター グループがクラスター ノード A (例: _**ascsha-clna**_) で実行されています。_**SAP WAC**_ クラスター グループに含まれ、ASCS/SCS インスタンスが使用する共有ディスク S: が、クラスター ノード A に割り当てられています。

![図 59: フェールオーバー クラスター マネージャー: SAP < SID > クラスター グループがクラスター ノード A で実行されている][sap-ha-guide-figure-5000]

"_**図 59:** フェールオーバー クラスター マネージャー: SAP < SID > クラスター グループがクラスター ノード A で実行されている_"

SIOS DataKeeper UI を使用して、共有ディスクのデータが、クラスター ノード A (例: _**ascsha clna [10.0.0.41]**_) のソース ボリューム S: から、クラスター ノード B (例: _**ascsha clnb [10.0.0.42]**_) のターゲット ボリューム _**S:**_ に同期的にレプリケートされることを確認できます。

![図 60: SIOS DataKeeper: クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5001]

"_**図 60:** SIOS DataKeeper: クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする_"


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>ノード A からノード B へのフェールオーバー プロセス

次の方法で、クラスター ノード A からクラスター ノード B への SAP < SID > クラスター グループのフェールオーバーを開始できます。

- フェールオーバー クラスター マネージャーを使用する

- フェールオーバー クラスター PowerShell を使用する

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- Windows ゲスト OS 内でクラスター ノード A を再起動する (これにより、ノード A からノード B への SAP < SID > クラスター グループの自動フェールオーバーが開始される)

- Azure Portal からクラスター ノード A を再起動する (これにより、ノード A からノード B への SAP < SID > クラスター グループの自動フェールオーバーが開始される)

- Azure PowerShell を使用してクラスター ノード A を再起動する (これにより、ノード A からノード B への SAP < SID > クラスター グループの自動フェールオーバーが開始される)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a>最終結果 - SAP ASCS/SCS インスタンスがクラスター ノード B で実行されている

フェールオーバー後、`SAP <SID>` クラスター グループは、クラスター ノード B (例: _**ascsha-clnb**_) で実行されています。

![図 61: フェールオーバー クラスター マネージャー: SAP < SID > クラスター グループがクラスター ノード B で実行されている][sap-ha-guide-figure-5002]

"_**図 61:** フェールオーバー クラスター マネージャー: SAP < SID > クラスター グループがクラスター ノード B で実行されている_"

現在、共有ディスクはクラスター ノード B にマウントされています。SIOS DataKeeper は、クラスター ノード B (例: _**ascsha-clnb [10.0.0.42]**_) のソース ボリューム S: から、クラスター ノード A (例: _**ascsha-clna [10.0.0.41]**_) のターゲット ボリューム S: にデータをレプリケートします。

![図 62: SIOS DataKeeper: クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5003]

"_**図 62:** SIOS DataKeeper: クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする_"

<!---HONumber=AcomDC_0907_2016-->
