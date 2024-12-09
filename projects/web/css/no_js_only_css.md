### Примеры использования сss без jscpirt

#### Карусель с прилипанием
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
#### Scroll
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

