# cs_cadlib_dev_docs

API Reference Guide для CSoft CADLib: МиА

Собранную Web-справку для CSoft CADLib: МиА (для Library) [смотри здесь](https://georggrebenyuk.github.io/cs_cadlib_dev_docs/Docs_out/CL_Viewer/html/index.html).

# 1. Краткое описание

Настоящий репозиторий содержит инструкции, а также собранную справку для .NET API CSoft CADLib: МиА. В связи с авторскими правами CSoft Development на свои библиотеки (файлы в составе поставки продуктов), в данном репозитории не будет приводится код, по которому пишется справка. Вместо это будут даны рекомендации, как его сформировать на своей стороне.

## 2. Подготовка

Необходимое ПО:

* выпуск CSoft CADLib: МиА, для которого необходимо собрать справку (вернее, целевые библиотеки из него. Ниже приведена таблица с информацией, на какие библиотеки надо обратить внимание);

| Имя                       | Описание (назначение)                                                                             | Польза | СОД | Библ. |
| ------------------------- | ------------------------------------------------------------------------------------------------- | ------ | --- | ----- |
| CSProject3D.dll           | Основная библиотека для CADLib-Viewer (СОД)                                                       | +      | +   | -     |
| CADLibMain.dll            | Основная библиотека для CADLib-Library                                                            | +      | -   | +     |
| AppNWEViewerComponent.dll | Подсистема визуализации сцены (отображение объектов, очистка сцены, добавление объектов на сцену) | +      | +   | -     |
| CADLibKernel.dll          | Описание содержимого БД (параметры, объекты, иерархия, классы геометрии)                          | +      | +   | +     |
| CadLibSqlProcs.dll        | Описание коллекции параметров объекта и процедур обработки геометрии (анализ наложений?)          | ?      | +   | -     |
| CdeLib.dll                | Работа с CDE (экспорт, чтение, данные)                                                            | ?      | +   | +     |
| CollisionEngine.dll       | Расширенное API работы с коллизиями                                                               | +      | +   | -     |
| CSAppServices.dll         | Вспомогательная библиотека (логи, параметры БД, локализация)                                      | ?      | +   | +     |
| mstManagedAPI.dll         | API для работы с данными  ModelStudio                                                             | +      | +   | -     |
| NData.dll                 | Доступ к БД (исполнение SQL запросов, чтение результата, ошибки)                                  | +      | +   | +     |
| MsBinaryPublisher.dll     | Вспомогательное API для своей реализации публикации в CADLib                                      | +      | +   | -     |

В таблице выше не указаны библиотеки вспомогательного характера: CoSql.Runtime.dll, CoSql.StdLib.dll, Crom.Controls.dll, CSViewModels.dll, Dependency.dll, DependencyCollections.dll, DocumentFormat.OpenXml.dll, FLib.dll, NExtensions.dll, NLog.dll, NApp.dll (UI для некоторых окон CADLib), Gantt.dll (Работа с диаграммой Ганта), CADLibWorkMgmt.dll (модуль "Работы"), CADLibControls.dll (UI большей части CADLib).

* скомпилированные версии JetBrains Refasmer или иные утилиты, позволяющие сформировать для .NET-библиотеки её reference-assembly версию (в репозитории присутствует одна из версий);

* декомпилятор dnSpy;

* doxygen - библиотека и утилита для генерации документации;

* \* хостинг или место для отображения документации (здесь, github pages);

## 3. Сборка документации

### Шаг 1. Копирование целевых dll

При помощи программки Everything скопировать DLL в папки настоящего репозитория `CL_Library` и `CL_Viewer` -- для обычного CADLib; `CLDX_Library` и `CLDX_Viewer` -- для CADLib DX.

```txt
"C:\Program Files (x86)\CSoft\Model Studio CS\Viewer\bin\x64\" CSProject3D.dll | AppNWEViewerComponent.dll | CADLibKernel.dll | CadLibSqlProcs.dll | CdeLib.dll | CollisionEngine.dll | CSAppServices.dll | mstManagedAPI.dll | MsBinaryPublisher.dll
"C:\Program Files (x86)\CSoft\Model Studio CS\Library\bin\x64\" CADLibMain.dll | CADLibKernel.dll | CdeLib.dll | CSAppServices.dll | NData.dll

"C:\Program Files\CSoft\Model Studio CS\3.0\MIA\bin\" CSProject3D.dll | AppNWEViewerComponent.dll | CADLibKernel.dll | CadLibSqlProcs.dll | CdeLib.dll | CollisionEngine.dll | CSAppServices.dll | mstManagedAPI.dll | MsBinaryPublisher.dll
"C:\Program Files (x86)\CSoft\Model Studio CS\3.0\Library\bin\x64\" CADLibMain.dll | CADLibKernel.dll | CdeLib.dll | CSAppServices.dll | NData.dll
```

### Шаг 2. Создать reference-assemblies при помощи Refasmer

```cmd
"Refasmer\net461\RefasmerExe" -v -O "RefAssembly\CL_Viewer" "Assembly\CL_Viewer\AppNWEViewerComponent.dll" "Assembly\CL_Viewer\CADLibKernel.dll" "Assembly\CL_Viewer\CadLibSqlProcs.dll" "Assembly\CL_Viewer\CdeLib.dll" "Assembly\CL_Viewer\CollisionEngine.dll" "Assembly\CL_Viewer\CSAppServices.dll" "Assembly\CL_Viewer\CSProject3D.dll" "Assembly\CL_Viewer\MsBinaryPublisher.dll" "Assembly\CL_Viewer\mstManagedAPI.dll"

"Refasmer\net461\RefasmerExe" -v -O "RefAssembly\CL_Library" "Assembly\CL_Library\CADLibMain.dll" "Assembly\CL_Library\CADLibKernel.dll" "Assembly\CL_Library\CdeLib.dll" "Assembly\CL_Library\CSAppServices.dll" "Assembly\CL_Library\NData.dll"
```

### Шаг 3. Декомпилировать reference-assemblies при помощи dnSPY

Открыть вручную в среде dnSpy каждую из dll и экспортировать её в проект в папку "Projects\" в зависимости от назначения

### Шаг 4. Запустить doxyfile

`Doxyfile_CL_Viewer` -- преднастроенная конфигурация для сборки документации (CHM) для компонента Viewer (СОД). В репозитории хостится версия, сделанная через HTML with navigation panel (Doxygen GUI -> Wizard -> Output -> HTML)

`Doxyfile_CL_Library` -- преднастроенная конфигурация для сборки документации (CHM) для компонента Viewer (Библиотека компонентов).
