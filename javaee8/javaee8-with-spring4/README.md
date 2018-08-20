JBoss EAP Java EE 8 with Spring 4
===============================

This BOM builds on the Java EE full profile BOM, adding Spring 4.
  
Usage
-----

To use the BOM, import into your dependency management:

    <dependencyManagement>
        <dependencies>
            <dependency>
               <groupId>org.jboss.bom</groupId>
               <artifactId>jboss-eap-javaee8-with-spring4</artifactId>
               <version>7.2.0.GA-redhat-SNAPSHOT</version>
               <type>pom</type>
               <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
