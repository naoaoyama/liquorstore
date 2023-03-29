<template>
<section>
    <h1>Billing Page</h1>
    <div class="billing row justify-content-center align-items-start g-2">
        <!-- Billing Page Section -->
        <div class="bill col-10" v-if="!emptyFlag">
            <h1>Shopping Cart</h1>
            <div class="table-responsive">
                <br/>
                <table class="table table-hover bill-table" v-if="chkoutflag">
                    <thead class="table-light">
                        <tr>
                            <th>Item</th>
                            <th>Item Name</th>
                            <th>Unit Price</th>
                            <th>Amount</th>
                            <th>Total</th>
                            <th>Delete</th>
                        </tr>
                        </thead>
                        <tbody class="table-group-divider">
                            <tr v-for="(pr,idx) in yourItems" :key="idx">
                                <td class="billimg">
                                    <img height=80 :src="require(`../img/pid_${pr.pid}.jpg`)" alt="error_img"/>
                                </td>
                                <td class="billname">{{pr.pname}}</td>
                                <td>${{pr.price}}</td>
                                <td id="billamt">
                                    <span><button @click="editItem('-',pr)" id="billplus">-</button></span>
                                    <span style="width:2vh;">[ {{pr.amount}} ]</span>
                                    <span><button @click="editItem('+',pr)" id="billplus">+</button></span>
                                </td>
                                <td>${{(pr.amount*pr.price).toFixed(2)}}</td>
                                <td><a @click="removeItem(pr)"><i class="fi fi-rs-trash"></i></a></td>
                            </tr>
                        </tbody>
                        <tfoot class="billfoot" v-if="shoppingCart==null?false:true">
                            <tr>
                                <td colspan="3" width=60%></td>
                                <td colspan="1" id="billfootrow">Subtotal({{cartNum}} - items)</td>
                                <td colspan="2" id="billfootrow2">${{shoppingCart.invoiceTotal()}}</td>
                            </tr>
                            <tr>
                                <td colspan="3"></td>
                                <td colspan="1">PST 10%</td>
                                <td colspan="2">${{(shoppingCart.invoiceTotal()/10).toFixed(2)}}</td>
                            </tr>
                            <tr>
                                <td colspan="3"></td>
                                <td colspan="1">PST 5%</td>
                                <td colspan="2">${{(shoppingCart.invoiceTotal()/20).toFixed(2)}}</td>
                            </tr>
                            <tr class="billtotal">
                                <td colspan="3"></td>
                                <td colspan="1">Total({{cartNum}} - items)</td>
                                <td colspan="2">${{(shoppingCart.invoiceTotal()*1.15).toFixed(2)}}</td>
                            </tr>
                        </tfoot>
                </table>
            </div>
            <br>
            <br>
            <div class="checkout" v-if="chkoutflag">
                <button @click="chkout" type="button" class="btn btn-success">CHECK OUT</button>
            </div>
            <br><br>
        </div>
        <div class="col-10 emptycart" v-if="emptyFlag">
            <br><br>
            <h2>Your Cart is Empty!</h2>
            <button type="button" class="btn btn-primary" @click="continueShop">Go back Shopping</button>
            <br><br>
        </div>
        <div class="col-10 chkout" v-if="!chkoutflag">
            <h2>Thank you for shopping with us!</h2>
            <br>
            <button type="button" class="btn btn-primary" @click="continueShop">Go back Shopping</button>
            <br>
            <br>
        </div>
    </div>
</section>
</template>
<script>
import ProductList from '../class/ProductList.js';

export default {
    name:'BillingPage',
    props:['loggedUser'],
    data(){
        return{
            shoppingCart:null,
            chkoutflag:true,
            yourItems:[],
            emptyFlag:false,
            cartNum:0
        }
    },
    methods:{
        editItem(op,pr){
            let qty=pr.amount;
            if (op=='+'){
                qty++;
            }else{
                qty--;
            }
            this.shoppingCart.shop(pr,qty);
            this.getYourItems();
            // console.log(qty);
        },
        getSessionItems(){
            this.shoppingCart = new ProductList(this.loggedUser.uid);
            let x= this.shoppingCart.getsData();
            // console.log(x);
            if (x!=null && x.length!=0){
                for(let i of x){
                    this.shoppingCart.setting(i[1]);
                }
            }else{
                this.shoppingCart=null;
                this.emptyFlag=true;
            }
        },
        getYourItems(){
            this.yourItems=[];
            this.cartNum=0;
            if(this.shoppingCart==null){
            return
            }
            let x= this.shoppingCart.getsData();
            if(x.length==0){
                this.emptyFlag=true;
                return;
            }
            if (x!=null){
                for(let i of x){
                    this.yourItems.push(i[1]);
                    this.cartNum+=i[1].amount;
                }
            }
        },
        removeItem(pr){
            this.shoppingCart.del(pr.pid);
            this.getYourItems();
            // let x= this.shoppingCart.getsData();
            // if(x.length==0){
            //     this.emptyFlag=true;
            // }
        },
        continueShop(){
            this.$emit('category',0);
            this.$router.push({name:'home'});
            return;
        },
        chkout(){
            this.chkoutflag=false;
            let keys = Object.keys(localStorage),
            i = keys.length;
            let counter=1000;
            while ( i-- ) {
                if(keys[i][0]=='x'){
                    counter++;
                }
            }
            let date = new Date().toLocaleDateString("en-GB");
            let key='x-'+this.loggedUser.uid+'-'+counter+'-'+date;
            this.shoppingCart.localStoring(key);
        }
    },
    mounted(){
        this.getSessionItems();
        this.getYourItems();
    }
}
</script>