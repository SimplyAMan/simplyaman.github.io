---
layout: default
title: “PKIX path building failed” and “unable to find valid certification path to requested target”
---
## “PKIX path building failed” and “unable to find valid certification path to requested target”

1. Зберігаємо сертифікат сайту в файл.
2. keytool -list -v -keystore /path/to/cacerts  > java_cacerts.txt
Enter keystore password:  changeit

[Докладніше](http://magicmonster.com/kb/prg/java/ssl/pkix_path_building_failed.html)

Для імпорту сертифікату maven repository це в моєму випадку виглядає:

```
c:\Program Files\Java\jdk1.8.0_144\jre\lib\security>"c:\Program Files\Java\jdk1.8.0_144\jre\bin\tool" -keystore cacerts -importcert -file maven.cer -alias maven
```

## keytool error: java.lang.Exception: Certificate not imported, alias already exists

Якщо при виконанні попередньої поради отримуємо помилку "keytool error: java.lang.Exception: Certificate not imported, alias already exists", то потрібно спочатку видалити старий сертифікат:

```
keytool -delete -alias <Key> -keystore <../.../... /lib/security/cacerts> -storepass changeit
```

[Докладніше](https://www.buggybread.com/2013/09/keytool-error-javalangexception.html)

## Відключення SSL для репозитарія

Якщо все перераховане вище не допомагає, то правимо файл c:\Users\<user>\.m2\settings.xml:
```
<settings>
  <activeProfiles>
    <!--make the profile active all the time -->
    <activeProfile>securecentral</activeProfile>
  </activeProfiles>
  <profiles>
    <profile>
      <id>securecentral</id>
      <!--Override the repository (and pluginRepository) "central" from the
         Maven Super POM -->
      <repositories>
        <repository>
          <id>central</id>
          <url>http://repo1.maven.org/maven2</url>
          <releases>
            <enabled>true</enabled>
          </releases>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>central</id>
          <url>http://repo1.maven.org/maven2</url>
          <releases>
            <enabled>true</enabled>
          </releases>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
</settings>
```

[Докладніше](https://stackoverflow.com/questions/25911623/problems-using-maven-and-ssl-behind-proxy)
