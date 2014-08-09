<properties linkid="manage-services-hdinsight-sample-pi-estimator" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight Pi estimator sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight Pi estimator sample" authors="bradsev" />

Das HDInsight Pi-Schätzungs-Beispiel
====================================

Dieses Beispiel beschreibt die Ausführung eines einfachen MapReduce-Programms, das den Wert der mathematischen Konstante Pi mit Azure HDInsight und Azure PowerShell schätzt.

Das Programm verwendet eine statistische (quasi-Monte Carlo) Methode, um den Wert von Pi zu schätzen. Zufällig platzierte Punkte in einem Einheits-Quadrat liegen mit einer Wahrscheinlichkeit gleich der Kreisoberfläche Pi/4 innerhalb eines Kreises, der sich im Quadrat befindet. Der Wert für Pi kann aus dem Wert 4R ermittelt werden, wobei R das Verhältnis zwischen der Anzahl der Punkte innerhalb des Kreises zur Gesamtanzahl der Punkte innerhalb des Quadrats ist. Je größer die Anzahl der Punkte, desto genauer die Schätzung.

Sie finden den PiEstimator-Javacode mit den Mapper- und Reducer-Funktionen zu Ihrer Information weiter unten. Das Mapper-Programm generiert eine bestimmte Anzahl Punkte an zufälligen Orten innerhalb eines Einheits-Quadrats und zählt anschließend, wie viele dieser Punkte innerhalb des Kreises liegen. Das Reducer-Programm sammelt die von den Mappern gezählten Punkte und schätzt anschließend den Wert von Pi mit der Formel 4R, wobei R das Verhältnis zwischen der Anzahl der Punkte innerhalb des Kreises zur Gesamtanzahl der Punkte innerhalb des Quadrats ist.

Das Skript in diesem Beispiel übermittelt einen Hadoop JAR-Job und wird mit 16 Maps ausgeführt, die gemäß der Parameterwerte jeweils 10 Millionen Beispielpunkte berechnen. Sie können diese Parameterwerte ändern, um die Genauigkeit der Pi-Schätzung zu verbessern. Als Referenz: die ersten 10 Dezimalstellen von Pi lauten 3.1415926535.

Die .jar-Datei mit den Dateien, die Hadoop in Azure für die Bereitstellung der Anwendung benötigt, ist eine .zip-Datei und steht zum Download bereit. Sie können diese Datei mit verschiedenen Komprimierungshilfsprogrammen entzippen und den Inhalt anschließend anzeigen.

Sie finden weitere Beispiele für die Verwendung von HDInsight für MapReduce-Jobs unter [Ausführen der HDInsight-Beispiele](/de-de/manage/services/hdinsight/howto-run-samples), zusammen mit Links zu Anweisungen für deren Ausführung.

**Sie erhalten Informationen zu folgenden Themen:**

-   Verwenden der Azure PowerShell zur Ausführung des Pi Estimator MapReduce-Programms in Azure HDInsight.
-   Wie sieht ein MapReduce-Programm in Java aus.

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren](http://www.windowsazure.com/de-de/pricing/free-trial/).

-   Sie benötigen ein bereitgestelltes HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters](/de-de/manage/services/hdinsight/provision-hdinsight-clusters/)

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

Themen in diesem Artikel
------------------------

