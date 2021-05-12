# Vue 3


> Creating the Vue App

Vue.createApp(Options Object)

Options Object를 매개변수로 전달한다.

Options Object 속성 예시

``` js
const app = Vue.createApp({
    data() {
        return {
        }
    },
    methods: {
        }
    },
    computed: {

    }
  })
  


```


번외 : ES6 shorthand
``` js
data : function(){
    return {
        '...'
    }     
}
```
-> data 함수 ES6 shorthand 변환

``` js
data() : {
    return {
        '...'
    }
}

```



> Attribute binding

v-bind directive : 속성 값을 동적으로 바인딩해줌.

``` html
<img v-bind:src="image">
```

shorthand 가능

``` html
<img :src="image">
<!-- 다른 예시 -->
<a :href="url">
<div :class ="isActive">
<span :style="isActive">
<span :disabled="isDisabled">
```


> Conditional Rendering

v-if, v-else, v-else-if

``` html
<p v-if="inventory > 10">In Stock</p>
<p v-else-if="inventory <= 10 && inventory > 0">
Almost sold out !</p>
<p v-else>Out of Stock</p>
```

v-show = visible
``` html
<p v-show="onSale">On Sale</p>
```

> List Rendering

v-for

key 속성은 DOM 요소에 고유 키를 부여하고, vue는 이 요소를 파악하고 앱 내에서 항목이 없데이트 될 때 추적할 수있다.

-> 성능 향상, 나중에 요소에 애니메이션을 적용하는 것과 같은 작업을 수행할 경우 Vue가 요소를 효과적으로 관리하는데 도움이 된다는 것을 알게된다고 함.

``` html
<ul>
    <li v-for="(detail, index) in details" :key="index">{{ detail }} </li>
</ul>
```

> Event Handling

v-on

컴포넌트 내에 methods option에 메소드를 정의하여 사용할 있음

shorthand @ 가능

```html

<button v-on:click="cart +=1">Add to Cart</button>
<!-- shorthand -->
<button @click="addToCart">Add to Cart</button>

```

``` js
// main.js
const app = Vue.createApp({
    data() {
        return {
            cart:0,
        }
    },
    methods : {
        addToCart(){
            this.cart +=1
        }
    }
})

```

> Class & Style Binding

v-bind를 이용한 class, style 바인딩

``` html
<!-- style값에 variant.color처럼 값 지정 가능 -->
<div 
    v-for="variant in variants" 
    class="color-circle"
    :style="{'background-color': variant.color}"
    :key="variant.id" 
    @mouseover="updateImage(variant.image)"
    >
</div>


<!-- boolean 값도 가능 -->
<img v-bind:src="image" 
    :class="{ 'out-of-stock-img' : !isStock }">

<!-- Camel, Kebab 두 가지 표기법이 가능함. -->

<div :style="{ backgroundColor: variant.color }"></div>
<div :style="{ 'background-color': variant.color }"></div>

<!-- JS Object 가능함 -->

<script>
    const app = Vue.createApp({
      data() {
          return {
            styles : {
                color : 'red',
                fontSize : '14px'
            }
          }
      }
    })
</script>

<div :style="styles"></div>

<!-- 삼항 연산자 가능 -->
<!-- XSS 취약하니 주의 신뢰할 수 있는 곳에서만 사용 -->
<button :class="[ isActive ? activeClass : '']"></>

```


> Computed Properties

계산된 값을 제공한다.

vue app 생성 시 options object에 computed 속성을 전달해서 사용한다.

아래 예시에서는 여러 variant가 존재할 때, selectedVariant 변수와 computed 속성을 이용해서 현재 선택된 variant의 이미지와 재고 정보가 화면에 출력되도록 하는 코드이다.


``` html
<!-- index.html -->

...

<div id="app">
<!-- image() -->
<img :src="image">

<!-- inStock() -->
<p v-if="inStock">In Stock</p>

<!-- 각 variant의 색깔로 칠해진 원이 화면에 출력됨 -->
<div class="color-circle" 
     v-for="(variant, index) in variants" 
     :key="variant.id" 
     @mouseover="updateVariant(index)" 
     :style="{ backgroundColor: variant.color }"></div>

</div>

...

<script src="./main.js"></script>
<script>
    const mountedApp = app.mount("#app")
</script>

...

```



