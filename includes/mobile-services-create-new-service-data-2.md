Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **+Erstellen**.
    
    ![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)
        
    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Geben Sie unter **Tabellenname** den Namen *TodoItem* ein, und klicken Sie auf den Häkchenknopf.

	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png)

Dadurch wird die neue Speichertabelle **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann.

> [WACOM.NOTE] 
Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

1.  Klicken Sie auf die neue Tabelle **TodoItem**, und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

2.  Klicken Sie auf die Registerkarte **Spalten**. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden:
    
    <table  border="1" cellpadding="10">
     	<tr>
     	<th>Spaltenname</th>
    
     	<th>Typ</th>
    
     	<th>Index</th>
    
     	</tr>
    
     	<tr>
     	<td>ID</td>
    
     	<td>Zeichenfolge</td>
    
     	<td>Indiziert</td>
    
     	</tr>
    
     	<tr>
     	<td>__createdAt</td>
    
     	<td>Datum</td>
    
     	<td>Indiziert</td>
    
     	</tr>
    
     	<tr>
     	<td>__updatedAt</td>
    
     	<td>Datum</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
     	<td>__version</td>
    
     	<td>timestamp (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

 	Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.     <div  class="dev-callout"><b>Hinweis</b>

    <p>Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>

    </div>


Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.