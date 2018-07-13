### diff源码

```javascript
//_diff用来递归找出差别,组装差异对象,添加到更新队列diffQueue。
ReactDOMComponent.prototype._diff = function(diffQueue, nextChildrenElements) {
    var self = this;
    //拿到之前的子节点的 component类型对象的集合,这个是在刚开始渲染时赋值的，记不得的可以翻上面
    //_renderedChildren 本来是数组，我们搞成map
    var prevChildren = flattenChildren(self._renderedChildren);
    //生成新的子节点的component对象集合，这里注意，会复用老的component对象
    var nextChildren = generateComponentChildren(prevChildren, nextChildrenElements);
    //重新赋值_renderedChildren，使用最新的。
    self._renderedChildren = []
    $.each(nextChildren, function(key, instance) {
        self._renderedChildren.push(instance);
    })

    /**注意新增代码**/
    var lastIndex = 0; //代表访问的最后一次的老的集合的位置

    var nextIndex = 0; //代表到达的新的节点的index
    //通过对比两个集合的差异，组装差异节点添加到队列中
    for (name in nextChildren) {
        if (!nextChildren.hasOwnProperty(name)) {
            continue;
        }
        var prevChild = prevChildren && prevChildren[name];
        var nextChild = nextChildren[name];
        //相同的话，说明是使用的同一个component,所以我们需要做移动的操作
        if (prevChild === nextChild) {
            //添加差异对象，类型：MOVE_EXISTING
            /**注意新增代码**/
            prevChild._mountIndex < lastIndex && diffQueue.push({
                    parentId: self._rootNodeID,
                    parentNode: $('[data-reactid=' + self._rootNodeID + ']'),
                    type: UPATE_TYPES.MOVE_EXISTING,
                    fromIndex: prevChild._mountIndex,
                    toIndex: nextIndex
                })
                /**注意新增代码**/
            lastIndex = Math.max(prevChild._mountIndex, lastIndex);

        } else { //如果不相同，说明是新增加的节点
            //但是如果老的还存在，就是element不同，但是component一样。我们需要把它对应的老的element删除。
            if (prevChild) {
                //添加差异对象，类型：REMOVE_NODE
                diffQueue.push({
                    parentId: self._rootNodeID,
                    parentNode: $('[data-reactid=' + self._rootNodeID + ']'),
                    type: UPATE_TYPES.REMOVE_NODE,
                    fromIndex: prevChild._mountIndex,
                    toIndex: null
                })

                //如果以前已经渲染过了，记得先去掉以前所有的事件监听，通过命名空间全部清空
                if (prevChild._rootNodeID) {
                    $(document).undelegate('.' + prevChild._rootNodeID);
                }

                /**注意新增代码**/
                lastIndex = Math.max(prevChild._mountIndex, lastIndex);

            }
            //新增加的节点，也组装差异对象放到队列里
            //添加差异对象，类型：INSERT_MARKUP
            diffQueue.push({
                parentId: self._rootNodeID,
                parentNode: $('[data-reactid=' + self._rootNodeID + ']'),
                type: UPATE_TYPES.INSERT_MARKUP,
                fromIndex: null,
                toIndex: nextIndex,
                markup: nextChild.mountComponent(self._rootNodeID + '.' + name) //新增的节点，多一个此属性，表示新节点的dom内容
            })
        }
        //更新mount的index
        nextChild._mountIndex = nextIndex;
        nextIndex++;
    }



    //对于老的节点里有，新的节点里没有的那些，也全都删除掉
    for (name in prevChildren) {
        if (prevChildren.hasOwnProperty(name) && !(nextChildren && nextChildren.hasOwnProperty(name))) {
            //添加差异对象，类型：REMOVE_NODE
            diffQueue.push({
                    parentId: self._rootNodeID,
                    parentNode: $('[data-reactid=' + self._rootNodeID + ']'),
                    type: UPATE_TYPES.REMOVE_NODE,
                    fromIndex: prevChildren[name]._mountIndex,
                    toIndex: null
                })
                //如果以前已经渲染过了，记得先去掉以前所有的事件监听
            if (prevChildren[name]._rootNodeID) {
                $(document).undelegate('.' + prevChildren[name]._rootNodeID);
            }
        }
    }
}

```