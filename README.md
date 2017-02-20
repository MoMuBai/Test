## Jcenter 
> 项目发布到公共的jcenter仓库
> 

### 项目所需的步骤
#### 一、添加插件
##### 在项目目录的build.gradle 中的 dependencies 添加：
```
classpath "com.jfrog.bintray.gradle:gradle-bintray-plugin:1.5" 
classpath 'com.github.dcendents:android-maven-gradle-plugin:1.4.1'
```

#### 二、配置gradle
##### 同时在需要添加到jcenter的libary的build.gradle根目录添加：
```
apply plugin: 'com.github.dcendents.android-maven' 
apply plugin: 'com.jfrog.bintray'
version = "1.0.1" //这里的version是可以更新的版本号*
```

##### (1)在local.properties添加用户名和key
```
bintray.user=openproject 
// your bintrayuser name 
bintray.apikey=****** 
// your bintray api key`
```
##### (2)继续在根目录添加：
```
def siteUrl = 'https://github.com/MoMuBai/Test' 
// #修改# // 项目的主页地址，我这里是我的PickerView项目在github的链接地址 
def gitUrl = 'https://github.com/MoMuBai/Test.git' 
// #修改# // 项目 git 地址，我这里同样是用Github上的git地址 
group = "com.mubai" 
// #修改# // 组名称
```

##### (3)打包javadocjar和sourcejar
```
install { 
    repositories.mavenInstaller {
        // This generates POM.xml with proper parameters
        pom {
            project {
                packaging 'aar'
                name 'Less Code For Android'
                url siteUrl
                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
                developers {
                    developer {
                        id 'mubai'
                        name 'mubai.lzw'
                        email 'quickly520@126.com'
                    }
                }
                scm {
                    connection gitUrl
                    developerConnection gitUrl
                    url siteUrl
                }
            }
        }
    }
}
```
##### (4)打包javadocjar和sourcejar
```
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}
task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}
artifacts {
    archives javadocJar
    archives sourcesJar
}
```
##### (5)上传到Jcenter仓库 需要去从local.properties中读取
``` 
Properties properties = new Properties()
properties.load(project.rootProject.file('local.properties').newDataInputStream())
bintray {
    user = properties.getProperty("bintray.user")
    key = properties.getProperty("bintray.apikey")
    configurations = ['archives']
    pkg {
        repo = "maven"
        name = "test-library"                // project name in jcenter
        websiteUrl = siteUrl
        vcsUrl = gitUrl
        licenses = ["Apache-2.0"]
        publish = true
    }
}
```
#### 三、执行命令
##### 通过命令进入项目目录执行命令
```
./gradlew javadocJar
./gradlew sourcesJar
./gradlew install
./gradlew bintrayUpload
```

### [Bintray](https://bintray.com/)所需的步骤
#### 一、注册账号也可以通过Git直接登录
#### 二、添加一个新的Repository(名字跟在gradle配置的一样)
#### 三、在gradle命令执行完成后可以到Bintray进行提交审核(一般几个小时)
#### 四、审核通过之后就可以进行依赖的添加使用了
`compile 'com.mubai:library:1.0.1'`