In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das Pi Estimator MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Ausführen des Beispiels mit Azure PowerShell](#run-sample)
2.  [Der Java-Code für das Pi Estimator MapReduce-Programm](#java-code)
3.  [Zusammenfassung](#summary)
4.  [Nächste Schritte](#next-steps)

Ausführen des Beispiels mit Azure PowerShell
--------------------------------------------

**So übermitteln Sie den MapReduce-Job**

1.  Öffnen Sie Azure PowerShell. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).
2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"   # Azure-Abonnementname
         $clusterName = "<ClusterName>"             # HDInsight-Clustername

3.  Führen Sie den folgenden Befehl aus, um eine MapReduce-Definition zu erstellen:

         $piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

    > [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

    Das erste Argument gibt die Anzahl der zu erstellenden Maps an (standardmäßig 16). Das zweite Argument gibt an, wie viele Proben pro Map erstellt werden sollen (standardmäßig 10 Millionen). Dieses Programm verwendet also 16\*10 Millionen = 160 Millionen zufälliger Punkte, um Pi zu schätzen.

4.  Führen Sie die folgenden Befehle aus, um den MapReduce-Job zu übermitteln und auf den Abschluss des Jobs zu warten:

         # Pi Estimator MapReduce-Job ausführen.
         Select-AzureSubscription $subscriptionName
         $piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

         # Auf Abschluss des Jobs warten.  
         $piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5.  Führen Sie den folgenden Befehl aus, um die Standardausgabe des MapReduce-Job abzurufen:

         # Ausgabe und Standardfehlerdatei des MapReduce-Jobs drucken
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

    Als Referenz: die ersten 10 Dezimalstellen von Pi lauten 3.1415926535.

Der Java-Code für das Pi Estimator MapReduce-Programm
-----------------------------------------------------

 	/**	
 	* Licensed to the Apache Software Foundation (ASF) under one	
 	* or more contributor license agreements. See the NOTICE file	
 	* distributed with this work for additional information	
 	* regarding copyright ownership. The ASF licenses this file	
 	* to you under the Apache License, Version 2.0 (the	
 	* "License"); you may not use this file except in compliance	
 	* with the License. You may obtain a copy of the License at	
 	*	
	* http://www.apache.org/licenses/LICENSE-2.0	
 	*	
 	* Unless required by applicable law or agreed to in writing, software	
 	* distributed under the License is distributed on an "AS IS" BASIS,	
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and	
 	* limitations under the License.		
 	*/	

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi	
	//using quasi-Monte Carlo method.	
	//	
	//Mapper:	
	//Generate points in a unit square	
	//and then count points inside/outside of the inscribed circle of the square.	
	//	
	//Reducer:	
	//Accumulate points inside/outside results from the mappers.	
	//Let numTotal = numInside + numOutside.	
	//The fraction numInside/numTotal is a rational approximation of	
	//the value (Area of the circle)/(Area of the square),	
	//where the area of the inscribed circle is Pi/4	
	//and the area of unit square is 1.	
	//Then, Pi is estimated value to be 4(numInside/numTotal). 	
	//	

 	public class PiEstimator extends Configured implements Tool {	
	//tmp directory for input/output	
 	static private final Path TMP_DIR = new Path(	
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");	

	//2-dimensional Halton sequence {H(i)},		
	//where H(i) is a 2-dimensional point and i >= 1 is the index.		
	//Halton sequence is used to generate sample points for Pi estimation.	 
 	private static class HaltonSequence {	
	// Bases	
 	static final int[] P = {2, 3}; 	
	//Maximum number of digits allowed	
 	static final int[] K = {63, 40}; 	

 	private long index;	
 	private double[] x;	
 	private double[][] q;	
 	private int[][] d;	

	//Initialize to H(startindex),	
	//so the sequence begins with H(startindex+1).	
 	HaltonSequence(long startindex) {	
 	index = startindex;	
 	x = new double[K.length];	
 	q = new double[K.length][];	
 	d = new int[K.length][];	
 	for(int i = 0; i < K.length; i++) {	
 	q[i] = new double[K[i]];	
 	d[i] = new int[K[i]];	
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;
	
 	for(int j = 0; j < K[i]; j++) {	
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];	
 	d[i][j] = (int)(k % P[i]);	
 	k = (k - d[i][j])/P[i];	
 	x[i] += d[i][j] * q[i][j];	
 	}	
 	}
 	}

	//Compute next point.	
	//Assume the current point is H(index).	
	//Compute H(index+1).	
	//@return a 2-dimensional point with coordinates in [0,1)^2		
 	double[] nextPoint() {		
 	index++;	
 	for(int i = 0; i < K.length; i++) {	
 	for(int j = 0; j < K[i]; j++) {	
 	d[i][j]++;	
 	x[i] += q[i][j];	
 	if (d[i][j] < P[i]) {	
 	break;		
 	}	
 	d[i][j] = 0;	
 	x[i] -= (j == 0? 1.0: q[i][j-1]);	
 	}	
 	}	
 	return x;	
 	}	
 	}	

	//Mapper class for Pi estimation.	
	//Generate points in a unit square and then		
	//count points inside/outside of the inscribed circle of the square.	
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.	
	//@param offset samples starting from the (offset+1)th sample.	
	//@param size the number of samples for this map	
	//@param out output {ture->numInside, false->numOutside}	
	//@param reporter	
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());		
 	long numInside = 0L;	
 	long numOutside = 0L;	

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.		
	//Accumulate points inside/outside results from the mappers.		
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.	
 	@Override	
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.		
	// @param isInside Is the points inside? 	
	// @param values An iterator to a list of point counts	
	// @param output dummy, not used here.	
	// @param reporter	

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}
	
 	//Reduce task done, write output to a file.	
 	@Override	
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class, 
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}
	
	//Run a map/reduce job for estimating Pi.	
	//@return the estimated value of Pi.		
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	) 	
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());
	
 	jobConf.setInputFormat(SequenceFileInputFormat.class);
	
 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);
	
 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);
	
 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);
	
 	// turn off speculative execution, because DFS doesn't handle		
 	// multiple writers to the same file.		
 	jobConf.setSpeculativeExecution(false);
	
 	//setup input/output directories	
 	final Path inDir = new Path(TMP_DIR, "in");	
 	final Path outDir = new Path(TMP_DIR, "out");	
 	FileInputFormat.setInputPaths(jobConf, inDir);	
 	FileOutputFormat.setOutputPath(jobConf, outDir);	
	
 	final FileSystem fs = FileSystem.get(jobConf);		
 	if (fs.exists(TMP_DIR)) {	
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");	
	 }	
	 if (!fs.mkdirs(inDir)) {	
	 throw new IOException("Cannot create input directory " + inDir);	
	 }	

	 //generate an input file for each map task		
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);	
	 final LongWritable offset = new LongWritable(i * numPoints);	
	 final LongWritable size = new LongWritable(numPoints);	
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();	
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }
	
	 //start a map/reduce job		
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");
	
	 //read outputs		
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }
	
	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }
	
	//Parse arguments and then runs a map/reduce job.	
	//Print output in standard out.		
	//@return a non-zero if there is an error. Otherwise, return 0. 	
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);
	
	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);
	
	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }
	
	 //main method for running it as a stand alone command. 		
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
  

Zusammenfassung
---------------

In diesem Lernprogramm haben Sie einen MapReduce-Job in HDInsight ausgeführt und Monte Carlo-Methoden verwendet, die große Datenmengen für diesen Dienst benötigen und generieren.

Nächste Schritte
----------------

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Beispiel: 10-GB-Graysort](/de-de/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Beispiel: Wortzählung](/de-de/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Beispiel: C\# Streaming](/de-de/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Verwenden von Pig mit HDInsight](/de-de/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
