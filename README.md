# Стартовый шаблон сборки фронтенда (версия 2.0)

## Структура
* папка `src` - основная папка с исходниками.
* папка `gulp` - папка с задачами gulp и файлом config.js.
* файл `gulpfile.babel.js` - основной файл входа для описания задач таск-раннера `GulpJS`.
* файл `.babelrc` - файл-конфиг для работы связки gulp + babel.
* файл `.editorconfig` - файл-конфиг для редактора кода (все файлы внутри шаблона соответствуют этому конфигу).
* файл `.gitignore` - служит для указания в нём файлов и папок, которые необходимо скрыть от системы контроля версий git.
* файл `package.json` - стандартный файл с описанием, зависимостями шаблона а так же с ключом `browserslist`, в котором указана поддержка браузеров для работы некоторых плагинов.
* файл `README.md` - файл с описанием структуры, нюансов работы и особенностями данного шаблона сборки фронтенда.

## Начало работы
* Клонируем этот репозиторий.
* Устанавливаем зависимости командой `npm i` (**ВАЖНО** - инструмент сборки `GulpJS` должен быть установлен глобально).
* Запускаем команду `gulp` - запустится процесс сборки. Результат будет доступен в браузере по адресу http://localhost:3000/.

## Принцип работы
* Все исходные файлы лежат в папке `src`.
* Результат работы сборщика отправляется в папку `build`.
* Есть два режима сборки - `development` и `production`.
* В файле `gulp/config.js` хранятся все основные пути, ключи и настройки.
* Для работы с файлами стилей используется препроцессор `SASS` и набор плагинов `PostCSS`.
* Для работы с файлами `HTML` используется шаблонизатор `PUG`.
* Для удобного отображения результата сборки, а так же "живой" перезагрузки браузера при изменениях, используется плагин `browser-sync`.
* Для работы с `SVG`-иконками используется набор плагинов, для создания из них одного файла-спрайта.

## Подробней о файле `config.js`
* Все пути и настройки, используемые в задачах `gulp`, хранятся в этом файле.
* Ключ `revision` может быть установлен в значении `true` или `false` (по умолчанию) - включает, или отключает версионирование статических файлов `CSS` и `JS` в режиме сборки `production`.
* Ключ `minifyHtml` может быть установлен в значении `true` или `false` (по умолчанию) - включает, или отключает минификацию файлов HTML в режиме сборки `production`.
* Ключ `splitOptions` содержит настройки для работы `PostCSS`-плагина `postcss-critical-split`. Подробней о работе плагина ниже.

## Описание основных задач
* `gulp` - главная задача (режим сборки - `development`):
  * удаляет папку `build` и файл `rev-manifest.json`
  * собирает `CSS`, `JS`, `HTML`-файлы из исходников
  * собирает спрайт из `SVG`-иконок
  * копирует статические файлы
  * запускает локальный сервер
  * собирает результат своей работы в папку `build`
  * запускает вотчер за файлами, и перезапускает соответственные задачи при их изменениях
* `gulp build` - задача сборки (режим сборки - `production`):
  * удаляет папку `build` и файл `rev-manifest.json`
  * собирает `CSS`, `JS`, `HTML`-файлы из исходников
  * минимизирует `CSS`, `JS`, `HTML`-файлы (в зависимости от настройки файла `config.js`)
  * встраивает стили из критического `CSS` инлайном в `HTML`
  * собирает спрайт из `SVG`-иконок
  * копирует статические файлы
  * собирает результат своей работы в папку `build`
* `gulp build:dev` - задача сборки (режим сборки - `development`):
  * удаляет папку `build` и файл `rev-manifest.json`
  * собирает `CSS`, `JS`, `HTML`-файлы из исходников
  * собирает спрайт из `SVG`-иконок
  * копирует статические файлы
  * собирает результат своей работы в папку `build`

