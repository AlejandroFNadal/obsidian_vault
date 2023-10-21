Inputs: [[KDS]] or [[KDF]]. We set a SQL sentence, and we cna join it with Reference data obtained from S3.

KDA generates an output stream, that can be given to [[KDF]].

We can use it to do streaming ETL, continous metric generation and responsive analytis. 
It is pretty expensive, although we pay only for resources consumed. 
Both SQL or Flink can be used to write the computation.
Lambda can be used for preprocessing.

In KDA, for machine learning, a good example that is provided as a SQL function. is RANDOM_CUT_FOREST, used to detect anomalies.  It adapts over time, it uses recently history.

Another example is HOTSPOTS, allows to locate and return information about relatively dense regions in your data. Example: a collection of overheated servers in a data center.
