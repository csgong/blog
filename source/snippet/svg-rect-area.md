
# DESCRIPTION

SVG的rect选择框的坐标计算

# CODE SNIPPET

```javascript
function calculatePoint(sx, sy, ex, ey) {

    function getArea(x, y, width, height) {
        return {x: x || 0, y: y || 0, width: width || 0, height: height || 0};
    }

    if (sx < ex && sy < ey) {  //从左到右下
        return getArea(sx, sy, ex - sx, ey - sy);
    }

    if (sx < ex && sy > ey) { //从左到右上
        return getArea(sx, ey, ex - sx, sy - ey);
    }

    if (sx > ex && sy > ey) { //从右到左上
        return getArea(ex, ey, sx - ex, sy - ey);
    }

    if (sx > ex && sy < ey) { //从右到左下
        return getArea(ex, sy, sx - ex, ey - sy);
    }
    return getArea();
}
```
