---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 0198bcdd34cd92822c779329affeb1a405264bae
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830534"
---
## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

::: zone pivot="programming-language-csharp"
[!INCLUDE [functions-cli-dotnet-prerequisites](functions-cli-dotnet-prerequisites.md)]
::: zone-end  
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other" 
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2)。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。 
:::zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 ビット)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 ビット)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 ビット)](https://www.python.org/downloads/release/python-368/)。これらは Azure Functions でサポートされています。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 または 11。 

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

::: zone-end
::: zone pivot="programming-language-other"
+ 使用している言語の開発ツール。 このチュートリアルでは、例として [R プログラミング言語](https://www.r-project.org/)を使用します。
::: zone-end

アクティブなサブスクリプションを含む Azure アカウントも必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。