[[Artifactory]] for sharing package/binary for development. Mainly use for [[Java]] projects with [[Maven]], [[Gradle]]. However, it can also manage [[Window OS|Win binary]], [[Linux Distro|Linux binary]], [[MacOS|Mac binary]], [[Container|Container image]], [[NodeJS|NPM package]], etc...

Notes:
- Free version has limit [[REST API]] support

# Sample [[Maven]] config

```xml title="~/.m2/settings.xml"
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="https://maven.apache.org/SETTINGS/1.1.0"
          xmlns:xsi="https://www.s3.org/2001/XMLSchema-instance"
          xmlns:schemaLocation="https://maven.apache.org/SETTINGS/1.1.0 https://maven.apache.org/xsd/settings-1.1.0.xsd">
	<servers>
		<!-- Note: config server & access credential here. Can specify multiple server -->
		<!-- Mirror setting can be used to force using the mirror link -->
		<server>
			<id>{unique id}</id>
			<username>{username}</username>
			<password>{plain password}</password>
		</server>
	</servers>
	
	<profiles>
		<profile>
			<id>{profile id}</id>
			<repositories>
				<!-- Note: can specify multiple. make sure <id> is the same as server -->
				<!-- Note: [[Maven]] will try from top to bottom. So we can add maven central to the bottom as fallback -->
				<repository>
					<id>{server id}</id>
					<url>{link}</url>
					<releases><enabled>true</enabled></releases>
					<snapshots><enabled>false</enabled></snapshots>
				</repository>
			</repositories>
			<pluginRepositories>
				<!-- Note: can specify multiple. make sure <id> is the same as server -->
				<!-- Note: [[Maven]] will try from top to bottom. So we can add maven central to the bottom as fallback -->
				<pluginRepository>
					<id>{server id}</id>
					<url>{link}</url>
					<releases><enabled>true</enabled></releases>
					<snapshots><enabled>false</enabled></snapshots>
				</pluginRepository>
			</pluginRepositories>
		</profile>
	</profiles>
	
	<activateProfiles>
		<activateProfile>{profile id}</activateProfile>
	</activateProfiles>
	
	<!-- NOTE: I am not sure about this proxies settings -->
	<proxies>
		<proxy>
			<id>{id}</id>
			<activate>true</activate>
			<protocol>http</protocol>
			<username>proxyuser</username>
			<password>proxypass</password>
			<host>{url}</host>
			<port>{port}</port>
			<nonProxyHosts>localhost|...</nonProxyHosts>
		</proxy>
	</proxies>
</settings>
```