# React 工作原理
**Virtual Dom VS Browser Dom**

React除了是MVC框架，数据驱动页面的特点之外，核心的就是他很"快"。 按照普遍的说法："因为直接操作DOM会带来重绘、回流等，带来巨大的性能损耗而导致渲染慢等问题。React使用了虚拟DOM，每次状态更新，React比较虚拟DOM的差异之后，再更改变化的内容，最后统一由React去修改真实DOM、完成页面的更新、渲染。"

- 虚拟DOM是React的核心，它的本质是JavaScript对象。
- BrowserDOM(也就是页面真实DOM)就是Browser对象了。

**DOM没什么好说的，主要说下虚拟DOM的一些特点：**

1.本质是JS对象，代表着真实的DOM
2.比真实DOM的比较和操作快的多
3.每秒可创建200,000个虚拟DOM节点
4.每次setState或props，都会创建一次全新的虚拟dom

前几点没什么好说的，注意第四点，也就是你每一个改动，每一个动作都会让React去根据当前的状态创建一个全新的Virtual DOM，而每次生成全新的，也是为了能够比较old dom和new dom之前的差别。

## Diff算法
React会抓取每个状态下的内容，生成一个全新的Virtual DOM，然后通过和前一个的比较，找出不同和差异。React的Diff算法有两个约定：

- 两个不同类型的元素，会产生两个不同的树
- 开发者，可以使用key关键字，告诉React哪些子元素在DOM下是稳定存在的、不变的。

**第二点着重说一下，举个例子：比如真实DOM的ul标签下，有一系列的<li>标签，然而当你想要重新排列这个标签时，如果你给了每个标签一个key值，React在比较差异的时候，就能够知道"你还是你，只不过位置变化了"。 React除了要最快的找到差异外，还希望变化是最小的。如果加了key，react就会保留实例，而不像之前一样，完全创造一个全新的DOM。**

例如：初始状态

```1234```

下一个状态后，序列变为

```1324```

```
对于我们来讲，其实就是调换了4和3的顺序。可是怎么让React知道，原来的那个3跑到了原来的4后面了呢？ 就是这个唯一的key起了作用。
```

## Diff运算实例
Diff在进行比较的时候，首先会比较两个根元素，当差异是类型的改变的时候，可能就要花更多的“功夫”了

### 不同类型的dom元素
比如现在状态有这样的一个改变:
```javascript
<div>
    <Counter />
</div>

-----------
<span>
	<Counter />
</span>
```
可以看到，从`<div>`变成了`<span>`，这种类型的改变，带来的是直接对`old tree`的整体摧毁，包括子元素`Counter`。 所以旧的实例`Counter`会被完全摧毁后，创建一个新的实例来，显然这种效率是低下的

### 同类型dom元素
当比较后发现两个是同类型的，那好办了，React会查看其属性的变化，然后直接修改属性，原来的实例都得意保留，从而使得渲染高效，比如：

```javascript
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```
除了`className`，包括`style`也是一样，增加、删除、修改都不会对整个 `dom tree`进行摧毁，而是属性的修改，保留其下面元素和节点

### 相同类型的组件元素
与上面类似，相同类型的组件元素，子元素的实力会保持，不会摧毁。 当组件更新时，实例保持不变，以便在渲染之间保持状态。React更新底层组件实例的props以匹配新元素，并在底层实例上调componentWillReceiveProps（）和componentWillUpdate（）。

接下来，调用render()方法，diff算法对前一个结果和新结果进行递归

### key props
如果前面对key的解释，还不够清除，这里用一个真正的实例来说明key的重要性吧。
- 场景一：在一个列表最后增加一个元素
  
```javascript
<ul>
  <li>first</li>
  <li>second</li>
</ul>
------
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```  
可以看到，在这种情况下，React只需要在最后insert一个新元素即可，其他的都不需要变化，这个时候React是高效的,但是如果在场景二下:

- 场景二：在列表最前面插入一个元素
```javascript
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
---
<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```
  
这对React可能就是灾难性的，因为React只知道前两个元素不同，因此会完全创新一个新的元素，最后导致三个元素都是重新创建的，这大大降低了效率。这个时候，key就排上用场了。当子元素有key时，React使用key将原始树中的子元素与后续树中的子元素相匹配。例如，在上面的低效示例中添加一个key可以使树转换更高效
```javascript
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
------
<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

这样，只有key值为2014的是新创建的，而2015和2016仅仅是移动了位置而已。
