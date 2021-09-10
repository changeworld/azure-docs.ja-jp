---
title: サポートされている分類の一覧
description: このページでは、Azure Purview でサポートされているシステム分類の一覧を示します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: 9eed079b7beda59f4835bb1263a226e2aa7f74c2
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272622"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure Purview でサポートされている分類

この記事では、Azure Purview (プレビュー) でサポートおよび定義されているシステム分類の一覧を示します。


- **[Distinct match threshold] (個別の一致のしきい値)** : スキャナーがデータ パターンを実行する前に、列に含まれている必要がある個別のデータ値の合計数です。 個別のデータのしきい値は、パターン マッチングとは関係ありませんが、パターン マッチングの前提条件です。 Microsoft のシステム分類ルールでは、分類の対象とする各列に、少なくとも 8 つの個別の値が必要です。 システムでは、この値を使用して、スキャナーが正確に分類するのに十分なデータが列に含まれていることを確認する必要があります。 たとえば、すべて値 1 が含まれている複数の行を含む列は分類されません。 1 つの行に値が含まれ、残りの行に null 値が含まれている列の場合も分類されません。 複数のパターンを指定すると、この値がそれぞれに適用されます。

- **[Minimum match threshold]\(最小の一致のしきい値\)** : これは、分類を適用するためにスキャナーによって検出される必要がある列の、データ値の最小一致率です。 システム分類の値は 60% に設定されています。


## <a name="defined-system-classifications"></a>定義済みのシステム分類

