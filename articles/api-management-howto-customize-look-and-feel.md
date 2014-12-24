<properties pageTitle="How to customize the look and feel of the developer portal in Azure API Management" metaKeywords="" description="How to customize the look and feel of the developer portal in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to customize the look and feel of the developer portal in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Como personalizar a aparência do portal do desenvolvedor no Gerenciamento de API do Azure

As cores, fontes, tamanhos, espaços e outros elementos relacionados à aparência do portal do desenvolvedor são definidos por regras de estilo. Há conjuntos dessas regras para cada elemento estrutural de uma página - o cabeçalho, o menu, o corpo de conteúdo, o título da página, etc. Nestas instruções, você aprenderá como modificar as regras de estilo.

Para editar as regras de estilo, clique em **Aparência** no menu **Portal do desenvolvedor** no Portal do editor. Em seguida, clique em **Iniciar personalização** para habilitar o editor de estilo.

Seu navegador alternará para uma página oculta no portal do desenvolvedor que contém exemplos de conteúdo, com exemplos para todas as regras de estilo usadas em qualquer parte do site. Para abrir o editor de estilo, mova o cursor sobre a linha cinza vertical fina na parte extrema esquerda da página. A barra de ferramentas do editor será exibida.

![Barra de ferramentas de personalização][Barra de ferramentas de personalização]

Existem dois modos principais de editar as regras de estilo - **Editar todas as regras** exibe uma lista de todas as regras de estilo usadas em todo lugar; enquanto **Separar elemento** permite selecionar um elemento na página em que você está e exibir os estilos somente para esse elemento.

Nesta seção, gostaríamos de alterar o estilo somente de um elemento específico - por exemplo, os cabeçalhos da página. Clique na opção **Separar elemento** da barra de ferramentas do editor de estilo e depois clique em **Selecionar elemento para personalizar**. Os elementos serão destacados à medida que você passar o mouse sobre eles, indicando quais estilos do elemento você verá ao clicar.

Mova o mouse sobre o texto que representa o título da página no cabeçalho e clique nele. Um conjunto de regras de estilo categorizadas e nomeadas será exibido no editor de estilos.

Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1, enquanto que o nome da fonte com alternativas está em in @headings-font-family.

> Se estiver familiarizado com [bootstrap][<http://getbootstrap.com/>], essas regras são, na verdade, [variáveis LESS][<http://getbootstrap.com/css/>] no tema do bootstrap usadas pelo portal do desenvolvedor.

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada em **@headings-color** e digite \#000000. Este é o código hexadecimal da cor preta. À medida que fizer isso verá um indicador de cor quadrado exibido no final da caixa de texto. Se você clicar nesse indicador, um seletor de cor permitirá que você escolha uma cor.

![Seletor de cor][Seletor de cor]

Ao finalizar as alterações nos estilos do elemento selecionado, clique em **Visualizar alterações** para ver os resultados na tela. Desta vez, elas serão visíveis somente para os Administradores. Para tornar essas alterações visíveis para qualquer um, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Publicar forma][Publicar forma]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento do cabeçalho - clique em **Separar elemento** no editor de estilo, selecione o elemento desejado e comece a modificar os valores das regras de estilo exibidas na tela.

  [Barra de ferramentas de personalização]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
  [Seletor de cor]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
  [Publicar forma]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png