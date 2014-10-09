<properties pageTitle="Customizing the developer portal in Azure API Management" metaKeywords="" description="Customizing the developer portal in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="Customizing the developer portal in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Personalização do portal do desenvolvedor no Gerenciamento de API do Azure

Este guia mostra como modificar a aparência do portal do desenvolvedor no Gerenciamento de API (Visualização) para manter consistência com a marca.

## Neste tópico

-   [Alterar o texto/logotipo nos cabeçalhos da página][]
-   [Alterar o estilo dos cabeçalhos][]
-   [Editar o conteúdo de uma página][]
-   [Próximas etapas][]

## <a name="change-page-headers"> </a>Alterar o texto/logotipo no cabeçalho da página

Um dos principais aspectos da personalização do portal é a substituição do texto na parte superior de todas as páginas pelo nome ou logotipo de sua empresa.

O conteúdo no portal do desenvolvedor é modificado através do Portal do Publicador, que á acessado no Portal de Gerenciamento do Azure. Para acessar o console de Gerenciamento de API, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Console de gerenciamento][]

O portal do desenvolvedor é baseado em CMS (Sistema de Gerenciamento de Conteúdo). O cabeçalho que aparece em cada página é um tipo especial de conteúdo conhecido como widget. Para editar o conteúdo do widget, clique em **Widgets** no menu **Portal do desenvolvedor** do lado esquerdo e depois selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widget][]

O conteúdo do cabeçalho é editável no campo **Corpo**. Altere o texto para "Fabrikam Developer Portal" e clique em **Salvar** na parte inferior da página.

Agora você poderá ver o novo cabeçalho em cada página no portal do desenvolvedor!

> Para abrir o portal do desenvolvedor enquanto estiver no console de gerenciamento, clique em **Portal do desenvolvedor** na barra superior.

## <a name="change-headers-styling"> </a>Alterar o estilo dos cabeçalhos

As cores, fontes, tamanhos, espaços e outros elementos relacionados ao estilo de qualquer página no portal são definidos pelas regras de status. Para editar os estilos, clique em **Aparência** no menu **Portal do Desenvolvedor** do Portal do Publicador. Em seguida, clique em **Iniciar personalização** para habilitar o editor de estilo.

Seu navegador alternará para uma página oculta no portal do desenvolvedor que contém exemplos de conteúdo, com exemplos para todas as regras de estilo usadas em qualquer parte do site. Para abrir o editor de estilo, mova o cursor sobre a linha cinza vertical fina na parte extrema esquerda da página. A barra de ferramentas do editor será exibida.

![Barra de ferramentas de personalização][]

Existem dois modos principais para editar as regras de estilo - **Editar todas as regras** exibe uma lista de todas as regras de estilo usadas em todo lugar; enquanto **Separar elemento** permite selecionar um elemento na página em que você está e exibir os estilos somente para esse elemento.

Nesta seção gostaríamos de alterar o estilo somente dos cabeçalhos. Clique na opção **Separar elemento** da barra de ferramentas do editor de estilo e depois clique em **Selecionar elemento para personalizar**. Os elementos serão destacados à medida que você passar o mouse sobre eles, indicando quais estilos do elemento você vai começar editar ao clicar. Mova o mouse sobre o texto que representa o nome da empresa no cabeçalho ("Fabrikam Developer Portal" se você seguiu as instruções na seção anterior) e clique nele. Um conjunto de regras de estilo categorizadas e nomeadas será exibido no editor de estilos.

Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1, enquanto que o nome da fonte com alternativas está em in @headings-font-family.

> Se estiver familiarizado com [bootstrap][], essas regras são, na verdade, [variáveis LESS][] no tema do bootstrap usadas pelo portal do desenvolvedor.

<p>
Vamos alterar a cor do texto do cabeçalho. Selecione a entrada em

<script type="text/javascript">
<!--
h='&#104;&#x65;&#x61;&#100;&#x69;&#110;&#x67;&#x73;&#x2d;&#x63;&#x6f;&#108;&#x6f;&#114;&#42;';a='&#64;';n='&#42;&#42;';e=n+a+h;
document.write('<a h'+'ref'+'="ma'+'ilto'+':'+e+'" clas'+'s="em' + 'ail">'+e+'<\/'+'a'+'>');
// -->
</script>
<noscript>
\*\* no campo cor do cabeçalho\*

</noscript>
\* e digite \#000000. Este é o código hexadecimal da cor preta. À medida que fizer isso verá um indicador de cor quadrado exibido no final da caixa de texto. Se você clicar nesse indicador, um seletor de cor permitirá que você escolha uma cor.

</p>
![Seletor de cor][]

Ao finalizar as alterações nos estilos do elemento selecionado, clique em **Visualizar alterações** para ver os resultados na tela. Desta vez, elas serão visíveis somente para os Administradores. Para tornar essas alterações visíveis para qualquer um, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Menu Publicar][]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento do cabeçalho - clique em **Separar elemento** no editor de estilo, selecione o elemento desejado e comece a modificar os valores das regras de estilo exibidas na tela.

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página

O portal do desenvolvedor consiste em páginas geradas automaticamente, como APIs, Produtos, Aplicativos, Questões e conteúdo escrito manualmente. Como ele está baseado em um sistema de gerenciamento de conteúdo, você pode criar esse conteúdo conforme necessário.

Para ver uma lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do desenvolvedor** no Console de gerenciamento.

![Gerenciar conteúdo][]

Clique na página “Bem-vindo" para editar o que é exibido na página inicial do portal do desenvolvedor. Faça as alterações desejadas, visualize-as se necessário e depois clique em **Publicar agora** para torná-las visíveis para todos.

> A página inicial usa um layout especial que permite exibir uma faixa na parte superior. Essa faixa não é editável na seção Conteúdo. Para editá-la, clique em **Widgets** no menu **Portal do desenvolvedor**, depois selecione **Página inicial** na lista suspensa **Camada atual** e, em seguida, abra o item **Faixa** na seção Em destaque. O conteúdo desse widget pode ser editado como o conteúdo de qualquer outra página.

## <a name="next-steps"> </a>Próximas etapas

-   Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][].

  [Alterar o texto/logotipo nos cabeçalhos da página]: #change-page-headers
  [Alterar o estilo dos cabeçalhos]: #change-headers-styling
  [Editar o conteúdo de uma página]: #edit-page-contents
  [Próximas etapas]: #next-steps
  [Console de gerenciamento]: ./media/api-management-customize-portal/api-management-management-console.png
  [Cabeçalho de widget]: ./media/api-management-customize-portal/api-management-widgets-header.png
  [Barra de ferramentas de personalização]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
  [bootstrap]: http://getbootstrap.com/
  [variáveis LESS]: http://getbootstrap.com/css/
  [Seletor de cor]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
  [Menu Publicar]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
  [Gerenciar conteúdo]: ./media/api-management-customize-portal/api-management-customization-manage-content.png
  [Introdução à configuração avançada de API]: ../api-management-get-started-advanced