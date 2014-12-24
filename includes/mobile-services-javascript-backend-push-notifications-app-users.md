﻿
1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e depois no serviço móvel.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png)

2. Clique na guia **Push**, selecione **Apenas usuários autenticados** para **Permissões** e, em seguida, clique em **Editar script**.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png)
	
	Isso permite que você personalize a função de retorno de chamada do registro da notificação por push. Se você usar o Git para editar seu código-fonte, a mesma função de registro é encontrada em `.\service\extensions\push.js`.

3. Substitua a função **registrar** existente por este código:

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	Isso adiciona uma marca ao registro que seja a ID do usuário conectado. As marcas fornecidas são validadas para prevenir que um usuário se registre com um ID de outro usuário. Assim que a notificação for enviada para este usuário, ela é recebida neste e em qualquer outro dispositivo registrado pelo usuário.

4. Clique na seta para voltar, clique na guia **Dados**, clique em **TodoItem**, clique no **Script** e selecione **Inserir**. 