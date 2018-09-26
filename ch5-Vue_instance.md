# 第五章 - Vue Instance

### $mount

```js
//下面這兩個都 = el: "#app"
vm.$mount("app")
vm.$mount(document.getElementById("app"))
```

### template
```js
// 會以template的優先，如果上面html裡面有，會把它蓋掉
const vm = new Vue({
    el: "app",
    template:"<div><h2>{{msg}}{{msg}}</h2></div>",
    data: {
        msg: "Hello",
    }
})
```

### 簡寫

v-on:click = @click
v-bind:style = :style

### data - 這個實例的狀態或是資料

```js
const data = {
    x: 1,
    y: 2
};

const vm = new Vue({
    el: "#app",
    data,//這裡可以這樣是因為object shorthand
});

vm.x = 3;
console.log(data.x);//3 vm.x跟data.x已經綁定了
console.log(vm.$data === data);//true

```

### methods - 這個實例的方法

```html
<div id="app">
    <h1>{{count}}<h1/>
    <button @click="add">+{{step}}</button>
</div>
```

methods不能寫成箭頭函式，因為this會出問題，箭頭函式的this是這個時候的this，也就是外面的scope(此例是global物件也就是window)

```js
const vm = new Vue({
    el: "#app",
    data:{
        count: 0,
        step: 1,
    },
    methods:{
        // add: function(){
        // }
        // object-shorthand
        add(){
            this.count += this.step;
            this.step += 1;
        },
        add2(){
            this.add();
        }
    }
});
```

### computed - 計算屬性的方法

```html
<div id="app">
    <input type="name" v-model="a"/>
    +
    <input type="number" v-model="b"/>
    =
    <input type="number" v-model="c"/>
    <br/>
    {{a}} + {{b}}
    <div>{{d}}</div>
</div>
```

```js
const vm = new Vue({
    el: "#app",
    data: {
        a: 0,
        b: 0,
    },
    computed: {
        c: {
            //當要取得資料會跑get
            get(){
                return parseInt(this.a, 10) + parseInt(this.b, 10);
            },
            //當要設定值時會跑set
            set(val){
                this.b = parseInt(val, 10) - parseInt(this.a, 10);
            }
        },
        d(){
            return parseInt(this.a, 10) + parseInt(this.b, 10);
        },
    },
})
```


### watch - 偵聽器(偵聽data,computed這些屬性)

```html
<div id="app">
    <input v-model="value"/>
    <span>{{valuex2}}</span><br/>
    <span>{{a}}</span>
    <input v-model="index.b"/>
</div>
```

```js
const vm = new Vue({
    el: "#app",
    data: {
        value: 0,
        a: 0,
        index: {
            b: 0,
        },
    },
    computed:{
        valuex2(){
            return parseInt(this.value, 10)* 2;
        }
    },
    watch: {
        // 偵聽data裡面value這個值
        value(val, oldVal){
            console.log(`${oldVal} -> ${val}`)
        },
        // 偵聽computed裡面valuex2的值
        valuex2(val, oldVal){
            console.log(`${oldVal} -> ${val}`)
        },
        //物件形式 - handler取代上述的效果
        a: {
            handler(val, oldVal){
                console.log(`${oldVal} -> ${val}`)
            },
            // immediate的意思是當Vue實例建立的時候，就會跑這個watch
            immediate: true,
        },
        index: {
            // handler不會在index裡面的b改變時觸發，因為index沒有改變，是裡面的b改變
            handler(val, oldVal){
                console.log(val === oldVal); // true, 因為前後都是指向input的物件，物件並沒有被更改
                console.log(`${oldVal.b} -> ${val.b}`)
            },
            // 增加deep屬性時，handler才會在index裡面的b改變時觸發
            deep: true,
        }
    },
})
```

### watch 與 computed 的差異與選擇時機

當可以使用computed的時候就應該使用computed，
watch非常耗費效能，
以此例，
兩個watch只需要寫一個computed，
假設有100個watch只要寫一個computed效能上就差很多了！
使用computed時，會把c暫存在一個地方，
即使a、b改變，只要算出來的結果沒變，他就不會更動C！
結論是computed第一比較簡潔，
第二非常符合聲明式coding的原則，
不是在a改變就做什麼事，
b改變就做什麼事，
而是直接聲明c就=a+b，
第三就是有效能上的考量，
所以只要可以使用computed就不要濫用watch。


```html
<div id="app">
    <input type="name" v-model="a"/>
    +
    <input type="number" v-model="b"/>
    =
    <input type="number" v-model="c"/>
</div>
```
```js
const vm = new Vue({
    el: "#app",
    data: {
        a: 0,
        b: 0,
        c: 0,//當用computed此省略
    },
    // computed: {
    //     c(){
    //             return parseInt(this.a, 10) + parseInt(this.b, 10);
    //     },
    // },
    watch: {
        a(val){
            this.c = parseInt(val) + parseInt(this.b);
        },
        b(val){
            this.c = parseInt(val) + parseInt(this.a);
        },
    }
})
```

### 生命週期
![](https://i.imgur.com/Xvo0uBp.png)
```js
const vm = new Vue({
    beforeCreadted(){

    },
    created(){

    },
    beforeMount(){

    },
    mounted(){

    },
    beforeUpdated(){

    },
    updated(){

    },
    beforeDestroy(){

    },
    destroyed(){

    },
});
```