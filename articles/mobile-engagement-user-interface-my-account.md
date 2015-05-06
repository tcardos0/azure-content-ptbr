﻿<properties 
   pageTitle="Interface de usuário do Mobile Engagement do Azure - Minha conta" 
   description="Visão geral da Interface de usuário da seção Minha conta do Mobile Engagement do Azure" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Mobile Engagement do Azure - Interface do usuário

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introdução</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navegação</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Página inicial</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Minha Conta</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Análise</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitoramento</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmentos</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Painel</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Configurações</a>
</div>

# Minha Conta
 
A seção Minha conta da interface do usuário é onde você pode visualizar e alterar as configurações associadas a sua conta, incluindo as configurações de perfil e testar as IDs de dispositivo. Essas configurações contêm itens que também podem ser acessados por meio da API do dispositivo.

**Consulte também:** 

-  [Guia de solução de problemas - Serviço][Link 2]
 
![MyAccount1][7]  

## Perfil:
Você pode exibir ou alterar suas configurações de conta: Senha, nome, sobrenome, organização, número de telefone, fuso horário ou aceitar ou recusar atualizações de email. Você também pode dar permissão a outro usuário para usar seu aplicativo com base em seu endereço de email a partir da "Página inicial".

**Consulte também:** 

-  [Documentação da interface do usuário - Página inicial][Link 13]

![MyAccount2][8]  

## Dispositivos:
Você pode exibir, adicionar ou remover IDs de dispositivo dos dispositivos de teste que você pode usar para testar suas campanhas de alcance ou envio de teste. Instruções contextuais sobre como localizar a ID de dispositivo dos dispositivos para cada plataforma (iOS, Android, Windows Phone, etc.) são exibidas quando você clicar em "Novo dispositivo". 
 
![MyAccount3][9]  
 
Para usar a API de envio ou a API do dispositivo, você precisa saber o identificador de dispositivo exclusivo de seus usuários (o parâmetro deviceid). Há várias maneiras para recuperá-lo:
 
1. No back-end, você pode usar o recurso "Get" da API do dispositivo para obter a lista completa de identificadores de dispositivos.
2. Em seu aplicativo, você pode usar o SDK para obtê-lo. (No Android, chame a função de getDeviceID() da classe agente e no iOS, leia a propriedade deviceid da classe agente).
3. De um aviso Reach, se a URL da ação associada ao comunicado contém o padrão {deviceid}, ele será substituído automaticamente pelo identificador do dispositivo disparando a ação.
http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata 
será substituído por:
http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata 
4. De um aviso Reach, se o código HTML do anúncio contém o padrão {deviceid}, ele será substituído automaticamente pelo identificador do dispositivo exibindo o anúncio da web.
Este é o identificador do dispositivo: {deviceid}
será substituído por:
Este é o identificador do dispositivo: XXXXXXXXXXXXXXXX
5.  Abra o aplicativo no dispositivo e execute um evento em seu aplicativo que tenha sido marcado.
A partir de "Interface do usuário - seu aplicativo - Monitoramento - Eventos -Detalhes", encontre o Evento executado na lista.
Clique para esse evento no Monitor.
Você deve encontrar o ID do dispositivo na lista de dispositivos que executaram este evento.
Em seguida, você pode copiar essa ID de dispositivo e registrá-la em "Interface do usuário - Minha conta - Novo dispositivo - Selecionar sua plataforma de dispositivo".
>(Lembre-se de que quando a IDFA está desabilitada para iOS, a ID do dispositivo pode alterar ao longo do tempo se você desinstalar e reinstalar o aplicativo.)

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/


 

<!--HONumber=47-->