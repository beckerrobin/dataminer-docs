---
uid: Cassandra_Java_updating
---

# Updating Java for Cassandra

By default, DataMiner installs Cassandra with its own Java 8 installation. This is typically located in *C:\Program Files\Cassandra\Java\bin*. DataMiner deploys Cassandra with **Java 1.8.0_91**. Cassandra depends on **Java 8**. We **do not recommend** deploying Java 11.

To update the Java version:

1. Download the latest OpenJDK 8 Java binaries from the official website.

   > [!NOTE]
   > You can download the latest OpenJDK 8 JRE binaries from [the OpenJDK wiki](https://wiki.openjdk.java.net/display/jdk8u/Main). Make sure to download the **Windows x64** archive. For example: *OpenJDK8U-jre_x64_windows_8u322b06.zip*

1. Stop the *Cassandra* service.

1. Extract the OpenJDK archive and update the binaries in *C:\Program Files\Cassandra\Java*.

1. Start the *Cassandra* service.
