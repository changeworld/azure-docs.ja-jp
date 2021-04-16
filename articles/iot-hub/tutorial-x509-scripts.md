---
title: チュートリアル - Microsoft のスクリプトを使用して Azure IoT Hub 向けの X.509 テスト証明書を作成する | Microsoft Docs
description: チュートリアル - カスタム スクリプトを使用して Azure IoT Hub 向けの CA 証明書とデバイス証明書を作成する
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378212"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>チュートリアル: Microsoft 提供のスクリプトを使用してテスト証明書を作成する

Microsoft では、独自の X.509 証明書を作成し、IoT ハブで認証する方法を学ぶうえで役立つ PowerShell スクリプトと Bash スクリプトをご用意しています。 スクリプトは、GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)にあります。 いずれも、デモンストレーションのみを目的としたものです。 これらを使って作成した証明書は、運用環境で使用しないでください。 証明書は固定のパスワード ("1234") が含まれており、30 日後に期限切れになります。 運用環境では、証明書の作成と有効期間の管理について、独自のベスト プラクティスを使用する必要があります。

## <a name="powershell-scripts"></a>Powershell スクリプト

### <a name="step-1---setup"></a>手順 1 - 設定

OpenSSL for Windows を入手します。 <https://www.openssl.org/docs/faq.html#MISC4> でダウンロードの場所を確認するか、<https://www.openssl.org/source/> を参照してソースからビルドします。 そのうえで、準備スクリプトを実行します。

1. この GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)から、作業場所とするローカル ディレクトリにスクリプトをコピーします。 ファイルはいずれも、このディレクトリの子として作成されます。

1. 管理者として PowerShell を起動します。

1. スクリプトを読み込んだディレクトリに変更を加えます。

1. コマンド ラインで、環境変数 `$ENV:OPENSSL_CONF` を OpenSSL 構成ファイル (openssl.cnf) が置かれているディレクトリに設定します。

1. `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` を実行して、PowerShell がスクリプトを実行できるようにします。

1. `. .\ca-certs.ps1` を実行します。 これにより、スクリプトの関数が PowerShell のグローバル名前空間に取り込まれます。

1. `Test-CACertsPrerequisites` を実行します。 PowerShell では、Windows 証明書ストアを使用して証明書を管理します。 このコマンドを使うと、OpenSSL を正しく設定できており、後から既存の証明書との間に名前の競合が発生することがないことを確認できます。

### <a name="step-2---create-certificates"></a>手順 2 - 証明書の作成

`New-CACertsCertChain [ecc|rsa]` を実行します。 CA 証明書には ECC をお勧めしますが、必須ではありません。 このスクリプトは、ディレクトリと Windows 証明書ストアを次の CA 証明書と中間証明書で更新するものです。

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

スクリプトを実行した後は、IoT ハブに新しい CA 証明書 (RootCA pem) を追加します。

1. IoT ハブにアクセスし、[証明書] に移動します。

1. **[追加]** を選択します。

1. CA 証明書の表示名を入力します。

1. CA 証明書をアップロードします。

1. **[保存]** を選択します。

### <a name="step-3---prove-possession"></a>手順 3 - 所有証明

CA 証明書を IoT ハブにアップロードしたので、その所有者が自分であることを証明する必要があります。

1. 新しいルート CA 証明書を選択します。

1. **[証明書の詳細]** ダイアログで、 **[確認コードを生成します]** を選択します。 詳細については、[CA 証明書の所有証明](tutorial-x509-prove-possession.md)に関するページを参照してください。

1. 確認コードを含む証明書を作成します。 たとえば、確認コードが `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` であれば、次を実行すると、作業ディレクトリに `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` というサブジェクトが含まれる新しい証明書が作成されます。 このスクリプトでは、`VerifyCert4.cer` という名前の証明書が作成されます。

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. **[証明書の詳細]** ダイアログで、`VerifyCert4.cer` を IoT ハブにアップロードします。

1. **[認証]** を選択します。

### <a name="step-4---create-a-new-device"></a>手順 4 - 新しいデバイスの作成

IoT ハブ用のデバイスを作成します。

