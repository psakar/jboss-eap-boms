Java EE 8 with tools recommended by JBoss EAP
===============================================

Java EE lacks any testing APIs, and for this reason JBoss developed the Arquillian project, along with it's various component projects, such as Arquillian Drone, and the sister project Shrinkwrap. This BOM builds on the Java EE full profile BOM, adding Arquillian to the mix. It also provides a version of JUnit and TestNG recommended for use with Arquillian.
 
Furthermore, this BOM adds the WildFly Maven deployment plugin. JBoss EAP's recommended mode of deployment is via the management APIs, and the Maven plugin is the recommended way to do this, if the customer is using Maven for building.
 
Usage
-----

To use the BOM, import into your dependency management:

    <dependencyManagement>
        <dependencies>
            <dependency>
               <groupId>org.jboss.bom</groupId>
               <artifactId>jboss-eap-javaee8-with-tools</artifactId>
               <version>7.2.0.GA-CR2</version>
               <type>pom</type>
               <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

Unfortunately, Maven doesn't allow you to specify plugin versions this way. To use the plugins associated with "Java EE with Tools recommended by JBoss EAP" BOM, add:

    <pluginManagement>
        <plugins>
            <!-- The Maven Surefire plugin tests your application. Here we ensure we are using a version compatible with Arquillian -->
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${version.surefire.plugin}</version>
            </plugin>
            <!-- The WildFly plugin deploys your war to a local JBoss EAP container -->
            <!-- To use, set the JBOSS_HOME environment variable and run:
                 mvn package wildfly:deploy -->
            <plugin>
                <groupId>org.wildfly.plugins</groupId>
                <artifactId>wildfly-maven-plugin</artifactId>
                <version>${version.org.wildfly.plugin}</version>
            </plugin>
        </plugins>
    </pluginManagement>

You'll need to take a look at the POM source in order to find the latest versions of plugins recommended.


### Deploying/undeploying your application to server

To be able to easily deploy (or undeploy) your application from the application server, include following in the ``<build>`` section of your pom.xml file:
	
    <plugins>
        <plugin>
            <groupId>org.wildfly.plugins</groupId>
            <artifactId>wildfly-maven-plugin</artifactId>
        </plugin>
    </plugins>
    
You'll be able to deploy your application via `mvn package wildfly:deploy`. See <https://github.com/wildfly/wildfly-maven-plugin> for further information how to use the plugin.
	
###Testing your application with Arquillian

To able to test your application with Arquillian, you have decide which type container you prefer. Arquillian allows you to choose 
between a managed invocation, where it controls startup and shutdown of the container and a remote invocation, which connects to a running instance of JBoss EAP.
See <https://docs.jboss.org/author/display/ARQ/Container+varieties> for further details. You may wish to set up two distint profiles, each using one type of
the container.
 	
To select JBoss EAP managed container, following dependency has to be added into the `<dependencies>` section of your pom.xml file:
	
    <dependency>
        <groupId>org.wildfly.arquillian</groupId>
        <artifactId>wildfly-arquillian-container-managed</artifactId>
        <scope>test</scope>
    </dependency>
	
Or for JBoss EAP remote container:

    <dependency>
        <groupId>org.wildfly.arquillian</groupId>
        <artifactId>wildfly-arquillian-container-remote</artifactId>
        <scope>test</scope>
    </dependency>
    
Apart from setting a container, you need to choose a testing framework (e.g. JUnit) and add the Arquillan bindings for it:

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.jboss.arquillian.junit</groupId>
        <artifactId>arquillian-junit-container</artifactId>
        <scope>test</scope>
    </dependency>

*Note: Don't forget to set JBOSS_HOME environment variable so Arquillian will be able to find your container location.
If you want to experiment with Arquillian settings, you can find plenty of information at <http://arquillian.org/>*

Arquillian allows you to setup two distinct protocols for communication between Arquillian and application server, a Servlet
based and a JMX based one. In order to have a protocol activated, you need to add its artifact into `<dependencies>` section and configure
it in `arquillian.xml` file. We recommend you to use the Servlet 3.0 protocol. To set up it, add following dependency:

    <dependency>
        <groupId>org.jboss.arquillian.protocol</groupId>
        <artifactId>arquillian-protocol-servlet</artifactId>
        <scope>test</scope>
    </dependency>

Arquillian configuration file (`arquillian.xml`) is located by default in `src/test/resources` directory. You need to setup
Servlet protocol as default:

    <?xml version="1.0" encoding="UTF-8"?>
    <arquillian xmlns="http://jboss.org/schema/arquillian"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://jboss.org/schema/arquillian
        http://jboss.org/schema/arquillian/arquillian_1_0.xsd">

        <!-- Uncomment to have test archives exported to the file system for inspection -->
        <!--    <engine>  -->
        <!--       <property name="deploymentExportPath">target/</property>  -->
        <!--    </engine> -->

        <!-- Force the use of the Servlet 3.0 protocol with all containers, as it is the most mature -->
        <defaultProtocol type="Servlet 3.0" />

        <!-- Example configuration for a managed/remote JBoss EAP instance -->
        <container qualifier="jboss" default="true">
        <!-- If you want to use the JBOSS_HOME environment variable, just delete the jbossHome property -->
        <!--<configuration>-->
        <!--<property name="jbossHome">/path/to/jboss/eap</property>-->
        <!--</configuration>-->
        </container>
    </arquillian>


### Testing your application with Arquillian Drone

Arquillian Drone uses the very same setup as plain Arquillian. Arquillian Drone lets you choice between different Selenium bindings.
Currently, we support three different Selenium based frameworks:
    
* Selenium DefaultSelenium - as known as Selenium 1.0
* Selenium WebDriver - as known as Selenium 2.0
* Arquillian Graphene - AJAX-enhanced Selenium 1.0 with a type safe API

In order to use Arquillian Drone, include following dependency into your ``<dependencies>`` section. You can use one of the
frameworks or include all of them together. More information about binding can be found at <https://docs.jboss.org/author/display/ARQ/Drone>:

    <!-- Arquillian Drone with DefaultSelenium -->
    <dependency>
        <groupId>org.jboss.arquillian.extension</groupId>
        <artifactId>arquillian-drone-selenium-depchain</artifactId>
        <type>pom</type>
        <scope>test</scope>
    </dependency>

    <!-- Arquillian Drone with WebDriver -->
    <dependency>
        <groupId>org.jboss.arquillian.extension</groupId>
        <artifactId>arquillian-drone-webdriver-depchain</artifactId>
        <type>pom</type>
        <scope>test</scope>
    </dependency>

    <!-- Arquillian Drone with Arquillian Graphene -->
    <dependency>
        <groupId>org.jboss.arquillian.graphene</groupId>
        <artifactId>arquillian-graphene</artifactId>
        <type>pom</type>
        <scope>test</scope>
    </dependency>

*Note: Each of listed Arquillian Dependency Chains already contains a certified version of Selenium. Should you need to use a different version (for example to test your 
application in a newer browser, compatible with the latest Selenium version only), you can get more information how to do that
at <https://community.jboss.org/wiki/SpecifyingSeleniumVersionInArquillianDrone>*

