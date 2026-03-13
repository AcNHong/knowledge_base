# Phaser开发

## Tween动画

要实现元素缩放（从大到小、从小到大），可能会出现放大之后无法缩小。这是因为可能gameObject的实际原本scala（缩放比）就不是1，可能是0.5之类的。

类似的：

```
const cell = this.add.image("texture")
cell.setDisplaySize(100,100)
this.scene.tweens.add({    
targets: cell,    
scaleX: 1.15,    
scaleY: 1.15,    
duration: 80
});

```

可能这个cell在设置setDisplaySize的大小，就已经有一个比例了，通过动画设置比例,实际原本的像素大小*1.15，而不是 原本的像素大小\*已经缩放的比例\*1.15 所以在画面中就会显得很大

假设原图1000*1000，通过preload加载：

```
this.scene.load.image("url","texture")
```

这时实际大小还是1000\*1000

通过添加image对象之后，这时候的缩放比来到了100/1000 = 0.1

```
const cell = this.add.image("texture")
cell.setDisplaySize(100,100)

```

所以通过tween实现缩放如果直接填写缩放比值，就会是1000*1.15 = 1150

要实现动画缩放视觉效果一致，就得当前大小的缩放比，例如

```
const cell = this.add.image("texture")
cell.setDisplaySize(100,100);
const cellOriginScale = [cell.scaleX,cell.scaleY];
this.scene.tweens.add({
  targets: cell,
   scaleX: cellOriginScale[0]*1.15,
   scaleY: cellOriginScale[1]*1.15,
   duration: 80,
});
这样才能保证是从当前的iamge对象大小进行缩放
恢复就用：
this.scene.tweens.add({ 
targets: cell, 
scaleX: cellOriginScale[0], 
scaleY: cellOriginScale[1], 
duration: 80 });

或者使用setDisplaySize或者setScale，但是会比较声音，用tween效果比较好。
```