1. IoT ハブで、 **[IoT デバイス]** セクションに移動します。

1. `mydevice` という ID の新しいデバイスを追加します。

1. 認証には、 **[X.509 CA 署名済み]** を選択します。

1. `New-CACertsDevice mydevice` を実行して、新しいデバイス証明書を作成します。 これにより、作業ディレクトリに次のファイルが作成されます。

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>手順 5 - デバイス証明書のテスト

「[証明書認証のテスト](tutorial-x509-test-certificate.md)」を参照し、デバイス証明書で IoT ハブに認証できるかどうかを確認します。 PFX バージョンの証明書 (`mydevice.pfx`) が必要になります。

### <a name="step-6---cleanup"></a>手順 6 - クリーンアップ

[スタート] メニューから **[コンピューター証明書の管理]** を開き、 **[証明書 - ローカル コンピューター] > [個人]** の順に移動します。 "Azure IoT CA TestOnly*" によって発行された証明書を削除します。 同じように、 **[信頼されたルート証明機関] > [証明書] と [中間証明機関] > [証明書]** からも、該当する証明書を削除します。

## <a name="bash-scripts"></a>Bash スクリプト

### <a name="step-1---setup"></a>手順 1 - 設定

1. Bash を起動します。

1. 作業場所とするディレクトリに変更を加えます。 すべてのファイルがこのディレクトリに作成されます。

1. `*.cnf` と `*.sh` を作業ディレクトリにコピーします。

### <a name="step-2---create-certificates"></a>手順 2 - 証明書の作成

1. `./certGen.sh create_root_and_intermediate` を実行します。 これにより、**certs** ディレクトリに次のファイルが作成されます。

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. IoT ハブにアクセスし、 **[証明書]** に移動します。

1. **[追加]** を選択します。

1. CA 証明書の表示名を入力します。

1. CA 証明書だけを IoT ハブにアップロードします。 証明書の名前は `./certs/azure-iot-test-only.root.ca.cert.pem.` です。

1. **[保存]** を選択します。

### <a name="step-3---prove-possession"></a>手順 3 - 所有証明

1. 前の手順で作成した新しい CA 証明書を選択します。

1. **[証明書の詳細]** ダイアログで、 **[確認コードを生成します]** を選択します。 詳細については、[CA 証明書の所有証明](tutorial-x509-prove-possession.md)に関するページを参照してください。

1. 確認コードを含む証明書を作成します。 たとえば、確認コードが `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` であれば、次を実行すると、作業ディレクトリに `verification-code.cert.pem` という名前で、`CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` というサブジェクトが含まれる新しい証明書が作成されます。

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. その証明書を、 **[証明書の詳細]** ダイアログで IoT ハブにアップロードします。

1. **[認証]** を選択します。

### <a name="step-4---create-a-new-device"></a>手順 4 - 新しいデバイスの作成

IoT ハブ用のデバイスを作成します。

1. IoT ハブで、[IoT デバイス] セクションに移動します。

1. `mydevice` という ID の新しいデバイスを追加します。

1. 認証には、 **[X.509 CA 署名済み]** を選択します。

1. `./certGen.sh create_device_certificate mydevice` を実行して、新しいデバイス証明書を作成します。 これにより、作業ディレクトリに `new-device.cert.pem` と `new-device.cert.pfx` の 2 ファイルが作成されます。

### <a name="step-5---test-your-device-certificate"></a>手順 5 - デバイス証明書のテスト

「[証明書認証のテスト](tutorial-x509-test-certificate.md)」を参照し、デバイス証明書で IoT ハブに認証できるかどうかを確認します。 PFX バージョンの証明書 (`new-device.cert.pfx`) が必要になります。

### <a name="step-6---cleanup"></a>手順 6 - クリーンアップ

今回の Bash スクリプトは作業ディレクトリに証明書を作成するだけのものなので、テストの完了後は証明書を削除します。

## <a name="next-steps"></a>次の手順

証明書をテストするために、「[証明書認証のテスト](tutorial-x509-test-certificate.md)」を参照しながら、証明書を使ってデバイスを IoT ハブに認証できるかどうかを確認してください。
