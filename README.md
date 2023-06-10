# 診療情報提供書FHIR記述仕様のプロファイルを使ったバリデーション実行

* [https://std.jpfhir.jp/](https://std.jpfhir.jp/)のサイトにある、[「packageの説明と使い方」](http://std.jpfhir.jp/wp-content/uploads/2023/05/eReferral-Package-readme-v2-1.pdf)の手順に基づき、診療情報提供書FHIR記述仕様のプロファイルを[公式バリデータ](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator)使ってバリデーション実行した例です。
    * Validatorの使用方法については[HL7のConfluenceのページ「Using the FHIR Validator」](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator)を参照ください。

## 実行のための手順

1. Open JDKのインストール
    * 公式Validatorの実行にJavaの実行環境が必要です。
        * [公式Validatorのドキュメント](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator#UsingtheFHIRValidator-JDKVersion)によると、現在サポートされているLTSバージョン（JDK11、17）での実行が確認済のこと
    * 例えば、[Amazon Corretto](https://aws.amazon.com/jp/corretto)等、何らかのOpen JDKのディストリビューションをインストールしてください。

1. 診療情報提供書のsnapshot形式のパッケージダウンロード
    * 診療情報提供書のsnapshot形式（親profileの記述をすべて含んだ上でそれに差分を反映した形式）の[FHIR package仕様](https://registry.fhir.org/learn)に従ったパッケージをダウンロードします。
        * [IGpackage2023.4.27 snapshot形式: jp-ereferral-0.9.6-snap.tgz](https://jpfhir.jp/fhir/eReferral/jp-ereferral-0.9.7-snap.tgz)
        * 現状2023/06/10時点で、Webページの表記は「0.9.6」となっていますが、ダウンロードリンク先のファイルの実際のバージョン番号は「0.9.7」になっています。

1. パッケージのtgzファイルのファイル名変更
    * 「jp-ereferral-0.9.x-snap.tgz」から「jpereferral#0.9.x.tgz」の形式に名前を変更します。

1. 診療情報提供書のサンプルデータの準備
    * 先ほどダウンロードした「jp-ereferral-0.9.x-snap.tgz」を解凍し、中に含まれている、診療情報提供書のサンプルデータ（Bundle-BundleReferralExample01.json）を取り出しておきます。
        * 解凍には、7Zip等のファイル解凍ソフトを使うとよいです。    
    * もしくは、自分が作成したサンプルデータを使用することもできます。

1. 公式Validatorのダウンロード
    * HAPI FHIRが提供する[公式Validtor validator_cli.jar](https://github.com/hapifhir/org.hl7.fhir.core/releases/latest/download/validator_cli.jar)をダウンロードします。

1. 公式Validatorの実行
    * 以下のjavaコマンドを実行します。        
        * オプションの詳細は「java -jar validator_cli.jar -?」でも確認できます。
            * もしくは、[HL7のConfluenceのページ「Using the FHIR Validator」](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator)を参照ください。        
        * -txオプションはインスタンスで使用されるコード（CodeSystemやValueSet）を格納したFHIR TerminologyサーバのURLだそうです。
            * [https://tx.jpfhir.jp:8081](https://tx.jpfhir.jp:8081)は日本医療情報学会FHIR実装検討WGがボランタリーに稼働しているもので、jp-core.r4 v1.1をサポートしており、メンテナンスでアクセスできない場合もあるとのこと
            * jp-coreのコードやValueSetをvalidationで必要としないのであれば、このオプションは省略可能で、省略した場合にはデフォルトで、「-tx https://tx.fhir.org/r4」を指定したことになるとのこと
            * 「-tx n/a」にすると未指定になる模様

## 実行例1
* コマンドの実行例1（-tx オプションあり）    

```sh
java -Dfile.encoding=UTF-8 -jar validator_cli.jar Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx https://tx.jpfhir.jp:8081 
```

* 上記コマンド実行結果
    * 実行すると、初回、HL7のパッケージ（？）のダウンロード（Fetch）処理が走るようです。
        * 2回目以降はキャッシュされて、その分は早くなりました。
    * 正しいサンプルデータのはずなのに、エラーも出てうまくいかず。。。
    * -txオプションありだと、なぜか、バリデーションの処理にとてつもなく時間がかかりました。（数分とか数十分ではなく、1時間近いレベル）なので、使い物にならないかも。。

```sh
>java -Dfile.encoding=UTF-8 -jar validator_cli.jar Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx https://tx.jpfhir.jp:8081 

FHIR Validation tool Version 6.0.15 (Git# cc14a4142f40). Built 2023-06-08T21:23:55.484Z (34 hours old)
  Java:   17.0.5 from C:\Program Files\Amazon Corretto\jdk17.0.5_8 on amd64 (64bit). 6136MB available
  Paths:  Current = …\git\fhir-validation-cli, Package Cache = C:\Users\dell\.fhir\packages
  Params: Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx https://tx.jpfhir.jp:8081
Scanning for versions (no -version parameter):
  Package jp-ereferral#0.9.7.tgz: 4.0
-> use version 4.0
  Locale: 日本/JP
  Jurisdiction: Japan
Loading
  Load FHIR v4.0 from hl7.fhir.r4.core#4.0.1Installing hl7.fhir.r4.core#4.0.1 to the package cache
  Fetching:
....................................................................................................
  Installing: .................................................................................................... done.
Installing hl7.fhir.xver-extensions#0.0.12 to the package cache
  Fetching:

  Installing:  done.
 - 4576 resources (02:20.504)
Installing hl7.terminology#5.1.0 to the package cache
  Fetching:
....................................................................................
  Installing: .................................................................................... done.
  Load hl7.terminology#5.1.0 - 4180 resources (01:01.632)
Installing hl7.fhir.uv.extensions#1.0.0 to the package cache
  Fetching:
....................................
  Installing: .................................... done.
Installing hl7.terminology.r5#5.0.0 to the package cache
  Fetching:
....................................................................................
  Installing: .................................................................................... done.
  Load hl7.terminology.r5#5.0.0 - 4174 resources (01:27.367)
  Load hl7.fhir.uv.extensions#1.0.0 - 1328 resources (00:00.042)
  Terminology server https://tx.jpfhir.jp:8081 - Version 2.0.0 (00:01.066)
  Load jp-ereferral#0.9.7.tgz+  .. load IG from jp-core.r4#1.1.1
Installing jp-core.r4#1.1.1 to the package cache
  Fetching:
2023-06-10 16:58:38.784 [main] INFO  o.h.f.u.npm.BasePackageCacheManager [BasePackageCacheManager.java:84] Failed to resolve package jp-core.r4#1.1.1 from server: http://packages.fhir.org (Invalid HTTP response 404 from http://packages.fhir.org/jp-core.r4/1.1.1 (Not Found) (content in c:\temp\http-log\fhir-http-2.log))
2023-06-10 16:58:39.635 [main] INFO  o.h.f.u.npm.BasePackageCacheManager [BasePackageCacheManager.java:84] Failed to resolve package jp-core.r4#1.1.1 from server: https://packages2.fhir.org/packages (Invalid HTTP response 404 from https://packages2.fhir.org/packages/jp-core.r4/1.1.1 (Not found) (content in c:\temp\http-log\fhir-http-3.log))
....
  Installing: .... done.
 - 230 resources (00:05.749)
  Package Summary: [hl7.fhir.r4.core#4.0.1, hl7.fhir.xver-extensions#0.0.12, hl7.terminology#5.1.0, hl7.terminology.r5#5.0.0, hl7.fhir.uv.extensions#1.0.0]
  Get set... Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_Amount but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_Amount but trying to go on
Can't find Timing profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationTiming but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Range profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRange_UnitOfTime but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage_Injection but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosageBase but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerDay but trying to go on
Can't find Range profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRange_UnitOfTime but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage_Injection but trying to go on
 go (00:04.549)
Validating
  Validate Bundle-BundleReferralExample01.json
Validate Bundle against http://hl7.org/fhir/StructureDefinition/Bundle|4.0.1..........20..........40..Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
...Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner..........20..........40..........60..........80..........100..|
..Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
...60..........80..........100..........120......|
Validate Bundle against http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferralValidate Composition against http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5|  
Validate Patient against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Patient_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Patient against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Patient|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Encounter against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5|
Validate Encounter against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|
Validate Encounter against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5|
Validate Encounter against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|
Validate Observation against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Observation_Common_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Observation against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Observation_Common|
Validate DocumentReference against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_DocumentReference_eClinicalSummary|1.1.5|
Validate DocumentReference against http://jpfhir.jp/fhir/core/StructureDefinition/JP_DocumentReference|
..|
 01:04:58.323
Done. Times: Loading: 05:02.250, validation: 01:04:58.324. Memory = 862Mb

*FAILURE*: 39 errors, 22 warnings, 21 notes
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type (line 39, col10): None of the codings provided are in the value set 'valueSet' (http://jpfhir.jp/fhir/Common/ValueSet/doc-typecodes|1.1.5), and a coding from this value set is required) (codes = http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes#57133-1)
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.category[0] (line 49, col12): None of the codings provided are in the value set 'valueSet' (http://jpfhir.jp/fhir/Common/ValueSet/doc-typecodes|1.1.5), and a coding from this value set is required) (codes = http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes#57133-1)
  Error @ Bundle.entry[18].resource/*DocumentReference/ccourseReferralExample01Inline*/.content[0].attachment.contentType (line 1219, col56): The value provided ('text/plain;charset=UTF-8') is not in the value set 'MimeType' (http://hl7.org/fhir/ValueSet/mimetypes|4.0.1), and a code is required from this value set) (error message = Error performing tx4 operation 'validate-code: timeout' (parameters = ""))
  Error @ Bundle.entry[0] (line 17, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[1] (line 392, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[2] (line 458, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[3] (line 495, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[4] (line 538, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[5] (line 581, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[6] (line 648, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[7] (line 715, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[8] (line 750, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[9] (line 785, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[10] (line 816, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[11] (line 861, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[12] (line 905, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[13] (line 954, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[14] (line 1003, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[15] (line 1053, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[16] (line 1105, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[17] (line 1156, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[18] (line 1193, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/ (line 19, col8): Composition.author:authorPractitioner: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5)
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[1].entry[1] (line 223, col20): Unable to find a match for profile urn:uuid:a9358f3e-c743-4998-aa89-1be54c31b432 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[3].entry[0] (line 269, col20): Unable to find a match for profile urn:uuid:5c650562-f4e4-40b3-87c6-e9a0376be7a8 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[0] (line 297, col20): Unable to find a match for profile urn:uuid:711b07ae-d20b-40b0-9aa9-c7f1981409e6 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[1] (line 302, col20): Unable to find a match for profile urn:uuid:5a1aae74-f720-415a-ba15-a6c8b5d4c5a1 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[8].entry[0] (line 380, col20): Unable to find a match for profile urn:uuid:a6e4fc73-a749-4583-94a7-18e4241b4c75 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_DocumentReference
  Error @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/ (line 460, col8): Encounter.reasonCode: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.meta (line 463, col10): Encounter.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta (line 500, col10): Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[4].resource.meta (line 543, col10): Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[7].resource.meta (line 720, col10): Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
  Error @ Bundle.entry[8].resource.meta (line 755, col10): Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
  Error @ Bundle.entry[11].resource/*Condition/cc2ReferralExample01Inline*/.meta (line 866, col10): Condition.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[13].resource/*Condition/ph1ReferralExample01Inline*/.meta (line 959, col10): Condition.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Unable to find a match for profile urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter
  Error @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.meta (line 1110, col10): AllergyIntolerance.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Unable to find a match for profile urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter
  Information @ Bundle (line 1, col2): Bundle.entry:composition: Found 0 matches, but unable to check minimum required (Bundle.entry) due to lack of slicing validation (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Information @ Bundle (line 1, col2): Bundle.entry:practitioners: Found 0 matches, but unable to check minimum required (Bundle.entry) due to lack of slicing validation (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0] (line 71, col12): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorPractitioner' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1] (line 122, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource: Organization.type: max allowed = 1, but found 2 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[0].coding[0].code: Value is 'dept' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[1].coding[0].system: Value is 'urn:oid:1.2.392.100495.20.2.51' but 
must be 'http://terminology.hl7.org/CodeSystem/organization-type'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[1].coding[0].code: Value is '03' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource: Specified profile type was 'Practitioner' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5', but found type 'Organization'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2] (line 127, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1] (line 155, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource: Organization.type: max allowed = 1, but found 2 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[0].coding[0].code: Value is 'dept' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[1].coding[0].system: Value is 'urn:oid:1.2.392.100495.20.2.51' but 
must be 'http://terminology.hl7.org/CodeSystem/organization-type'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[1].coding[0].code: Value is '11' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource: Specified profile type was 'Practitioner' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5', but found type 'Organization'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2] (line 160, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type (line 39, col10): None of the codings provided are in the value set 'FHIR Document Type Codes' (http://hl7.org/fhir/ValueSet/doc-typecodes|4.0.1), and a coding is recommended to come from this value set) (codes = http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes#57133-1)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.encounter (line 64, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0] (line 71, col12): Details for urn:uuid:3e6a0ba2-d781-4fd7-9de6-e077b690daed matching against profile http://hl7.org/fhir/StructureDefinition/Practitioner|4.0.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[1].entry[1] (line 223, col20): Details for urn:uuid:a9358f3e-c743-4998-aa89-1be54c31b432 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[3].entry[0] (line 269, col20): Details for urn:uuid:5c650562-f4e4-40b3-87c6-e9a0376be7a8 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[0] (line 297, col20): Details for urn:uuid:711b07ae-d20b-40b0-9aa9-c7f1981409e6 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[1] (line 302, col20): Details for urn:uuid:5a1aae74-f720-415a-ba15-a6c8b5d4c5a1 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[8].entry[0] (line 380, col20): Details for urn:uuid:a6e4fc73-a749-4583-94a7-18e4241b4c75 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_DocumentReference|1.1.5
  Information @ Bundle.entry[7].resource/*Organization/referralToOrganizationDeptExample01Inline*/.partOf (line 745, col10): Details for urn:uuid:9f92f003-69e6-4983-85eb-fb49a3110b59 matching against profile http://hl7.org/fhir/StructureDefinition/Organization|4.0.1
  Information @ Bundle.entry[8].resource/*Organization/referralFromOrganizationDeptExample01Inline*/.partOf (line 780, col10): Details for urn:uuid:2e979bec-720b-4e36-8eb9-ebe661172af3 matching against profile http://hl7.org/fhir/StructureDefinition/Organization|4.0.1
  Information @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|1.1.1
  Information @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|1.1.1
  Warning @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type.coding[0] (line 41, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.category[0].coding[0] (line 51, col16): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.class (line 476, col10): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.classHistory[0].class (line 483, col14): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - 
should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 596, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 596, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.21#13) is not 
in the value set 'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that the validator cannot judge what is suitable).  (error message = None of the provided codes [urn:oid:1.2.392.100495.20.3.21#13] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1')
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 603, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 603, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.22#1) is not in the value set 'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that the validator cannot judge what is suitable).  (error message = None of the provided codes [urn:oid:1.2.392.100495.20.3.22#1] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1')
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 663, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[6].resource.extension[0].value.ofType(Coding) (line 663, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 663, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.21#13) is not in the value set 'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that the validator cannot judge what is suitable).  (error message = None of the provided codes [urn:oid:1.2.392.100495.20.3.21#13] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1')
  Warning @ Bundle.entry[6].resource.extension[0].value.ofType(Coding) (line 663, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.21#13) is not in the value set 'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that the validator cannot judge what is suitable).  (error message = None of the provided codes [urn:oid:1.2.392.100495.20.3.21#13] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1')
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 670, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[6].resource.extension[1].value.ofType(Coding) (line 670, col14): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 670, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.22#1) is not in 
the value set 'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that 
the validator cannot judge what is suitable).  (error message = None of the provided codes [urn:oid:1.2.392.100495.20.3.22#1] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1')
  Warning @ Bundle.entry[6].resource.extension[1].value.ofType(Coding) (line 670, col14): The Coding provided (urn:oid:1.2.392.100495.20.3.22#1) is not in the value set 'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (note that the validator cannot judge what is suitable).  (error 
message = None of the provided codes [urn:oid:1.2.392.100495.20.3.22#1] are in the value set 'http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1')
  Warning @ Bundle.entry[7].resource/*Organization/referralToOrganizationDeptExample01Inline*/.type[1].coding[0] (line 737, col16): Error performing tx4 operation 'validate-code: timeout' (parameters = "")    
  Warning @ Bundle.entry[7].resource.type[1].coding[0] (line 737, col16): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[8].resource/*Organization/referralFromOrganizationDeptExample01Inline*/.type[1].coding[0] (line 772, col16): Error performing tx4 operation 'validate-code: timeout' (parameters = "")  
  Warning @ Bundle.entry[8].resource.type[1].coding[0] (line 772, col16): Error performing tx4 operation 'validate-code: timeout' (parameters = "")
  Warning @ Bundle.entry[9].resource/*Encounter/purposeReferralExample01Inline*/.class (line 804, col10): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[17].resource/*Observation/psobsCommonExample01Inline*/.category[0] (line 1174, col12): Wrong Display Name '身体検査・観察' for http://terminology.hl7.org/CodeSystem/observation-category#exam - should be one of 1 choices: 'Exam' (for the language(s) 'en')
```

## 実行例2
* コマンドの実行例2（-tx オプション未指定） 

```sh
java -Dfile.encoding=UTF-8 -jar validator_cli.jar Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx n/a
```

* 上記コマンド実行結果
```sh
>java -Dfile.encoding=UTF-8 -jar validator_cli.jar Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx n/a
FHIR Validation tool Version 6.0.15 (Git# cc14a4142f40). Built 2023-06-08T21:23:55.484Z (36 hours old)
  Java:   17.0.5 from C:\Program Files\Amazon Corretto\jdk17.0.5_8 on amd64 (64bit). 6136MB available
  Paths:  Current = …\git\fhir-validation-cli, Package Cache = C:\Users\dell\.fhir\packages
  Params: Bundle-BundleReferralExample01.json -display-issues-are-warnings -ig jp-ereferral#0.9.7.tgz -tx n/a
Scanning for versions (no -version parameter):
  Package jp-ereferral#0.9.7.tgz: 4.0
-> use version 4.0
  Locale: 日本/JP
  Jurisdiction: Japan
Loading
  Load FHIR v4.0 from hl7.fhir.r4.core#4.0.1 - 4576 resources (00:06.906)
  Load hl7.terminology#5.1.0 - 4180 resources (00:03.237)
  Load hl7.terminology.r5#5.0.0 - 4174 resources (00:01.952)
  Load hl7.fhir.uv.extensions#1.0.0 - 1328 resources (00:00.040)
  Terminology server null - Version n/a: No Terminology Server (00:00.000)
  Load jp-ereferral#0.9.7.tgz+  .. load IG from jp-core.r4#1.1.1
Installing jp-core.r4#1.1.1 to the package cache
  Fetching:
2023-06-10 18:33:51.323 [main] INFO  o.h.f.u.npm.BasePackageCacheManager [BasePackageCacheManager.java:84] Failed to resolve package jp-core.r4#1.1.1 from server: http://packages.fhir.org (Invalid HTTP response 404 from http://packages.fhir.org/jp-core.r4/1.1.1 (Not Found) (content in c:\temp\http-log\fhir-http-2.log))
2023-06-10 18:33:52.346 [main] INFO  o.h.f.u.npm.BasePackageCacheManager [BasePackageCacheManager.java:84] Failed to resolve package jp-core.r4#1.1.1 from server: https://packages2.fhir.org/packages (Invalid HTTP response 404 from https://packages2.fhir.org/packages/jp-core.r4/1.1.1 (Not found) (content in c:\temp\http-log\fhir-http-3.log))
....
  Installing:  - 230 resources (00:04.478)
  Package Summary: [hl7.fhir.r4.core#4.0.1, hl7.fhir.xver-extensions#0.0.12, hl7.terminology#5.1.0, hl7.terminology.r5#5.0.0, hl7.fhir.uv.extensions#1.0.0]
  Get set... Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find HumanName profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_HumanName but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_Amount but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_Amount but trying to go on
Can't find Timing profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationTiming but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Range profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRange_UnitOfTime but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage_Injection but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerPeriod but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosageBase but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Ratio profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRatio_DosePerDay but trying to go on
Can't find Range profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationRange_UnitOfTime but trying to go on
Can't find Quantity profile http://hl7.org/fhir/StructureDefinition/SimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Quantity profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationSimpleQuantity but trying to go on
Can't find Dosage profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_MedicationDosage_Injection but trying to go on
 go (00:04.457)
Validating
  Validate Bundle-BundleReferralExample01.json
Validate Bundle against http://hl7.org/fhir/StructureDefinition/Bundle|4.0.1..........20..........40..Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
...Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner..........20..........40..........60..........80..........100..|
..Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department..........20..........40..........60..........80..........100..|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
...60..........80..........100..........120......|
Validate Bundle against http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferralValidate Composition against http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5|
Validate Patient against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Patient_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Patient against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Patient|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Practitioner against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5|
Validate Practitioner against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Practitioner|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Organization against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5|
Validate Organization against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Organization|
Validate Encounter against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5|
Validate Encounter against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|
Validate Encounter against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5|
Validate Encounter against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate Condition against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5|
Validate Condition against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5|
Validate AllergyIntolerance against http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|
Validate Observation against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Observation_Common_eClinicalSummary|1.1.5..........20..........40..........60..........80.........|
Validate Observation against http://jpfhir.jp/fhir/core/StructureDefinition/JP_Observation_Common|
Validate DocumentReference against http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_DocumentReference_eClinicalSummary|1.1.5|
Validate DocumentReference against http://jpfhir.jp/fhir/core/StructureDefinition/JP_DocumentReference|
..|
 00:02.377
Done. Times: Loading: 00:22.461, validation: 00:02.379. Memory = 609Mb

*FAILURE*: 35 errors, 13 warnings, 32 notes
  Error @ Bundle.entry[0] (line 17, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[1] (line 392, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[2] (line 458, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[3] (line 495, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[4] (line 538, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[5] (line 581, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[6] (line 648, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[7] (line 715, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[8] (line 750, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[9] (line 785, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[10] (line 816, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[11] (line 861, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[12] (line 905, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[13] (line 954, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[14] (line 1003, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[15] (line 1053, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[16] (line 1105, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[17] (line 1156, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[18] (line 1193, col6): Slicing cannot be evaluated: Profile based discriminators must have a type with a profile (Bundle.entry:bundleData.resource in profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/ (line 19, col8): Composition.author:authorPractitioner: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5)
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[1].entry[1] (line 223, col20): Unable to find a match for profile urn:uuid:a9358f3e-c743-4998-aa89-1be54c31b432 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[3].entry[0] (line 269, col20): Unable to find a match for profile urn:uuid:5c650562-f4e4-40b3-87c6-e9a0376be7a8 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[0] (line 297, col20): Unable to find a match for profile urn:uuid:711b07ae-d20b-40b0-9aa9-c7f1981409e6 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance
  Error @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[1] (line 302, col20): Unable to find a match for profile urn:uuid:5a1aae74-f720-415a-ba15-a6c8b5d4c5a1 among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance
  Error @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/ (line 460, col8): Encounter.reasonCode: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.meta (line 463, col10): Encounter.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Encounter_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta (line 500, col10): Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[4].resource.meta (line 543, col10): Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[7].resource.meta (line 720, col10): Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
  Error @ Bundle.entry[8].resource.meta (line 755, col10): Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
  Error @ Bundle.entry[11].resource/*Condition/cc2ReferralExample01Inline*/.meta (line 866, col10): Condition.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[13].resource/*Condition/ph1ReferralExample01Inline*/.meta (line 959, col10): Condition.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Condition_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Unable to find a match for profile urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter
  Error @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.meta (line 1110, col10): AllergyIntolerance.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_AllergyIntolerance_eClinicalSummary|1.1.5)
  Error @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Unable to find a match for profile urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e among choices: http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter
  Information @ Bundle (line 1, col2): Bundle.entry:composition: Found 0 matches, but unable to check minimum required (Bundle.entry) due to lack of slicing validation (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Information @ Bundle (line 1, col2): Bundle.entry:practitioners: Found 0 matches, but unable to check minimum required (Bundle.entry) due to lack of slicing validation (from http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Bundle_eReferral|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0] (line 71, col12): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorPractitioner' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Does not match slice 'authorDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1] (line 122, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource: Organization.type: max allowed = 1, but found 2 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[0].coding[0].code: Value is 'dept' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[1].coding[0].system: Value is 'urn:oid:1.2.392.100495.20.2.51' but 
must be 'http://terminology.hl7.org/CodeSystem/organization-type'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.type[1].coding[0].code: Value is '03' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Does not match slice 'referralToDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[1]: Bundle.entry[7].resource: Specified profile type was 'Practitioner' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5', but found type 'Organization'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2] (line 127, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Does not match slice 'referralToDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[0].entry[2]: Bundle.entry[4].resource.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1] (line 155, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource: Organization.type: max allowed = 1, but found 2 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[0].coding[0].code: Value is 'dept' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[1].coding[0].system: Value is 'urn:oid:1.2.392.100495.20.2.51' but 
must be 'http://terminology.hl7.org/CodeSystem/organization-type'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.type[1].coding[0].code: Value is '11' but must be 'prov'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource.meta: Organization.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5)
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Does not match slice 'referralFromDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[1]: Bundle.entry[8].resource: Specified profile type was 'Practitioner' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5', but found type 'Organization'
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2] (line 160, col16): This element does not match any known slice defined in the profile http://jpfhir.jp/fhir/eReferral/StructureDefinition/JP_Composition_eReferral|1.1.5
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromOrganization' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromDepartment' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource: Specified profile type was 'Organization' in profile 'http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Organization_eClinicalSummary_department|1.1.5', but found type 'Practitioner'
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Does not match slice 'referralFromDoctor' (discriminator: resolve().conformsTo('http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary'))
    slice info: Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[1].entry[2]: Bundle.entry[3].resource/*Practitioner/referralFromPractitionerExample01Inline*/.meta: Practitioner.meta.lastUpdated: minimum required = 1, but only found 0 (from http://jpfhir.jp/fhir/eClinicalSummary/StructureDefinition/JP_Practitioner_eClinicalSummary|1.1.5)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type (line 39, col10): None of the codings provided are in the value set 'FHIR Document Type Codes' (http://hl7.org/fhir/ValueSet/doc-typecodes|4.0.1), and a coding is recommended to come from this value set) (codes = http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes#57133-1)
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.encounter (line 64, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.author[0] (line 71, col12): Details for urn:uuid:3e6a0ba2-d781-4fd7-9de6-e077b690daed matching against profile http://hl7.org/fhir/StructureDefinition/Practitioner|4.0.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[1].entry[1] (line 223, col20): Details for urn:uuid:a9358f3e-c743-4998-aa89-1be54c31b432 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[3].entry[0] (line 269, col20): Details for urn:uuid:5c650562-f4e4-40b3-87c6-e9a0376be7a8 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Condition|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[0] (line 297, col20): Details for urn:uuid:711b07ae-d20b-40b0-9aa9-c7f1981409e6 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.section[2].section[4].entry[1] (line 302, col20): Details for urn:uuid:5a1aae74-f720-415a-ba15-a6c8b5d4c5a1 matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_AllergyIntolerance|1.1.1
  Information @ Bundle.entry[7].resource/*Organization/referralToOrganizationDeptExample01Inline*/.partOf (line 745, col10): Details for urn:uuid:9f92f003-69e6-4983-85eb-fb49a3110b59 matching against profile http://hl7.org/fhir/StructureDefinition/Organization|4.0.1
  Information @ Bundle.entry[8].resource/*Organization/referralFromOrganizationDeptExample01Inline*/.partOf (line 780, col10): Details for urn:uuid:2e979bec-720b-4e36-8eb9-ebe661172af3 matching against profile http://hl7.org/fhir/StructureDefinition/Organization|4.0.1
  Information @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[15].resource/*AllergyIntolerance/alg1ReferralExample01Inline*/.encounter (line 1090, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|1.1.1
  Information @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://hl7.org/fhir/StructureDefinition/Encounter|4.0.1
  Information @ Bundle.entry[16].resource/*AllergyIntolerance/alg1ReferralExample02Inline*/.encounter (line 1141, col10): Details for urn:uuid:7cad1f19-3435-451d-9a71-a81b61f3358e matching against profile http://jpfhir.jp/fhir/core/StructureDefinition/JP_Encounter|1.1.1
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type.coding[0] (line 41, col14): Code System URI 'http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes' is unknown so the code cannot be validated
  Information @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.category[0].coding[0] (line 51, col16): Code System URI 'http://jpfhir.jp/fhir/Common/CodeSystem/doc-typecodes' is unknown so the code cannot be validated
  Information @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 596, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.21' is unknown so the code cannot be validated
  Information @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 603, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.22' is unknown so the code cannot be validated
  Information @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 663, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.21' is unknown so the code cannot be validated
  Information @ Bundle.entry[6].resource.extension[0].value.ofType(Coding) (line 663, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.21' is unknown so the code cannot be validated
  Information @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 670, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.22' is unknown so the code cannot be validated
  Information @ Bundle.entry[6].resource.extension[1].value.ofType(Coding) (line 670, col14): Code System URI 'urn:oid:1.2.392.100495.20.3.22' is unknown so the code cannot be validated
  Information @ Bundle.entry[7].resource/*Organization/referralToOrganizationDeptExample01Inline*/.type[1].coding[0] (line 737, col16): Code System URI 'urn:oid:1.2.392.100495.20.2.51' is unknown so the code cannot be validated
  Information @ Bundle.entry[7].resource.type[1].coding[0] (line 737, col16): Code System URI 'urn:oid:1.2.392.100495.20.2.51' is unknown so the code cannot be validated
  Information @ Bundle.entry[8].resource/*Organization/referralFromOrganizationDeptExample01Inline*/.type[1].coding[0] (line 772, col16): Code System URI 'urn:oid:1.2.392.100495.20.2.51' is unknown so the code cannot be validated
  Information @ Bundle.entry[8].resource.type[1].coding[0] (line 772, col16): Code System URI 'urn:oid:1.2.392.100495.20.2.51' is unknown so the code cannot be validated
  Warning @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.type (line 39, col10): Unable to check whether the code is in the value set http://jpfhir.jp/fhir/Common/ValueSet/doc-typecodes|1.1.5
  Warning @ Bundle.entry[0].resource/*Composition/compositionReferralExample01Inline*/.category[0] (line 49, col12): Unable to check whether the code is in the value set http://jpfhir.jp/fhir/Common/ValueSet/doc-typecodes|1.1.5
  Warning @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.class (line 476, col10): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[2].resource/*Encounter/encounterReferralExample01Inline*/.classHistory[0].class (line 483, col14): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - 
should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 596, col14): Could not confirm that the codes provided are in the value set 
'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what is suitable)
  Warning @ Bundle.entry[5].resource/*Organization/referralFromOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 603, col14): Could not confirm that the codes provided are in the value set 
'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what is suitable)
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[0].value.ofType(Coding) (line 663, col14): Could not confirm that the codes provided are in the value set 'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what 
is suitable)
  Warning @ Bundle.entry[6].resource.extension[0].value.ofType(Coding) (line 663, col14): Could not confirm that the codes provided are in the value set 'JP Core PrefectureNumber ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_PrefectureNumber_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what is suitable)
  Warning @ Bundle.entry[6].resource/*Organization/referralToOrganizationExample01Inline*/.extension[1].value.ofType(Coding) (line 670, col14): Could not confirm that the codes provided are in the value set 'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what is suitable)
  Warning @ Bundle.entry[6].resource.extension[1].value.ofType(Coding) (line 670, col14): Could not confirm that the codes provided are in the value set 'JP Core MedicalFeeScoreType ValueSet' (http://jpfhir.jp/fhir/core/ValueSet/JP_MedicalFeeScoreType_VS|1.1.1), and a code should come from this value set unless it has no suitable code (the validator cannot judge what is suitable)
  Warning @ Bundle.entry[9].resource/*Encounter/purposeReferralExample01Inline*/.class (line 804, col10): Wrong Display Name '外来' for http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB - should be one of 1 choices: 'ambulatory' (for the language(s) 'en') for 'http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB'
  Warning @ Bundle.entry[17].resource/*Observation/psobsCommonExample01Inline*/.category[0] (line 1174, col12): Wrong Display Name '身体検査・観察' for http://terminology.hl7.org/CodeSystem/observation-category#exam - should be one of 1 choices: 'Exam' (for the language(s) 'en')
  Warning @ Bundle.entry[18].resource/*DocumentReference/ccourseReferralExample01Inline*/.content[0].attachment.contentType (line 1219, col56): The value provided ('text/plain;charset=UTF-8') could not be validated because the code system urn:ietf:bcp:13 is not known
```