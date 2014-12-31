﻿1. Öffnen Sie im App-Projekt die Datei "AndroidManifest.xml". Ersetzen Sie im Code in den nächsten beiden Schritten "**my_app_package**" durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des Attributs "package" des Tags "manifest" entspricht. 

2. Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen Element "uses-permission" ein:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Fügen Sie den folgenden Code nach dem Starttag "application" ein: 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Laden Sie das [Mobile Services Android SDK] herunter, und entzippen Sie es. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner "libs Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner "libs".

    <div class="dev-callout"><b>Hinweis</b>
	<p>Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.</p>
    </div>

5.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie den folgenden Import-Ausdruck ein:

		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import com.microsoft.windowsazure.mobileservices.notifications.Registration;


6. Fügen Sie die folgende private Variable zu der Klasse hinzu: ersetzen Sie "<PROJECT_NUMBER>" mit der Projektnummer, die Ihrer App im vorherigen Vorgang durch Google zugewiesen wurde:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Ändern Sie die Definition von MobileServiceClient von privat zu öffentlich-statisch, damit sie folgendes Format erhält:

		public static MobileServiceClient mClient;


8. Fügen Sie die folgende Methode in "ToDoActivity.java" der "ToDoActivity"-Klasse hinzu, um die Registrierung für Benachrichtigungen zu ermöglichen.

        /**
		 * Registers mobile services client to receive GCM push notifications
		 * @param gcmRegistrationId The Google Cloud Messaging session Id returned 
		 * by the call to GoogleCloudMessaging.register in NotificationsManager.handleNotifications
		 */
		public void registerForPush(String gcmRegistrationId)
		{
			String [] tags = {null};
			ListenableFuture<Registration> reg = mClient.getPush().register(gcmRegistrationId, tags);
			
	    	Futures.addCallback(reg, new FutureCallback<Registration>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(Registration reg) {
	    			createAndShowDialog(reg.getRegistrationId() + " resistered", "Registration");
	    		}
	    	});
		}



9. Als Nächstes müssen wir eine neue Klasse zum Behandeln von Benachrichtigungen hinzufügen. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten "src"), klicken Sie auf **Neu** und dann auf **Klasse**.

10. Geben Sie in **Name** "MyHandler" und in **Übergeordnete Klasse** "com.microsoft.windowsazure.notifications.NotificationsHandler" ein, und klicken Sie dann auf **Fertig stellen**

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	Daraufhin wird die neue MyHandler-Klasse erstellt.

11. Fügen Sie die folgenden import-Anweisungen für die "MyHandler"-Klasse ein:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. Fügen Sie als Nächstes die folgenden Mitglieder für die "MyHandler"-Klasse ein:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. In der "MyHandler"-Klasse fügen Sie den folgenden Code zum Überschreiben der **onRegistered**-Methode hinzu: wodurch Ihr Gerät im Benachrichtigungshub des mobilen Diensts registriert wird.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. In der "MyHandler"-Klasse fügen Sie den folgenden Code zum Überschreiben der **onReceive**-Methode hinzu, wodurch die Benachrichtigung nach ihrem Empfang angezeigt wird.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


15. Aktualisieren Sie in der Datei "TodoActivity.java" die **onCreate**-Methode der "ToDoActivity"-Klasse, um die Benachrichtigungsbehandlungsklasse zu registrieren. Stellen Sie sicher, dass dieser Code hinzufügt wird, nachdem MobileServiceClient instanziiert wurde.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ihre App kann Pushbenachrichtigungen nun unterstützen.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q