<template>
<section>
   <div class="billing row justify-content-center align-items-start g-2">
        <!-- Order History Page Section -->
        <div class="history col-10" v-if="(historyList.length==0)?false:true">
            <h1>Order History</h1>
            <div class="table-responsive">
                <br/>
                <table class="table table-hover history-table">
                    <thead class="table-light">
                        <th>Oder#</th>
                        <th>Date</th>
                        </thead>
                        <tbody class="table-group-divider">
                            <tr v-for="(pr,idx) in historyDates" :key="idx" @click="selItem(idx)" class="tr-history">
                                <td>{{pr[2]}}</td>
                                <td>{{pr[3]}}</td>
                            </tr>
                        </tbody>
                </table>
                <div id="myModal" class="modal" v-if="itemFlag">
  <!-- Modal content -->
                <div class="modal-content">
                    <span class="close" @click="closingX">&times;</span>
                        <div class="newaccount" id="dialog-form" title="&#128525; Enjoy Your Life With Us" >
                            <h1>Order # {{orderNum}}</h1>
                            <table id="table-history">
                                <thead>
                                    <tr>
                                        <th>Item</th>
                                        <th>Price</th>
                                        <th>Amount</th>
                                        <th>Total</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="(pr,idx) in selEle" :key="idx" style="text-transform: lowercase;">
                                        <td style="text-transform: lowercase;">{{pr.pname}}</td>
                                        <td>$ {{pr.price}}</td>
                                        <td>{{pr.amount}}</td>
                                        <td>$ {{pr.amount*pr.price}}</td>
                                    </tr>
                                </tbody>
                                <tfoot>
                                    <tr>
                                        <td colspan="3" style="text-align:end;">Subtotal</td>
                                        <td>$ {{numTotal}}</td>
                                    </tr>
                                    <tr>
                                        <td colspan="3" style="text-align:end;">Total w/taxes</td>
                                        <td>$ {{(numTotal*1.15).toFixed(2)}}</td>
                                    </tr>
                                </tfoot>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
            <div class="checkout">
                <button type="button" class="btn btn-primary" @click="continueShop">Go back Shopping</button>
                <br>
                <br>
            </div>
        </div>
        <div class="col-10 emptycart" v-if="(historyList.length==0)?true:false">
            <br><br>
            <h2>You do not have any order yet!</h2>
            <br>
            <div class="checkout">
                <button type="button" class="btn btn-primary" @click="continueShop">Go back Shopping</button>
                <br>
                <br>
            </div>
        </div>
    </div> 
</section>
</template>
<script>
import ProductList from '../class/ProductList.js';
export default {
    name:'HistoryPage',
    props:['loggedUser'],
    data(){
        return{
            historyList:[],
            historyDates:[],
            itemFlag:false,
            selEle:null,
            numTotal:0,
            orderNum:0
        }
    },
    methods:{
        gettingLocalItems(){
            this.historyList=[];
            this.historyDates=[];
            let keys = Object.keys(localStorage),
            i = keys.length;
            while ( i-- ) {
                if (keys[i][0]=='x'){
                    let prearr=keys[i].split('-');
                    if (prearr[1]==this.loggedUser.uid){
                        this.historyDates.push(prearr);
                        this.historyList.push(localStorage.getItem(keys[i]));
                    }
                }
            }
            this.decrypting();
        },
        decrypting(){
            let values = new ProductList(this.loggedUser.uid);
            let newarr=[]
            for(let x of this.historyList){
                newarr.push(JSON.parse(values.decrypt(x)));
            }
            this.historyList=[];
            this.historyList=[...newarr];
            // console.log(this.historyDates)
        },
        continueShop(){
            this.$emit('category',0);
            this.$router.push({name:'home'});
            return;
        },
        selItem(idx){
            this.itemFlag=true;
            this.orderNum=this.historyDates[idx][2];
            this.selEle=[];
            let sum=0;
            for(let x of this.historyList[idx]){
                sum+=x[1].price*x[1].amount;
                this.selEle.push(x[1]);
            }
            this.numTotal=sum;
        },
        closingX(){
            this.itemFlag=false;
        }
    },
    mounted(){
        this.gettingLocalItems();
    }
}
</script>