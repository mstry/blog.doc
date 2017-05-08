---
title: spark security
date: 2017-04-27 21:49:38
categories:
tags:
---

```ini
# $SPARK_CONF_DIR/spark-defaults.conf
spark.ssl.akka.enabled true
spark.ssl.fs.enabled true
spark.ssl.keyPassword mapr123
spark.ssl.keyStore /opt/mapr/conf/ssl_keystore
spark.ssl.keyStorePassword mapr123
spark.ssl.trustStore /opt/mapr/conf/ssl_truststore
spark.ssl.trustStorePassword mapr123
spark.ssl.protocol TLSv1.2
spark.ssl.enabledAlgorithms TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA
spark.yarn.principal hdfs/elm-serv@HADOOP.COM
spark.yarn.keytab /opt/krb5-1.9/keytab/hdfs.keytab
```

# References #

1. [MapR - Configure SSL Encryption for Spark on YARN](http://maprdocs.mapr.com/home/Spark/ConfigureSparkOnYarn_Encryption.html)
