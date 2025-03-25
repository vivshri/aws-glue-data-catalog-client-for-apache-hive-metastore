## AWS Glue Data Catalog Client for Apache Hive Metastore

The AWS Glue Data Catalog is a fully managed, Apache Hive Metastore-compatible metadata repository. Customers can use the Data Catalog as a central repository to store structural and operational metadata for their data.

AWS Glue provides out-of-the-box integration with Amazon EMR that enables customers to use the AWS Glue Data Catalog as an external Hive Metastore. To learn more, visit our [documentation](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive-metastore-glue.html).

This is an open-source implementation of the Apache Hive Metastore client on Amazon EMR clusters that uses the AWS Glue Data Catalog as an external Hive Metastore. It serves as a reference implementation for building a Hive Metastore-compatible client that connects to the AWS Glue Data Catalog. It may be ported to other Hive Metastore-compatible platforms such as other Hadoop and Apache Spark distributions.

This package is compatible with Spark 3 and Hive 3.

---

## Getting Started with Patched Hive Versions

This repository assumes you're working with a patched version of Hive, already prepared for Glue compatibility. The [vivshri/hive-glue-catalog-patch](https://github.com/vivshri/hive-glue-catalog-patch) repository provides those patches for Hive 2.3 and 3.1.

### ⚠️ Ensure Java 8 is used and the latest version of Maven
All builds and installations **must** use Java 8 (OpenJDK 1.8) and Maven version 3.9.9 or above

### Step 1: Clone the Patched Hive Repository

```bash
git clone https://github.com/vivshri/hive-glue-catalog-patch.git
cd hive-glue-catalog-patch
```

### Step 2: Build Hive 2.3 for Spark Compatibility

Spark 3.5 still uses Hive 2.3. Use the patched branch to build and install locally:

```bash
git checkout branch-2.3-glue
mvn clean install -DskipTests
```

This installs Hive version `2.3.10-glue-1`.

### Step 3: Build Hive 3.1 for Hive Integration

```bash
git checkout branch-3.1-glue
mvn clean install -DskipTests
```

This installs Hive version `3.1.3-glue-1`.

---

## Installing Required Dependency for Hive Build

Hive may fail to build due to the missing `pentaho-aggdesigner-algorithm` dependency. You can manually install it using the following steps:

Download the jar from:

[https://repo.huaweicloud.com/repository/maven/huaweicloudsdk/org/pentaho/pentaho-aggdesigner-algorithm/5.1.5-jhyde/pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar](https://repo.huaweicloud.com/repository/maven/huaweicloudsdk/org/pentaho/pentaho-aggdesigner-algorithm/5.1.5-jhyde/pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar)

Then install it into your local Maven repository:

```bash
mvn install:install-file -Dfile=pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar \
    -DgroupId=org.pentaho \
    -DartifactId=pentaho-aggdesigner-algorithm \
    -Dversion=5.1.5-jhyde \
    -Dpackaging=jar
```

---

## Building the Glue Data Catalog Client

Now build the actual Glue Data Catalog Client, which consumes the Hive jars installed above.

```bash
git clone https://github.com/awslabs/aws-glue-data-catalog-client-for-apache-hive-metastore.git
cd aws-glue-data-catalog-client-for-apache-hive-metastore
git checkout branch-3.4.2
mvn clean install -DskipTests
```

### Build Only Individual Clients

To build only the Spark client:

```bash
cd aws-glue-datacatalog-spark-client
mvn clean package -DskipTests
```

To build only the Hive 3 client:

```bash
cd aws-glue-datacatalog-hive3-client
mvn clean package -DskipTests
```

---

## Configuring Hive to Use the Glue Client

Ensure the AWS Glue Data Catalog client JAR is in Hive's classpath. Then set the following HiveConf:

```xml
<property>
  <name>hive.metastore.client.factory.class</name>
  <value>com.amazonaws.glue.catalog.metastore.AWSGlueDataCatalogHiveClientFactory</value>
</property>
```

---

## Configuring Spark to Use the Glue Client

Install the Spark Glue client JAR in Spark's classpath and configure HiveConf in Spark's `hive-site.xml`:

```xml
<property>
  <name>hive.metastore.client.factory.class</name>
  <value>com.amazonaws.glue.catalog.metastore.AWSGlueDataCatalogHiveClientFactory</value>
</property>
```

---

## Enabling Client-side Caching for Catalog Metadata

You can optionally enable caching for Glue metadata responses:

### Table Cache
```xml
<property>
  <name>aws.glue.cache.table.enable</name>
  <value>true</value>
</property>
<property>
  <name>aws.glue.cache.table.size</name>
  <value>1000</value>
</property>
<property>
  <name>aws.glue.cache.table.ttl-mins</name>
  <value>30</value>
</property>
```

### Database Cache
```xml
<property>
  <name>aws.glue.cache.db.enable</name>
  <value>true</value>
</property>
<property>
  <name>aws.glue.cache.db.size</name>
  <value>1000</value>
</property>
<property>
  <name>aws.glue.cache.db.ttl-mins</name>
  <value>30</value>
</property>
```

> ⚠️ Caching is disabled by default.

---

## License

This library is licensed under the Apache 2.0 License.

