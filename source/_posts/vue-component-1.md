title: Vue 组件开发心得(1)
date: 2017-03-24 17:07:44
author: [mac.meng]
tags: [javascript,Vue]
date: 2017-03-24 17:07:44
---

**一句话经验**


## computed干什么用

1. 动态数据计算,不确定数据有没有
```
computedTagId: function(){
  vm = this;
  // 如果第三个品类选择不存在, 那么是-1
  if(vm.tagIdLevel3 == -1){
      return vm.tagIdLevel2;
  } else {
      return vm.tagIdLevel3;
  }
},
```

2. 动态dom选择,不确定dom节点没有没有
```
$licenceDate: () => $(".licence_validateDate"),
$speDate: () => $(".spe_pmt_validateDate")
```

## $refs

1. 不同组件之间的 \$refs 不混用, 可以重名, 你也可以访问到父级的 \$refs 可以理解为是个树的结构
    $refs 是个Object, 可以遍历. 可以.xx 和 [xx]
2. ref 放在什么东西上就是什么, reference的意思

```
// this.$refs['username'] 就是个dom对象
<div ref="username"></dom>

// this.$refs.city 就是个vue组件(vue instance)
<auto-select ref="city"></auto-select>

```
<!-- more -->

## mixin 用来做什么
和业务不强相关又不能独立变成组件的东西.
写 mixin 的时候可以,拿到组件的data和prop,可以在完整的生命周期中做事情. 基本上可以理解extend 到 vue的options 中去. 不过有merge优先级选择 `Vue.config.optionMergeStrategies`

* 监控, log
* 性能统计
* 每个组件都需要包含的内容, uuid等, 组件通讯等
* 将过大的.vue文件拆成多个 mixin 方便重构 


## directive 用来做什么
一些dom event相关的事情, 也是配合这组件取用, 需要实现有限的hook funcion. 适合移动端手势, 统一滚动等等

* vue-tap
* vue-focus 
* vue-clickaway
* vue-scroll

## little tips
### tips1
如果你想传入的props 不是string类型,记得加 `v-bind:` 或者简写成 `:`

```
// 如果写成 searchCount="2", 那么 组件内部 
// typeof this.searchCount == String
<amap-search 
 :searchCount="2"
 :autoConfirm="false"
 :useClick="true"
 title="amap title"
 @userInput="handleUserInput"
 @pickedLocation="handlePickedLocation">
</amap-search>
```


