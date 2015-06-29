<properties 
	pageTitle="Usando marcas para organizar os recursos do Azure" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="micflan"/>


# Usando marcas para organizar os recursos do Azure

O portal do Azure e o Gerenciador de Recursos subjacente são ferramentas que organizam seus recursos e personalizam sua experiência, tornando-a sob medida para você.

No portal clássico do Azure, as assinaturas são a única maneira de classificar e agrupar recursos. Com o portal do Azure, [apresentamos os grupos de recursos](./resource-group-portal.md), que permitem agrupar entidades relacionadas. Isso se tornou ainda mais valioso quando [introduzimos o acesso baseado em função](./role-based-access-control-configure.md). Agora, no mesmo espírito, você pode marcar seus recursos com pares de chave/valor para exibir os recursos e categorizá-los ainda mais em grupos e, dentro do portal, em assinaturas.

Agrupe os recursos por equipe, projeto ou até mesmo ambiente para concentrar-se exatamente no que você deseja ver, quando você precisa vê-lo.


## Marcas no portal do Azure

Marcar recursos e grupos de recursos no portal é fácil. Use o hub Procurar para navegar até o recurso ou o grupo de recursos que você gostaria de marcar e clique na parte de Marcas na seção Visão geral, na parte superior da lâmina.

![Parte de marcas nas folhas de recurso e grupo de recursos](./media/resource-group-using-tags/rgblade.png)

Isso abrirá uma lâmina com a lista de marcas que já foram aplicadas. Se esta for sua primeira marca, a lista estará vazia. Para adicionar uma marca, especifique um nome e valor e pressione Enter. Depois de adicionar algumas marcas, você verá opções de preenchimento automático com base em valores e nomes de marca pré-existentes, para melhor assegurar uma taxonomia consistente entre os recursos e evitar erros comuns, como erros de ortografia.

![Marcar recursos com pares de nome/valor](./media/resource-group-using-tags/tag-resources.png)

Aqui, você pode clicar em cada marca individual para exibir uma lista de todos os recursos com a mesma marca. É claro que, se esta for sua primeira marca, essa lista não será muito interessante. Por enquanto, vamos pular para o PowerShell para marcar todos os nossos recursos rapidamente.


## Marcação com o PowerShell

A primeira coisa que deve ser feita é captar o [módulo do PowerShell do Azure](./install-configure-powershell.md) mais recente. Se esta é a primeira vez que você está usando o módulo PowerShell do Azure, [leia a documentação](./install-configure-powershell.md) para se familiarizar. Para os fins deste artigo, vamos supor que você já adicionou uma conta e selecionou uma assinatura com os recursos que deseja marcar.

A marcação está disponível somente para recursos e grupos de recursos disponíveis do [Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), portanto, a próxima coisa que precisamos fazer é passar a usar o Gerenciador de Recursos. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

As marcas existem diretamente em recursos e grupos de recursos, portanto, para ver quais marcas já estão aplicadas, podemos simplesmente obter um recurso ou grupo de recursos com `Get-AzureResource` ou `Get-AzureResourceGroup`, respectivamente. Vamos começar com um grupo de recursos.

![Obtendo marcas com Get-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver. Para marcar um grupo de recursos, simplesmente usaremos `Set-AzureResourceGroup` e especificaremos um valor e nome de marca.

![Obtendo marcas com Set-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Lembre-se de que as marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará usar uma matriz com todas as marcas que você deseja manter. Para remover uma marca, simplesmente salve a matriz sem aquela que deseja remover.

O processo é o mesmo para os recursos, exceto pelo fato de que você usará os cmdlets `Get-AzureResource` e `Set-AzureResource`. Para obter recursos ou grupos de recursos com uma marca específica, use o cmdlet `Get-AzureResource` ou `Get-AzureResourceGroup` com o parâmetro `-Tag`.

![Obtendo recursos e grupos de recursos marcados com Get-AzureResource e Get-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Marcação com o Gerenciador de Recursos

O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## Gerenciando sua taxonomia

Anteriormente, falamos sobre como o preenchimento automático ajuda você a garantir a consistência e evitar erros. O preenchimento automático é realizado com base na taxonomia da configuração de marcas disponíveis para a assinatura. Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia toda a assinatura, mas você também pode realizar um pré-preenchimento dessa taxonomia com nomes de marca e valores que você deseja usar como recursos e serão marcados no futuro.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet `Get-AzureTag`.

![Get-AzureTag no PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcas internas, que você deve ignorar e evitar alterar.

Use o cmdlet `New-AzureTag` para adicionar novas marcas à taxonomia. Essas marcas serão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet `Remove-AzureTag` para removê-la da taxonomia.

Para exibir sua taxonomia de marcas no portal, use o hub Procurar para exibir Tudo e, em seguida, selecionar Marcas.

![Localizar marcas pelo hub Procurar](./media/resource-group-using-tags/browse-tags.png)

Fixe as marcas mais importantes no seu quadro inicial para acesso rápido e você estará pronto para começar. Divirta-se!

![Fixar marcas no Quadro Inicial](./media/resource-group-using-tags/pin-tags.png)

## Próximas etapas
Introdução

- [Visão Geral do Gerenciador de Recursos do Azure](./resource-group-overview.md)  
- [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md)
- [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./xplat-cli-azure-resource-manager.md)  
- [Usando o Portal do Azure para gerenciar os recursos do Azure](./resource-group-portal.md)  
  
Criação e implantação de aplicativos
  
- [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md)  
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](./resource-group-template-deploy.md)  
- [Solucionando problemas de implantações de grupos de recursos no Azure](./resource-group-deploy-debug.md)  
- [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md)  
- [Operações avançadas de modelo](./resource-group-advanced-template.md)  
  
Gerenciar e auditar o acesso
  
- [Gerenciar e auditar o acesso a recursos](./resource-group-rbac.md)  
- [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](./resource-group-authenticate-service-principal.md)  
- [Criar uma nova entidade de serviço do Azure usando o portal clássico do Azure](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=58_postMigration-->