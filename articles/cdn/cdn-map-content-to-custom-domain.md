<properties 
	 pageTitle="Como mapear o conteúdo da CDN (rede de distribuição de conteúdo) para um domínio personalizado" 
	 description="Este tópico demonstra como mapear conteúdo da CDN para um domínio personalizado." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="01/22/2016" 
	 ms.author="casoper"/>

# Como mapear o domínio personalizado para o ponto de extremidade da CDN (Rede de Distribuição de Conteúdo)
Você pode mapear um domínio personalizado para um ponto de extremidade da CDN para usar seu próprio nome de domínio em URLs para o conteúdo armazenado em cache, em vez de usar um subdomínio do azureedge.net.

Há duas maneiras de mapear seu domínio personalizado para um ponto de extremidade da CDN.

1. [Criar um registro CNAME com seu registrador de domínios e mapear seu domínio e subdomínio personalizados para o ponto de extremidade da CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint) 
	
	Um Registro CNAME é um recurso do DNS que mapeia um domínio de origem a um domínio de destino. Nesse caso, o domínio de origem corresponde a seu domínio e subdomínio personalizados (o subdomínio é sempre necessário). O domínio de destino é o ponto de extremidade da CDN.

	O processo de mapeamento de seu domínio personalizado para o ponto de extremidade da CDN pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você está registrando o domínio no Portal do Azure.
	 
2. [Adicionar uma etapa de registro intermediária com **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

	Se atualmente o seu domínio personalizado oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que não permita um tempo de inatividade, então você pode usar o subdomínio **cdnverify** do Azure para fornecer uma etapa intermediária de registro para que os usuários possam acessar seu domínio durante o mapeamento do DNS.

Depois de registrar seu domínio personalizado usando um dos procedimentos acima, você desejará [verificar se o subdomínio personalizado referencia seu ponto de extremidade da CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Você deve criar um registro CNAME com seu registrador de domínios para mapear o domínio para o ponto de extremidade da CDN. Os registros CNAME mapeiam subdomínios específicos como www.meudominio.com ou meublog.meudominio.com. Não é possível mapear um registro CNAME para um domínio raiz, como meudominio.com.
>    
> Um subdomínio só pode ser associado a um ponto de extremidade da CDN. O registro CNAME que você criar roteará todo o tráfego endereçado ao subdomínio para o ponto de extremidade especificado. Por exemplo, se você associar **www.meudominio.com** ao seu ponto de extremidade da CDN, não poderá associá-lo a outros pontos de extremidade do Azure, como um ponto de extremidade da conta de armazenamento ou um ponto de extremidade de serviço de nuvem. No entanto, você pode usar outros subdomínios do mesmo domínio para pontos de extremidade de serviço diferentes. Você também pode mapear outros subdomínios para o mesmo ponto de extremidade da CDN.
>
> Observe que levará até **90 minutos** para a propagação das alterações de domínio personalizado nos nós de borda da CDN.

## Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure

1.	Faça logon no [Portal do Azure](https://portal.azure.com/).
2.	Clique em **Procurar**, em **Perfis CDN** e no perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3.	Na folha **Perfil CDN**, clique no ponto de extremidade da CDN ao qual você deseja associar o subdomínio.
4.	Na parte superior da folha de ponto de extremidade, clique no botão **Adicionar Domínio Personalizado**. Na folha **Adicionar um domínio personalizado**, você verá o nome do host do ponto de extremidade, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME. O formato do endereço do nome do host aparecerá como **&lt;EndpointName>.azureedge.net**. Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME.  
5.	Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6.	Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras CNAME, Alias ou Subdomínios.
7.	Crie um novo registro CNAME que mapeie o subdomínio escolhido (por exemplo, **www** ou **cdn**) para o nome de host fornecido na folha **Adicionar domínios personalizados**.
8.	Retorne à folha **Adicionar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato **www.meudominio.com** ou **cdn.meudominio.com**.   

	O Azure verificará se o registro CNAME existe para o nome de domínio que você digitou. Se o CNAME estiver correto, seu domínio personalizado será validado. Lembre-se de que levará até 90 minutos para a propagação das configurações de domínio personalizado para todos os nós de borda da CDN.

	Observe que, em alguns casos, pode levar algum tempo para que o registro CNAME se propague para servidores de nomes na Internet. Se seu domínio não for validado imediatamente e você achar que o registro CNAME está correto, aguarde alguns minutos e tente novamente.


## Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure usando o subdomínio intermediário cdnverify.  

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**, em **Perfis CDN** e no perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3. Na folha **Perfil CDN**, clique no ponto de extremidade da CDN ao qual você deseja associar o subdomínio.
4. Na parte superior da folha de ponto de extremidade, clique no botão **Adicionar Domínio Personalizado**. Na folha **Adicionar um domínio personalizado**, você verá o nome do host do ponto de extremidade, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME. O formato do endereço do nome do host aparecerá como **&lt;EndpointName>.azureedge.net**. Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME.
5. Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
7. Crie um novo registro CNAME e forneça um alias de subdomínio que inclua o subdomínio **cdnverify**. Por exemplo, o subdomínio especificado estará no formato **cdnverify.www** ou **cdnverify.cdn**. Então, forneça o nome de host, que é o ponto de extremidade da CDN, no formato **cdnverify.&lt;NomeDoPontoDeExtremidade>.azureedge.net**.
8. Retorne à folha **Adicionar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato **www.meudominio.com** ou **cdn.meudominio.com**. Observe que, nessa etapa, você não precisa prefaciar o subdomínio com **cdnverify**.  

	O Azure verificará se o registro CNAME existe para o nome de domínio cdnverify que você inseriu.
9. Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para seu ponto de extremidade da CDN. Após aguardar 90 minutos para permitir a propagação das configurações do domínio personalizado para os nós de borda da CDN, volte ao site do registrador de DNS e crie outro registro CNAME que mapeie o subdomínio para o ponto de extremidade CDN. Por exemplo, especifique o subdomínio como **www** ou **cdn** e o nome de host como **&lt;NomeDoPontoDeExtremidade>.azureedge.net**. Com essa etapa, o registro do seu domínio personalizado está concluído. 
10.	Por fim, você pode excluir o Registro CNAME criado usando **cdnverify**, já que ele era necessário apenas como uma etapa intermediária.  


## Verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN

- Depois de concluir o registro de seu domínio personalizado, você pode acessar o conteúdo armazenado em cache no ponto de extremidade da CDN usando o domínio personalizado. Primeiro, verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN armazenará conteúdo em cache em contêineres de blobs públicos. Para testar o domínio personalizado, verifique se o contêiner está definido para permitir acesso público e se contém pelo menos um blob.
- Em seu navegador, navegue até o endereço do blob usando o domínio personalizado. Por exemplo, se seu domínio personalizado for **cdn.meudominio.com**, a URL para um blob armazenado em cache será semelhante à seguinte URL: http://cdn.mydomain.com/mypubliccontainer/acachedblob.jpg

- Se o ponto de extremidade da CDN estiver associado a um serviço de nuvem, o endereço de seu conteúdo armazenado em cache será semelhante à seguinte URL: http://cdn.mydomain.com/mycloudservice

## Consulte também

[Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=AcomDC_0204_2016-->