# QtLocalizationForRpi
This page shows how to localize Qt Widgets/Quick Applications running on rpi.
Click the follow image to view this tutorial on Youtube.

[![Youtube video link](https://i.ytimg.com/vi/JtTtzYZ_Nk0/hqdefault.jpg)](//youtu.be/JtTtzYZ_Nk0 "Youtube Video")

- Cross Compilation https://youtu.be/8kpHgNKPooc
- Remote Debugging https://youtu.be/QWz-4R4kMIo
- Localization https://youtu.be/JtTtzYZ_Nk0

## Localize text
Build and install qttools module.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/d207ca9b-e0c6-45c9-bd8a-97dfacc020f5)

Add LinguistTools component to find_package in CMakeLists.txt.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/2665930f-349a-478c-aaa0-0aa7bb220264)

Add following code to CMakeLists.txt.
```
qt_add_translations(appHelloWorld TS_FILES string_en.ts string_fr.ts)
```

Goto **Project** select **Build**. On **Build Steps** check **appHelloWorld_lrelease** and **appHelloWorld_update**.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/f1bcf4ac-4cce-42ab-a551-8ac9d5aab6c0)

Build project and you will find appHelloWorld_en.ts and appHelloWorld_fr.ts created in the source folder. Open **Qt Linguist** to finish translation.

Goto **Project** select **Run**. On **Deployment** check **Override deployment data from build system**. Add 3 generated files as bellow.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/7bfffdc1-de75-4b58-9aeb-1b2adb5023ac)

Then on **Run** change **working directory:** to **/usr/local/bin**.

![image](https://github.com/MuyePan/QtLocalizationForRpi/assets/136073506/32ef52ec-d639-4d8a-a34e-e081912bf6cc)

You can turn off **appHelloWorld_lrelease**, **appHelloWorld_update** and **Override deployment data from build system** to speed up debugging.

Finally Modify main.cpp. This is just a simple demo of initilization and you should do more in real project.
```
QTranslator *tr = new QTranslator;
tr->load("string_en.qm");
QGuiApplication app(argc, argv);
app.installTranslator(tr);
```

## Localize asset
Subclass **QQmlAbstractUrlInterceptor** and implement **QUrl intercept(const QUrl& path, QQmlAbstractUrlInterceptor::DataType type)**
```
#include "asseturlhandler.h"

#include <QDir>
#include <QGuiApplication>
#include <QUrl>

#define ASSET_URL_SCHEME    "asset"

AssetUrlHandler::AssetUrlHandler(const QList<QString> &suffixes) {
    QList<QString> paths;
    paths.append(":" + QGuiApplication::applicationName().mid(3) + "/assets/images");
    paths.append(QGuiApplication::applicationDirPath() + "/assets/images");

    QDir::setSearchPaths(ASSET_URL_SCHEME, paths);
    m_suffixes = suffixes;
}

QUrl AssetUrlHandler::intercept(const QUrl &path, DataType type) {
    if (type == QQmlAbstractUrlInterceptor::DataType::QmldirFile) {
        return path;
    }

    auto scheme = path.scheme();
    if (scheme == QLatin1String(ASSET_URL_SCHEME)) {
        QString fileNameFull = path.fileName();
        QString fileExt = fileNameFull.mid(fileNameFull.lastIndexOf(".") + 1);
        QString fileName = fileNameFull.mid(0, fileNameFull.lastIndexOf("."));

        for (auto &suffix : m_suffixes) {
            QFileInfo fi(QString(ASSET_URL_SCHEME) + ":" + fileName + "_" + suffix + "." + fileExt);
            if (fi.exists()) {
                if (fi.filePath().startsWith(":/")) {
                    return QUrl("qrc" + fi.filePath());
                }
                return QUrl::fromLocalFile(fi.filePath());
            }
        }

        QFileInfo fi(QString(ASSET_URL_SCHEME) + ":" + fileNameFull);
        if (fi.exists()) {
            if (fi.filePath().startsWith(":/")) {
                return QUrl("qrc" + fi.filePath());
            }
            return QUrl::fromLocalFile(fi.filePath());
        }
    }
    return path;
}
```

main.cpp
```
    QQmlApplicationEngine engine;
    engine.addUrlInterceptor(new AssetUrlHandler({"en"}));
```

qml file
```
Image {
    id: logo
    source: "asset:logo.png"
    anchors.centerIn: parent
}
```



