<template>
<section>
<h1>Detail Info</h1>
<div v-if="selFlag">
<h4>{{selElement.pname}}</h4>
<div class="detail">
    <section>
        <!-- Item Detail Display Section  -->
        <figure class="itemdetail">
            <img :src="require(`../img/pid_${selElement.pid}.jpg`)" alt="error_img">
            <figcaption>
                <div>
                    <h4>{{selElement.pname}}</h4>
                    <small>{{selElement.size}}ml</small>
                </div>
                <p>$ {{selElement.price}}</p>
                <div class="mb-3 qty">
                    <span>Quantity : </span>
                    <select class="form-select" name="qty" id="qty" v-model="qty">
                        <option value="1" selected>1</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                        <option value="5">5</option>
                        <option value="6">6</option>
                        <option value="7">7</option>
                        <option value="8">8</option>
                        <option value="9">9</option>
                        <option value="10">10</option>
                    </select>
                </div>
                <aside>
                    <h5>COUNTRY : {{selElement.country}}</h5>
                    <h5>YEAR    :{{selElement.year}}</h5>
                    <h5>ALCOHOL : <span>{{selElement.alcohol}} %</span></h5>
                </aside>
            </figcaption>
            <!-- Add Cart Section -->
            <div class="buybtn">
                <!-- <button type="button" class="btn btn-danger" @click="addHandler">Add To Cart</button> -->
                <button type="button" class="btn btn-success" @click="addHandler">Add To Cart</button>
                <button type="button" class="btn btn-danger" v-if="userVerify" @click="delHandler" :disabled="delFlag">Delete From Cart</button>
                <button type="button" class="btn btn-warning" v-if="userVerify" @click="goToCart">Go to Cart</button>
                <button type="button" class="btn btn-warning" v-if="!userVerify" @click="goToSign">Sign in</button>
                <button type="button" class="btn btn-primary" @click="continueShop">Continue Shopping</button>
            </div>
            <!-- Cart Display Section -->
            <div class="availstore" style="display:flex; flex-direction:column; justify-content:start; align-items:center;" v-if="userVerify">
                <h6>Your Items In Cart</h6>
                <ul style="width:35vh">
                    <li v-for="(el,idx) of yourItems" :key="idx" style="text-transform:lowercase; width:100%">({{el.amount}}) - {{el.pname}}</li>
                </ul>
            </div>
        </figure>
        <hr/>
        <!-- Recommend Section  -->
        <article class="recommend">
            <h2>YOU MIGHT ALSO LIKE</h2>
            <div>                
            </div>
        </article>
    </section>
</div>
</div>
</section>

</template>
<script>

import ProductList from '../class/ProductList.js';
export default {
    name:'DetailPage',
    props:['selElement','userVerify','loggedUser'],
    data(){
        return{
            selFlag:false,
            qty:1,
            shoppingCart:null,
            yourItems:[],
            delFlag:true
        }
    },
    methods:{
        continueShop(){
            this.$emit('category',0);
            this.$router.push({name:'home'});
            return;
        },
        delHandler(){
            this.shoppingCart.del(this.selElement.pid);
            this.delFlag=true;
            this.getYourItems();
        },
        addHandler(){
            if(this.userVerify===false){
                alert('To create shopping cart you have to sign in first');
            }else{
                if(this.shoppingCart==null){
                    this.shoppingCart= new ProductList(this.loggedUser.uid);
                }
                this.shoppingCart.shop(this.selElement,this.qty);
                this.delFlag=false;
                this.getYourItems();
                // console.log(this.yourItems);
            }
        },
        goToSign(){
            this.$router.push({name:'login'});
        },
        getSessionItems(){
            this.shoppingCart = new ProductList(this.loggedUser.uid);
            let x= this.shoppingCart.getsData();
            if (x!=null){
                for(let i of x){
                    this.shoppingCart.setting(i[1]);
                }
                if(this.shoppingCart.has(this.selElement.pid)){
                    this.delFlag=false;
                }
            }else{
                this.shoppingCart=null;
            }
        },
        getYourItems(){
            this.yourItems=[];
            let x= this.shoppingCart.getsData();
            if (x!=null){
                for(let i of x){
                    this.yourItems.push(i[1]);
                }
            }
        },
        goToCart(){
            this.$router.push({name:'billing'});
        }
    },
    mounted(){
        if(this.selElement!=null){
            this.selFlag=true;
        }
        this.getSessionItems();
        this.getYourItems();
    }
}
</script>