# QtLocalizationForRpi
This page shows how to localize Qt Widgets/Quick Applications running on rpi.
Click the follow image to view this tutorial on Youtube.

## Localize text
Add LinguistTools component to find_package in CMakeLists.txt.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/2665930f-349a-478c-aaa0-0aa7bb220264)

Add following code to CMakeLists.txt.
```
qt_add_translations(appHelloWorld TS_FILES appHelloWorld_en.ts appHelloWorld_fr.ts)
```

Goto **Project** select **Build**. On **Build Steps** check **appHelloWorld_lrelease** and **appHelloWorld_update**.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/f1bcf4ac-4cce-42ab-a551-8ac9d5aab6c0)

Build project and you will find appHelloWorld_en.ts and appHelloWorld_fr.ts created in the source folder. Open **Qt Linguist** to finish translation.

Goto **Project** select **Run**. On **Deployment** check **Override deployment data from build system**. Add 3 generated files as bellow.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/7bfffdc1-de75-4b58-9aeb-1b2adb5023ac)

Then on **Run** change **working directory:** to **/usr/local/bin**.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/32ef52ec-d639-4d8a-a34e-e081912bf6cc)

You can turn off **appHelloWorld_lrelease**, **appHelloWorld_update** and **Override deployment data from build system** to accelerate debugging.

Finally Modify main.cpp. This is just a simple demo of initilization and you should do more in real project.
```
QTranslator *tr = new QTranslator;
tr->load("appHelloWorld_en.qm");
QGuiApplication app(argc, argv);
app.installTranslator(tr);
```
## Localize asset
Subclass **QQmlAbstractUrlInterceptor** and implement **QUrl intercept(const QUrl& path, QQmlAbstractUrlInterceptor::DataType type)**

## Localize code