## Описание остальных задач
### Сборка стилей
`gulp sass` - задача сборки файлов стилей. Нюансы:
* Исходные файлы стилей храним в папке `src/sass`.
* В сборку включен [normalize.css v8.0.1](http://github.com/necolas/normalize.css).
* Для удобного написания респонсив медиа-запросов в сборку включен набор миксинов [Include Media](http://include-media.com).
* В сборку включен базовый набор кастомных миксинов и функций (смотрите соответствующие файлы).
* В сборке используется обработка пост-процессором `PostCSS`. Описание плагинов, включенных в сборку:
  * `lost` - гибкая сетка, для подробной информации обратитесь к [документации](http://lostgrid.org/docs.html) (пример использование есть в файле `src/sass/global/_start-examples.scss`)
  * `postcss-animation` - добавляет анимацию из библиотеки [animate.css](https://daneden.github.io/animate.css/) (пример использование есть в файле `src/sass/global/_start-examples.scss`)
  * `postcss-will-change-transition` - добавляет `will-change` в стили, в которых используется `transition`
  * `postcss-will-change` - добавляет `backface-visibility: hidden;` в стили, в которых используется `will-change`
  * `postcss-flexbugs-fixes` - пытается исправить известные баги с `flexbox`-свойствами
  * `postcss-pxtorem` - конвертирует значения `px` в `rem`. По умолчанию затрагивает только свойства, связанные со шрифтами
  * `postcss-momentum-scrolling` - добавляет `-webkit-overflow-scrolling: touch;` в стили, в которых используется `overflow: scroll;`
  * `postcss-aspect-ratio` - добавляет возможность задать блоку размеры с соотношением сторон, используя так называемый "паддинг-хак" (пример использование есть в файле `src/sass/global/_start-examples.scss`)
  * `postcss-color-mod-function` - добавляет возможность смешивать цвета (пример использование есть в файле `src/sass/global/_start-examples.scss`)
  * `postcss-pseudo-class-enter` - добавляет возможность использовать псевдо-класс `:enter`, получая на выходе `:hover, :focus` (пример использование есть в файле `src/sass/global/_start-examples.scss`)
  * `css-declaration-sorter` - сортирует свойства стилей. По умолчанию используется подход `concentric-css`
  * `autoprefixer` - добавляет вендорные префиксы. Плагин "смотрит" на список поддерживаемых браузеров в файле `package.json`, и добавляет только необходимые для этих браузеров префиксы
  * `css-mqpacker` - сортирует респонсив медиа-выражения, объединяя повторяющиеся в одно объявление. В опциях плагина нужно указать, какой подход к верстке используется - desktop или mobile-first (по умолчанию)
* В сборке реализовано разделение `CSS` на критический и остальной. Для объявления, какие именно свойства должны уйти в критический `CSS`, в исходных файлах используются управляющие комментарии `/* critical:start */` и `/* critical:end */` (пример использование есть в файле `src/sass/global/_start-examples.scss`). Поменять эти управляющие комментарии можно в файле `config.js`.
* В режиме сборки `development`, применяется минификация `CSS` при помощи плагина `cssnano`.
* В режиме сборки `development`, и установленном ключе `revision` в значение `true`, применяется версионирование `CSS`, при помощи добавление в имя файла хеш-суммы этого файла.

### Сборка скриптов
`gulp js` - задача сборки файлов скриптов. Нюансы:
* По умолчанию используется два файла скриптов: `bundle.js` - для библиотек и `main.js` для собственных скриптов.
* Для работы с библиотеками, их подключение выполняется в файле `gulp/tasks/js.js`. Нужно прописать путь к библиотеке, предварительно положив файлы скриптов в папку `src/js/libs`. Пример такого подключения, а так же некоторые библиотеки включены в сборку (смотрите файл `gulp/tasks/js.js`, задачу `js:bundle`).
* В режиме сборки `development`, применяется минификация `JS` при помощи плагина `gulp-uglify-es`.
* В режиме сборки `development`, и установленном ключе `revision` в значение `true`, применяется версионирование `JS`, при помощи добавление в имя файла хеш-суммы этого файла.

### Сборка HTML
`gulp pug` - задача сборки файлов `HTML`. Нюансы:
* В сборке используется применение шаблонизатора `PUG`. Для ознакомления с принципами разработки при помощи данного инструмента, обратитесь к [документации](https://pugjs.org/api/getting-started.html) а так же, изучите базовую структуру, представленную в данном шаблоне в папке `src/pug`.
* В режиме сборки `development`, критические стили из файлов `CSS` вставляется инлайном в файлы `HTML` (для этого в строчке подключения таких стилей в `PUG` надо дополнительно прописать атрибут `inline` - пример в файле `src/pug/layouts/_css-critical.pug`).
* В режиме сборки `development`, и установленном ключе `minifyHtml` в значение `true`, применяется минификация файлов `HTML`
* в сборке учтены все нюансы по автоматической замене путей к файлам `CSS` и `JS` после их переименований (если такие произошли в процессе сборки).

### Сборка SVG-спрайта
`gulp sprite` - задача сборки `SVG`-иконок а один файл спрайта. Нюансы:
* `SVG`-иконки должны быть предварительно подготовлены, а именно:
  * иконки должны быть разобраны
  * иконки должны быть моноцветными
  * рекомендуется проводить ручную оптимизацию при помощи инструмента [SVGOMG](https://jakearchibald.github.io/svgomg/)
* Готовый спрайт с иконками после сборки будет находиться в папке `build/img/sprites`.
* Подключение иконок выполняется при помощи миксина в `PUG` - пример: `+icon('icon-telegram', 'Telegram icon')`, где `icon-telegram` - название иконки, и одновременно ее класс, а второй (необязательный) атрибут `Telegram icon` - это `aria-label` для этой иконки.
* Размеры подключенным иконкам можно задавать в `CSS` при помощи свойства `font-size` (размер будет высчитан по высоте).
* Цвет подключенным иконкам можно задавать в `CSS` при помощи свойства `color`.
* Для корректного отображения `SVG`-спрайтов в браузере `IE11` нужно подключить `JS`-полифил `svg4everybody` (подключен по умолчанию).

### Копирование статических файлов
`gulp copy` - копирует изображения, шрифты, вендорные `JS`-библиотеки. При необходимости - можно расширить функционал по аналогии (смотрите файл `gulp/tasks/copy.js`).

### Очистка рабочих фалов
`gulp clean` - удаляет папки и файлы, созданные в процессе сборки.

---
## В случае обнаружений проблем/багов/недочетов - отправляйте пулл-реквесты с исправлениями.

---
> Лицензия - MIT: ДАННОЕ ПРОГРАММНОЕ ОБЕСПЕЧЕНИЕ ПРЕДОСТАВЛЯЕТСЯ «КАК ЕСТЬ», БЕЗ КАКИХ-ЛИБО ГАРАНТИЙ, ЯВНО ВЫРАЖЕННЫХ ИЛИ ПОДРАЗУМЕВАЕМЫХ, ВКЛЮЧАЯ ГАРАНТИИ ТОВАРНОЙ ПРИГОДНОСТИ, СООТВЕТСТВИЯ ПО ЕГО КОНКРЕТНОМУ НАЗНАЧЕНИЮ И ОТСУТСТВИЯ НАРУШЕНИЙ, НО НЕ ОГРАНИЧИВАЯСЬ ИМИ. НИ В КАКОМ СЛУЧАЕ АВТОРЫ ИЛИ ПРАВООБЛАДАТЕЛИ НЕ НЕСУТ ОТВЕТСТВЕННОСТИ ПО КАКИМ-ЛИБО ИСКАМ, ЗА УЩЕРБ ИЛИ ПО ИНЫМ ТРЕБОВАНИЯМ, В ТОМ ЧИСЛЕ, ПРИ ДЕЙСТВИИ КОНТРАКТА, ДЕЛИКТЕ ИЛИ ИНОЙ СИТУАЦИИ, ВОЗНИКШИМ ИЗ-ЗА ИСПОЛЬЗОВАНИЯ ПРОГРАММНОГО ОБЕСПЕЧЕНИЯ ИЛИ ИНЫХ ДЕЙСТВИЙ С ПРОГРАММНЫМ ОБЕСПЕЧЕНИЕМ.
