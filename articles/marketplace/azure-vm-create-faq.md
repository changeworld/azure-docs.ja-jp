---
title: Azure Marketplace の VM についての一般的な質問
description: Azure Marketplace で仮想マシンを作成するときによく発生する一般的な質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283423"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace の VM についての一般的な質問

これらのよく寄せられる質問 (FAQ) では、Azure Marketplace に仮想マシン (VM) オファーを作成するときに発生する可能性のある一般的な問題について説明します。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>VM で使用するよう仮想プライベート ネットワーク (VPN) を構成するにはどうすればよいですか?

Azure Resource Manager デプロイ モデルを使用している場合、3 つのオプションがあります。

- [Azure portal を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [Azure PowerShell を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [CLI を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure ベースの VM で Microsoft サーバー ソフトウェアを実行するための Microsoft サポート ポリシーはどのようなものですか?

詳細については 「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM で、スタートアップ タスクのカスタム スクリプト拡張機能を管理するにはどうすればよいですか?

Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法と、Windows システムでのトラブルシューティング手順の詳細については、「[Windows でのカスタムのスクリプト拡張機能](/azure/virtual-machines/extensions/custom-script-windows)」を参照してください。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>32 ビットのアプリケーションやサービスは Azure Marketplace でサポートされていますか?

いいえ。 サポートされるオペレーティング システムと Azure VM の標準サービスはすべて 64 ビットです。 ほとんどの 64 ビット オペレーティング システムは、下位互換性のために 32 ビット バージョンのアプリケーションをサポートしていますが、VM ソリューションの一部として 32 ビット アプリケーションを使用することはサポートされておらず、非常にお勧めできません。 アプリケーションを 64 ビットのプロジェクトとして再作成してください。

詳細と例については、次の記事をご覧ください。

- [32 ビット アプリケーションの実行](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>エラー:VHD は、既にリソースとしてイメージ リポジトリに登録されています

VHD からイメージを作成しようとするたびに、Azure PowerShell で "VHD は、既にリソースとしてイメージ リポジトリに登録されています" というエラーが発生します。 これまでイメージを作成したことはなく、Azure でこの名前のイメージは見つかっていません。 解決するにはどうすればよいですか?

この問題は通常、ロックがある VHD から VM を作成した場合に発生します。 この VHD から割り当てられた VM がないことを確認し、操作を再試行してください。 それでもこの問題が解決しない場合は、サポート チケットを開いてください。 [パートナー センターのサポート](support.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [VM 認定のトラブルシューティング](azure-vm-create-certification-faq.md)
