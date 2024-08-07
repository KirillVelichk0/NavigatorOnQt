# Предисловие
Данный проект был реализован на 2 курсе бакалавриата. По-хорошему, его бы переделать и улучшить, но пока руки не доходят.
# **Навигатор**
___
Данный небольшой проект является учебным, отражает некоторые навыки в использовании следующих технологий: *C++*, *Qt* и *MySql*, а также знание основ многопоточности и некоторых алгоритмов на графах.  
___
Данная программа позволяет отстраивать маршрут между двумя введенными "адресами" на небольшой территории. Адреса вводятся в формате Дом/здание.

Просчет маршрута выполняется в отдельном потоке, за счет чего предоставляется неблокирующийся интерфейс приложения. Синхронизация данных происходит за счет мьютексов и разделяемых указателей `shared_ptr`. Обработчик использует условную переменную для экономии процессорного времени. Ввиду того, что обработчик потенциально должен быть доступен практически из любой точки программы, а также должен быть в единственном экземпляре, класс обработчик `NavigatorHandler` является синглотоновским.

Для построения маршрута используется алгоритм ___d* lite___. Данный алгоритм является онлайн-алгоритмом на графах, что означает, что он способен осуществлять работу при изменяющихся весах графа, а также ему одновременно не требуется загружать весь граф, алгоритм может обойтись лишь его участком. Описанные возможности данного алгоритма позволяют использовать его для построения маршрутов на реальной карте. Несмотря на то, что в программе не реализована возможность изменения и обработки изменения весов графа, данную возомжность можно достаточно быстро добавить.

Визуальная составляющая приложения реализована за счет фреймворка *Qt*. Для отображения карты создан класс QPictureBox, отрисовывающий карту и возможный маршрут. Карта в некоторых пределах перемещаема и масштабируема, что реализовано за счет перегрузки событий. Также, в момент начала и окончания просчета маршрута объект данного класса посылает сигнал объекту `mainwindow`, за счет чего происходит синхронизация анимации ожидания, представленной в виде вращающихся по кругу точек.

База данных используется для хранений координат препятствий (представленных в виде домов), а также координат входов в подъезды в отдельной таблице. В таблице подъездов в качетсве внешних ключей использованы id домов из таблицы домов. Подключение и работа с базой данных реализована за счет библиотеки *QtSql*, входящей в состав *Qt*.


Все логические модули представлены отдельными классами. Так, например, всю логику построения маршрута содержит в себе класс `NavigatorTask`, он в свою очередь явно вызывается в классе-обработчике `NavigatorHandler`. При этом, т.к. использование объектов класса `NavigatorTask` где-то вне класса-обработчика не предполагается, в классе-обработчике создан отдельный метод добавления задачи по указанным адресам, который использует фабричный метод генерации задачи по указанным адресам.

___
Инструкция к сборке программы.
В проекте содержится папка AdditionalComponents, которая содержит __дамп__ базы данных, 3 dll файла необходимых для функционирования соединения с базой данных, а также папку __imageformats__ с содержащемися внутри dll для анимации gif и саму gif. Используемая версия Qt: 6.1.3.

В проекте используется база данных MySql версии 8.0., так что должна быть установлена данная версия или более старшая. Дамп должен быть распакован и помещен в базу данных. После сборки проекта, вышеупомянутые dll файлы и папку __imageformats__ нужно поместить в каталог проекта непосредственно к exe-файлу. Проект был скомпилирован с использованием __MinGW__, так что при компиляции желательно использовать именно его.
