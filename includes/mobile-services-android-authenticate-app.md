﻿
1. Öffnen Sie die Datei ToDoActivity.java im Paket-Explorer in Eclipse und fügen Sie die folgenden Import-Anweisungen hinzu.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu: 
	
	private void authenticate() {
	    // Login using the Google provider.
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	});   	
	}


Damit wird eine neue Methode erstellt, die den Authentifizierungsprozess handhabt. Der Benutzer wird mithilfe einer Google-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    <div class="dev-callout"><b>Hinweis</b>
	<p>Falls Sie einen anderen Identitätsanbieter als Google verwenden, ändern Sie den an die <strong>login</strong>-Methode übergebenen Wert auf einen der folgenden Werte: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> oder <em>windowsazureactivedirectory</em>.</p>
    </div>

3. Fügen Sie in der **onCreate**-Methode die folgende Codezeile nach dem Code hinzu, der das "MobileServiceClient"-Objekt instanziiert.

		authenticate();

	Dieser Aufruf startet den Authentifizierungsprozess.

4. Verschieben Sie den verbleibenden Code nach "authenticate();" in der **onCreate**-Methode in eine neue **createTable**-Methode mit folgendem Code:

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden. 

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.