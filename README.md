# Domino Java 9+ Compatibility Patches

This project contains patches and tips for compiling and running Domino code on Java 9 and above.

### org.openntf.domino.java.api.corba.patch

CORBA was [removed](http://openjdk.java.net/jeps/320) from Java 10, but the packaged Notes.jar plugin as of Domino 9.0.1 Feature Pack 10 does not reflect this change. The `org.openntf.domino.java.api.corba.patch` fragment includes an independent CORBA API distribution and adds it to the Notes.jar runtime. To use it in Maven-run tests, add the dependency to your project and the plugin to your target platform:

```xml
<project>
  <!-- ... -->
  <repositories>
    <repository>
      <id>artifactory.openntf.org</id>
      <name>artifactory.openntf.org</name>
      <url>https://artifactory.openntf.org/openntf</url>
    </repository>
  </repositories>
  <dependencies>
    <dependency>
      <groupId>org.openntf.domino</groupId>
      <artifactId>org.openntf.domino.java.api.corba.patch</artifactId>
      <version>1.0.0-SNAPSHOT</version>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.eclipse.tycho</groupId>
        <artifactId>target-platform-configuration</artifactId>
        <configuration>
          <dependency-resolution>
            <extraRequirements>
              <requirement>
                <type>eclipse-plugin</type>
                <id>com.ibm.notes.java.api.win32.linux</id>
                <versionRange>9.0.1</versionRange>
              </requirement>
              <requirement>
                <type>eclipse-plugin</type>
                <id>org.openntf.domino.java.api.corba.patch</id>
                <versionRange>0.0.0</versionRange>
              </requirement>
            </extraRequirements>
          </dependency-resolution>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

```

### java.lang.NoClassDefFoundError: java/sql/Time

Since Domino-targetting plugins such as ODA are not compiled with Java 9+ in mind, they are likely to run across `NoClassDefFoundError`s for core classes during compile-time testing. The fix for this is to use a newer Equinox framework than is normally used by the Domino runtime. Since this may have unexpected consequences elsewhere, the safest route for now is to add the Oxygen update site on Java 9+ only:

```xml
<project>
  <!-- ... -->
  <profiles>
    <profile>
      <id>java9</id>
      <activation>
        <jdk>[9,</jdk>
      </activation>

      <repositories>
        <repository>
          <id>oxygen</id>
          <layout>p2</layout>
          <url>http://download.eclipse.org/releases/oxygen</url>
        </repository>
      </repositories>
    </profile>
  </profiles>
</project>
```



## License

This project is licensed under the Apache License 2.0.