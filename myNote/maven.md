## 引用本地jar包
指定scope为system，并且指定systemPath即可
	
	<dependency>
		<groupId>...</groupId>
		<artifactId>...</artifactId>
		<version>...</version>
		<scope>system</scope>
		<systemPath>${basedir}/src/lib/xxx.jar</systemPath>
	</dependency>
