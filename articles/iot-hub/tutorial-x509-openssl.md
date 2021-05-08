---
title: チュートリアル - OpenSSL を使用して Azure IoT Hub 向けの X.509 テスト証明書を作成する | Microsoft Docs
description: チュートリアル - OpenSSL を使用して Azure IoT Hub 向けの CA 証明書とデバイス証明書を作成する
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
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378297"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>チュートリアル: OpenSSL を使用してテスト証明書を作成する

信用のある証明機関から X.509 証明書を購入することも可能ですが、IoT ハブのデバイス認証をテストするのが目的であれば、テスト証明書の階層を独自に作成するか、自己署名証明書を使用すれば十分です。 これから紹介する例では、[OpenSSL](https://www.openssl.org/) と [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) を使用し、証明機関 (CA)、下位 CA、デバイス証明書を作成します。 そのうえで、下位 CA とデバイス証明書に署名し、証明書の階層に追加します。 これは、例示のみを目的として紹介しているものです。

## <a name="step-1---create-the-root-ca-directory-structure"></a>手順 1 - ルート CA のディレクトリ構造を作成する

証明機関のディレクトリ構造を作成します。

* **certs** ディレクトリには、新しい証明書が格納されます。
* **db** ディレクトリは、証明書データベースに使用します。
* **private** ディレクトリには、CA の秘密キーが格納されます。

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>手順 2 - ルート CA の構成ファイルを作成する

CA を作成する前に構成ファイルを作成し、`rootca.conf` として rootca ディレクトリに保存します。

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>手順 3 - ルート CA を作成する

まずは、rootca ディレクトリにキーと証明書署名要求 (CSR) を生成します。

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

次に、自己署名の CA 証明書を作成します。 テストが目的であれば、自己署名が適しています。 コマンド ラインでは、構成ファイルの ca_ext 拡張機能を指定します。 これらは、証明書がルート CA のものであり、証明書と証明書失効リスト (CRL) の署名に利用できることを示しています。 証明書に署名し、データベースにコミットします。

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>手順 4 - 下位 CA のディレクトリ構造を作成する

下位 CA のためのディレクトリ構造を作成します。

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>手順 5 - 下位 CA の構成ファイルを作成する

構成ファイルを作成し、subca.conf として `subca` ディレクトリに保存します。

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>手順 6 - 下位 CA を作成する

`rootca/db/serial` ファイルに、下位 CA 証明書のための新しいシリアル番号を作成します。

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>作成する下位 CA 証明書とデバイス証明書のそれぞれについて、新しいシリアル番号を作成する必要があります。 別々の証明書のシリアル番号を同じにすることはできません。

この例は、下位 CA または登録 CA を作成する方法を示したものです。 証明書の署名にはルート CA を利用できるので、下位 CA の作成は絶対に必要というわけではありません。 もっとも、下位 CA を使用すると、ルート CA がオフラインに保たれ、下位 CA がクライアント証明書を発行するという、実際の証明書階層を模倣できます。

キーと証明書署名要求 (CSR) の生成には、構成ファイルを使用します。

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

CSR をルート CA に提出し、ルート CA を使って下位 CA の証明書を発行および署名します。 コマンド ラインでは、拡張機能のスイッチに sub_ca_ext を指定します。 この拡張機能は、その証明書が証明書と証明書失効リスト (CRL) に署名できる CA のものであることを示します。 プロンプトが表示されたら、証明書に署名し、データベースにコミットします。

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>手順 7 - 所有証明を行う

ルート CA の証明書と下位 CA の証明書を両方とも作成できました。 そのいずれかを使用して、デバイス証明書に署名することができます。 選択した方を、IoT ハブにアップロードする必要があります。 以降の手順では、下位 CA 証明書の使用を前提としています。 IoT ハブに下位 CA 証明書をアップロードおよび登録するには:

1. Azure portal で自分の IoT ハブに移動し、 **[設定] > [証明書]** の順に選択します。

1. **[追加]** を選択して、新しい下位 CA 証明書を追加します。

1. **[証明書名]** フィールドに表示名を入力し、前に作成した PEM 証明書ファイルを選択します。

1. **[保存]** を選択します。 証明書が、証明書の一覧に **[未確認]** の状態で表示されます。 証明書の所有者であることは、確認プロセスで証明します。

   
1. 証明書を選択して、 **[証明書の詳細]** ダイアログを表示します。

1. **[確認コードを生成します]** を選択します。 詳細については、[CA 証明書の所有証明](tutorial-x509-prove-possession.md)に関するページを参照してください。

1. 確認コードをクリップボードにコピーします。 証明書のサブジェクトとして確認コードを設定する必要があります。 たとえば、確認コードが BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A であれば、手順 9 に示すように、それを証明書のサブジェクトとして追加します。

1. 秘密キーを作成します。

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. 秘密キーから、証明書署名要求 (CSR) を生成します。 証明書のサブジェクトとして確認コードを追加します。

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. ルート CA の構成ファイルと、所有証明証明書の CSR を使用して証明書を作成します。

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. **[証明書の詳細]** ビューで、新しい証明書を選択します。 PEM ファイルを探す場合には、certs フォルダーに移動します。

12. 証明書をアップロードしたら、 **[確認]** を選択します。 CA 証明書の状態が **[確認済み]** に変わるはずです。

## <a name="step-8---create-a-device-in-your-iot-hub"></a>手順 8 - IoT ハブにデバイスを作成する

Azure portal で自分の IoT ハブに移動し、次の値を使って新しい IoT デバイス ID を作成します。

1. デバイス証明書のサブジェクト名と一致する **[デバイス ID]** を指定します。

1. 認証タイプは **[X.509 CA 署名済み]** を選択します。

1. **[保存]** を選択します。

## <a name="step-9---create-a-client-device-certificate"></a>手順 9 - クライアント デバイス証明書を作成する

クライアント証明書を生成するには、まず秘密キーを生成する必要があります。 次のコマンドは、OpenSSL を使って秘密キーを作成する方法を示したものです。 キーは subca ディレクトリに作成します。

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

キーの証明書署名要求 (CSR) を作成します。 チャレンジ パスワードやオプションの会社名を入力する必要はありません。 ただし、Common Name フィールドにデバイス ID を入力する必要があります。

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

CSR が期待どおりのものであることを確認します。

```bash
openssl req -text -in device.csr -noout
```

署名のうえ証明書の階層に追加するために、下位 CA に CSR を送信します。 `-extensions` スイッチでは、`client_ext` を指定します。 発行された証明書の `Basic Constraints` を見ると、この証明書が CA のものでないことがわかります。 複数の証明書に署名する場合は、それぞれの証明書を生成する前に必ず openssl `rand -hex 16 > db/serial` コマンドを使用して、シリアル番号を更新してください。

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>次の手順

「[証明書認証のテスト](tutorial-x509-test-certificate.md)」を参照し、証明書を使ってデバイスを IoT ハブに認証できるかどうかを確認します。
