---
title: 如何将模型部署到 Azure 容器实例
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 容器实例将 Azure 机器学习模型部署为 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475471"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>将模型部署到 Azure 容器实例
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 Azure 机器学习将模型部署为 Azure 容器实例 (ACI) 上的 Web 服务。 如果满足下列任一条件，请使用 Azure 容器实例：

- 你需要快速部署并验证你的模型。 不需要提前创建 ACI 容器。 将在部署过程中创建它们。
- 正在测试一个开发中的模型。 

有关 ACI 的配额和区域可用性的信息，请参阅 [Azure 容器实例的配额和区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)文章。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 工作区中注册的机器学习模型。 如果尚未注册模型，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 本文中的 Python 代码片段假设设置了以下变量____：

    * `ws` - 设置为工作区。
    * `model` - 设置为注册的模型。
    * `inference_config` - 设置为模型的推理配置。

    有关如何设置这些变量的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的 CLI 片段假设已创建 `inferenceconfig.json` 文档____。 有关如何创建此文档的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="deploy-to-aci"></a>部署到 ACI

要将模型部署到 Azure 容器实例，请创建一个描述所需计算资源的部署配置____。 例如，核心数和内存。 此外，还需要一个推理配置，描述托管模型和 Web 服务所需的环境____。 有关如何创建推理配置的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

要使用 CLI 进行部署，请使用以下命令。 将 `mymodel:1` 替换为注册的模型的名称和版本。 将 `myservice` 替换为要赋予此服务的名称：

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

有关详细信息，请参阅 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 参考文档。 

## <a name="using-vs-code"></a>使用 VS Code

请参阅[使用 VS Code 部署模型](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 无需事先创建 ACI 容器即可进行测试。 将据需要创建 ACI 容器。

## <a name="update-the-web-service"></a>更新 Web 服务

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑难解答](how-to-troubleshoot-deployment.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用应用程序见解监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
