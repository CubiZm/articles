# Текстурированные градиенты на чистом CSS

*оригинал статьи*: http://rentafounder.com/textured-gradients-in-pure-css/

Мы собираемся создать вот такой фон на чистом CSS:

![](http://rentafounder.com/resources/gradients-goal.jpg)

Конечно, мы могли бы пойти легким путем и создать градиент используя CSS (и есть [инструменты](http://www.colorzilla.com/gradient-editor/) которые делают их создание простым делом)

![](http://rentafounder.com/resources/gradients-simple.jpg)

Но это не выглядит очень впечатляюще, так как мы можем использовать только линейный или радиальный градиент. А мы хотим больше элементов случайности и чтобы изображение выглядело бы более естественно, менее геометрично и предсказуемо.

Самый распространенный способ: взять фотографию и размыть её. TinEye предлагает нам [бесплатный онлайн инструмент](http://labs.tineye.com/multicolr/) для этого. Он позволяет нам найти в CC Flickr изображения, основанные на цветах, которые они содержат. Я добавил несколько цветов для поиска и выбрал [эту картинку](https://www.flickr.com/photos/54377877@N02/8112846300) на Flickr от пользователя [“whatapar”](https://www.flickr.com/photos/whatapar/) (используя лицензию CC 2.0).

![](http://rentafounder.com/resources/gradients-original.jpg)

Большая часть информации на этом фото нам не нужна, поэтому давайте промасштабируем её до размера 8x6 пикселей.

![](http://rentafounder.com/resources/gradients-small.png)

[TinyPNG](https://tinypng.com/) уменьшит наше изображение до 326 байт и мы будем использовать [простой онлайн инструмент](http://dopiaza.org/tools/datauri/index.php) для преобразования наших изображений в формат data URI и в таком виде мы можем подключить его напрямую в наш CSS: 

```css
body {
    background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAgAAAAGCAMAAADJ2y/JAAAAilBMVEWVsF28hjlmaLKik6tscKNqZKOZr51fZ5Rxc5GjuI9WTYJnaIBsfXBVb26IpWKMlFdlglVndlV1hlRna1Rue1E5SlH/q0uJc0rimEhxdkBXbj/LhDtahzqjgjpcczp9nTnvmzlZWTNYZS85ailmUChLXSYpdSICOSEYIiFMaB4yURAmHApOSgAVDwB+JAIrAAAALnRSTlP+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+iNdOOwAAAD1JREFUCNcFwYMBADEABLDr27ZZd//1moDJ0ItSp8e31H4QJxyTyNwC2mAnbX7iVriGmR5N+YCs4/ZW3W8BZWUEDXOg7OsAAAAASUVORK5CYII=");
    background-repeat: no-repeat;
    background-size: cover;
}
```

Было бы очень здорово, если бы все так и выглядело на самом деле. И действительно, в Chrome это выглядит весьма хорошо. 

![](http://rentafounder.com/resources/gradients-chrome.jpg)

Но Firefox не размывает наше масштабированное изображение, по крайней мере не очень сильно: 

![](http://rentafounder.com/resources/gradients-firefox.jpg)

Можно было бы добавить отдельный `div` для заднего фона, и применить `filter: blur(10px)` к нему. Но я немного пурист и добавление оберток для добавления чисто визуальных эффектов это то, чего я стараюсь избегать. 

Большинство современных браузеров на сегодняшний день поддерживают SVG, и в SVG документацию [включен фильтр размытия по Гаусу](https://www.w3.org/TR/SVG/filters.html#feGaussianBlurElement) (что будет выглядеть намного лучше, чем наша примитивная интерполяция в Chrome, рассмотренная выше). Давайте создадим SVG файл с нашим изображением 8x6:

```html
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink= "http://www.w3.org/1999/xlink" viewBox="1 1 6 4">
  <image filter="url(#b)" width="8" height="6" xlink:href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAgAAAAGCAMAAADJ2y/JAAAAilBMVEWVsF28hjlmaLKik6tscKNqZKOZr51fZ5Rxc5GjuI9WTYJnaIBsfXBVb26IpWKMlFdlglVndlV1hlRna1Rue1E5SlH/q0uJc0rimEhxdkBXbj/LhDtahzqjgjpcczp9nTnvmzlZWTNYZS85ailmUChLXSYpdSICOSEYIiFMaB4yURAmHApOSgAVDwB+JAIrAAAALnRSTlP+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+/v7+iNdOOwAAAD1JREFUCNcFwYMBADEABLDr27ZZd//1moDJ0ItSp8e31H4QJxyTyNwC2mAnbX7iVriGmR5N+YCs4/ZW3W8BZWUEDXOg7OsAAAAASUVORK5CYII="/>
  <filter id="b"><feGaussianBlur stdDeviation=".5" /></filter>
</svg>
```

Вот что мы увидим:

![](http://rentafounder.com/resources/gradients-svg.jpg)

Вы наверняка заметили этот неожиданный блок: `viewBox = "1 1 6 4" ` в нашем SVG изображении в один пиксель с каждой стороны. Это происходит потому, что фильтр Гауса предполагает, что все изображения белого цвета и поэтому создает белую дымку на границах. Обрезка границ заставит его исчезнуть. 

Еще раз сконвертируем наш SVG файл в data URI и вставим в наш CSS:

```css
body {
  background-image: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSAiaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgdmlld0JveD0iMSAxIDYgNCI+PGltYWdlIGZpbHRlcj0idXJsKCNiKSIgd2lkdGg9IjgiIGhlaWdodD0iNiIgeGxpbms6aHJlZj0iZGF0YTppbWFnZS9wbmc7YmFzZTY0LGlWQk9SdzBLR2dvQUFBQU5TVWhFVWdBQUFBZ0FBQUFHQ0FNQUFBREoyeS9KQUFBQWlsQk1WRVdWc0YyOGhqbG1hTEtpazZ0c2NLTnFaS09acjUxZlo1UnhjNUdqdUk5V1RZSm5hSUJzZlhCVmIyNklwV0tNbEZkbGdsVm5kbFYxaGxSbmExUnVlMUU1U2xIL3EwdUpjMHJpbUVoeGRrQlhiai9MaER0YWh6cWpnanBjY3pwOW5UbnZtemxaV1ROWVpTODVhaWxtVUNoTFhTWXBkU0lDT1NFWUlpRk1hQjR5VVJBbUhBcE9TZ0FWRHdCK0pBSXJBQUFBTG5SU1RsUCsvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NytpTmRPT3dBQUFEMUpSRUZVQ05jRndZTUJBREVBQkxEcjI3WlpkLy8xbW9ESjBJdFNwOGUzMUg0UUp4eVR5TndDMm1BbmJYN2lWcmlHbVI1TitZQ3M0L1pXM1c4QlpXVUVEWE9nN09zQUFBQUFTVVZPUks1Q1lJST0iLz48ZmlsdGVyIGlkPSJiIj48ZmVHYXVzc2lhbkJsdXIgc3RkRGV2aWF0aW9uPSIuNSIgLz48L2ZpbHRlcj48L3N2Zz4=");
  background-repeat: no-repeat;
  background-size: cover;
}
```

Выглядит хорошо, даже в Firefox: 

![](http://rentafounder.com/resources/gradients-ffsvg.jpg)

Есть еще одна вещь, которую я бы хотел сделать -- придать фону немного текстуры. Опять же используя SVG (хотя в PNG тоже может хорошо получиться):

```html
<svg width="3" height="3" viewBox="0 0 3 3"
  xmlns:svg="http://www.w3.org/2000/svg" xmlns="http://www.w3.org/2000/svg"
  xmlns:xlink="http://www.w3.org/1999/xlink">
  <rect x="0" y="0" width="1" height="1" style="fill:rgba(0,0,0,.2)"/>
</svg>
```

Это черный, слегка прозрачный один пиксель `(1,1)`. Еще раз конвертируем в data-URI это и повторим его для нашего градиента:

```css
html {
  background-image: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSAiaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgdmlld0JveD0iMSAxIDYgNCI+PGltYWdlIGZpbHRlcj0idXJsKCNiKSIgd2lkdGg9IjgiIGhlaWdodD0iNiIgeGxpbms6aHJlZj0iZGF0YTppbWFnZS9wbmc7YmFzZTY0LGlWQk9SdzBLR2dvQUFBQU5TVWhFVWdBQUFBZ0FBQUFHQ0FNQUFBREoyeS9KQUFBQWlsQk1WRVdWc0YyOGhqbG1hTEtpazZ0c2NLTnFaS09acjUxZlo1UnhjNUdqdUk5V1RZSm5hSUJzZlhCVmIyNklwV0tNbEZkbGdsVm5kbFYxaGxSbmExUnVlMUU1U2xIL3EwdUpjMHJpbUVoeGRrQlhiai9MaER0YWh6cWpnanBjY3pwOW5UbnZtemxaV1ROWVpTODVhaWxtVUNoTFhTWXBkU0lDT1NFWUlpRk1hQjR5VVJBbUhBcE9TZ0FWRHdCK0pBSXJBQUFBTG5SU1RsUCsvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NysvdjcrL3Y3Ky92NytpTmRPT3dBQUFEMUpSRUZVQ05jRndZTUJBREVBQkxEcjI3WlpkLy8xbW9ESjBJdFNwOGUzMUg0UUp4eVR5TndDMm1BbmJYN2lWcmlHbVI1TitZQ3M0L1pXM1c4QlpXVUVEWE9nN09zQUFBQUFTVVZPUks1Q1lJST0iLz48ZmlsdGVyIGlkPSJiIj48ZmVHYXVzc2lhbkJsdXIgc3RkRGV2aWF0aW9uPSIuNSIgLz48L2ZpbHRlcj48L3N2Zz4=");
  background-repeat: no-repeat;
  background-size: cover;
}

body {
  background-image: url("data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMyIgaGVpZ2h0PSIzIiB2aWV3Qm94PSIwIDAgMyAzIg0KICB4bWxuczpzdmc9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciDQogIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIj48cmVjdCB4PSIwIiB5PSIwIiB3aWR0aD0iMSIgaGVpZ2h0PSIxIiBzdHlsZT0iZmlsbDpyZ2JhKDAsMCwwLC4yKSIvPjwvc3ZnPg==");
  margin: 0;
}
```

Итак, мы имеем конечный файл весом всего 1,440 байт (без сжатия). 

![](http://rentafounder.com/resources/gradients-goal.jpg)

Если хотите увидеть текстуру получше, то вот увеличенное изображение: 

![](http://rentafounder.com/resources/gradients-texture.jpg)

Я заметил, что некоторым браузерам нужно время для отображения градиента. Я предполагаю это из-за того, что идет вычисление размера SVG для всей страницы и фильтр Гауса так же увеличивает время загрузки из-за своего размера.Так как у нас нет на данный момент GPU-загрузки для сайтов, то у нас есть такой компромисс: данное решение снижает сетевой трафик, но страница загружается несколько медленней. Для ускорейния загрузки, наверное, стоит загружать градиентные изображения.