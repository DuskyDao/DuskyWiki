#web #img #picture #srcset
### Tag **picture**
##### Стандартный тег `img` с возможностью указывать разные картинки для разных разрешений экрана
```html
<img
  srcset="
    elva-fairy-320w.jpg 320w,
    elva-fairy-480w.jpg 480w,
    elva-fairy-800w.jpg 800w
  "
  sizes="(max-width: 320px) 280px,
            (max-width: 480px) 440px,
            800px"
  src="elva-fairy-800w.jpg"
  alt="Elva dressed as a fairy" />
```
##### Разные разрешения изображений в зависимости от ширины экрана
```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg" />
  <source media="(min-width: 800px)" srcset="elva-800w.jpg" />
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva" />
</picture>
```
##### Разные форматы изображений в зависимости от поддержки браузерами
```html
<picture>
  <source type="image/svg+xml" srcset="pyramid.svg" />
  <source type="image/webp" srcset="pyramid.webp" />
  <img
    src="pyramid.png"
    alt="regular pyramid built from four equilateral triangles" />
</picture>
```