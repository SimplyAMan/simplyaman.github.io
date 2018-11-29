---
layout: default
title: Add Oracle to your pom.xml
---

# Add Oracle to your pom.xml

Note
Due to Oracle license restrictions, the Oracle JDBC driver is not available in the public Maven repository. To use the Oracle JDBC driver with Maven, you have to download and install it manually into your Maven local repository.

## Get Oracle JDBC Driver

Two ways to get the Oracle jdbc driver:

1. Visit [Oracle website](http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html) to get the Oracle JDBC driver ojdbc6.jar or ojdbc7.jar
2. Not recommend, but you can get the JDBC driver from the Oracle database installed folder, for example {ORACLE_HOME}\jdbc\lib\ojdbc6.jar

## Maven Install

To install the Oracle jdbc drivers :

1. **ojdbc6.jar**

```
$ mvn install:install-file -Dfile={Path/to/your/ojdbc6.jar} -DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.2.0 -Dpackaging=jar
```
2 ojdbc7.jar

```
$ mvn install:install-file -Dfile={Path/to/your/ojdbc7.jar} -DgroupId=com.oracle -DartifactId=ojdbc7 -Dversion=12.1.0 -Dpackaging=jar
```

3. pom.xml

```
<dependencies>
        <!-- ojdbc6.jar example -->
        <dependency>
            <groupId>com.oracle</groupId>
            <artifactId>ojdbc6</artifactId>
            <version>11.2.0</version>
        </dependency>

        <!-- ojdbc7.jar example -->
        <dependency>
            <groupId>com.oracle</groupId>
            <artifactId>ojdbc7</artifactId>
            <version>12.1.0</version>
        </dependency>
</dependencies>
```

[Get Oracle JDBC drivers from the Oracle Maven Repository](https://blogs.oracle.com/dev2dev/get-oracle-jdbc-drivers-and-ucp-from-oracle-maven-repository-without-ides)