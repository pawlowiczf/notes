


```yaml
services:
  nexus:
    image: sonatype/nexus3:latest
    container_name: nexus
    ports:
      - "80:8081"
    volumes:
      - nexus-data:/nexus-data
    restart: unless-stopped

volumes:
  nexus-data:
```

```xml
<repositories>
  <repository>
    <id>central</id>
    <name>Maven Central</name>
    <url>https://repo.maven.apache.org/maven2</url>
  </repository>
  <repository>
    <id>nexus-ecmsp-releases</id>
    <name>nexus-ecmsp-releases</name>
    <url>https://nexus.ecmsp.pl/repository/maven-releases/</url>
  </repository>
  <repository>
    <id>nexus-ecmsp-snapshots</id>
    <name>nexus-ecmsp-snapshots</name>
    <url>https://nexus.ecmsp.pl/repository/maven-snapshots/</url>
  </repository>
</repositories>
```