<properties
	pageTitle="Política do Gerenciador de Recursos do Azure | Microsoft Azure"
	description="Descreve como usar a política do Gerenciador de Recursos do Azure para evitar violações em escopos diferentes como assinatura de recursos individuais ou grupos de recursos."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="12/18/2015"
	ms.author="gauravbh;tomfitz"/>

# Usar a política para gerenciar recursos e controlar o acesso

O Gerenciador de Recursos do Azure agora permite que você controle o acesso por meio de políticas personalizadas. Com as políticas, você pode impedir que os usuários em sua organização violem convenções que são necessárias para gerenciar os recursos de sua organização.

Crie definições de política que descrevem as ações ou os recursos que são especificados negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual.

Neste artigo, explicaremos a estrutura básica de linguagem de definição de política que você pode usar para criar políticas. Em seguida, descreveremos como você pode aplicar essas políticas em escopos diferentes e, por fim, mostraremos alguns exemplos sobre como você pode obter isso por meio da API REST.

A política está disponível atualmente como uma visualização.

## Qual é a diferença dela em relação ao RBAC?

Há algumas diferenças importantes entre a política e o controle de acesso baseado em função, mas a primeira coisa que é preciso entender é que as políticas e o RBAC funcionam juntos. Para poder usar a política, o usuário deve ser autenticado pelo RBAC. Ao contrário do RBAC, a política é um sistema de permissão padrão e negação explícita.

O RBAC concentra-se nas ações que um **usuário** pode realizar em escopos diferentes. Por exemplo, um usuário específico é adicionado à função de colaborador de um grupo de recursos no escopo do desejado, para que o usuário possa fazer alterações a esse grupo de recursos.

A política concentra-se nas ações de **recurso** em vários escopos. Por exemplo, por meio de políticas, é possível controlar os tipos de recursos que podem ser provisionados ou restringir os locais em que os recursos podem ser provisionados.

## Cenários comuns

Um cenário comum é requerer marcas departamentais para fins de estorno. Uma organização talvez queira permitir operações somente quando o centro de custo apropriado for associado. Caso contrário, a solicitação será negada. Isso os ajudaria a cobrar o centro de custo apropriado para as operações executadas.

Outro cenário comum é que a organização talvez queira controlar os locais onde os recursos são criados. Ou talvez queiram controlar o acesso aos recursos, permitindo que somente determinados tipos de recursos sejam provisionados.

Da mesma forma, uma organização pode controlar o catálogo de serviços ou aplicar as convenções de nomenclatura desejadas para os recursos.

Usando políticas, esses cenários podem ser facilmente obtidos, conforme descrito abaixo.

## Estrutura da definição de política

A definição de política é criada usando JSON. Consiste em uma ou mais condições/operadores lógicos que definem as ações e o efeito que informa o que acontece quando as condições são atendidas.

Basicamente, uma política contém o seguinte:

**Condição/Operadores lógicos:** contém um conjunto de condições que podem ser manipuladas por meio de um conjunto de operadores lógicos.

**Efeito:** descreve qual será o efeito quando a condição for satisfeita – negar ou auditar. Um efeito de auditoria emitirá um log de aviso de serviço de evento. Por exemplo, um administrador pode criar uma política que ocasiona uma auditoria se alguém criar uma VM grande e examinar os logs mais tarde.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Operadores lógicos

Os operadores lógicos compatíveis junto com a sintaxe estão listados abaixo:

| Nome do operador | Sintaxe |
| :------------- | :------------- |
| Not | "not" : {&lt;condition or operator &gt;} |
| e | "allOf" : [ {&lt;condição ou operador &gt;},{&lt;condição ou operador &gt;}] |
| Ou | "anyOf" : [ {&lt;condição ou operador &gt;},{&lt;condição ou operador &gt;}] |

O Gerenciador de Recursos permite que você especifique uma lógica complexa em sua política por meio de operadores aninhados. Por exemplo, você pode recusar a criação de recursos em um local específico para um tipo de recurso especificado. Veja abaixo um exemplo de operadores aninhados.

## Condições

Uma condição avalia se um **campo** ou uma **fonte** atende a determinados critérios. Os nomes de condição e a sintaxe com suporte são listados abaixo:

| Nome da condição | Sintaxe |
| :------------- | :------------- |
| É igual a | "equals" : "&lt;value&gt;" |
| Como | "like" : "&lt;value&gt;" |
| Contém: | "contains" : "&lt;value&gt;"|
| No | "in" : [ "&lt;value1&gt;","&lt;value2&gt;" ]|
| ContainsKey | containsKey" : "&lt;keyName&gt;" |

## Campos e fontes

