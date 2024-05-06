+++
title = 'No_js_only_css'
date = 2024-01-26T21:52:44+02:00
draft = false
+++
### **Примеры использования сss без jscpirt**
#### Содержание:
1. **[Карусель с прилипанием](#карусель-с-прилипанием)**
2. **[]()**
3. **[]()**
4. **[]()**
5. **[]()**
6. **[]()**
7. **[]()**
8. **[]()**
9. **[]()**

### **Карусель с прилипанием**
```html
<ul class="horizontal-media-scroller">
    <li>
        <a href="#">
            <figure>
                <picture>
                    <img alt="..." loading="lazy" src="..." />
                </picture>
                <figcaption>Image Caption</figcaption>
            </figure>
        </a>
    </li>
    <li>...</li>
</ul>
```
**css**
```css
.horizontal-media-scroller {
    display: grid;
    grid-auto-flow: column;
    grid-gap: calc(var(--gap) / 2);

    overflow-x: auto;
    overscroll-behavior-x: contain;
    scroll-snap-type: x mandatory;
    scroll-padding-inline: var(--gap);
}
.horizontal-media-scroller figure {
    scroll-snap-align: start;
}
```
### Scroll
```css
scroll-behavior: smooth;
```
sticky header
```css
scroll-margin-top: 100px;
```


ZMfesukr123
поменять пароль в /etc/zm/zm.conf
sudo zmupdate.pl -f

