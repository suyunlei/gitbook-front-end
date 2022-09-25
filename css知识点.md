# 重绘和回流
## 重绘 Repaint
由于节点的几何属性发生改变或者由于样式发生改变而不会影响布局的，称为重绘。例如`outline`, `visibility`, `color`, `background-color`等，重绘的代价是高昂的，每次重绘浏览器必须验证DOM树上其他节点元素的可见性。

## 回流 Reflow
回流是是布局或者几何属性需要改变就称为回流。回流是影响浏览器性能的关键因素，因为其变化涉及到部门页面或者整个页面的布局更新。回流必定会发生重绘，重绘不一定会引发回流。

## 避免方法
### CSS
- 使用`transform`替代`top` (CSS3)
- 使用`visibility`替代`display:none`
- 避免使用`table`布局
- 尽可能在DOM树的最末端改变class
- 避免设置多层内联样式，CSS选择符是从右往左匹配查找的。
    即避免： `div > p > span`
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上
- 避免使用CSS表达式
- **CSS3硬件加速**。可以让transform, opacity, filters 这些动画不会引起回流重绘。

### JS
- **避免频繁操作样式**，最好一次性重写style属性，或者将样式列表定义为lass并且一次性更改class属性
- **避免频繁操作DOM**，创建一个`documentFragment`，在它上面应用所有DOM操作，最后再把它添加到文档中
- **避免频繁读取会引发回流/重绘的属性**，如果确实需要多次使用，用一个变量缓存起来。
- **对具有复杂动画的元素使用绝对定位**，使它脱离文档流，否则会引起父元素及后续元素频繁回流。

#### documentFragment的使用
最常用的方法是使用 DocumentFragment 创建并组成一个 DOM 子树，然后使用 Node 接口方法（如：appendChild() 或 insertBefore()）将其插入到 DOM 中。这种情况下会插入片段的所有子节点，并留下一个空的 DocumentFragment。因为所有的节点会被一次插入到文档中，所以仅会发生一个重渲染的操作，而不是每个节点分别被插入到文档中从而发生多次重渲染的操作。

**html**
``` html
<ul id="list"></ul>
```
**javascript**
``` javascript
const list = document.querySelector('#list')
const fruits = ['Apple', 'Orange', 'Banana', 'Melon']

const fragment = new DocumentFragment()
fruits.forEach((fruit) => {
  const li = document.createElement('li')
  li.textContent = fruit
  fragment.appendChild(li)
})

list.appendChild(fragment)
```

# 元素的居中
## 行内元素水平居中
行内元素包括: text, img, button, a 等等：
> 只需要给父类元素添加text-align：center即可。
## 块级元素水平居中(div)
1. 给定width的div 
    - 只需要加 `margin: 0 auto`即可
2. 没有给定width的div
    - 给**该div**添加`display：table`， 并且设置`margin:auto` (div的大小会根据内容大小而定)
    - 将**子元素**设置为`display:inline-block`，并且给父元素添加`text-align:center`
    - 给**父元素**设置为`display:flex`, 并且给**父元素**添加`justify-content:center`
    - 给**父元素**设置为`display:flex`，并且给**子元素**添加`margin:0 auto`
    - 给**父元素**设置：`display: grid;
  justify-content: center`;

## 块级元素垂直居中(div)
1. **父元素**设置成`display:table`, **子元素**设置成`display:table-cell`,并且给**子元素**添加`vertical-align:middle`
2. **父元素**设置成`display:flex; align-items:center`
3. **父元素**设置成`display:grid; align-items:center`

## 水平垂直居中
1.
``` CSS 
.father{
  position: relative;
  width:500px;
  height:500px;
}
.son{
  position: absolute;
  left:0;
  right:0;
  top:0;
  bottom:0;
  width:200px;
  height:200px;
  margin:auto;
}
```
2. 绝对定位+负margin
3. flex;
4. css-grid;
