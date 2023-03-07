# Java-docker
Java Docker Setup


## Jenkins Build Setup
> ### Plugins (Install below plugins on Jenkins)
>  - Credentials Plugin
>  - Git plugin
>  - Maven Integration
>  - Maven Info Plugin
>  - Deploy to container Plugin
>  - Maven Dependency Update Trigger
> ### Global Tool Configuration
>
> #### Configure Open JDK 16
>  - Add JDK
>  - Delete Installer
>  - Add Installer, select `Extract *.zip/*.tar`
>  - Download URL for binary archive (Set value)
>   - https://download.java.net/java/GA/jdk16.0.1/7147401fd7354114ac51ef3e1328291f/9/GPL/openjdk-16.0.1_linux-x64_bin.tar.gz
>  - Subdirectory of extracted archive (Set value)
>   - jdk-16.0.1
> 
> #### Configure MAVEN
>  - Add Maven
>   - Select version `3.8.1` from 
>   - Apply & Save
>  - Credentials
>   - Add credentials for Github
>   - Add credentials for Tomcat
> ### Configure Jenkins Job
>  - Click New Item
>  - Select Maven Project and Click Ok
>  - General Section
>   - Description (optional)
>   - Select JDK from drop down `openJdk16`
>  - Source code management
>   - Select Git
>   - Set Github repo url
>   - Select Git credentials (which we configured on credentials settings)
>   - Select Branch `*\main`
>   - Select repo browser from list `Bitbucket`
>   - Set Bit bucket Repo HTTPS URL
>  - Build Triggers
>   - Check `Build whenever a SNAPSHOT dependency is built`
>   - Check `Poll SCM`
>   - Set schedule EG: `H * * * *` (Poll every 1 hour)
>  - Build Environment
>   - Check `Delete workspace before build starts`
>   - Check `Add timestamps to the Console Output`
>  - Pre Steps
>   - Add Pre Build Step
>   - Select `Execute Shell`
>   - Add Below command to execute in command Box (Substitute DB credentials)
>    ```
>    cd models \
>    && mvn liquibase:update -Ddb.url="jdbc:postgresql://db:5432/db_name" -Ddb.user=db_user -Ddb.password=db_pass \
>    && cd ..
>    ```
>  - Build
>   - Set `Root POM` as `pom.xml`
>   - Set `Goals and options` as below command
>    ```
>    mvn clean install -Ddb.url="jdbc:postgresql://db:5432/db_name" -Ddb.user=db_user -Ddb.password=db_pass
>    ```
>  - Post-build Actions
>   - Add post-build action
>   - Select `Deploy war/ear` to container
>   - Set `**/*.war` as value of `WAR/EAR` files field
>   - Set `context path` as `ProjectName`
>   - Add Container and Select `Tomcat9.x Remote`
>   - Select credentials for tomcat that we configured at credentials page
>   - Set Tomcat server url EG: `http://localhost:8080/`
>  - Apply & Save the configuration
>  
> #### Check Build by clicking on Build Now
