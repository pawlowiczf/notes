
`mvn clean install -U`
`mvn dependency:purge-local-repository`

```xml

<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
<servers>
    <server>
        <id>nexus-ecmsp-snapshots</id> 
        <username>nexus-ecmsp-client</username>
        <password>nexus</password>
    </server>
    <server>
        <id>nexus-ecmsp-releases</id> 
        <username>nexus-ecmsp-client</username>
        <password>nexus</password>
    </server>
</servers>

     <mirrors>
          <mirror>
               <id>maven-default-http-blocker</id>
               <mirrorOf>dummy</mirrorOf>
               <name>Dummy mirror to override default blocking mirror that blocks http</name>
               <url>http://0.0.0.0/</url>
         </mirror>
    </mirrors>
</settings>



```

```xml
<dependency>
    <groupId>com.ecmsp</groupId>
    <artifactId>protos</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</dependency>

<repositories>
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

```
name: Compile Proto Artifacts using Buf

on:
    pull_request:
        branches: [main2]
    push:
        branches: [main2]
    workflow_dispatch:

jobs:
    compile-proto:
        runs-on: ubuntu-latest

        steps:
            - 
                name: Checkout
                uses: actions/checkout@v4
            
            -
                name: Setup Java
                uses: actions/setup-java@v3
                with:
                    distribution: 'temurin'
                    java-version: '21'

            - 
                name: Install Protoc
                uses: arduino/setup-protoc@v3             

            - 
                name: Replace Secrets in pom.xml and settings.xml
                run: |
                    sed -i "s|\${secrets.NEXUS_URL}|${{ secrets.NEXUS_URL }}|g" pom.xml 
                    sed -i "s|\${secrets.NEXUS_USERNAME}|${{ secrets.NEXUS_USERNAME }}|g" .m2/settings.xml 
                    sed -i "s|\${secrets.NEXUS_PASSWORD}|${{ secrets.NEXUS_PASSWORD }}|g" .m2/settings.xml 

            -
                name: Copy Proto files to separate folder
                run: |
                    mkdir -p generated/.m2
                    mkdir -p generated/src/main/java/
                    mkdir -p generated/src/main/proto
                    cp -r protos generated/src/main/proto
                    cp .m2/settings.xml generated/.m2/
                    cp pom.xml generated/

            - 
                name: Compile, Build and Deploy
                working-directory: ./generated
                run: |
                    protoc -I=src/main/proto --java_out=src/main/java $(find src/main/proto -name "*.proto")
                    mvn -B -s .m2/settings.xml clean deploy
               
            -
                name: Commit and push stubs to proto-stubs-view repository
                run: |
                    BRANCH_NAME=stubs-update-$(echo $GITHUB_SHA | cut -c1-7)

                    git config --global user.name "github-actions"
                    git config --global user.email "actions@github.com"
                    git clone https://x-access-token:${{ secrets.ACTIONS_USER_TOKEN }}@github.com/ecmsp-project/proto-stubs-view.git

                    cd proto-stubs-view 
                    git checkout -b $BRANCH_NAME
                    cp -r ../generated/* .
                    rm -rf .m2
                    git add .
                    git commit -m "compile new version of .proto files"

                    git checkout main
                    git reset --hard $BRANCH_NAME
                    git push origin main --force



```



[schema-definitions-repository](files/schema-definitions-main.zip)