<properties
	pageTitle="PHP unter Windows für SQL-Datenbank | Microsoft Azure"
	description="Enthält ein PHP-Beispielprogramm, das eine Verbindung zwischen einem Windows-Client mit Behandlung vorübergehender Fehler und Azure SQL-Datenbank herstellt, sowie Links zu den auf dem Client erforderlichen Softwarekomponenten."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="mebha"/>


# Herstellen einer Verbindung mit SQL-Datenbank mithilfe von PHP unter Windows mit Behandlung vorübergehender Fehler


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


In diesem Thema wird veranschaulicht, wie Sie von einer in PHP geschriebenen Clientanwendung, die unter Windows ausgeführt wird, eine Verbindung mit Azure SQL-Datenbank herstellen können.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]


## Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge


Im Thema [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Verbinden mit einer Datenbank und Abfragen der Datenbank 

Das Demoprogramm wurde so entworfen, dass ein vorübergehender Fehler beim Herstellen einer Verbindung zu einer Wiederholung führt. Ein vorübergehender Fehler während des Abfragebefehls bewirkt jedoch, dass das Programm die Verbindung verwirft und eine neue Verbindung herstellt, bevor der Abfragebefehl wiederholt wird. Microsoft spricht keinerlei Empfehlung für oder gegen diese Entwurfsentscheidung aus. Das Demoprogramm soll lediglich die Entwurfsflexibilität veranschaulichen, die Ihnen zur Verfügung steht.

<br>Die Länge dieses Codebeispiel ist größtenteils auf die Catch-Exception-Logik zurückzuführen. Eine kürzere Version der Datei "Program.cs" finden Sie [hier](https://azure.microsoft.com/de-de/documentation/articles/sql-database-develop-php-simple-windows/). <br>Die Main-Methode befindet sich in der Datei "Program.cs". Die Aufrufreihenfolge lautet wie folgt: * Main calls ConnectAndQuery. * ConnectAndQuery calls EstablishConnection. * EstablishConnection calls IssueQueryCommand.

Mit der [sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php)-Funktion können Sie ein Resultset aus einer Abfrage einer SQL-Datenbank abrufen. Diese Funktion akzeptiert praktisch jede Abfrage und das Verbindungsobjekt und gibt ein Resultset zurück, das mithilfe von [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) durchlaufen werden kann.

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command. 
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established"; 
		        echo "<br>";
		 
		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        { 
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate."); 
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>
	
## Weitere nützliche Informationen


Weitere Informationen über die Installation und Verwendung von PHP finden Sie unter [Accessing SQL Server Databases with PHP](http://technet.microsoft.com/library/cc793139.aspx) (Zugreifen auf SQL Server-Datenbanken mit PHP, in englischer Sprache).

 

<!---HONumber=July15_HO4-->