``` js
// main.js

const app = Vue.createApp({
    data() {
        return {
            // 현재 선택된 variant
            selectedVariant : 0,       
            variants: [
              { id: 2234, color: 'green', image: './assets/images/socks_green.jpg' , quantity: 50},
              { id: 2235, color: 'blue', image: './assets/images/socks_blue.jpg', quantity : 0 },
            ]
        }
    },
    methods: {
        updateVariant(index) {
            this.selectedVariant = index
        }
    },
    // Computed Properties
    computed : {
        // 현재 선택된 variant의 image를 return
        image(){
            return this.variants[this.selectedVariant].image
        },
        // 현재 선택된 variant의 quantity를 return
        inStock(){
            return this.variants[this.selectedVariant].quantity
        }
    }
})


```


> Components & Props

```재사용성을 위한 것들```

컴포넌트는 ```"독립적인 단위 모듈"```

이는 레고와 비슷하다고 하는데 개인적으로 적당한 실제 시나리오를 적어봤다.

- 붕어빵 틀(Components)에 재료(Props)를 넣어 원하는 재료가 들어간 붕어빵을 만든다. 

위 예시만 봐도 잘 활용하면 재사용성이 뛰어날 것임이 느껴진다.

그럼 이제 Vue에서는 어떻게 사용하는지 예시를 통해 알아보자. 참고로 아래 예시 출처는 [Vue Mastery](https://www.vuemastery.com/courses/intro-to-vue-3/intro-to-vue3)이다

예시에서 주로 봐야할 부분은 다음과 같다.

- ```ProductDisplay```에 ```Prop```으로 ```premium```을 넘기고 computed 속성의 ```shipping()```을 통해서 ```premium = True``` 라면 ```배송비가 Free```로 표시된다.
- ```ProductDetails```에 ```Prop```으로 ```details```를 넘긴다.

```
폴더 구조는 아래와 같다.
CSS, Image는 생략한다.

Root Folder
├─ index.html
├─ main.js
└─ components
   ├─ ProductDisplay.js
   └─ ProductDetails.js

```

``` html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue Mastery</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@3.0.0-beta.12/dist/vue.global.js"></script>
  </head>
  <body>
    <div id="app">
      <div class="nav-bar"></div>

      <div class="cart">Cart({{ cart }})</div>
      <product-display :premium="premium"></product-display>
      
      
    </div>

    <!-- Import App -->
    <script src="./main.js"></script>

    <script src="./components/ProductDetails.js"></script>

    <script src="./components/ProductDisplay.js"></script>


    <!-- Mount App -->
    <script>
      const mountedApp = app.mount('#app')
    </script>
  </body>
</html>


```
``` js
// ProductDisplay.js

app.component('product-display', {
    props: {
        premium: {
            type: Boolean,
            required: true
        }
    },
    template: 
    // /*html*/은 vscode의 es6-string-html를 참고.
    /*html*/
    `
    <div class="product-display">
        <div class="product-container">
          <div class="product-image">
            <img v-bind:src="image">
          </div>
          <div class="product-info">
            <h1>{{ title }}</h1>

            <p v-if="inStock">In Stock</p>
            <p v-else>Out of Stock</p>

            <p>Shipping : {{ shipping }}</p>

            <product-detail :details="details"></product-detail>

            <div 
              v-for="(variant, index) in variants" 
              :key="variant.id" 
              @mouseover="updateVariant(index)" 
              class="color-circle" 
              :style="{ backgroundColor: variant.color }">
            </div>
            
            <button class="button" :class="{ disabledButton: !inStock }" :disabled="!inStock" v-on:click="addToCart">Add to Cart</button>
          </div>
        </div>
    </div>
    `,
    data() {
        return {
            product: 'Socks',
            brand: 'Vue Mastery',
            selectedVariant: 0,
            details: ['50% cotton', '30% wool', '20% polyester'],
            variants: [
              { id: 2234, color: 'green', image: './assets/images/socks_green.jpg', quantity: 50 },
              { id: 2235, color: 'blue', image: './assets/images/socks_blue.jpg', quantity: 0 },
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        },
        updateVariant(index) {
            this.selectedVariant = index
        }
    },
    computed: {
        title() {
            return this.brand + ' ' + this.product
        },
        image() {
            return this.variants[this.selectedVariant].image
        },
        inStock() {
            return this.variants[this.selectedVariant].quantity
        },
        shipping(){
            if (this.premium) {
                return 'Free'                
            }
            return '2.99'
        }
    }
})
```


``` js
// ProductDetails.js

app.component('product-detail',{
    props: {
        details: {
            type: Array,
            required: true
        }
    },
    template: 
    /*html*/
    `
    <ul>
        <li v-for="detail in details">{{ detail }}</li>
    </ul>
    `
})
    
```


``` js
// main.js

const app = Vue.createApp({
    data() {
        return {
            cart: 0,
            premium: true
        }
    }
})

```


> Communcation Events

컴포넌트끼리의 소통



Vue에서는 아래와 같이 $emit으로 이벤트를 뿌리고 

``` js
addToCart() {
    this.$emit('add-to-cart', this.variants[this.selectedVariant].id)    
},
removeFromCart(){
    this.$emit('remove-from-cart', this.variants[this.selectedVariant].id)
}
```
다음과 같이 이벤트가 바인딩된 컴포넌트에서 이벤트를 수신한다. = 뒤에는 이벤트를 처리할 메소드이다.

``` html 
<product-display :premium="premium" @add-to-cart="updateCart" @remove-from-cart="removeById"></product-display> 
``` 



이때 ``` this.variants[this.selectedVariant].id ```와 같이 ```payload```를 같이 뿌릴 수 있다.

```emit```은 ```'방출하다'```를 의미하고 


```payload```는 ```사용에 있어서 전송되는 데이터```를 뜻한다.

그리고 이를 수신하는 메소드들은 아래 methods와 같다.

``` js
const app = Vue.createApp({
    data() {
        return {
            cart: [],
            premium: true
        }
    },
    methods: {
        updateCart(id) {
            this.cart.push(id)
        },
        removeById(id){
            const index = this.cart.indexOf(id)
            if(index > -1){
                this.cart.splice(index, 1)
            }
        }

    }
})

```


> Forms & v-model

양방향 바인딩.

```v-model.number="rating"``` -> modifier. number로 typecast해줌

```@submit.prevent="onSubmit"``` -> prevent browser refresh

form태그와 함께 아래 예시처럼 사용할 수 있음.

``` js
app.component('review-form', {
    template: 
    /*html*/    
    `
    <form class="review-form" @submit.prevent="onSubmit">
        <h3>Leave a review</h3>
        <label for="name">Name:</label>
        <input id="name" v-model="name">

        <label for="review">Review:</label>
        <textarea id="review" v-model="review"></textarea>

        <label for="rating">Raing:</label>
        <select id="rating" v-model.number="rating">
            <option>5</option>
            <option>4</option>
            <option>3</option>
            <option>2</option>
            <option>1</option>
        </select>
        
        <label for="recommend">Would you recommend this product?</label>
        <select id=recommend v-model="recommend">
            <option>Yes</option>
            <option>No</option>
        </select>

        <input class="button" type="submit" value="Submit">    
    </form>
    `,
    data(){
        return {
            name: '',
            review: '',
            rating: null,
            recommend: null
        }
    },
    methods: {
        onSubmit(){

            if(this.name === '' || this.review === '' || this.rating == null){
                alert('Review is incomplete. Please fill out every field')
                return
            }
            let productReview = {
                name: this.name,
                review: this.review,
                rating: this.rating,
                recommend: this.recommend
            }
            this.$emit('review-submitted', productReview)
            this.name = ''
            this.review = ''
            this.rating = null
            this.recommend = null
        }
    }
    
    
})

```