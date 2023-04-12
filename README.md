# Guice converted to jakarta namespace

[Google guice](https://github.com/google/guice) does [not support jakarta namespace yet](https://github.com/google/guice/issues/1383).

This project is **not a fork**. It just converts guice automatically using [tomcat migration tool](https://github.com/apache/tomcat-jakartaee-migration)
and re-publish converted jars into maven central (with different group).

* **Source** jars loaded from maven central and converted automatically 
* Project compiles **converted** sources producing all required artifacts for maven central
* POM files are manually re-worked (to contain correct jakarta dependencies)
* MANIFEST re-applied (to keep OSGI exports)

Migrated apis:

* jakarta.inject (all)
* jakarta.servlet (guice-servlet)
* jakarta.persistence (guice-persist)

## Attention

Migration from `javax.inject` means that your code should not use `javax.inject.Inject` and other annotations.
All such places **must** be changed either to jakarta `jakarta.inject.Inject` or to 
google `com.google.inject.Inject` (same for Singleton, Provider, etc.)

Guice will simply ignore old annotations! As a result, if you use some 3rd party library,
it **must** also not use `javax.inject` classes or guice might work incorrectly.

For gradle, there is [a special plugin](https://github.com/nebula-plugins/gradle-jakartaee-migration-plugin) 
which could convert dependencies automatically (assuming you building application and bundle all dependencies in it).

## Setup
                                                        
IMPORTANT: guice jar does not bundle asm version (like `com.google.inject:guice:5.1.0:classes`)
dependency, so you can easily upgrade asm version, if you need.

Gradle:

```groovy
dependencies {
    implementation platform('ru.vyarus.guice.jakarta:guice-bom:5.1.0')

    implementation 'ru.vyarus.guice.jakarta:guice'
    implementation 'ru.vyarus.guice.jakarta:guice-servlet'
}
```

Maven:

```xml      
<dependencyManagement>  
    <dependencies>
        <dependency>
            <groupId>ru.vyarus.guice.jakarta</groupId>
            <artifactId>guice-bom</artifactId>
            <version>5.1.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>          
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>ru.vyarus.guice.jakarta</groupId>
        <artifactId>guice</artifactId>
    </dependency>
    <dependency>
        <groupId>ru.vyarus.guice.jakarta</groupId>
        <artifactId>guice-servlet</artifactId>
    </dependency>
</dependencies>
```

Dependencies could, of course, be used without BOM too.

## Usage

Original (javax.) dependency           |  Migrated (jakarta.) dependency
--------------|-------------------------
[com.google.inject:guice-bom](https://mvnrepository.com/artifact/com.google.inject/guice-bom/5.1.0) | ru.vyarus.guice.jakarta:guice-bom
[com.google.inject:guice](https://mvnrepository.com/artifact/com.google.inject/guice/5.1.0) | ru.vyarus.guice.jakarta:guice
[com.google.inject.extensions:guice-assistedinject](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-assistedinject/5.1.0) | ru.vyarus.guice.jakarta:guice-assistedinject
[com.google.inject.extensions:guice-grapher](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-grapher/5.1.0) | ru.vyarus.guice.jakarta:guice-grapher
[com.google.inject.extensions:guice-jmx](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-jmx/5.1.0) | ru.vyarus.guice.jakarta:guice-jmx
[com.google.inject.extensions:guice-jndi](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-jndi/5.1.0) | ru.vyarus.guice.jakarta:guice-jndi
[com.google.inject.extensions:guice-persist](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-persist/5.1.0) | ru.vyarus.guice.jakarta:guice-persist
[com.google.inject.extensions:guice-servlet](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-servlet/5.1.0) | ru.vyarus.guice.jakarta:guice-servlet
[com.google.inject.extensions:guice-testlib](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-testlib/5.1.0) | ru.vyarus.guice.jakarta:guice-testlib
[com.google.inject.extensions:guice-throwingproviders](https://mvnrepository.com/artifact/com.google.inject.extensions/guice-throwingproviders/5.1.0) | ru.vyarus.guice.jakarta:guice-throwingproviders

NOTE: spring and struct2 modules are missed (anyone need them?)