Azure Purview では、[RegEx](https://wikipedia.org/wiki/Regular_expression) と[ブルーム フィルター](https://wikipedia.org/wiki/Bloom_filter)でデータが分類されます。 次の一覧では、Azure Purview で定義されているシステム分類の形式、パターン、キーワードについて説明します。

各分類名には、MICROSOFT がプレフィックスとして付けられます。

## <a name="bloom-filter-classifications"></a>ブルーム フィルターの分類

## <a name="city-country-and-place"></a>市区町村、国、場所

市区町村、国、場所のフィルターは、データの準備に使用できる最適なデータセットを使用して準備されています。

## <a name="person"></a>Person

Person ブルーム フィルターは、以下の 2 つのデータセットを使用して準備されています。

- [2010 年の米国国勢調査の姓に関するデータ (162-K エントリ)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [一般的な赤ちゃんの名前 (SSN から)、1880 年から 2019 年のすべての年を使用 (98-K エントリ)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx の分類

## <a name="aba-routing"></a>ABA ルーティング

### <a name="format"></a>Format

9 桁の数字 (書式設定されたパターンまたは書式設定されていないパターン)

### <a name="pattern"></a>Pattern

書式設定済み:

- 0、1、2、3、6、7、または 8 で始まる 4 桁の数字
- 1 つのハイフン
- 4 桁の数字
- 1 つのハイフン
- 1 桁の数字

書式設定なし: 0、1、2、3、6、7、または 8 で始まる 9 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_aba_routing"></a>Keyword\_aba\_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>アルゼンチンの国民 ID (DNI) 番号

### <a name="format"></a>Format

ピリオドの有無にかかわらず 8 桁

### <a name="pattern"></a>Pattern

8 桁の数字:

- 2 桁の数字
- 1 つの省略可能なピリオド
- 3 桁の数字
- 1 つの省略可能なピリオド
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_argentina_national_id"></a>Keyword\_argentina\_national\_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>オーストラリアの銀行口座番号

### <a name="format"></a>Format

銀行の州支店番号の有無にかかわらず、6 ～ 10 桁の数字

### <a name="pattern"></a>Pattern

口座番号は 6 ～ 10 桁の数字です。

オーストラリアの銀行の州支店番号:

- 3 桁の数字
- 1 つのハイフン
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_bank_account_number"></a>Keyword\_australia\_bank\_account\_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```
## <a name="australia-business-number"></a>オーストラリアのビジネス番号

### <a name="format"></a>Format
11 桁の数字 (省略可能な区切り記号を含む)

### <a name="pattern"></a>Pattern
11 桁の数字と省略可能な区切り記号:

- 2 桁の数字
- 省略可能なハイフンまたはスペース
- 3 桁の数字
- 省略可能なハイフンまたはスペース
- 3 桁の数字
- 省略可能なハイフンまたはスペース
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_business_number"></a>Keyword\_australia\_business\_number
```
australia business no
business number
abn#
businessid#
business id
abn
businessno#
```

## <a name="australia-company-number"></a>オーストラリアの企業番号 

### <a name="format"></a>Format
9 桁の数字と区切り記号

### <a name="pattern"></a>Pattern
9 桁の数字と区切り記号:

- 3 桁の数字
- 1 つのスペース
- 3 桁の数字
- 1 つのスペース
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_company_number"></a>Keyword\_australia\_company\_number
```
acn
australia company no
australia company no#
australia company number
australian company no
australian company no#
australian company number
```
## <a name="australia-drivers-license-number"></a>オーストラリアの運転免許証番号

### <a name="format"></a>Format
9 桁の文字と数字

### <a name="pattern"></a>Pattern
9 桁の文字と数字:

- 2 桁の数字または文字 (大文字と小文字は区別されません)
- 2 桁の数字
- 5 桁の数字または文字 (大文字と小文字は区別されません)

OR

- 省略可能な 1 ～ 2 文字 (大文字と小文字は区別されません)
- 4 ～ 9 桁の数字

OR

- 9 桁の数字または文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_drivers_license_number"></a>Keyword\_australia\_drivers\_license\_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword\_australia\_drivers\_license\_number\_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>オーストラリア メディケア番号

### <a name="format"></a>Format

10 ～ 11 桁の数字

### <a name="pattern"></a>Pattern

10 ～ 11 桁の数字:

- 最初の桁は 2 ～ 6 の範囲です
- 9 桁目はチェック ディジットです
- 10 桁目は発行番号です
- 11 桁目 (省略可能) は個人番号です

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_medicare_number"></a>Keyword\_Australia\_Medicare\_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>オーストラリアのパスポート番号

### <a name="format"></a>Format

1 文字の後に 7 桁の数字

### <a name="pattern"></a>Pattern

1 文字 (大文字と小文字は区別されません) の後に 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>オーストラリアの納税者番号

### <a name="format"></a>Format

8 ～ 9 桁の数字

### <a name="pattern"></a>Pattern

次のように、通常はスペースが含まれる 8 ～ 9 桁の数字

- 3 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 2 ～ 3 桁の数字 (最後の桁はチェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```
## <a name="austria-identity-card"></a>オーストリアの身分証明書

### <a name="format"></a>Format

文字、数字、特殊文字の 24 文字の組み合わせ

### <a name="pattern"></a>Pattern

24 文字:

- 22 文字 (大文字と小文字は区別されません)、数字、円記号、スラッシュ、またはプラス記号
- 2 文字 (大文字と小文字は区別されません)、数字、円記号、スラッシュ、プラス記号、または等号

### <a name="keywords"></a>キーワード

#### <a name="keyword_austria_eu_national_id_card"></a>Keyword\_austria\_eu\_national\_id\_card

```
identity number
national id
personalausweis republik österreich
```
## <a name="austria-tax-identification-number"></a>オーストリアの納税者番号

### <a name="format"></a>Format

9 桁の数字 (省略可能なハイフンとスラッシュを含む)

### <a name="pattern"></a>Pattern

9 桁の数字 (省略可能なハイフンとスラッシュを含む):

- 2 桁の数字
- 1 つのハイフン (省略可能)
- 3 桁の数字
- 1 つのスラッシュ (省略可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords\_austria\_eu\_tax\_file\_number

```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```
## <a name="austria-passport-number"></a>オーストラリアのパスポート番号

### <a name="format"></a>Format
1 文字の後に省略可能なスペースと 7 桁の数字

### <a name="pattern"></a>Pattern
1 文字、7 桁の数字、1 つのスペースの組み合わせ:

- 1 文字 (大文字と小文字は区別されません)
- 1 つのスペース (省略可能)
- 7 桁の数字

### <a name="keywords"></a>キーワード
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_austria_eu_passport_number"></a>Keywords\_austria\_eu\_passport\_number
```
reisepassnummer
reisepasse
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="austria-social-security-number"></a>オーストリアの社会保障番号 

### <a name="format"></a>Format
指定した形式の 10 桁の数字

### <a name="pattern"></a>Pattern
10 桁の数字:

- シリアル番号に対応する 3 桁の数字
- 1 桁のチェック ディジット
- 生年月日 (DDMMYY) に対応する 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_austria_eu_ssn_or_equivalent"></a>Keywords\_austria\_eu\_ssn\_or\_equivalent
```
austrian ssn
ehic number
ehic no
insurance code
insurancecode#
insurance number
insurance no
krankenkassennummer
krankenversicherung
socialsecurityno
socialsecurityno#
social security no
social security number
social security code
sozialversicherungsnummer
sozialversicherungsnummer#
soziale sicherheit kein
sozialesicherheitkein#
ssn#
ssn
versicherungscode
versicherungsnummer
zdravstveno zavarovanje
```

## <a name="austria-tax-identification-number"></a>オーストリアの納税者番号 

### <a name="format"></a>Format
9 桁の数字 (省略可能なハイフンとスラッシュを含む)

### <a name="pattern"></a>Pattern
9 桁の数字 (省略可能なハイフンとスラッシュを含む):

- 2 桁の数字
- 1 つのハイフン (省略可能)
- 3 桁の数字
- 1 つのスラッシュ (省略可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords\_austria\_eu\_tax\_file\_number
```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```

## <a name="austria-value-added-tax"></a>オーストリアの付加価値税 
### <a name="format"></a>Format
11 文字の英数字パターン

### <a name="pattern"></a>Pattern
11 文字の英数字パターン:

- A または a
- T または t
- 省略可能なスペース
- U または u
- 省略可能なスペース
- 2 桁または 3 桁の数字
- 省略可能なスペース
- 4 桁の数字
- 省略可能なスペース
- 1 桁または 2 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_austria_value_added_tax"></a>Keyword\_austria\_value\_added\_tax
```
vat number
vat#
austrian vat number
vat no.
vatno#
value added tax number
austrian vat
mwst
umsatzsteuernummer
mwstnummer
ust.-identifikationsnummer
umsatzsteuer-identifikationsnummer
vat identification number
atu number
uid number
```
## <a name="belgium-drivers-license-number"></a>ベルギーの運転免許証番号 

### <a name="format"></a>Format
スペースと区切り記号のない 10 桁の数字

### <a name="pattern"></a>Pattern
10 桁

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
 
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_belgium_eu_drivers_license_number"></a>Keywords\_belgium\_eu\_driver's\_license\_number
```
rijbewijs
rijbewijsnummer
führerschein
führerscheinnummer
füehrerscheinnummer
fuhrerschein
fuehrerschein
fuhrerscheinnummer
fuehrerscheinnummer
permis de conduire
numéro permis conduire
```
## <a name="belgium-national-number"></a>ベルギーの国民番号

### <a name="format"></a>Format
11 桁の数字と省略可能な区切り記号

### <a name="pattern"></a>Pattern
11 桁の数字と区切り記号:

- YY.MM.DD 形式の生年月日 (6 桁の数字と 2 つの省略可能なピリオド)
- 1 つの省略可能な区切り記号 (ドット、ダッシュ、スペース)
- 3 桁の連続する数字 (男性の場合は奇数、女性の場合は偶数)
- 1 つの省略可能な区切り記号 (ドット、ダッシュ、スペース)
- 2 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number
```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="belgium-passport-number"></a>ベルギーのパスポート番号 

### <a name="format"></a>Format
2 文字の後に 6 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 文字の後に 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_belgium_eu_passport_number"></a>Keywords\_belgium\_eu\_passport\_number
```
numéro passeport
paspoort nr
paspoort-nr
paspoortnummer
paspoortnummers
Passeport carte
Passeport livre
Pass-Nr
Passnummer
reisepass kein
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="belgium-value-added-tax-number"></a>ベルギーの付加価値税番号 
### <a name="format"></a>Format
12 文字の英数字パターン

### <a name="pattern"></a>Pattern
12 文字の英数字パターン:

- 1 文字 B または b
- 1 文字 E または e
- 1 桁の数字 0
- 1 から 9 の 1 桁の数字
- 省略可能なドットまたはハイフンまたはスペース
- 4 桁の数字
- 省略可能なドットまたはハイフンまたはスペース
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_belgium_value_added_tax_number"></a>Keyword\_belgium\_value\_added\_tax\_number
```
nº tva
vat number
vat no
numéro t.v.a
umsatzsteuer-identifikationsnummer
umsatzsteuernummer
btw
btw#
vat#
```

## <a name="brazil-cpf-number"></a>ブラジルの CPF 番号

### <a name="format"></a>Format

1 桁のチェック ディジットが含まれた 11 桁の数字 (書式設定されている場合と書式設定されていない場合があります)

### <a name="pattern"></a>Pattern

書式設定済み:

- 3 桁の数字
- 1 つのピリオド
- 3 桁の数字
- 1 つのピリオド
- 3 桁の数字
- 1 つのハイフン
- 2 桁の数字 (チェック ディジット)

書式設定なし:

- 11 桁の数字 (最後の 2 桁はチェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf
```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>ブラジルの法人番号 (CNPJ)

### <a name="format"></a>Format

登録番号、分岐番号、チェック ディジット、区切り記号を含む 14 桁の数字

### <a name="pattern"></a>Pattern

14 桁の数字と区切り記号:

- 2 桁の数字
- 1 つのピリオド
- 3 桁の数字
- 1 つのピリオド
- 3 桁の数字 (最初の 8 桁は登録番号です)
- スラッシュ
- 4 桁の分岐番号
- 1 つのハイフン
- 2 桁の数字 (チェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj
```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>ブラジルの国民識別カード (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Pattern

:::no-loc text="Registro Geral (old format):":::

- 2 桁の数字
- 1 つのピリオド
- 3 桁の数字
- 1 つのピリオド
- 3 桁の数字
- 1 つのハイフン
- 1 桁の数字 (チェック ディジット)

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 桁
- 1 つのハイフン
- 1 桁の数字 (チェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg
```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```
## <a name="bulgaria-passport-number"></a>ブルガリアのパスポート番号 

### <a name="format"></a>Format
9 桁の数字
### <a name="pattern"></a>Pattern
スペースと区切り記号のない 9 桁の数字
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_bulgaria_eu_passport_number"></a>Keywords\_bulgaria\_eu\_passport\_number
```
номер на паспорта
номер на паспорт
паспорт №
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="bulgaria-uniform-civil-number"></a>ブルガリアの統一市民番号

### <a name="format"></a>Format
スペースと区切り記号のない 10 桁の数字

### <a name="pattern"></a>Pattern
スペースと区切り記号のない 10 桁の数字

- 生年月日 (DDMMYY) に対応する 6 桁の数字
- 出生順位に対応する 2 桁の数字
- 性別に対応する 1 桁の数字: 男性の場合は偶数、女性の場合は奇数
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_bulgaria_eu_national_id_card"></a>Keywords\_bulgaria\_eu\_national\_id\_card
```
bnn#
bnn
bucn#
bucn
edinen grazhdanski nomer
egn#
egn
identification number
national id
national number
nationalnumber#
nationalnumber
personal id
personal no
personal number
personalidnumber#
social security number
ssn#
ssn
uniform civil id
uniform civil no
uniform civil number
uniformcivilno#
uniformcivilno
uniformcivilnumber#
uniformcivilnumber
unique citizenship number
егн#
егн
единен граждански номер
идентификационен номер
личен номер
лична идентификация
лично не
национален номер
номер на гражданството
униформ id
униформ граждански id
униформ граждански не
униформ граждански номер
униформгражданскиid#
униформгражданскине.#
```

## <a name="canada-bank-account-number"></a>カナダの銀行口座番号

### <a name="format"></a>Format

7 桁または 12 桁の数字

### <a name="pattern"></a>Pattern

カナダの銀行口座番号は 7 桁または 12 桁です。

カナダの銀行口座番号は次のとおりです。

- 5 桁の数字
- 1 つのハイフン
- 3 桁の数字、または
- 1 桁のゼロ &quot;0&quot;
- 8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number
```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>カナダの運転免許証番号

### <a name="format"></a>Format

州によって異なる

### <a name="pattern"></a>Pattern

アルバータ、ブリティッシュコロンビア、マニトバ、ニュー ブランズウィック、ニューファンドランド/ラブラドル、ノバ スコシア、オンタリオ、プリンス エドワード島、ケベック、サスカチュワンをカバーするさまざまなパターン

### <a name="keywords"></a>キーワード

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[province\_name]\_drivers\_license\_name
```
- The province abbreviation, for example AB
- The province name, for example Alberta
```
#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>カナダの医療サービス番号

### <a name="format"></a>Format

10 桁

### <a name="pattern"></a>Pattern

10 桁

### <a name="keywords"></a>キーワード

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>カナダのパスポート番号

### <a name="format"></a>Format

2 つの大文字の後に 6 桁の数字

### <a name="pattern"></a>Pattern

2 つの大文字の後に 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>カナダの個人保健識別番号 (PHIN)

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>カナダの社会保険番号

### <a name="format"></a>Format

9 桁の数字 (省略可能なハイフンまたはスペースを含む)

### <a name="pattern"></a>Pattern

書式設定済み:

- 3 桁の数字
- 1 つのハイフンまたはスペース
- 3 桁の数字
- 1 つのハイフンまたはスペース
- 3 桁の数字

書式設定なし: 9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_sin"></a>Keyword\_sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>チリの身分証明書番号

### <a name="format"></a>Format

7 ～ 8 桁の数字と区切り記号、1 つのチェック ディジットまたは文字

### <a name="pattern"></a>Pattern

7 ～ 8 桁の数字と区切り記号:

- 1 ～ 2 桁の数字
- 1 つの省略可能なピリオド
- 3 桁の数字
- 1 つの省略可能なピリオド
- 3 桁の数字
- 1 つのダッシュ
- チェック ディジットである 1 桁の数字または文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>中国の在留身分証明書 (PRC) 番号

### <a name="format"></a>Format

18 桁の数字

### <a name="pattern"></a>Pattern

18 桁の数字:

- 6 桁の数字 (住所コード)
- YYYYMMDD 形式の 8 桁の数字 (生年月日)
- 3 桁の数字 (順序コード)
- 1 桁の数字 (チェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>クレジット カード番号

### <a name="format"></a>Format

14 ～ 16 桁の数字。書式設定されている場合と書式設定されていない場合があります (dddddddddddddddd)。また、Luhn テストに合格する必要があります。

### <a name="pattern"></a>Pattern

Visa、MasterCard、Discover Card、JCB、American Express、ギフト カード、ディナー カードなど、世界中のすべての主要ブランドのカードを検出する、複雑で堅牢なパターン。

### <a name="keywords"></a>キーワード

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>クロアチアの運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプでのみ使用できます。

### <a name="format"></a>Format

スペースと区切り記号のない 8 桁の数字

### <a name="pattern"></a>Pattern

8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>クロアチアの身分証明書番号

この機密情報タイプのエンティティは、EU の国民識別番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="croatia-passport-number"></a>クロアチアのパスポート番号
### <a name="format"></a>Format
スペースと区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern
9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_croatia_eu_passport_number"></a>Keywords\_croatia\_eu\_passport\_number
```
broj putovnice
br. Putovnice
br putovnice
```
## <a name="croatia-personal-identification-oib-number"></a>クロアチアの個人識別 (OIB) 番号

### <a name="format"></a>Format

11 桁の数字

### <a name="pattern"></a>Pattern

11 桁の数字:

- 10 桁
- 最後の桁はチェック ディジットです

### <a name="keywords"></a>キーワード

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="cyprus-drivers-license-number"></a>キプロスの運転免許証番号 

### <a name="format"></a>Format
スペースと区切り記号のない 12 桁の数字
### <a name="pattern"></a>Pattern
12 桁
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_cyprus_eu_drivers_license_number"></a>Keywords\_cyprus\_eu\_driver's\_license\_number
```
άδεια οδήγησης
αριθμό άδειας οδήγησης
άδειες οδήγησης
```
## <a name="cyprus-identity-card"></a>キプロスの身分証明書

### <a name="format"></a>Format
スペースと区切り記号のない 10 桁の数字
### <a name="pattern"></a>Pattern

10 桁
### <a name="keywords"></a>キーワード

#### <a name="keywords_cyprus_eu_national_id_card"></a>Keywords\_cyprus\_eu\_national\_id\_card
```
id card number
identity card number
kimlik karti
national identification number
personal id number
ταυτοτητασ
```
## <a name="cyprus-passport-number"></a>キプロスのパスポート番号
### <a name="format"></a>Format
1 文字の後に 6 桁から 8 桁の数字 (スペースまたは区切り記号を含まない)
### <a name="pattern"></a>Pattern
1 文字の後に 6 桁から 8 桁の数字
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_cyprus_eu_passport_number"></a>Keywords\_cyprus\_eu\_passport\_number
```
αριθμό διαβατηρίου
pasaportu
Αριθμός Διαβατηρίου
κυπριακό διαβατήριο
διαβατήριο#
διαβατήριο
αριθμός διαβατηρίου
Pasaport Kimliği
pasaport numarası
Pasaport no.
Αρ. Διαβατηρίου
```
#### <a name="keywords_cyprus_eu_passport_date"></a>Keywords\_cyprus\_eu\_passport\_date
```
expires on
issued on
```
## <a name="cyprus-tax-identification-number"></a>キプロスの納税者番号

### <a name="format"></a>Format
指定したパターンの 8 桁の数字と 1 文字

### <a name="pattern"></a>Pattern

8 桁の数字と 1 文字:

- "0" または "9"
- 7 桁の数字
- 1 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード
#### <a name="keywords_cyprus_eu_tax_file_number"></a>Keywords\_cyprus\_eu\_tax\_file\_number
```
tax id
tax identification code
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tic#
tic
tin id
tin no
tin#
vergi kimlik kodu
vergi kimlik numarası
αριθμός φορολογικού μητρώου
κωδικός φορολογικού μητρώου
φορολογική ταυτότητα
φορολογικού κωδικού
```
## <a name="czech-republic-drivers-license-number"></a>チェコ共和国の運転免許証番号 
### <a name="format"></a>Format
2 文字の後に 6 桁の数字
### <a name="pattern"></a>Pattern
8 桁の文字と数字:

- 文字 'E' (大文字と小文字は区別されません)
- 1 文字
- 1 つのスペース (省略可能)
- 6 桁の数字
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_czech_republic_eu_drivers_license_number"></a>Keywords\_czech\_republic\_eu\_driver's\_license\_number
```
řidičský prúkaz
řidičské průkazy
číslo řidičského průkazu
čísla řidičských průkazů
```

## <a name="czech-passport-number"></a>チェコ共和国のパスポート番号 
### <a name="format"></a>Format
スペースや区切り記号のない 8 桁の数字
### <a name="pattern"></a>Pattern
スペースや区切り記号のない 8 桁の数字

### <a name="keywords"></a>キーワード
#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_czech_republic_eu_passport_number"></a>Keywords\_czech\_republic\_eu\_passport\_number
```
cestovní pas
číslo pasu
cestovní pasu
passeport no
čísla pasu
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-drivers-license-number"></a>デンマークの運転免許証番号 

### <a name="format"></a>Format
スペースと区切り記号のない 8 桁の数字

### <a name="pattern"></a>Pattern
8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_denmark_eu_drivers_license_number"></a>Keywords\_denmark\_eu\_driver's\_license\_number
```
kørekort
kørekortnummer
```
## <a name="denmark-passport-number"></a>デンマークのパスポート番号 
### <a name="format"></a>Format
スペースと区切り記号のない 9 桁の数字
### <a name="pattern"></a>Pattern
9 桁の数字
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_denmark_eu_passport_number"></a>Keywords\_denmark\_eu\_passport\_number
```
pasnummer
Passeport n°
pasnumre
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-personal-identification-number"></a>デンマークの個人識別番号

### <a name="format"></a>Format

1 つのハイフンが含まれた 10 桁の数字

### <a name="pattern"></a>Pattern

10 桁の数字:

- DDMMYY 形式の 6 桁の数字 (生年月日)
- 1 つのハイフン
- 4 桁の数字 (最後の桁はチェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```
## <a name="estonia-drivers-license-number"></a>エストニアの運転免許証番号 
### <a name="format"></a>Format
2 文字の後に 6 桁の数字
### <a name="pattern"></a>Pattern
2 文字と 6 桁の数字:

- 文字 "ET" (大文字と小文字は区別されません)
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_estonia_eu_drivers_license_number"></a>Keywords\_estonia\_eu\_driver's\_license\_number
```
-- permis de conduire
juhilubade numbrid
juhiloa number
juhiluba
```
## <a name="estonia-passport-number"></a>エストニアのパスポート番号 
### <a name="format"></a>Format
1 文字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)
### <a name="pattern"></a>Pattern
1 文字の後に 7 桁の数字
### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_estonia_eu_passport_number"></a>Keywords\_estonia\_eu\_passport\_number
```
eesti kodaniku pass passi number passinumbrid document number document no dokumendi nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="estonia-personal-identification-code-isikukood"></a>エストニアの個人識別コード (isikukood)

### <a name="format"></a>Format
スペースと区切り記号のない 11 桁の数字

### <a name="pattern"></a>Pattern
11 桁の数字:

- 性別と誕生世紀に対応する 1 桁の数字 (奇数は男性、偶数は女性、1 から 2: 19 世紀、3 から 4: 20 世紀、5 から 6: 21 世紀)
- 生年月日 (YYMMDD) に対応する 6 桁の数字
- 同じ日に生まれた人を区別するシリアル番号に対応する 3 桁の数字
- 1 桁のチェック ディジット
### <a name="keywords"></a>キーワード
#### <a name="keywords_estonia_eu_national_id_card"></a>Keywords\_estonia\_eu\_national\_id\_card
```
id-kaart
ik
isikukood#
isikukood
maksu id
maksukohustuslase identifitseerimisnumber
maksunumber
national identification number
national number
personal code
personal id number
personal identification code
personal identification number
personalidnumber#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="eu-debit-card-number"></a>EU デビット カード番号

### <a name="format"></a>Format

16 桁の数字

### <a name="pattern"></a>Pattern

複雑で堅牢なパターン

### <a name="keywords"></a>キーワード

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>EU の運転免許証番号

これらのエンティティは、EU の運転免許証番号の機密情報タイプです。

- オーストリア
- ベルギー
- ブルガリア
- クロアチア
- キプロス
- チェコ語
- デンマーク
- エストニア
- フィンランド
- フランス
- ドイツ
- ギリシャ
- ハンガリー
- アイルランド
- イタリア
- ラトビア
- リトアニア
- ルクセンブルグ
- マルタ
- オランダ
- ポーランド
- ポルトガル
- ルーマニア
- スロバキア
- スロベニア
- スペイン
- スウェーデン
- 英国

## <a name="eu-national-identification-number"></a>EU の国民識別番号

これらのエンティティは、EU の国民識別番号の機密情報タイプです。

- オーストリア
- ベルギー
- ブルガリア
- クロアチア
- キプロス
- チェコ語
- デンマーク
- エストニア
- フィンランド
- フランス
- ドイツ
- ギリシャ
- ハンガリー
- アイルランド
- イタリア
- ラトビア
- リトアニア
- ルクセンブルグ
- マルタ
- オランダ
- ポーランド
- ポルトガル
- ルーマニア
- スロバキア
- スロベニア
- スペイン
- 英国

## <a name="eu-passport-number"></a>EU のパスポート番号

これらのエンティティは、EU のパスポート番号の機密情報タイプです。これらのエンティティは、EU のパスポート番号バンドルです。

- オーストリア
- ベルギー
- ブルガリア
- クロアチア
- キプロス
- チェコ語
- デンマーク
- エストニア
- フィンランド
- フランス
- ドイツ
- ギリシャ
- ハンガリー
- アイルランド
- イタリア
- ラトビア
- リトアニア
- ルクセンブルグ
- マルタ
- オランダ
- ポーランド
- ポルトガル
- ルーマニア
- スロバキア
- スロベニア
- スペイン
- スウェーデン
- 英国

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU の社会保障番号または同等の ID

これらのエンティティは、EU の社会保障番号または同等の ID の機密情報タイプです。

- オーストリア
- ベルギー
- クロアチア
- チェコ語
- デンマーク
- フィンランド
- フランス
- ドイツ
- ギリシャ
- ハンガリー
- ポルトガル
- スペイン
- スウェーデン

## <a name="eu-tax-identification-number"></a>EU 納税者番号

これらのエンティティは、EU 納税者番号の機密情報タイプです。

- オーストリア
- ベルギー
- ブルガリア
- クロアチア
- キプロス
- チェコ語
- デンマーク
- エストニア
- フィンランド
- フランス
- ドイツ
- ギリシャ
- ハンガリー
- アイルランド
- イタリア
- ラトビア
- リトアニア
- ルクセンブルグ
- マルタ
- オランダ
- ポーランド
- ポルトガル
- ルーマニア
- スロバキア
- スロベニア
- スペイン
- スウェーデン
- 英国

## <a name="finland-drivers-license-number"></a>フィンランドの運転免許証番号 

### <a name="format"></a>Format
1 つのハイフンが含まれた 10 桁の数字

### <a name="pattern"></a>Pattern
1 つのハイフンが含まれた 10 桁の数字:

- 6 桁の数字
- 1 つのハイフン
- 3 桁の数字
- 1 桁の数字または文字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_finland_eu_drivers_license_number"></a>Keywords\_finland\_eu\_driver's\_license\_number
```
ajokortti
permis de conduire
ajokortin numero
kuljettaja lic.
körkort
körkortnummer
förare lic.
ajokortit
ajokortin numerot
```
## <a name="finland-european-health-insurance-number"></a>フィンランドの欧州健康保険番号

### <a name="format"></a>Format
20 桁の数字
### <a name="pattern"></a>Pattern
20 桁の数字
- 10 桁の数字 - 8024680246
- 1 つの省略可能なスペースまたはハイフン
- 10 桁

### <a name="keywords"></a>キーワード
#### <a name="keyword_finland_european_health_insurance_number"></a>Keyword\_finland\_european\_health\_insurance\_number
```
ehic#
ehic
finlandehicnumber#
finska sjukförsäkringskort
health card
health insurance card
health insurance number
hälsokort
sairaanhoitokortin
sairausvakuutuskortti
sairausvakuutusnumero
sjukförsäkring nummer
sjukförsäkringskort
suomen sairausvakuutuskortti
terveyskortti
```
## <a name="finland-national-id"></a>フィンランドの国民 ID

### <a name="format"></a>Format

6 桁の数字、世紀を示す文字、3 桁の数字、1 桁のチェック ディジット

### <a name="pattern"></a>Pattern

パターンには次の値をすべて含める必要があります。

- DDMMYY 形式の 6 桁の数字 (生年月日)
- 世紀マーカー ('-'、'+'、'a' のいずれか)
- 3 桁の数字 (個人識別番号)
- チェック ディジットである 1 桁の数字または文字 (大文字と小文字が区別されます)

### <a name="keywords"></a>キーワード

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>フィンランドのパスポート番号

この機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

9 桁の文字と数字の組み合わせ

### <a name="pattern"></a>Pattern

9 桁の文字と数字の組み合わせ:

- 2 文字 (大文字と小文字は区別されません)
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>フランスの運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

12 桁

### <a name="pattern"></a>Pattern

フランスの電話番号など類似のパターンに対する検証を考慮した 12 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```
## <a name="france-health-insurance-number"></a>フランスの健康保険番号
### <a name="format"></a>Format
21 桁の数字
### <a name="pattern"></a>Pattern
21 桁の数字:

- 10 桁
- 1 つの省略可能なスペース
- 10 桁
- 1 つの省略可能なスペース
- 1 桁の数字
### <a name="keywords"></a>キーワード
#### <a name="keyword_france_health_insurance_number"></a>Keyword\_France\_health\_insurance\_number
```
insurance card
carte vitale
carte d'assuré social
```
## <a name="france-national-id-card-cni"></a>フランスの国民 ID カード (CNI)

### <a name="format"></a>Format

12 桁

### <a name="pattern"></a>Pattern

12 桁

### <a name="keywords"></a>キーワード

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>フランスのパスポート番号

この機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

9 桁の数字と文字

### <a name="pattern"></a>Pattern

9 桁の数字と文字:

- 2 桁の数字
- 2 文字 (大文字と小文字は区別されません)
- 5 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>フランスの社会保障番号 (INSEE) または同等の ID

この機密情報タイプのエンティティは、EU の社会保障番号とその同等の ID の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

15 桁

### <a name="pattern"></a>Pattern

次の 2 つのパターンのいずれかに一致する必要があります。

- 13 桁の数字、1 つのスペースの後に 2 桁の数字、または
- 15 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```
## <a name="france-tax-identification-number-numro-spi"></a>フランスの納税者番号 (numéro SPI) 
### <a name="format"></a>Format 
13 桁の数字
### <a name="pattern"></a>Pattern
13 桁の数字

- 0、1、2、または 3 である必要がある 1 桁の数字
- 1 桁の数字
- 1 つのスペース (省略可能)
- 2 桁の数字
- 1 つのスペース (省略可能)
- 3 桁の数字
- 1 つのスペース (省略可能)
- 3 桁の数字
- 1 つのスペース (省略可能)
- 3 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_france_eu_tax_file_number"></a>Keywords\_france\_eu\_tax\_file\_number
```
numéro d'identification fiscale
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="france-value-added-tax-number"></a>フランスの付加価値税番号 

### <a name="format"></a>Format 
13 文字の英数字パターン

### <a name="pattern"></a>Pattern
13 文字の英数字パターン:

- 2 文字 - FR (大文字と小文字は区別されません)
- 1 つの省略可能なスペースまたはハイフン
- 2 桁の文字または数字
- 1 つの省略可能なスペース、ドット、ハイフン、またはコンマ
- 3 桁の数字
- 1 つの省略可能なスペース、ドット、ハイフン、またはコンマ
- 3 桁の数字
- 1 つの省略可能なスペース、ドット、ハイフン、またはコンマ
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_france_value_added_tax_number"></a>Keyword\_France\_value\_added\_tax\_number
```
vat number
vat no
vat#
value added tax
siren identification no numéro d'identification taxe sur valeur ajoutée
taxe valeur ajoutée
taxe sur la valeur ajoutée
n° tva
numéro de tva
numéro d'identification siren
```
## <a name="germany-drivers-license-number"></a>ドイツの運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

11 桁の数字と文字の組み合わせ

### <a name="pattern"></a>Pattern

11 桁の数字と文字 (大文字と小文字は区別されません)

- 1 桁の数字または文字
- 2 桁の数字
- 6 桁の数字または文字
- 1 桁の数字
- 1 桁の数字または文字

### <a name="keywords"></a>キーワード

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>ドイツの身分証明書番号

### <a name="format"></a>Format

2010 年 11 月 1 日以降:9 桁の文字と数字

1987 年 4 月 1 日から 2010 年 10 月 31 日まで:10 桁の数字

### <a name="pattern"></a>Pattern

2010 年 11 月 1 日以降:

- 1 文字 (大文字と小文字は区別されません)
- 8 桁の数字

1987 年 4 月 1 日から 2010 年 10 月 31 日まで:

- 10 桁

### <a name="keywords"></a>キーワード

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>ドイツのパスポート番号

この機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

10 桁の数字または文字

### <a name="pattern"></a>Pattern

パターンには次の値をすべて含める必要があります。

- 最初の文字は、数字または次のセット (C、F、G、H、J、K) の文字です
- 3 桁の数字
- 5 桁の数字または次のセット (C、～ H、J ～ N、P、R、T、V ～ Z) の文字
- 1 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```
## <a name="germany-tax-identification-number"></a>ドイツの納税者番号 

### <a name="format"></a>Format 
スペースと区切り記号のない 11 桁の数字

### <a name="pattern"></a>Pattern
11 桁の数字

- 2 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 2 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_germany_eu_tax_file_number"></a>Keywords\_germany\_eu_tax\_file\_number
```
identifikationsnummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnnummer
```
## <a name="germany-value-added-tax-number"></a>ドイツの付加価値税番号 

### <a name="format"></a>Format 
11 文字の英数字パターン

### <a name="pattern"></a>Pattern
11 文字の英数字パターン:

- 1 文字 D または d
- 1 文字 E または e
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペースまたはコンマ
- 3 桁の数字
- 1 つの省略可能なスペースまたはコンマ
- 3 桁の数字

### <a name="keywords"></a>キーワード 

#### <a name="keyword_germany_value_added_tax_number"></a>Keyword\_germany\_value\_added\_tax\_number
```
vat number
vat no
vat#
vat#  mehrwertsteuer
mwst
mehrwertsteuer identifikationsnummer
mehrwertsteuer nummer
```
## <a name="greece-drivers-license-number"></a>ギリシャの運転免許証番号 
スペースと区切り記号のない 9 桁の数字

### <a name="format"></a>Format 
スペースと区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern
9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_greece_eu_drivers_license_number"></a>Keywords\_greece\_eu\_driver's\_license\_number
```
δεια οδήγησης
Adeia odigisis
Άδεια οδήγησης
Δίπλωμα οδήγησης
```
## <a name="greece-national-id-card"></a>ギリシャの国民 ID カード

### <a name="format"></a>Format

7 ～ 8 桁の文字と数字、1 つのダッシュの組み合わせ

### <a name="pattern"></a>Pattern

7 桁の文字と数字 (古い形式):

- 1 文字 (任意のギリシャ文字)
- 1 つのダッシュ
- 6 桁の数字

8 桁の文字と数字 (新しい形式):

- ギリシャ文字とラテン アルファベットの両方に大文字が含まれる 2 文字 (ABEZHIKMNOPTYX)
- 1 つのダッシュ
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```
## <a name="greece-passport-number"></a>ギリシャのパスポート番号

### <a name="format"></a>Format 
2 文字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 文字の後に 7 桁の数字

### <a name="keywords"></a>キーワード
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_greece_eu_passport_number"></a>Keywords\_greece\_eu\_passport\_number
```
αριθμός διαβατηρίου
αριθμούς διαβατηρίου
αριθμός διαβατηριο
```
## <a name="greece-social-security-number-amka"></a>ギリシャの社会保障番号 (AMKA)

### <a name="format"></a>Format 
スペースと区切り記号のない 11 桁の数字

### <a name="pattern"></a>Pattern
- 生年月日 YYMMDD としての 6 桁の数字
- 4 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_greece_eu_ssn_or_equivalent"></a>Keywords\_greece\_eu\_ssn\_or\_equivalent
```
- ssn
- ssn#
- social security no
- socialsecurityno#
- social security number
- amka
- a.m.k.a.
- Αριθμού Μητρώου Κοινωνικής Ασφάλισης
```
## <a name="greece-tax-identification-number"></a>ギリシャの納税者番号

### <a name="format"></a>Format 
スペースと区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern
9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_greece_eu_tax_file_number"></a>Keywords\_greece\_eu\_tax\_file\_number
```
afm#
afm
aφμ|aφμ αριθμός
aφμ
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax registry no
tax registry number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
taxregistryno#
tin id
tin no
tin#
αριθμός φορολογικού μητρώου
τον αριθμό φορολογικού μητρώου
φορολογικού μητρώου νο
```
## <a name="hong-kong-identity-card-hkid-number"></a>香港特別行政区の身分証明書 (HKID) 番号

### <a name="format"></a>Format

8 ～ 9 桁の文字と数字、最後の文字を囲む省略可能なかっこの組み合わせ

### <a name="pattern"></a>Pattern

8 ～ 9 文字の組み合わせ:

- 1 ～ 2 文字 (大文字と小文字は区別されません)
- 6 桁の数字
- 最後の文字 (任意の数字または文字 A)。これはチェック ディジットであり、オプションでかっこで囲みます。

### <a name="keywords"></a>キーワード

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```
## <a name="hungary-drivers-license-number"></a>ハンガリーの運転免許証番号 

### <a name="format"></a>Format 
2 文字の後に 6 桁の数字

### <a name="pattern"></a>Pattern
2 文字と 6 桁の数字:

- 2 文字 (大文字と小文字は区別されません)
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_hungary_eu_drivers_license_number"></a>Keywords\_hungary\_eu\_driver's\_license\_number
```
vezetoi engedely
vezetői engedély
vezetői engedélyek
```
## <a name="hungary-passport-number"></a>ハンガリーのパスポート番号 

### <a name="format"></a>Format 
2 文字の後に 6 桁または 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 文字の後に 6 桁または 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_hungary_eu_passport_number"></a>Keywords\_hungary\_eu\_passport\_number
```
útlevél száma
Útlevelek száma
útlevél szám
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
有効期限の発行日

## <a name="hungary-personal-identification-number"></a>ハンガリーの個人識別番号 

### <a name="format"></a>Format 
11 桁の数字

### <a name="pattern"></a>Pattern
11 桁の数字:

- 性別に対応する 1 桁の数字、男性の場合 1、女性の場合 2 です。 1900 年より前に生まれた市民や二重市民権を持つ市民に対して、他の番号も可能です。
- 生年月日 (YYMMDD) に対応する 6 桁の数字
- シリアル番号に対応する 3 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_hungary_eu_national_id_card"></a>Keywords\_hungary\_eu\_national\_id\_card
```
id number
identification number
sz ig
sz. ig.
sz.ig.
személyazonosító igazolvány
személyi igazolvány
```
## <a name="hungary-social-security-number-taj"></a>ハンガリーの社会保障番号 (TAJ) 

### <a name="format"></a>Format 
スペースと区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern
9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_hungary_eu_ssn_or_equivalent"></a>Keywords\_hungary\_eu\_ssn\_or\_equivalent
```
- hungarian social security number
- social security number
- socialsecuritynumber#
- hssn#
- socialsecuritynno
- hssn
- taj
- taj#
- ssn
- ssn#
- social security no
- áfa
- közösségi adószám
- általános forgalmi adó szám
- hozzáadottérték adó
- áfa szám
- magyar áfa szám
```
## <a name="hungary-value-added-tax-number"></a>ハンガリーの付加価値税番号 

### <a name="format"></a>Format 
10 文字の英数字パターン

### <a name="pattern"></a>Pattern
10 文字の英数字パターン:

- 2 文字 - HU または hu
- 省略可能なスペース
- 8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_hungary_value_added_tax_number"></a>Keyword\_Hungary\_value\_added\_tax\_number
```
vat
value added tax number
vat#
vatno#
hungarianvatno#
tax no.
value added tax áfa
közösségi adószám
általános forgalmi adó szám
hozzáadottérték adó
áfa szám
```

## <a name="hungary-tax-identification-number"></a>ハンガリーの納税者番号 

### <a name="format"></a>Format 
スペースまたは区切り記号がない 10 桁の数字

### <a name="pattern"></a>Pattern
10 桁の数字:

- "8" である必要がある 1 桁の数字
- 8 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_hungary_eu_tax_file_number"></a>Keywords\_hungary\_eu\_tax\_file\_number
```
adóazonosító szám
adóhatóság szám
adószám
hungarian tin
hungatiantin#
tax authority no
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat number
```
## <a name="ip-address"></a>IP アドレス

### <a name="format"></a>Format

#### <a name="ipv4"></a>IPv4:

書式設定されたバージョン (ピリオドあり) および書式設定されていないバージョン (ピリオドなし) の IPv4 アドレスを示す複雑なパターン

#### <a name="ipv6"></a>IPv6 : よく寄せられる質問 ◆セグ : 次の TU を含む◇

書式設定された IPv6 番号 (コロンを含む) を示す複雑なパターン

### <a name="pattern"></a>Pattern

### <a name="keywords"></a>キーワード

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>インドの基本税務番号 (PAN)

### <a name="format"></a>Format

10 桁の文字または数字

### <a name="pattern"></a>Pattern

10 桁の文字または数字:

- 3 文字 (大文字と小文字は区別されません)
- C、P、H、F、A、T、B、L、J、G の 1 文字 (大文字と小文字は区別されません)
- 1 文字
- 4 桁の数字
- 1 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>インドの一意識別 (Aadhaar) 番号

### <a name="format"></a>Format

省略可能なスペースまたはダッシュを含む 12 桁の数字

### <a name="pattern"></a>Pattern

12 桁の数字:

- 1 桁の数字 (0 または 1 以外)
- 3 桁の数字
- 1 つの省略可能なスペースまたはダッシュ
- 4 桁の数字
- 1 つの省略可能なスペースまたはダッシュ
- 最後の数字 (チェック ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>インドネシアの身分証明書 (KTP) 番号

### <a name="format"></a>Format

省略可能なピリオドを含む 16 桁の数字

### <a name="pattern"></a>Pattern

16 桁の数字:

- 2 桁の数字 (州コード)
- 1 つのピリオド (省略可能)
- 2 桁の数字 (摂政管区または都市コード)
- 2 桁の数字 (小区域コード)
- 1 つのピリオド (省略可能)
- DDMMYY 形式の 6 桁の数字 (生年月日)
- 1 つのピリオド (省略可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN (International Banking Account Number)

### <a name="format"></a>Format

国番号 (2 文字)、チェック ディジット (2 桁の数字)、:::no-loc text="bban"::: 番号 (最大 30 文字)

### <a name="pattern"></a>Pattern

パターンには次の値をすべて含める必要があります。

- 2 文字の国番号
- 2 桁のチェック ディジット (後に省略可能な 1 つのスペースが続きます)
- 4 桁の文字または数字、1 ～ 7 グループ (スペースで区切ることができます)
- 1 ～ 3 桁の文字または数字

国ごとに形式が少し異なります。 IBAN の機密情報タイプは、次の 60 か国に該当します。

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>キーワード

なし

## <a name="ireland-drivers-license-number"></a>アイルランドの運転免許証番号 

### <a name="format"></a>Format 
6 桁の数字の後に 4 文字

### <a name="pattern"></a>Pattern
6 桁の数字と 4 文字:

- 6 桁の数字
- 4 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_ireland_eu_drivers_license_number"></a>Keywords\_ireland\_eu\_driver's\_license\_number
```
ceadúnas tiomána
ceadúnais tiomána 
```
## <a name="ireland-passport-number"></a>アイルランドのパスポート番号 


### <a name="format"></a>Format 
2 桁の文字または数字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 桁の文字または数字の後に 7 桁の数字:

- 2 桁の数字または文字 (大文字と小文字は区別されません)
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_ireland_eu_passport_number"></a>Keywords\_ireland\_eu\_passport\_number
```
passeport numero
uimhreacha pasanna
uimhir pas
uimhir phas
uimhreacha pas
uimhir cárta
uimhir chárta
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="ireland-personal-public-service-pps-number"></a>アイルランドの個人公共サービス (PPS) 番号

### <a name="format"></a>Format

古い形式 (2012 年 12 月 31 日まで):

- 7 桁の数字の後に 1 ～ 2 文字

新しい形式 (2013 年 1 月 1 日以降):

- 7 桁の数字の後に 2 文字

### <a name="pattern"></a>Pattern

古い形式 (2012 年 12 月 31 日まで):

- 7 桁の数字
- 1 ～ 2 文字 (大文字と小文字は区別されません)

新しい形式 (2013 年 1 月 1 日以降):

- 7 桁の数字
- アルファベットのチェック ディジットである 1 文字 (大文字と小文字は区別されません)
- 省略可能な 1 文字 (A ～ I の範囲または &quot;W&quot;)

### <a name="keywords"></a>キーワード

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>イスラエルの銀行口座番号

### <a name="format"></a>Format

13 桁の数字

### <a name="pattern"></a>Pattern

書式設定済み:

- 2 桁の数字
- 1 つのダッシュ
- 3 桁の数字
- 1 つのダッシュ
- 8 桁の数字

書式設定なし:

- 13 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>イスラエルの国民識別番号

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>イタリアの運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

10 桁の文字と数字の組み合わせ

### <a name="pattern"></a>Pattern

10 桁の文字と数字の組み合わせ:

- 1 文字 (大文字と小文字は区別されません)
- 文字 &quot;A&quot; または &quot;V&quot; (大文字と小文字は区別されません)
- 7 桁の数字
- 1 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```
## <a name="italy-fiscal-code"></a>イタリアの個人納税者番号 

### <a name="format"></a>Format 
指定したパターンの 16 文字の文字と数字の組み合わせ

### <a name="pattern"></a>Pattern
16 文字の文字と数字の組み合わせ:

- 姓の先頭の 3 つの子音に対応する 3 文字
- 名の 1 番目、3 番目、および 4 番目の子音に対応する 3 文字
- 誕生年の末尾の桁に対応する 2 桁の数字
- 誕生月の文字に対応する 1 文字。文字はアルファベット順に使用されますが、A から E、H、L、M、P、R から T の文字のみが使用されます (つまり、1 月は A、10 月は R)
- 性別を区別するために、誕生日の日に対応する 2 桁の数字で、女性の誕生日には 40 が追加されます
- 人が出生した地方自治体に固有の市外局番に対応する 4 桁の数字 (外国の場合は全国規模のコードが使われます)
- 1 桁のパリティ数字 

### <a name="keywords"></a>キーワード

#### <a name="keywords_italy_eu_national_id_card"></a>Keywords\_italy\_eu\_national\_id\_card
```
codice fiscal
codice fiscale
codice id personale
codice personale
fiscal code
numero certificato personale
numero di identificazione fiscale
numero id personale
numero personale
personal certificate number
personal code
personal id code
personal id number
personalcodeno#
tax code
tax id
tax identification no
tax identification number
tax identity number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="italy-passport-number"></a>イタリアのパスポート番号 

### <a name="format"></a>Format 
2 桁の文字または数字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 桁の文字または数字の後に 7 桁の数字:

- 2 桁の数字または文字 (大文字と小文字は区別されません)
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_italy_eu_passport_number"></a>Keywords\_italy\_eu\_passport\_number
```
italiana passaporto
passaporto italiana
passaporto numero
numéro passeport
numero di passaporto
numeri del passaporto
passeport italien
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="italy-value-added-tax"></a>イタリアの付加価値税 

### <a name="format"></a>Format 
省略可能な区切り記号を含む 13 文字の英数字パターン

### <a name="pattern"></a>Pattern
省略可能な区切り記号を含む 13 文字の英数字パターン:

- I または i
- T または t
- 省略可能なスペース、ドット、ハイフン、またはコンマ
- 11 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_italy_value_added_tax_number"></a>Keyword\_italy\_value\_added\_tax\_number
```
vat number
vat no
vat#
iva
iva#
```
## <a name="japan-bank-account-number"></a>日本の銀行口座番号

### <a name="format"></a>Format

7 桁または 8 桁の数字

### <a name="pattern"></a>Pattern

銀行口座番号:

- 7 桁または 8 桁の数字
- 銀行口座の支店コード:
- 4 桁の数字
- 1 つのスペースまたはダッシュ (省略可能)
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>日本の運転免許証番号

### <a name="format"></a>Format

12 桁

### <a name="pattern"></a>Pattern

12 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```
## <a name="japanese-my-number--corporate"></a>日本のマイナンバー (企業) 


### <a name="format"></a>Format 
13 桁の数字

### <a name="pattern"></a>Pattern
13 桁の数字:

- 1 から 9 までの 1 桁の数字
- 12 桁

### <a name="keywords"></a>キーワード

#### <a name="keyword_japan_my_number_corporate"></a>Keyword\_japan\_my\_number\_corporate
```
corporate number
マイナンバー
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
法人番号
指定通知書
```
## <a name="japanese-my-number--personal"></a>日本のマイナンバー (個人) 


### <a name="format"></a>Format 
12 桁の数字

### <a name="pattern"></a>Pattern
12 桁の数字:

- 4 桁の数字
- 1 つの省略可能なスペース、ドット、またはハイフン
- 4 桁の数字
- 1 つの省略可能なスペース、ドット、またはハイフン
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_japan_my_number_personal"></a>Keyword\_japan\_my\_number\_personal
```
my number
マイナンバー
個人番号
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
通知カード
```
## <a name="japan-passport-number"></a>日本のパスポート番号

### <a name="format"></a>Format

2 文字の後に 7 桁の数字

### <a name="pattern"></a>Pattern

2 文字 (大文字と小文字は区別されません) の後に 7 桁の数字

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>日本の在留カード番号

### <a name="format"></a>Format

12 桁の文字と数字

### <a name="pattern"></a>Pattern

12 桁の文字と数字:

- 2 文字 (大文字と小文字は区別されません)
- 8 桁の数字
- 2 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>日本の住民票コード

### <a name="format"></a>Format

11 桁の数字

### <a name="pattern"></a>Pattern

11 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>日本の社会保険番号 (SIN)

### <a name="format"></a>Format

7 ～ 12 桁の数字

### <a name="pattern"></a>Pattern

7 ～ 12 桁の数字:

- 4 桁の数字
- 1 つのハイフン (省略可能)
- 6 桁の数字、または
- 7 ～ 12 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```
## <a name="latvia-drivers-license-number"></a>ラトビアの運転免許証番号

### <a name="format"></a>Format
3 文字の後に 6 桁の数字

### <a name="pattern"></a>Pattern
3 文字と 6 桁の数字:

- 3 文字 (大文字と小文字は区別されません)
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_latvia_eu_drivers_license_number"></a>Keywords\_latvia\_eu\_driver's\_license\_number
```
autovadītāja apliecība
autovadītāja apliecības
vadītāja apliecība
```
## <a name="latvia-passport-number"></a>ラトビアのパスポート番号 

### <a name="format"></a>Format 
2 桁の文字または数字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern
2 桁の文字または数字の後に 7 桁の数字:

- 2 桁の数字または文字 (大文字と小文字は区別されません)
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_latvia_eu_passport_number"></a>Keywords\_latvia\_eu\_passport\_number
```
pase numurs
pase numur
pases numuri
pases nr
passeport no
n° du Passeport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="latvia-personal-code-personas-kods-or-pic"></a>ラトビアの個人コード (Personas kods または PIC)  

### <a name="format"></a>Format 
11 桁の数字と 1 つの省略可能なハイフン

### <a name="pattern"></a>Pattern
古い形式

11 桁の数字と 1 つのハイフン:

- 生年月日 (DDMMYY) に対応する 6 桁の数字
- 1 つのハイフン
- 誕生世紀に対応する 1 桁の数字 (19 世紀の場合は "0"、20 世紀の場合は "1"、21 世紀の場合は "2")
- ランダムに生成された 4 桁の数字
- 新しい形式

11 桁の数字

- 2 桁の数字 "32"
- 9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_latvia_eu_national_id_card"></a>Keywords\_latvia\_eu\_national\_id\_card
```
administrative number
alvas nē
birth number
citizen number
civil number
electronic census number
electronic number
fiscal code
healthcare user number
id#
id-code
identification number
identifikācijas numurs
id-number
individual number
latvija alva
nacionālais id
national id
national identifying number
national identity number
national insurance number
national register number
nodokļa numurs
nodokļu id
nodokļu identifikācija numurs
personal certificate number
personal code
personal id code
personal id number
personal identification code
personal identifier
personal identity number
personal number
personal numeric code
personalcodeno#
personas kods
population identification code
public service number
registration number
revenue number
social insurance number
social security number
state tax code
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
voter’s number
```
## <a name="lithuania-drivers-license-number"></a>リトアニアの運転免許証番号 

### <a name="format"></a>Format 
スペースと区切り記号のない 8 桁の数字

### <a name="pattern"></a>Pattern
8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_lithuania_eu_drivers_license_number"></a>Keywords\_lithuania\_eu\_driver's\_license\_number
```
vairuotojo pažymėjimas
vairuotojo pažymėjimo numeris
vairuotojo pažymėjimo numeriai
```
## <a name="lithuania-passport-number"></a>リトアニアのパスポート番号 

### <a name="format"></a>Format 
スペースまたは区切り記号のない 8 桁の数字または文字

### <a name="pattern"></a>Pattern
8 桁の数字または文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_lithuania_eu_passport_number"></a>Keywords\_lithuania\_eu\_passport\_number
```
paso numeris
paso numeriai
paso nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="lithuania-personal-code-asmens-kodas"></a>リトアニアの個人コード (Asmens kodas)  

### <a name="format"></a>Format 
スペースと区切り記号のない 11 桁の数字

### <a name="pattern"></a>Pattern
スペースと区切り記号のない 11 桁の数字:

- 人の性別と誕生世紀に対応する 1 桁の数字 (1 から 6)
- 生年月日 (YYMMDD) に対応する 6 桁の数字
- 生年月日のシリアル番号に対応する 3 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_lithuania_eu_national_id_card"></a>Keywords\_lithuania\_eu\_national\_id\_card
```
asmeninis skaitmeninis kodas
asmens kodas
citizen service number
mokesčių id
mokesčių identifikavimas numeris
mokesčių identifikavimo numeris
mokesčių numeris
national identification number
personal code
personal numeric code
piliečio paslaugos numeris
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unikalus identifikavimo kodas
unikalus identifikavimo numeris
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="luxembourg-drivers-license-number"></a>ルクセンブルクの運転免許証番号 

### <a name="format"></a>Format 
スペースと区切り記号のない 6 桁の数字

### <a name="pattern"></a>Pattern
6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_luxemburg_eu_drivers_license_number"></a>Keywords\_luxemburg\_eu\_driver's\_license\_number
```
fahrerlaubnis
Führerschäin
```
## <a name="luxembourg-passport-number"></a>ルクセンブルクのパスポート番号 

### <a name="format"></a>Format 
スペースまたは区切り記号のない 8 桁の数字または文字

### <a name="pattern"></a>Pattern
8 桁の数字または文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_luxemburg_eu_passport_number"></a>Keywords\_luxemburg\_eu\_passport\_number
```
ausweisnummer
luxembourg pass
luxembourg passeport
luxembourg passport
no de passeport
no-reisepass
nr-reisepass
numéro de passeport
pass net
pass nr
passnummer
passeport nombre
reisepässe
reisepass-nr
reisepassnummer
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="luxemburg-national-identification-number---natural-persons"></a>ルクセンブルグの国民識別番号 (自然人)

### <a name="format"></a>Format 
スペースまたは区切り記号がない 13 桁の数字

### <a name="pattern"></a>Pattern
13 桁の数字:

- 11 桁の数字
- 2 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_luxemburg_eu_national_id_card"></a>Keywords\_luxemburg\_eu\_national\_id\_card
```
eindeutige id
eindeutige id-nummer
eindeutigeid#
id personnelle
idpersonnelle#
idpersonnelle
individual code
individual id
individual identification
individual identity
numéro d'identification personnel
personal id
personal identification
personal identity
personalidno#
personalidnumber#
persönliche identifikationsnummer
unique id
unique identity
uniqueidkey#
```
## <a name="luxemburg-national-identification-number---non-natural-persons"></a>ルクセンブルグの国民識別番号 (自然人以外)

### <a name="format"></a>Format 
11 桁の数字

### <a name="pattern"></a>Pattern
11 桁の数字

- 2 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 2 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords\_luxemburg\_eu\_tax\_file\_number
```
carte de sécurité sociale
étain non
étain#
identifiant d'impôt
luxembourg tax identifikatiounsnummer
numéro d'étain
numéro d'identification fiscal luxembourgeois
numéro d'identification fiscale
social security
sozialunterstützung
sozialversécherung
sozialversicherungsausweis
steier id
steier identifikatiounsnummer
steier nummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnzahl
```
## <a name="malaysia-identification-card-number"></a>マレーシアの身分証明書番号

### <a name="format"></a>Format

省略可能なハイフンを含む 12 桁の数字

### <a name="pattern"></a>Pattern

12 桁の数字:

- YYMMDD 形式の 6 桁の数字 (生年月日)
- 1 つのダッシュ (省略可能)
- 2 文字 (出生地コード)
- 1 つのダッシュ (省略可能)
- 3 桁のランダムな数字
- 1 桁の数字 (性別コード)

### <a name="keywords"></a>キーワード

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```
## <a name="malta-drivers-license-number"></a>マルタの運転免許証番号 

### <a name="format"></a>Format 
指定したパターンの 2 文字と 6 桁の数字の組み合わせ

### <a name="pattern"></a>Pattern
2 文字と 6 桁の数字の組み合わせ:

- 2 文字 (数字または文字、大文字と小文字は区別されません)
- 1 つのスペース (省略可能)
- 3 桁の数字
- 1 つのスペース (省略可能)
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_malta_eu_drivers_license_number"></a>Keywords\_malta\_eu\_driver's\_license\_number
```
liċenzja tas-sewqan
liċenzji tas-sewwieq
```
## <a name="malta-identity-card-number"></a>マルタの身分証明書番号 

### <a name="format"></a>Format 
7 桁の数字の後に 1 文字

### <a name="pattern"></a>Pattern
7 桁の数字の後に 1 文字:

- 7 桁の数字
- "M、G、A、P、L、H、B、Z" の 1 文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_malta_eu_national_id_card"></a>Keywords\_malta\_eu\_national\_id\_card
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="malta-passport-number"></a>マルタのパスポート番号 

### <a name="format"></a>Format 
スペースや区切り記号のない 7 桁の数字

### <a name="pattern"></a>Pattern
7 桁の数字

### <a name="keywords"></a>キーワード
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_malta_eu_passport_number"></a>Keywords\_malta\_eu\_passport\_number
```
numru tal-passaport
numri tal-passaport
Nru tal-passaport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="malta-tax-id-number"></a>マルタの納税者番号 

### <a name="format"></a>Format 
マルタ国民の場合:

- 指定したパターンの 7 桁の数字と 1 文字

マルタ以外の国民とマルタの事業体:

- 9 桁の数字

### <a name="pattern"></a>Pattern

マルタの国民: 7 桁の数字と 1 文字

- 7 桁の数字
- 1 文字 (大文字と小文字は区別されません)

マルタ以外の国民とマルタの事業体: 9 桁の数字

- 9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_malta_eu_tax_file_number"></a>Keywords\_malta\_eu\_tax\_file\_number
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-citizens-service-bsn-number"></a>オランダの市民サービス (BSN) 番号

### <a name="format"></a>Format

省略可能なスペースを含む 8 ～ 9 桁の数字

### <a name="pattern"></a>Pattern

8 ～ 9 桁の数字:

- 3 桁の数字
- 1 つのスペース (省略可能)
- 3 桁の数字
- 1 つのスペース (省略可能)
- 2 ～ 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-drivers-license-number"></a>オランダの運転免許証番号 

### <a name="format"></a>Format 

スペースと区切り記号のない 10 桁の数字

### <a name="pattern"></a>Pattern

10 桁

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_netherlands_eu_drivers_license_number"></a>Keywords\_netherlands\_eu\_driver's\_license\_number
```
permis de conduire
rijbewijs
rijbewijsnummer
rijbewijzen
rijbewijs nummer
rijbewijsnummers
```

## <a name="netherlands-passport-number"></a>オランダのパスポート番号 

### <a name="format"></a>Format 

スペースまたは区切り記号のない 9 桁の文字または数字

### <a name="pattern"></a>Pattern

9 桁の文字または数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_netherlands_eu_passport_number"></a>Keywords\_netherlands\_eu\_passport\_number
```
paspoort nummer
paspoortnummers
paspoortnummer
paspoort nr
```
## <a name="netherlands-tax-identification-number"></a>オランダの納税者番号 

### <a name="format"></a>Format 

スペースや区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_netherlands_eu_tax_file_number"></a>Keywords\_netherlands\_eu\_tax\_file\_number
```
btw nummer
hollânske tax identification
hulandes impuesto id number
hulandes impuesto identification
identificatienummer belasting
identificatienummer van belasting
impuesto identification number
impuesto number
nederlands belasting id nummer
nederlands belasting identificatie
nederlands belasting identificatienummer
nederlands belastingnummer
nederlandse belasting identificatie
netherlands tax identification
netherland's tax identification
netherlands tin
netherland's tin
tax id
tax identification no
tax identification number
tax identification tal
tax no#
tax no
tax number
tax registration number
tax tal
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="netherlands-value-added-tax-number"></a>オランダの付加価値税番号 

### <a name="format"></a>Format 

14 文字の英数字パターン

### <a name="pattern"></a>Pattern

14 文字の英数字パターン:

- N または n
- L または l
- 省略可能なスペース、ドット、またはハイフン
- 9 桁の数字
- 省略可能なスペース、ドット、またはハイフン
- B または b
- 2 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_netherlands_value_added_tax_number"></a>Keyword\_netherlands\_value\_added\_tax\_number
```
vat number
vat no
vat#
wearde tafoege tax getal
btw nûmer
btw-nummer
```
## <a name="new-zealand-bank-account-number"></a>ニュージーランドの銀行口座番号 

### <a name="format"></a>Format 

省略可能な区切り記号を含む 14 桁から 16 桁のパターン

### <a name="pattern"></a>Pattern

省略可能な区切り記号を含む 14 桁から 16 桁のパターン:

- 2 桁の数字
- 省略可能なハイフンまたはスペース
- 3 から 4 桁の数字
- 省略可能なハイフンまたはスペース
- 7 桁の数字
- 省略可能なハイフンまたはスペース
- 2 から 3 桁の数字
- 省略可能なハイフンまたはスペース

### <a name="keywords"></a>キーワード

#### <a name="keyword_new_zealand_bank_account_number"></a>Keyword\_new\_zealand\_bank\_account\_number
```
account number
bank account
bank_acct_id
bank_acct_branch
bank_acct_nbr
```
## <a name="new-zealand-driver-license"></a>ニュージーランドの運転免許証 

### <a name="format"></a>Format 

8 文字の英数字パターン

### <a name="pattern"></a>Pattern

8 文字の英数字パターン

- 2 文字
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_new_zealand_drivers_license_number"></a>Keyword\_new\_zealand\_drivers\_license\_number
```
driverlicence
driverlicences
driver lic
driver licence
driver licences
driverslic
driverslicence
driverslicences
drivers lic
drivers lics
drivers licence
drivers licences
driver'lic
driver'lics
driver'licence
driver'licences
driver' lic
driver' lics
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's licence
driver's licences
driverlic#
driverlics#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver licence#
driver licences#
driverslic#
driverslics#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's licence#
driver's licences#
international driving permit
international driving permits
nz automobile association
new zealand automobile association
```
## <a name="new-zealand-inland-revenue-number"></a>ニュージーランドの内国税収入番号 

### <a name="format"></a>Format 

省略可能な区切り記号を含む 8 桁または 9 桁の数字

### <a name="pattern"></a>Pattern

省略可能な区切り記号を含む 8 桁または 9 桁の数字

- 2 桁または 3 桁の数字
- 1 つの省略可能なスペースまたはハイフン
- 3 桁の数字
- 1 つの省略可能なスペースまたはハイフン
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_new_zealand_inland_revenue_number"></a>Keyword\_new\_zealand\_inland\_revenue\_number
```
ird no.
ird no#
nz ird
new zealand ird
ird number
inland revenue number
```
## <a name="new-zealand-social-welfare-number"></a>ニュージーランドの社会福祉番号

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字

- 3 桁の数字
- 省略可能なハイフン
- 3 桁の数字
- 省略可能なハイフン
- 3 桁の数字

#### <a name="keyword_new_zealand_social_welfare_number"></a>Keyword\_new\_zealand\_social\_welfare\_number
```
social welfare #
social welfare#
social welfare No.  
social welfare number
swn#
```
## <a name="new-zealand-ministry-of-health-number"></a>ニュージーランド保健省 (Ministry of Health) 番号

### <a name="format"></a>Format

3 文字、1 つのスペース (省略可能)、4 桁の数字

### <a name="pattern"></a>Pattern

- 'I' と 'O' を除く 3 文字 (大文字と小文字は区別されません)
- 1 つのスペース (省略可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>ノルウェーの識別番号

### <a name="format"></a>Format

11 桁の数字

### <a name="pattern"></a>Pattern

11 桁の数字:

- DDMMYY 形式の 6 桁の数字 (生年月日)
- 3 桁の数字 (個人番号)
- 2 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>フィリピンの多目的統一 ID 番号

### <a name="format"></a>Format

ハイフンで区切られた 12 桁の数字

### <a name="pattern"></a>Pattern

12 桁の数字:

- 4 桁の数字
- 1 つのハイフン
- 7 桁の数字
- 1 つのハイフン
- 1 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-drivers-license-number"></a>ポーランドの運転免許証番号

### <a name="format"></a>Format

2 つのスラッシュを含む 14 桁の数字

### <a name="pattern"></a>Pattern

14 桁の数字と 2 つのスラッシュ:

- 5 桁の数字
- スラッシュ
- 2 桁の数字
- スラッシュ
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_poland_eu_drivers_license_number"></a>Keywords\_poland\_eu\_driver's\_license\_number
```
prawo jazdy
prawa jazdy
```

## <a name="poland-identity-card"></a>ポーランドの身分証明書

### <a name="format"></a>Format

3 文字と 6 桁の数字

### <a name="pattern"></a>Pattern

3 文字 (大文字と小文字は区別されません) の後に 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>ポーランドの国民 ID (PESEL)

### <a name="format"></a>Format

11 桁の数字

### <a name="pattern"></a>Pattern

- YYMMDD 形式の 6 桁の数字 (生年月日)
- 4 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>ポーランドのパスポート番号

この機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

2 文字と 7 桁の数字

### <a name="pattern"></a>Pattern

2 文字 (大文字と小文字は区別されません) の後に 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="poland-regon-number"></a>ポーランドの REGON 番号

### <a name="format"></a>Format

9 桁または 14 桁の数字

### <a name="pattern"></a>Pattern

9 桁または 14 桁の数字:

- 9 桁の数字または
- 9 桁の数字
- ハイフン
- 5 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_poland_regon_number"></a>Keywords\_poland\_regon\_number
```
regon id
statistical number
statistical id
statistical no
regon number
regonid#
regonno#
company id
companyid#
companyidno#
numer statystyczny
numeru regon
numerstatystyczny#
numeruregon#
```
## <a name="poland-tax-identification-number"></a>ポーランドの納税者番号

### <a name="format"></a>Format

スペースまたは区切り記号がない 11 桁の数字

### <a name="pattern"></a>Pattern

11 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_poland_eu_tax_file_number"></a>Keywords\_poland\_eu\_tax\_file\_number
```
nip#
nip
numer identyfikacji podatkowej
numeridentyfikacjipodatkowej#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat id#
vat id
vat no
vat number
vatid#
vatid
vatno#
```
## <a name="portugal-citizen-card-number"></a>ポルトガルの市民カード番号

### <a name="format"></a>Format

8 桁の数字

### <a name="pattern"></a>Pattern

8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>ポルトガルの運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプでのみ使用できます。

### <a name="format"></a>Format

2 つのパターン - 2 文字の後に特殊文字が含まれる 5 ～ 8 桁の数字

### <a name="pattern"></a>Pattern

パターン 1:2 文字の後に特殊文字が含まれる 5 ～ 6 桁の数字:

- 2 文字 (大文字と小文字は区別されません)
- ハイフン
- 5 桁または 6 桁の数字
- 1 つのスペース
- 1 桁の数字

パターン 2:1 文字の後に特殊文字が含まれる 6 ～ 8 桁の数字:

- 1 文字 (大文字と小文字は区別されません)
- ハイフン
- 6 桁または 8 桁の数字
- 1 つのスペース
- 1 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```
## <a name="portugal-passport-number"></a>ポルトガルのパスポート番号

### <a name="format"></a>Format

1 文字の後に 6 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern

1 文字の後に 6 桁の数字:

- 1 文字 (大文字と小文字は区別されません)
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_portugal_eu_passport_number"></a>Keywords\_portugal\_eu\_passport\_number
```
número do passaporte
portuguese passport
portuguese passeport
portuguese passaporte
passaporte nº
passeport nº
números de passaporte
portuguese passports
número passaporte
números passaporte
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="portugal-tax-identification-number"></a>ポルトガルの納税者番号

### <a name="format"></a>Format

省略可能なスペースを含む 9 桁の数字

### <a name="pattern"></a>Pattern

- 3 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字
- 1 つの省略可能なスペース
- 3 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_portugal_eu_tax_file_number"></a>Keywords\_portugal\_eu\_tax\_file\_number
```
cpf#
cpf
nif#
nif
número de identificação fisca
numero fiscal
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="romania-drivers-license-number"></a>ルーマニアの運転免許証番号

### <a name="format"></a>Format

1 文字の後に 8 桁の数字

### <a name="pattern"></a>Pattern

1 文字の後に 8 桁の数字:

- 1 文字 (大文字と小文字は区別されません) または数字
- 8 桁の数字

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_romania_eu_drivers_license_number"></a>Keywords\_romania\_eu\_driver's\_license\_number
```
permis de conducere
permisului de conducere
permisului conducere
permisele de conducere
permisele conducere
permis conducere
```

## <a name="romania-passport-number"></a>ルーマニアのパスポート番号

### <a name="format"></a>Format

スペースと区切り記号のない 8 桁または 9 桁の数字

### <a name="pattern"></a>Pattern

8 桁または 9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_romania_eu_passport_number"></a>Keywords\_romania\_eu\_passport\_number
```
numărul pașaportului numarul pasaportului numerele pașaportului Pașaport nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="romania-personal-numeric-code-cnp"></a>ルーマニアの身分証明書 (CNP)

### <a name="format"></a>Format

スペースと区切り記号のない 13 桁の数字

### <a name="pattern"></a>Pattern

- 1 から 9 の 1 桁の数字
- 生年月日 (YYMMDD) を表す 6 桁の数字
- 2 桁の数字 (01 から 52 または 99 を指定可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_romania_eu_national_id_card"></a>Keywords\_romania\_eu\_national\_id\_card
```
cnp#
cnp
cod identificare personal
cod numeric personal
cod unic identificare
codnumericpersonal#
codul fiscal nr.
identificarea fiscală nr#
id-ul taxei
insurance number
insurancenumber#
national id#
national id
national identification number
număr identificare personal
număr identitate
număr personal unic
număridentitate#
număridentitate
numărpersonalunic#
numărpersonalunic
număru de identificare fiscală
numărul de identificare fiscală
personal numeric code
pin#
pin
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
uniqueidentityno
```

## <a name="russia-passport-number-domestic"></a>ロシアのパスポート番号 (国内)

### <a name="format"></a>Format

10 桁の数字

### <a name="pattern"></a>Pattern

10 桁の数字:

- 2 桁の数字
- 1 つの省略可能なスペースまたはハイフン
- 2 桁の数字
- 1 つの省略可能なスペース
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_russia_passport_number_domestic"></a>Keyword\_russia\_passport\_number\_domestic
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```
## <a name="russia-passport-number-international"></a>ロシアのパスポート番号 (海外)

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字:

- 2 桁の数字
- 1 つの省略可能なスペースまたはハイフン
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_russia_passport_number_international"></a>Keywords\_russia\_passport\_number\_international
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```

## <a name="saudi-arabia-national-id"></a>サウジアラビアの国民 ID

### <a name="format"></a>Format

10 桁

### <a name="pattern"></a>Pattern

10 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>シンガポールの国民登録 ID カード (NRIC) 番号

### <a name="format"></a>Format

9 桁の文字と数字

### <a name="pattern"></a>Pattern

- 9 桁の文字と数字:
- 文字 &quot;F&quot;、&quot;G&quot;、&quot;S&quot;、または &quot;T&quot; (大文字と小文字は区別されません)
- 7 桁の数字
- 1 文字のアルファベットのチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```
## <a name="slovakia-drivers-license-number"></a>スロバキアの運転免許証番号

### <a name="format"></a>Format

1 文字の後に 7 桁の数字

### <a name="pattern"></a>Pattern

1 文字の後に 7 桁の数字

- 1 文字 (大文字と小文字は区別されません) または数字
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovakia_eu_drivers_license_number"></a>Keywords\_slovakia\_eu\_driver's\_license\_number
```
vodičský preukaz
vodičské preukazy
vodičského preukazu
vodičských preukazov
```
## <a name="slovakia-personal-number"></a>スロバキアの個人番号

### <a name="format"></a>Format
省略可能な円記号を含む 9 桁または 10 桁の数字

### <a name="pattern"></a>Pattern
- 生年月日を表す 6 桁の数字
- 省略可能なスラッシュ (/)
- 3 桁の数字
- 省略可能な 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_slovakia_eu_national_id_card"></a>Keywords\_slovakia\_eu\_national\_id\_card
```
azonosító szám
birth number
číslo národnej identifikačnej karty
číslo občianského preukazu
daňové číslo
id number
identification no
identification number
identifikačná karta č
identifikačné číslo
identity card no
identity card number
národná identifikačná značka č
national number
nationalnumber#
nemzeti személyazonosító igazolvány
personalidnumber#
rč
rodne cislo
rodné číslo
social security number
ssn#
ssn
személyi igazolvány szám
személyi igazolvány száma
személyigazolvány szám
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="slovenia-drivers-license-number"></a>スロベニアの運転免許証番号

### <a name="format"></a>Format

スペースと区切り記号のない 9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovenia_eu_drivers_license_number"></a>Keywords\_slovenia\_eu\_driver's\_license\_number
```
vozniško dovoljenje
vozniška številka licence
vozniških dovoljenj
številka vozniškega dovoljenja
številke vozniških dovoljenj
```
## <a name="slovenia-passport-number"></a>スロベニアのパスポート番号

### <a name="format"></a>Format

2 文字の後に 7 桁の数字 (スペースまたは区切り記号を含まない)

### <a name="pattern"></a>Pattern

2 文字の後に 7 桁の数字:

- 文字 "P"
- 1 文字の大文字
- 7 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- passport number
- passportnumbers
- passport numbers
```
#### <a name="keywords_slovenia_eu_passport_number"></a>Keywords\_slovenia\_eu\_passport\_number
```
- številka potnega lista
- potek veljavnosti
- potni list#
- datum rojstva
- potni list
- številke potnih listov
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
- date of issue
- date of expiry
```
## <a name="slovenia-tax-identification-number"></a>スロベニアの納税者番号

### <a name="format"></a>Format

スペースや区切り記号がない 8 桁の数字

### <a name="pattern"></a>Pattern

- 1 から 9 の 1 桁の数字
- 6 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_slovenia_eu_tax_file_number"></a>Keywords\_slovenia\_eu\_tax\_file\_number
```
davčna številka
identifikacijska številka davka
številka davčne datoteke
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="slovenia-unique-master-citizen-number"></a>スロベニアの市民識別番号

### <a name="format"></a>Format

スペースや区切り記号のない 13 桁の数字

### <a name="pattern"></a>Pattern

指定したパターンの 13 桁の数字:

- 生年月日 (DDMMLLL) に対応する 7 桁の数字で、"LLL" は出生年の末尾の 3 桁に対応する
- 出生地域に対応する 2 桁の数字 "50"
- 同じ日に生まれた人の性別とシリアル番号の組み合わせに対応する 3 桁の数字。 男性の場合は 000 から 499、女性の場合は 500 から 999。
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_slovenia_eu_national_id_card"></a>Keywords\_slovenia\_eu\_national\_id\_card
```
edinstvena številka glavnega državljana
emšo
enotna maticna številka obcana
id card
identification number
identifikacijska številka
identity card
nacionalna id
nacionalni potni list
national id
osebna izkaznica
osebni koda
osebni ne
osebni številka
personal code
personal number
personal numeric code
številka državljana
unique citizen number
unique id number
unique identity number
unique master citizen number
unique registration number
uniqueidentityno #
uniqueidentityno#
```

## <a name="south-africa-identification-number"></a>南アフリカの識別番号

### <a name="format"></a>Format

13 桁の数字 (スペースが含まれる場合があります)

### <a name="pattern"></a>Pattern

13 桁の数字:

- YYMMDD 形式の 6 桁の数字 (生年月日)
- 4 桁の数字
- 1 桁の市民インジケーター
- 数字 &quot;8&quot; または &quot;9&quot;
- 1 桁の数字 (チェックサム ディジット)

### <a name="keywords"></a>キーワード

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>韓国の住民登録番号

### <a name="format"></a>Format

1 つのハイフンが含まれた 13 桁の数字

### <a name="pattern"></a>Pattern

13 桁の数字:

- YYMMDD 形式の 6 桁の数字 (生年月日)
- 1 つのハイフン
- 世紀と性別によって決定される 1 桁の数字
- 4 桁の数字 (出生地コード)
- 先行する数字が同一である人を区別するために使用される 1 桁の数字
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-dni"></a>スペインの DNI 

### <a name="format"></a>Format

8 桁の数字の後に 1 文字

### <a name="pattern"></a>Pattern

7 桁の数字の後に 1 文字

- 8 桁の数字
- 1 つの省略可能なスペースまたはハイフン
- 1 文字のチェック文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_spain_eu_national_id_card"></a>Keywords\_spain\_eu\_national\_id\_card
```
carné de identidad
dni#
dni
dninúmero#
documento nacional de identidad
identidad único
identidadúnico#
insurance number
national identification number
national identity
nationalid#
nationalidno#
nie#
nie
nienúmero#
número de identificación
número nacional identidad
personal identification number
personal identity no
unique identity number
uniqueid#
```
## <a name="spain-drivers-license-number"></a>スペインの運転免許証番号 

### <a name="format"></a>Format

8 桁の数字の後に 1 文字

### <a name="pattern"></a>Pattern

8 桁の数字の後に 1 文字:

- 8 桁の数字
- 1 桁の数字または文字 (大文字と小文字は区別されません)

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_spain_eu_drivers_license_number"></a>Keywords\_spain\_eu\_driver's\_license\_number
```
permiso de conducción
permiso conducción
licencia de conducir
licencia conducir
permiso conducir
permiso de conducir
permisos de conducir
permisos conducir
carnet conducir
carnet de conducir
licencia de manejo
licencia manejo
```
## <a name="spain-passport-number"></a>スペインのパスポート番号 

### <a name="format"></a>Format

スペースまたは区切り記号がない 8 文字または 9 文字の文字と数字の組み合わせ

### <a name="pattern"></a>Pattern

8 文字または 9 文字の文字と数字の組み合わせ:

- 2 桁の数字または文字
- 1 桁の数字または文字 (省略可能)
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_spain_eu_passport_number"></a>Keywords\_spain\_eu\_passport\_number
```
libreta pasaporte
número pasaporte
españa pasaporte
números de pasaporte
número de pasaporte
números pasaporte
pasaporte no
Passeport n°
n° Passeport
pasaporte no.
pasaporte n°
spain passport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="spain-social-security-number-ssn"></a>スペインの社会保障番号 (SSN)

この機密情報タイプのエンティティは、EU の社会保障番号または同等の ID の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

11 ～ 12 桁の数字

### <a name="pattern"></a>Pattern

11 ～ 12 桁の数字:

- 2 桁の数字
- 1 つのスラッシュ (省略可能)
- 7 ～ 8 桁の数字
- 1 つのスラッシュ (省略可能)
- 2 桁の数字

### <a name="keywords"></a>キーワード

なし

## <a name="spain-tax-identification-number"></a>スペインの納税者番号 

### <a name="format"></a>Format

指定したパターンの 7 桁または 8 桁の数字と 1 文字または 2 文字

### <a name="pattern"></a>Pattern

スペインの国民 ID カードを持つスペインの自然人:

- 8 桁の数字
- 1 つの大文字 (大文字と小文字が区別されます)

スペインの国民 ID カードのない非居住スペイン人

- 1 つの大文字 "L" (大文字と小文字が区別されます)
- 7 桁の数字
- 1 つの大文字 (大文字と小文字が区別されます)

スペインの国民 ID カードのない 14 歳以下の居住スペイン人:

- 1 つの大文字 "K" (大文字と小文字が区別されます)
- 7 桁の数字
- 1 つの大文字 (大文字と小文字が区別されます)

外国人の識別番号を持つ外国人

- "X"、"Y"、または "Z" の 1 つの大文字 (大文字と小文字が区別されます)
- 7 桁の数字
- 1 つの大文字 (大文字と小文字が区別されます)

外国人の識別番号を持たない外国人

- "M" の 1 つの大文字 (大文字と小文字が区別されます)
- 7 桁の数字
- 1 つの大文字 (大文字と小文字が区別されます)

### <a name="keywords"></a>キーワード

#### <a name="keywords_spain_eu_tax_file_number"></a>Keywords\_spain\_eu\_tax\_file\_number
```
cif
cifid#
cifnúmero#
número de contribuyente
número de identificación fiscal
número de impuesto corporativo
spanishcifid#
spanishcifid
spanishcifno#
spanishcifno
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="sweden-drivers-license-number"></a>スウェーデンの運転免許証番号

### <a name="format"></a>Format

1 つのハイフンが含まれた 10 桁の数字

### <a name="pattern"></a>Pattern

1 つのハイフンが含まれた 10 桁の数字:

- 6 桁の数字
- 1 つのハイフン
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_sweden_eu_drivers_license_number"></a>Keywords\_sweden\_eu\_driver's\_license\_number
```
ajokortti
permis de conducere
ajokortin numero
kuljettajat lic.
drivere lic.
körkort
numărul permisului de conducere
שאָפער דערלויבעניש נומער
förare lic.
דריווערס דערלויבעניש
körkortsnummer
```

## <a name="sweden-national-id"></a>スウェーデンの国民 ID

### <a name="format"></a>Format

10 桁または 12 桁の数字と 1 つの省略可能な区切り記号

### <a name="pattern"></a>Pattern

10 桁または 12 桁の数字と 1 つの省略可能な区切り記号:

- 2 桁の数字 (省略可能)
- 日付形式 YYMMDD の 6 桁の数字
- &quot;-&quot; または &quot;+&quot; の区切り記号 (省略可能)
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>スウェーデンのパスポート番号

この機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

8 桁の数字

### <a name="pattern"></a>Pattern

8 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="sweden-tax-identification-number"></a>スウェーデンの納税者番号

### <a name="format"></a>Format

指定したパターンの 10 桁の数字と記号

### <a name="pattern"></a>Pattern

10 桁の数字と記号:

- 生年月日 (DDMMYY) に対応する 6 桁の数字
- プラス記号またはマイナス記号
- 識別番号を一意にする 3 桁の数字:
- 1990 年より前に発行された番号の場合、7 番目と 8 番目の数字は出生地または外国生まれの人を識別します
- 9 番目の位置の数字は、男性の場合は奇数または女性の場合は偶数で性別を示します
- 1 桁のチェック ディジット

### <a name="keywords"></a>キーワード

#### <a name="keywords_sweden_eu_tax_file_number"></a>Keywords\_sweden\_eu\_tax\_file\_number
```
personal id number
personnummer
skatt id nummer
skatt identifikation
skattebetalarens identifikationsnummer
sverige tin
tax file
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="swift-code"></a>SWIFT コード

### <a name="format"></a>Format

4 文字の後に 5 ～ 31 桁の文字または数字

### <a name="pattern"></a>Pattern

4 文字の後に 5 ～ 31 桁の文字または数字:

- 4 文字の銀行コード (大文字と小文字は区別されません)
- 1 つの省略可能なスペース
- 4 ～ 28 桁の文字または数字 (基本銀行口座番号 (BBAN))
- 1 つの省略可能なスペース
- 1 ～ 3 桁の文字または数字 (BBAN の残り)

### <a name="keywords"></a>キーワード

#### <a name="keyword_swift"></a>Keyword\_swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
\#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="switzerland-ssn-ahv-number"></a>スイスの SSN AHV 番号

### <a name="format"></a>Format

13 桁の数字

### <a name="pattern"></a>Pattern

13 桁の数字:

- 3 桁の数字 - 756
- 省略可能なドット
- 4 桁の数字
- 省略可能なドット
- 4 桁の数字
- 省略可能なドット
- 2 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_swiss_ssn_ahv_number"></a>Keyword\_swiss\_ssn\_AHV\_number
```
ahv
ssn
pid
insurance number
personalidno#
social security number
personal id number
personal identification no.
insuranceno#
uniqueidno#
unique identification no.
avs number
personal identity no versicherungsnummer
identifikationsnummer
einzigartige identität nicht
sozialversicherungsnummer
identification personnelle id
numéro de sécurité sociale
```

## <a name="taiwan-national-identification-number"></a>台湾の国民識別番号

### <a name="format"></a>Format

1 文字 (英語) の後に 9 桁の数字

### <a name="pattern"></a>Pattern

1 文字 (英語) の後に 9 桁の数字:

- 1 文字 (英語、大文字と小文字は区別されません)
- 数字 &quot;1&quot; または &quot;2&quot;
- 8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>台湾のパスポート番号

### <a name="format"></a>Format

- 生体認証パスポート番号: 9 桁の数字
- 非生体認証パスポート番号: 9 桁の数字

### <a name="pattern"></a>Pattern

生体認証パスポート番号:

- 文字 &quot;3&quot;
- 8 桁の数字

非生体認証パスポート番号:

- 9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>台湾の在留証明書 (ARC/TARC) 番号

### <a name="format"></a>Format

10 桁の文字と数字

### <a name="pattern"></a>Pattern

10 桁の文字と数字:

- 2 文字 (大文字と小文字は区別されません)
- 8 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>英国 運転免許証番号

この機密情報タイプのエンティティは、EU の運転免許証番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

指定された形式の 18 桁の文字と数字の組み合わせ

### <a name="pattern"></a>Pattern

18 桁の文字と数字:

- 5 文字 (大文字と小文字は区別されません) または文字の代わりに数字 &quot;9&quot;
- 1 桁の数字
- 生年月日を示す日付形式 MMDDY の 5 桁の数字 (ドライバーが女性の場合、7 文字目には 50 が加算されます。つまり、01 ～ 12 の代わりに 51 ～ 62 になります)
- 2 文字 (大文字と小文字は区別されません) または文字の代わりに数字 &quot;9&quot;
- 5 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>英国 選挙人名簿番号

### <a name="format"></a>Format

2 文字の後に 1 ～ 4 桁の数字

### <a name="pattern"></a>Pattern

2 文字 (大文字と小文字は区別されません) の後に 1 ～ 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- 協議会の指名
- 申請フォーム
- 選挙人登録
- 選挙人名簿

## <a name="uk-national-health-service-number"></a>英国 国民健康保険番号

### <a name="format"></a>Format

スペースで区切られた 10 ～ 17 桁の数字

### <a name="pattern"></a>Pattern

10 ～ 17 桁の数字:

- 3 桁または 10 桁の数字
- 1 つのスペース
- 3 桁の数字
- 1 つのスペース
- 4 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>英国 国民保険番号 (NINO)

この機密情報タイプのエンティティは、EU の国民識別番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

7 文字またはスペースまたはダッシュで区切られた 9 文字

### <a name="pattern"></a>Pattern

可能な 2 つのパターン:

- 2 文字 (このパターンで検証される有効な NINO では、このプレフィックスに特定の文字のみが使用されます。大文字と小文字は区別されません)
- 6 桁の数字
- 'A'、'B'、'C'、または 'D' (プレフィックスと同様に、サフィックスに使用できるのは特定の文字のみです。大文字と小文字は区別されません)

OR

- 2 文字
- 1 つのスペースまたはダッシュ
- 2 桁の数字
- 1 つのスペースまたはダッシュ
- 2 桁の数字
- 1 つのスペースまたはダッシュ
- 2 桁の数字
- 1 つのスペースまたはダッシュ
- 'A'、'B'、'C'、または 'D'


### <a name="keywords"></a>キーワード

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>英国 納税者参照番号

この機密情報タイプは、次の用途でのみ使用できます。

- データ損失の防止ポリシー
- 通信コンプライアンス ポリシー
- 情報ガバナンス
- レコードの管理
- Microsoft Cloud App Security

### <a name="format"></a>Format

スペースと区切り記号のない 10 桁の数字

### <a name="pattern"></a>Pattern

10 桁

### <a name="keywords"></a>キーワード

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="ukraine-passport-domestic"></a>ウクライナのパスポート (国内)

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_ukraine_passport_domestic"></a>Keyword\_ukraine\_passport\_domestic
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
персональний
```
## <a name="ukraine-passport-international"></a>ウクライナのパスポート (海外)

### <a name="format"></a>Format

8 文字の英数字パターン

#### <a name="pattern"></a>Pattern

8 文字の英数字パターン:

- 2 桁の文字または数字
- 6 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_ukraine_passport_international"></a>Keyword\_ukraine\_passport\_international
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
```
## <a name="us-bank-account-number"></a>米国の銀行口座番号

### <a name="format"></a>Format

6 ～ 17 桁の数字

### <a name="pattern"></a>Pattern

6 ～ 17 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>米国の運転免許証番号

### <a name="format"></a>Format

州によって異なります

### <a name="pattern"></a>Pattern

州 (ニューヨークなど) に依存します。

- ddd のように書式設定された 9 桁の数字が適合します。
- ddddddddd のような 9 桁の数字は適合しません。

### <a name="keywords"></a>キーワード

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- 州略称 (例: &quot;NY&quot;)
- 州名 (例: &quot;ニューヨーク&quot;)

## <a name="us-individual-taxpayer-identification-number-itin"></a>米国の個人納税者番号 (ITIN)

### <a name="format"></a>Format

&quot;9&quot; で始まり、4 桁目の数字として &quot;7&quot; または &quot;8&quot; を含む 9 桁の数字。省略可能なスペースまたはダッシュで書式設定されます

### <a name="pattern"></a>Pattern

書式設定済み:

- 数字 &quot;9&quot;
- 2 桁の数字
- 1 つのスペースまたはダッシュ
- &quot;7&quot; または &quot;8&quot;
- 1 桁の数字
- 1 つのスペースまたはダッシュ
- 4 桁の数字

書式設定なし:

- 数字 &quot;9&quot;
- 2 桁の数字
- &quot;7&quot; または &quot;8&quot;
- 5 桁の数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_itin"></a>Keyword\_itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>米国の社会保障番号 (SSN)

### <a name="format"></a>Format

9 桁の数字 (書式設定されたパターンまたは書式設定されていないパターン)

> [!Note]
> 2011 年中頃までに発行された SSN には、有効になるには数値の特定の部分が特定の範囲内である必要がある厳密な書式設定があります (ただし、チェックサムはありません)。
>

### <a name="pattern"></a>Pattern

4 つの関数が、4 つの異なるパターンで SSN を検索します。

- Func\_ssn は、ダッシュまたはスペースで書式設定された 2011 年までの厳密な書式設定で SSN を検索します (ddd-dd-dddd または ddd dd dddd)
- Func\_unformatted\_ssn は、書式設定のない 9 桁の連続する数字を使用して、2011 年までの厳密な書式設定で SSN を検索します (ddddddddd)
- Func\_randomized\_formatted\_ssn は、ダッシュまたはスペースで書式設定された 2011 年以降の SSN を検索します (ddd-dd-dddd または ddd dd dddd)
- Func\_randomized\_unformatted\_ssn は、書式設定のない 9 桁の連続する数字を使用して 2011 年以降の SSN を検索します (ddddddddd)

### <a name="keywords"></a>キーワード

#### <a name="keyword_ssn"></a>Keyword\_ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>米国または英国 パスポート番号

英国の パスポート番号の機密情報タイプのエンティティは、EU のパスポート番号の機密情報タイプに含まれており、スタンドアロンの機密情報タイプのエンティティとして使用できます。

### <a name="format"></a>Format

9 桁の数字

### <a name="pattern"></a>Pattern

9 桁の連続する数字

### <a name="keywords"></a>キーワード

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
