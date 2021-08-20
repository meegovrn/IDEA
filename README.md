## Репозиторий с автоматическими тестами для мобильного приложения МВидео

- тестирование UI (ветки `develop` & `job/android`)
- работа с Android платформой через UI
- позволяет настраивать гибкие ответы и запросы по API (заглушки)
- тестирование API (ветка `developApi`)


### Настройка
Установить:
- jdk 8
- Android Studio или Intellij Idea с плагином для android (чтобы удобно управлять эмуляторами и Android sdk)
- Android Sdk (если скачивали Android Studio -- sdk можно установить из неё, иначе скачать отдельно)
- nodejs, npm (версия 12 подходит)
- appium (`npm install -g appium`, `npm install -g appium-doctor`, запустить `appium-doctor` и исправить ошибки, если будут)

Настроить переменные окружения:
- JAVA_HOME
- ANDROID_SDK
- Добавить в PATH пути до `$ANDROID_SDK/platform-tools` и `$ANDROID_SDK/tools` (для тестов не обязательно, для удобства)

Создать эмулятор (рекомендуется устройство Nexus 5X, CPU/ABI: Intel Atom (x86), API level: 29).

В корне проекта необходимо создать файл `serenity.properties` с таким содержимым:
```properties
feature.file.encoding = UTF-8
serenity.report.encoding = UTF-8
serenity.take.screenshots = FOR_FAILURES
webdriver.driver = appium

# Три следующих свойства зависят от параметров созданного эмулятора.
appium.platformName = Android
appium.platformVersion = 11.0
appium.avd = Nexus_5X

# А тут именно пустое значение должно быть, убирать совсем свойство нельзя
appium.app =

appium.appPackage = ru.filit.mvideo.b2c.debug
appium.appActivity = ru.filit.mvideo.b2c.ui.splash.view.SplashScreen
appium.unicodeKeyboard = true
appuim.automationName = UiAutomator2
appium.newCommandTimeout = 300
appium.log-level = error
```

#### Настройка на MAC
- Java (JDK + SDK)
- IntelliJ IDEA
- ADB / XCode 11.7
- Appium & other tools  
install brew: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`  
```shell script
brew install node  
npm install -g appium  
brew install carthage  
brew install rbenv ruby-build  
rbenv install 2.7.2  
rbenv global 2.7.2  
gem install cocoapods  
brew install xcodegen
```
clone with Git mobile repo from 'stash.mvideo.ru/stash/scm/moapb/mobappb2c-ios.git' and run on it: `xcodegen`, `pod install`  
open mobile project 'MOBAPP_B2C.xcworkspace' in Xcode, find 'BASE_URL' setting and replace in property 'autotest' value to 'http://mapp-autotest-balancer.corp.mvideo.ru:8093'  
build Xcode project  

### Конфигурации запуска UI тестов
#### Idea
- Через Cucumber (предпочтительно)  
Идём в Run/Debug configurations - Templates - Cucumber java:
```text
Main class: net.serenitybdd.cucumber.cli.Main
Glue: behaviour.steps
VM options: -Dappium.path=/path/to/appium/build/lib/main.js
Working directory: $MODULE_WORKING_DIR$
Use classpath of module: b2c-automated-testing
```
Сохраняем шаблон. После этого любой feature-файл можно запустить через ПКМ - Run / debug.
<br/>
<br/>
- Через JUnit
Run/Debug configurations - Add new configuration - JUnit:
```text
Class: runner.TestRunner
VM options: -ea -Dskip.test=true -Dappium.path=/path/to/appium/build/lib/main.js -Dcucumber.options="--tags '@desired_feature_tag'" 
```
Сохраняем конфигурацию, после чего используем её для выполнения фичей по заданным тегам.

#### Maven
```shell script
cd /path/to/b2c-automated-testing
mvn clean verify -DskipTests=true -Dappium.path=/path/to/appium/build/lib/main.js -Dcucumber.options="--tags @smoke,@smoke_prod" 
```

### Конфигурации запуска API тестов
Используется только JUnit-runner. Конфигурация для Idea и maven аналогичны соответствующим конфигурациям
для UI тестов, кроме некоторых ключей jvm:
- `-Dappium.path` - не требуется
- `-Dbase.host=autotest` - необходимо задать имя стенда для прогона. Список стендов можно найти в файле
`test_data.json`.

### Прокси
Чтобы направить всю jvm через прокси (например Charles), задать ключи:
```text
-DproxyHost=127.0.0.1
-DproxyPort=8080
```
___
<br/>
<br/>
<br/>

#### Для справки, из старой документации
1. Configurate **serenity.properies** file:
- IOS real
	

    webdriver.driver=appium
    feature.file.encoding=UTF-8
    serenity.report.encoding=UTF-8
    serenity.take.screenshots=FOR_FAILURES
    appium.wdaLocalPort=8100
    appium.bundleId=ru.fil-it.mvideo.b2c.mobappb2c
    appium.platformName=iOS
    appium.udid=5e8e8fc511f12fe5233243149e017d859634a766
    appium.deviceName=iPhone 5s
    appium.platformVersion=11.3
    #appium.automationName=XCUITest
    appium.app=
    appium.xcodeOrgId=Y325WHG7XD
    appium.xcodeSigningId=iPhone Developer
    appium.usePrebuiltWDA=true

- Android real



    # Android 6.1 / Meizu 3s
    webdriver.driver=appium
    feature.file.encoding=UTF-8
    serenity.report.encoding=UTF-8
    serenity.take.screenshots=FOR_FAILURES
    
    appium.automationName=uiautomator2
    appium.platformName=Android
    appium.platformVersion=5.1
    appium.deviceName=M3s
    appium.udid=Y15CKBPC227CC
    appium.systemPort=8200
    appium.appPackage=ru.filit.mvideo.b2c
    appium.appActivity=ru.filit.mvideo.b2c.ui.main.view.MainActivity
    appium.app=
