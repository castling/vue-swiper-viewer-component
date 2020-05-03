# vue-swiper-viewer-component

## Description:
Swiper.jsを用いた画像配列のビューア。Vue.jsのコンポーネント。

## Installation

```bash
npm i castling/vue-swiper-viewer-component
```

## Usage:

```javascript
import SwiperViewer from 'castling/vue-swiper-viewer-component'

Vue.comonent('swiper-viewer', SwiperViewer)
```

# props

* `images` `{Array<String>}` image URLs
* `page` `{Number}` showing page number
* `showSwitch` `{Boolean}` show/hide swiper buttons
