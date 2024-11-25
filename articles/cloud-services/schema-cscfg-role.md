---
title: Azure Cloud Services (クラシック) のロール スキーマ | Microsoft Docs
description: サービス構成ファイルの Role 要素では、ロールごとにデプロイするロール インスタンスの数、構成値、および証明書のサムプリントを指定します。
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimtckit
ms.custom: ''
ms.openlocfilehash: 72a9035a1185c91a65fad01d19b919c5f609f49d
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823140"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Azure Cloud Services (クラシック) 構成のロール スキーマ

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

構成ファイルの `Role` 要素で、サービスの各ロールにデプロイするロール インスタンス数、すべての構成設定の値、およびロールに関連付けられているすべての証明書のサムプリントを指定します。

Azure サービス構成スキーマの詳細については、「[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md)」(クラウド サービス (クラシック) 構成スキーマ) を参照してください。 Azure サービス定義スキーマの詳細については、「[Cloud Service (classic) Definition Schema](schema-csdef-file.md)」(クラウド サービス (クラシック) 定義スキーマ) を参照してください。

##  <a name="role-element"></a><a name="Role"></a> Role 要素
次の例は、`Role` 要素とその子要素を示します。

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

次の表は、`Role` 要素の属性の説明です。

| 属性 | 説明 |
| --------- | ----------- |
| name   | 必須。 ロール名を指定します。 この名前は、サービス定義ファイルでロールに指定された名前と一致する必要があります。|
| vmName | 省略可能。 仮想マシンの DNS 名を指定します。 名前は 10 文字以下にする必要があります。|

次の表は、`Role` 要素の子要素の説明です。

| 要素 | 説明 |
| ------- | ----------- |
| インスタンス | 必須。 ロールにデプロイするインスタンス数を指定します。 `count` 属性のインスタンス数を整数で定義します。|
| 設定   | 省略可能。 ロールの設定のコレクションで、設定の名前と値を指定します。 設定名は、`name` 属性を使用して文字列で定義し、設定値は `value` 属性を使用して文字列で定義します。|
| Certificate | 省略可能。 ロールに関連付けるサービス証明書の名前、サムプリント、およびアルゴリズムを指定します。 証明書名は、`name` 属性を使用して文字列で定義します。 証明書のサムプリントは、`thumbprint` 属性を使用して、スペースを含まない 16 進数の文字列で定義します。 16 進数は、数字と大文字の英字を使用して表す必要があります。 証明書アルゴリズムは、`thumbprintAlgorithm` 属性を使用して文字列で定義します。|

## <a name="see-also"></a>参照
[Cloud Service (クラシック) 構成スキーマ](schema-cscfg-file.md)