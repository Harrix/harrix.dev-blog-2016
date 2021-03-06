---
categories: [it, programming]
tags: [Qt, C++, Qt Quick, QML]
---

# Qt Quick и C++ в Qt — Краткая инструкция

Это укороченный вариант [статьи](/blog/2016/add-2-num-qt-quick/). Там рассматривается конкретный пример работающего приложения, а здесь расписывается без воды, как добавить C++ часть в приложение Qt Quick.

В [статье](/blog/2016/add-2-num-qt-quick-controls) рассказывается о подобном приложении, но с использованием компонентов Qt Quick Controls.

- Создайте приложение `Qt Quick Application`.

- Создадим новый класс. Правой кнопкой щелкнем по проекту и выбираем пункт `Add New…`.

- Там выбираем `C++ Class`.

- Далее вводим название нашего нового класса, например, `HandlerSignals`, а в качестве базового класса выбираем `QObject`.

- Заголовочный файл `handlersignals.h` меняем на следующий, где `cppSlot` — наш код, что будет исполняться из QML:

```h
#ifndef HANDLERSIGNALS_H
#define HANDLERSIGNALS_H

#include <QObject>
#include <QVariant>

class HandlerSignals : public QObject
{
    Q_OBJECT
public:
    explicit HandlerSignals(QObject *parent = 0);

signals:

public slots:
    void cppSlot(const QString &msg);
};

#endif // HANDLERSIGNALS_H
```

- Файл класса `handlersignals.cpp` меняем на такой:

```cpp
#include "handlersignals.h"

HandlerSignals::HandlerSignals(QObject *parent) : QObject(parent)
{

}

void HandlerSignals::cppSlot(const QString &msg) {
//Здесь располагается код С++, который вызывается QML элементом.
}:

```

- Перейдем к файлу `main.cpp` и меняем его на такой:

```cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>

#include "handlersignals.h"

int main(int argc, char *argv[])
{
    QGuiApplication app(argc, argv);

    QQmlApplicationEngine engine;
    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));

    QObject* root = engine.rootObjects()[0];

    HandlerSignals *handlerSignals= new HandlerSignals(root);

    QObject::connect(root, SIGNAL(qmlSignal(QString)),
                     handlerSignals, SLOT(cppSlot(QString)));

    return app.exec();
}
```

- В корневом элементе в QML файле прописываем сигнал. Например, такой:

```qml
signal qmlSignal(string msg)
```

- Теперь находите в нужном QML объекте можем отправить этот сигнал:

```qml
//Действие мыши
MouseArea {
    id: mouseArea1
    anchors.fill: parent
    hoverEnabled: true;
    onClicked: {qmlSignal("яблок")}
}
```
