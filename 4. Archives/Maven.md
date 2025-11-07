[[Build tool]] for [[Java]] and its ecosystem ([[Kotlin]], [[Scala]], etc...)

# Distribution setting
Upload to [[Artifactory]] like [[JFrog]]
```xml title="pom.xml
<distributionManagement>
	<!-- NOTE: can set multiple -->
	<repository>
		<id>{server id from .m2 settings}</id>
		<url>{url}</url>
	</repository>
	
	<snapshotRepository>
		<id>{server id from .m2 settings}</id>
		<url>{url}</url>
	</snapshotRepository>
</distributionManagement>
```

# Deploy a package to [[Artifactory]] directly
```shell
# Cannot push from .m2
mvn deploy:deploy-file \
	-DrepositoryId=<m2 settings id> \
	-Durl="<url>" \
	-Dpackagin=jar \
	-Dfile="<path/to/file.jar>" \
	-DpomFIle="<path/to/pom.xml>"
```