As condições são formadas por meio do uso de campos e fontes. Um campo representa propriedades na carga de solicitação de recursos. Uma fonte representa as características da solicitação em si.

Há suporte para os seguintes campos e fontes:

Campos: **nome**, **forma**, **tipo**, **local**, **tags**, **tags.***.

Fontes: **ação**.

Para saber mais sobre ações, confira [RBAC - Funções internas](active-directory/role-based-access-built-in-roles.md). Atualmente, a política só funciona em solicitações PUT.

## Exemplos de definições de política

Agora veremos como definir a política para obter os cenários listados acima.

### Estorno: Requerem marcas departamentais

A política a seguir nega todas as solicitações que não têm uma marca que contenha a chave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### Conformidade geográfica: Garantir locais de recursos

O exemplo abaixo mostra uma política que negará todas as solicitações em que o local não seja Norte da Europa ou Europa Ocidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Curadoria de serviço: Selecione o catálogo de serviços

O exemplo abaixo mostra o uso do código-fonte. Ele mostra que ações somente nos serviços do tipo Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/*, Microsoft.Network/* são permitidas. Todo o resto será negado.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Convenção de nomenclatura

O exemplo abaixo mostra o uso de curingas que é compatível com a condição "como". A condição declara que se o nome corresponder ao padrão mencionado (namePrefix * nameSuffix), a solicitação é negada.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### Requisito de marca apenas para recursos de Armazenamento

O exemplo abaixo mostra como aninhar operadores lógicos para exigir uma marca de aplicativo somente para recursos de armazenamento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "source": "action",
                "like": "Microsoft.Storage/*"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## Atribuição de política

As políticas podem ser aplicadas em escopos diferentes, como assinatura, grupos de recursos e recursos individuais. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, será aplicável a todos os recursos desse grupo de recursos.

## Criando uma política

Esta seção fornece detalhes sobre como uma política pode ser criada usando a API REST.

### Crie definição de política com a API REST

Você pode criar uma política com a [API REST para Definições de Política](https://msdn.microsoft.com/library/azure/mt588471.aspx). A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes.

Para criar uma nova política, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Com um corpo de solicitação semelhante ao seguinte:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


Definição de política pode ser definida como um dos exemplos mostrados acima. Para a versão de API, use *2015-10-01-preview*. Para obter exemplos e mais detalhes, veja a [API REST para Definições de Política](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Criar definição de política usando o PowerShell

Você pode criar uma nova definição de política usando o cmdlet New-AzureRmPolicyDefinition, conforme mostrado abaixo. Os exemplos a seguir criam uma política para permitir recursos somente na Europa Setentrional e Ocidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

A saída da execução é armazenada no objeto $policy, visto que ele pode ser usado durante a atribuição da política. Para o parâmetro de política, o caminho para um arquivo .json que contém a política também pode ser fornecido em vez de especificar a política incorporada, conforme mostrado abaixo.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Aplicando uma política

### Atribuição de política com a API REST

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx). A API REST permite que você crie e exclua as atribuições de políticas e obtenha informações sobre as atribuições existentes.

Para criar uma nova atribuição de política, execute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

A {Atribuição da política} é o nome da atribuição da política. Para a versão de API, use *2015-10-01-preview*.

Com um corpo de solicitação semelhante ao seguinte:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Para obter exemplos e mais detalhes, veja a [API REST para Atribuições de Política](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Atribuição de política usando o PowerShell

Você pode aplicar a política criada acima por meio do PowerShell para o escopo desejado usando o cmdlet New-AzureRmPolicyAssignment, conforme mostrado abaixo:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Aqui, $policy é o objeto de política que foi retornado como resultado da execução do cmdlet New-AzureRmPolicyDefinition, conforme mostrado acima. O escopo aqui é o nome do grupo de recursos que você especificar.

Se você desejar remover a atribuição de política acima, você pode fazê-lo da seguinte forma:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover as definições de políticas por meio dos cmdlets Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e Remove-AzureRmPolicyDefinitionn respectivamente.

Da mesma forma, você pode obter, alterar ou remover as atribuições da política por meio dos cmdlets Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e Remove-AzureRmPolicyAssignment respectivamente.

##Eventos de auditoria de política

Depois de aplicar a política, você começará a ver eventos relacionados à política. Você pode acessar o portal ou usar o PowerShell para obter esses dados.

Para exibir todos os eventos relacionados ao efeito de recusa, você pode usar o comando a seguir.

    Get-AzureRmLog | where {$_.subStatus -eq "Forbidden"}     

Para exibir todos os eventos relacionados ao efeito de auditoria, você pode usar o comando a seguir.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 
    

<!---HONumber=AcomDC_0128_2016-->