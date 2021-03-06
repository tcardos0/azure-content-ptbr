<properties 
	pageTitle="Solucionar problemas do Application Insights em um projeto Web Java" 
	description="Guia de solução de problemas: monitoramento em tempo real aplicativos Java com o Application Insights." 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="awills"/>
 
# Solução de problemas e perguntas e respostas para o Application Insights para Java

Dúvidas ou problemas com o [Visual Studio Application Insights em Java][java]? Aqui estão algumas dicas.


## Erros de compilação

*No Eclipse, ao adicionar o SDK do Application Insights por meio de Maven ou Gradle, recebo erros de validação de soma de verificação ou de compilação.*

* Se o elemento <version> de dependência estiver usando um padrão com caracteres curinga (ex.: Maven `<version>[1.0,)</version>` ou Gradle `version:'1.0.+'`), tente definir uma versão específica, como `1.0.2`. Veja as [notas de versão](app-insights-release-notes-java.md) da versão mais recente.

## Sem dados 

*Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.*

* Espere um minuto e clique em Atualizar. Os gráficos são atualizados periodicamente, mas você também pode atualizá-los manualmente. O intervalo de atualização depende do intervalo de tempo do gráfico.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto)
* Verifique se não há um nó `<DisableTelemetry>true</DisableTelemetry>` no arquivo xml.
* Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.
* No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
* Ative o log para a janela de console do IDE adicionando um elemento `<SDKLogger />` sob o nó raiz no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto) e verifique se há entradas precedidas com [Erro].
* Certifique-se de que o arquivo ApplicationInsights.xml correto foi carregado com êxito pelo SDK do Java, examinando as mensagens de saída do console para uma instrução "Arquivo de configuração foi descoberto com êxito".
* Se não for encontrado no arquivo de configuração, verifique as mensagens de saída para ver onde o arquivo de configuração está sendo procurado e certifique-se de que o ApplicationInsights.xml seja localizado em um desses locais de pesquisa. Como regra geral, você pode colocar o arquivo de configuração perto dos JARs do SDK do Application Insights. Por exemplo: no Tomcat, isso poderia significar que a pasta WEB-INF/lib.



#### Eu costumava ver os dados, mas eles foram interrompidos

* Verifique o [blog de status](http://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra Configurações/Cota e Preços para descobrir. Nesse caso, você pode atualizar seu plano ou então pagar por capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).



## Sem dados de uso

*Vejo dados sobre solicitações e tempos de resposta, mas não há dados de exibição de página, de navegador ou de usuário.*

Você configurou com êxito seu aplicativo para enviar telemetria do servidor. Agora, a próxima etapa é [configurar suas páginas da Web para enviar telemetria por meio do navegador da Web][usage].

Como alternativa, se o cliente for um aplicativo em um [telefone ou outro dispositivo][platforms], você poderá enviar telemetria por meio dele.

Use a mesma chave de instrumentação para configurar a telemetria de seu cliente e do servidor. Os dados serão exibidos no mesmo recurso do Application Insights, e você poderá correlacionar eventos do cliente e do servidor.



## Desabilitando a telemetria

*Como desabilitar a coleta da telemetria?*

No código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou**

Atualize o arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto). Adicione o seguinte sob o nó raiz:

    <DisableTelemetry>true</DisableTelemetry>

Usando o método XML, você precisa reiniciar o aplicativo ao alterar o valor.

## Alterando o destino

*Como alterar o recurso do Azure ao qual meu projeto envia dados?*

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se você tiver adicionado o Application Insights a seu projeto usando o Kit de Ferramentas do Azure para Eclipse, clique com o botão direito do mouse em seu projeto Web, selecione **Azure**, **Configurar Application Insights** e altere a chave.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta de recursos em seu projeto.


## A tela inicial do Azure

*Estou examinando o [portal do Azure](https://portal.azure.com). O mapa me diz algo sobre meu aplicativo?*

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No painel inicial do Azure (tela inicial), como localizar dados sobre meu aplicativo?*

Supondo que você tenha [configurado seu aplicativo para o Application Insights][java], clique em Procurar, selecione Application Insights e selecione o recurso de aplicativo criado para seu aplicativo. Para acessar essa opção mais rapidamente no futuro, você pode fixar o aplicativo no painel inicial.

## Servidores de intranet

*Posso monitorar um servidor em minha intranet?*

Sim, desde que o servidor possa enviar telemetria para o portal do Application Insights pela Internet pública.

Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.

## Retenção de dados 

*Por quanto tempo os dados são mantidos no portal? É seguro?*

Consulte [Privacidade e retenção de dados][data].

## Próximas etapas

*Configurei o Application Insights para meu aplicativo de servidor Java. O que mais posso fazer?*

* [Monitorar a disponibilidade de suas páginas da Web][availability]
* [Monitorar o uso da página da Web][usage]
* [Acompanhar o uso e diagnosticar problemas em seus aplicativos de dispositivos][platforms]
* [Escrever código para acompanhar o uso de seu aplicativo][track]
* [Capturar logs de diagnóstico][javalogs]


## Obter ajuda

* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0128_2016-->