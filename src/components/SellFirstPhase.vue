<template>
    <div>
        <!-- SELL Component  -->
        <div class="currency_validate" style="margin-top:-0.7rem" v-if="trade_type === 'SELL'">
            <div class="mb-3">
                <label class="me-sm-2">Coin Type </label>
                <div class="input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text"><i class="cc BTC-alt" v-if="selected_coin_name === 'Bitcoin'"></i> <i class="cc SOL-alt" v-if="selected_coin_name === 'Solana'"></i> <i class="cc TX-alt" v-if="selected_coin_name === 'TRON'"></i> <img src="../../public/assets/images/pm-64.png" width="38px" v-if="selected_coin_name === 'Perfect Money'"/><i class="cc ETH-alt"  v-if="selected_coin_name == 'Ethereum'"></i><i class="cc LTC-alt"  v-if="selected_coin_name == 'LiteCoin'"></i><i class="cc DOGE-alt"  v-if="selected_coin_name == 'Doge Coin'"></i><i class="cc USDT-alt" v-if="selected_coin_name == 'USDT' "></i><i class="cc XRP-alt" v-if="selected_coin_name == 'Ripple'"></i></label>
                    </div>
                    <select class="form-control" v-model="coin_type" @change="setCoinDetails()">
                        <option value="">Select Coin</option>
                        <option :value="[{coin_name:coin.coin_name, coin_id:coin.id, buy_rate:coin.buy_rate, sell_rate:coin.sell_rate, minimum_sell_limit:coin.minimum_sell_limit, minimum_buy_limit:coin.minimum_buy_limit, shortcode:coin.coin_short_code, sell_active_status:coin.sell_active_status, buy_active_status:coin.buy_active_status,first_address:coin.first_address, second_address:coin.second_address, third_address:coin.third_address, fourth_address:coin.fourth_address, fifth_address:coin.fifth_address}]" v-for="coin in coins" :key="coin">{{coin.coin_name}}</option>
                    </select>
                </div>
            </div>

            <div class="mb-3">
                <label class="me-sm-2">Payment Method</label>
                <div class="input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text" ><i class="fa fa-bank" style="margin-top:7px !important; margin-bottom:7px"></i></label>
                    </div>
                    <select class="form-control" v-model="bank" > 
                        <option value="">Select Bank</option>
                        <option :value="account.id" v-for="account in savedAccounts" :key="account"> {{account.account_number}} {{account.bank_name}}</option>
                    </select>
                </div>
            </div>
            
             <!-- Perfect Money Sell Form Section -->
              <form method="POST" action="https://perfectmoney.is/api/step1.asp" v-if="selected_coin_name == 'Perfect Money'">
                <div class="mb-3">
                    <label class="me-sm-2">Enter Your Amount </label>
                    <div class="input-group">
                        <label class="input-group-text">$</label><input type="number"  step="0.01"  class="form-control"  v-model="dollar_amount" name="PAYMENT_AMOUNT" 
                            placeholder="Amount in USD" @input="dollarBasedCalculation(trade_type)">
                        <label class="input-group-text">???</label><input type="text"   v-model="naira_amount" class="form-control"
                            placeholder="Naira Value" @input="nairaBasedCalculation(trade_type)">
                       
                    </div>
                    <div class="input-group mt-2" v-if="coin_shortcode === 'PM'">
                        <input type="hidden" name="PAYEE_ACCOUNT" value="U37368280" />
                        <input type="hidden" name="PAYEE_NAME" value="Smart Convert" />
                        <input type="hidden" name="PAYMENT_UNITS" value="USD" />
                        <input type="hidden" name="PAYMENT_URL" value="https://ponpar.com" />
                        <input type="hidden" name="NOPAYMENT_URL" value="https://ponpar.com" />
                        <input value="mailto:smartconvert@outlook.com" type="hidden" name="STATUS_URL" /> 
                        <!-- End of Hidden Fields -->
                    </div>
                    <div class="input-group mt-2" v-else>
                        <input type="text"   class="form-control" v-model="coin_amount" 
                            placeholder="Amount of Coin" @input="coinBasedCalculation(trade_type)">
                    </div>

                    <div class="input-group mt-2" v-if="coin_shortcode !== 'PM' && trade_type === 'BUY'">
                        <input type="text"   class="form-control" v-model="coin_address" 
                            placeholder="Enter Your Coin  Address" >
                    </div>
                    <div class="d-flex justify-content-between mt-3">
                        <p class="mb-0">Minimum Limit</p>
                        <h4 class="mb-0">???{{minimum_sell_limit}} </h4>
                    </div>
                </div>
                <div class="row mt-3">
                    <div class="col-lg-6">
                        <button type="submit" class="btn btn-success btn-block">Proceed</button>
                    </div>
                    <div class="col-lg-6" @click="reloadPage">
                        <span style="color:white; font-size: 22px; float: right; cursor: pointer;" > <i class="las la-arrow-left"></i> Back</span>
                    </div>
                     
                </div>
            </form>
            <!--End of Perfect MOney Sell Form Section  -->

            <!-- Other Coins Form Section -->
            
                <div class="mb-3" v-else>
                    <label class="me-sm-2">Enter Your Amount </label>
                    <div class="input-group">
                    <label class="input-group-text">$</label><input type="text"  class="form-control" v-model="dollar_amount" 
                        placeholder="Amount in USD" @input="dollarBasedCalculation(trade_type)" required>
                    <label class="input-group-text">???</label><input type="text"   v-model="naira_amount" class="form-control"
                        placeholder="Naira Value" @input="nairaBasedCalculation(trade_type)" required>
                    </div>
                    <div class="input-group mt-2" v-if="coin_shortcode === 'PM'">
                        <input type="text"   class="form-control" v-model="pm_account" 
                            placeholder="Enter your PM Account" required>
                    </div>
                    <div class="input-group mt-2" v-else>
                        <input type="text"   class="form-control" v-model="coin_amount" 
                            placeholder="Amount of Coin" @input="coinBasedCalculation(trade_type)" required>
                    </div>
                    <div class="d-flex justify-content-between mt-3">
                        <p class="mb-0">Minimum Limit</p>
                        <h4 class="mb-0">${{minimum_sell_limit}} </h4>
                    </div>
                    <div class="row mt-3">
                    <div class="col-lg-6" v-if="loading">
                        <button type="submit" @click="firstPhase(trade_not_active, trade_type)" class="btn btn-success btn-block buttonload" :disabled="loading" style="color:white"> Processing Request <i class="fa fa-circle-o-notch fa-spin" style="font-size:larger;"></i></button>
                    </div>
                    <div class="col-lg-6" v-else>
                        <button type="submit" @click="firstPhase(trade_not_active, trade_type)" class="btn btn-success btn-block" >Proceed</button>
                    </div>
                    <div class="col-lg-6" @click="reloadPage">
                        <span style="color:white; font-size: 22px; float: right; cursor: pointer;" > <i class="las la-arrow-left"></i> Back</span>
                    </div>
                    </div>
                </div>
           
            <!--End of Other Coins Form Section  -->
        </div>

        <!-- END SELL -->

        
        <!-- BUY Component  -->
        <div class="currency_validate" style="margin-top:-0.7rem" v-if="trade_type === 'BUY'">
            <div class="mb-3">
                <label class="me-sm-2">Coin Type </label>
                <div class="input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text"><i class="cc BTC-alt" v-if="selected_coin_name === 'Bitcoin'"></i> <i class="cc TX-alt" v-if="selected_coin_name === 'TRON'"></i> <img src="../../public/assets/images/pm-64.png" width="38px" v-if="selected_coin_name === 'Perfect Money'"/><i class="cc ETH-alt"  v-if="selected_coin_name == 'Ethereum'"></i><i class="cc LTC-alt"  v-if="selected_coin_name == 'LiteCoin'"></i><i class="cc DOGE-alt"  v-if="selected_coin_name == 'Doge Coin'"></i><i class="cc USDT-alt" v-if="selected_coin_name == 'USDT' "></i><i class="cc XRP-alt" v-if="selected_coin_name == 'Ripple'"></i></label>
                    </div>
                    <select class="form-control" v-model="coin_type" @change="setCoinDetails()">
                        <option value="">Select Coin</option>
                        <option :value="[{coin_name:coin.coin_name, coin_id:coin.id, buy_rate:coin.buy_rate, sell_rate:coin.sell_rate, minimum_sell_limit:coin.minimum_sell_limit, minimum_buy_limit:coin.minimum_buy_limit, shortcode:coin.coin_short_code, sell_active_status:coin.sell_active_status, buy_active_status:coin.buy_active_status}]" v-for="coin in coins" :key="coin">{{coin.coin_name}}</option>
                    </select>
                </div>
            </div>

            <div class="mb-3">
                <label class="me-sm-2">Payment Method</label>
                <div class="input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text" ><i class="fa fa-bank" style="margin-top:7px !important; margin-bottom:7px"></i></label>
                    </div>
                    <select class="form-control" v-model="buy_payment_mode" > 
                        <option value="">Select Payment Mode</option>
                        <option value="TRANSFER">TRANSFER</option>
                        <option value="CASH DEPOSIT">CASH DEPOSIT</option>
                    </select>
                </div>
            </div>
          
            <div class="mb-3">
                    <label class="me-sm-2">Enter Your Amount </label>
                    <div class="input-group">
                    <label class="input-group-text">$</label><input type="text"  class="form-control" v-model="dollar_amount" 
                            placeholder="Amount in USD" @input="dollarBasedCalculation(trade_type)" required>
                   
                    <label class="input-group-text">???</label><input type="text" v-model="naira_amount" class="form-control"
                            placeholder="Naira Value" @input="nairaBasedCalculation(trade_type)" required>
                    </div>
                    <div class="input-group mt-2" v-if="coin_shortcode === 'PM'">
                        <input type="text"   class="form-control" v-model="pm_account" 
                            placeholder="Enter your PM Account" required>
                    </div>
                    <div class="input-group mt-2" v-else>
                        <input type="text"class="form-control" v-model="coin_amount" 
                            placeholder="Amount of Coin" @input="coinBasedCalculation(trade_type)" required>
                    </div>

                    <div class="input-group mt-2" v-if="coin_shortcode !== 'PM'">
                        <input type="text"   class="form-control" v-model="coin_address" 
                            placeholder="Enter Your Coin  Address" required>
                    </div>
                    <div class="d-flex justify-content-between mt-3">
                        <p class="mb-0">Minimum Limit</p>
                        <h4 class="mb-0">${{minimum_buy_limit}} </h4>
                    </div>
               
                <div class="row mt-3">
                    <div class="col-lg-6" v-if="loading">
                        <button type="submit" @click="firstPhase(trade_not_active, trade_type)" class="btn btn-success btn-block buttonload" :disabled="loading">Processing Request <i class="fa fa-circle-o-notch fa-spin"></i></button>
                    </div>
                    <div class="col-lg-6" v-else>
                        <button type="submit" @click="firstPhase(trade_not_active, trade_type)" class="btn btn-success btn-block" >Proceed</button>
                    </div>
                    <div class="col-lg-6" @click="reloadPage">
                        <span style="color:white; font-size: 22px; float: right; cursor: pointer;" > <i class="las la-arrow-left"></i> Back</span>
                    </div>
                     
                </div>
                </div>
        </div>
        <!-- END BUY Component -->
    </div>
</template>

<script>
import Api from '../views/Api'
    export default{
        name: 'SellFirstPhase',
        props: [
            'coins', 'savedAccounts', 'coinCurrentValue', 'trade_not_active', 'trade_type'
        ],
        data(){
            return{
                naira_amount: '',
                coin_amount: '',
                dollar_amount:'',
                currentPhase: 'SellSecondPhase',
                coin_type: [],
                bank: '',
                selected_coin_name:'',
                minimum_buy_limit: '',
                minimum_sell_limit: '',
                coin_sell_rate: '',
                coin_buy_rate: '',
                btc_rate: '',
                eth_rate: '',
                doge_rate:'',
                pm_rate:'',
                xrp_rate:'',
                trx_rate:'',
                ltc_rate:'',
                usdt_rate:'',
                coin_shortcode: '',
                current_coin_value: '',
                coin_id: '',
                selected: true,
                pm_account: '',
                buy_payment_mode: '',
                coin_address: '',
                buy_Phase: 'BuyPreviewPhase',
                address_check: this.$store.state.addressInfo.address,
                loading: false,
                wallet_address_id: '',
                address_account_id: '',
                transaction_ref: '',
                sell_active_status: false,
                buy_active_status: false,
                send_address: '',
                mycelium_status: false,
                first_address: '',
                second_address: '',
                third_address: '',
                fourth_address: '',
                fifth_address: ''

            }
        },
        methods: {
            async firstPhase(trade_not_active, trade_type){
                this.loading = true
                let verification_status = localStorage.getItem('id')
               
                if (this.dollar_amount === '' || this.naira_amount === '' || this.coin_name === ''){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 100,
                    message:'Chief! Please fill in all details'})
                    this.loading = false
                }else if(trade_type === 'SELL' && this.dollar_amount < this.minimum_sell_limit || trade_type === 'BUY' && this.dollar_amount < this.minimum_buy_limit){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 200,
                    message:'Chief! Amount specified is lower than the minimum limit'})
                    this.loading = false
                }else if(verification_status === "1" && this.dollar_amount > 200){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 300,
                    message:'Chief! you can\'t transact more than $200. Click Account to upload your ID for verification'})
                    this.loading = false
                }else if(verification_status === "3" &&  this.dollar_amount > 200){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 300,
                    message:'Chief! Your ID upload is awaiting admin approval'})
                    this.loading = false
                }else if(this.buy_active_status == false){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 300,
                    message:'The selected coin isn\'t available to buy at the moment.'})
                    this.loading = false
                }else if(this.sell_active_status == false){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 300,
                    message:'The selected coin isn\'t available to sell at the moment.'})
                    this.loading = false
                } else if (this.trade_type == 'SELL' && this.bank == ''){
                    this.$toast.error({
                    title:'Oops!',
                    position: 'bottom left',
                    showDuration: 100,
                    message:'Chief! Please select a bank account or create one'})
                    this.loading = false 
                }
                else{
                let tradeData = {
                    dollar_amount: parseFloat(this.dollar_amount),
                    naira_amount: parseFloat(this.naira_amount),
                    coin_amount: parseFloat(this.coin_amount),
                    coin_name: this.selected_coin_name,
                    bank_account: this.bank,
                    trade_type: trade_type,
                }
                let formData = {}
                    if (trade_type === 'SELL'){
                        if(this.selected_coin_name === "Perfect Money"){
                            console.log("Perfect Money");
                        }else{
                            const addressArray = [this.coin_type[0].first_address, this.coin_type[0].second_address, this.coin_type[0].third_address, this.coin_type[0].fourth_address, this.coin_type[0].fifth_address]
                            let randomAddressSelection = Math.floor(Math.random() * addressArray.length) 
                            let receivingAddress = addressArray[randomAddressSelection]
                            console.log(addressArray[randomAddressSelection]);
                            this.coin_address = receivingAddress
                        }
                        // else if(this.selected_coin_name === "Bitcoin"){
                        //     await Api.axios_instance.post("https://gateway.gear.mycelium.com/gateways/b31f6babde01f965c84a3e82e11d4b1c04d06536397cdef303f449565e0caa9b/orders?amount="+this.dollar_amount)
                        //     .then((response) => {
                        //             this.coin_address = response.data.address
                        //             this.transaction_ref = response.data.payment_id
                        //             this.address_account_id  = response.data.id
                        //             this.mycelium_status = true
                                    
                        //             // Trade details in vue  store 
                        //             let lowerCasedCoinName = this.selected_coin_name.toLowerCase()
                        //                 lowerCasedCoinName = lowerCasedCoinName.split(" ").join("");
                        //                 let storeData = {
                        //                     address: this.coin_address,
                        //                     network: this.selected_coin_name,
                        //                     dollar_amount: this.dollar_amount,
                        //                     coin_amount: this.coin_amount,
                        //                     coin_name: lowerCasedCoinName,
                        //                 }

                        //                 formData = {
                        //                     dollar_amount: parseFloat(this.dollar_amount),
                        //                     naira_amount: parseFloat(this.naira_amount),
                        //                     coin_amount: parseFloat(this.coin_amount),
                        //                     coin: this.coin_id,
                        //                     bank: this.bank,
                        //                     trade_type: trade_type,
                        //                     buy_payment_mode: this.buy_payment_mode,
                        //                     pm_account: this.pm_account,
                        //                     wallet_address_id: this.transaction_ref,
                        //                     address_account_id: this.address_account_id,
                        //                     coin_address: this.coin_address
                        //                 }
                        //             this.$store.commit('uniqueAddressStore', storeData)
                        //         })
                    
                        //     .catch((error) => {
                        //         console.error(error)
                        //     }) 
                        // } else{
                        //     if(this.selected_coin_name === "USDT" || this.selected_coin_name === "TRON"){
                        //         this.coin_address = "TEu47d6okBh1ouCCcubur9GGQhKWjwthWW"
                        //     } else if (this.selected_coin_name === "Solana"){
                        //         this.coin_address = "JBJy5KmRnaMRcVKFueW3xFgTFs9mXpckFbRwYdMJ2Tpa"
                        //     } else if (this.selected_coin_name === "Doge Coin"){
                        //         this.coin_address = "DPbYsEkEqVyBar399sb1TBGENPeUpKZoBS"
                        //     } else if (this.selected_coin_name === "LiteCoin"){
                        //         this.coin_address = "ltc1qsa89exxyhld2yyr4t0hppzd78ratwg7f8y58yn"
                        //     }  else if (this.selected_coin_name === "Ripple"){
                        //         this.coin_address = "rPgLfcKCKUCtfRQrrtoXKzTA9zn2bFhmSM"
                        //     }  else if (this.selected_coin_name === "Ethereum"){
                        //         this.coin_address = "0x9a44f1ae2ECba6ce31b3B824301b230A575A27C3"
                        //     }  
                        // }
                    }  // Trade details in vue store 
                let lowerCasedCoinName = this.selected_coin_name.toLowerCase()
                    lowerCasedCoinName = lowerCasedCoinName.split(" ").join("");
                    let storeData = {
                        address: this.coin_address,
                        network: this.selected_coin_name,
                        dollar_amount: this.dollar_amount,
                        coin_amount: this.coin_amount,
                        coin_name: lowerCasedCoinName,
                    }
                    this.$store.commit('uniqueAddressStore', storeData)
                    
                if (this.coin_shortcode === "PM"){
                    formData = {
                        dollar_amount: parseFloat(this.dollar_amount),
                        naira_amount: parseFloat(this.naira_amount),
                        coin: this.coin_id,
                        trade_type: trade_type,
                        buy_payment_mode: this.buy_payment_mode,
                        pm_account: this.pm_account,
                        coin_address: this.coin_address
                    }
                }
                else{
                    formData = {
                        dollar_amount: parseFloat(this.dollar_amount),
                        naira_amount: parseFloat(this.naira_amount),
                        coin_amount: parseFloat(this.coin_amount),
                        coin: this.coin_id,
                        trade_type: trade_type,
                        buy_payment_mode: this.buy_payment_mode,
                        pm_account: this.pm_account,
                        coin_address: this.coin_address
                    }
                }
                this.$store.commit('currentTrade', tradeData)
                await Api.axios_instance.post(Api.baseUrl+'api/v1/create-transaction/', formData)
                .then(response => {
                    this.$emit('getTransactions')
                    this.$toast.success({
                    title:'Welldone Boss!',
                    message:'Order Has Been created'
                    })
                }).finally(() => {
                    this.loading = true
                })  
            
                if(trade_type == 'SELL'){
                    this.$emit('firstPhase', this.currentPhase)
                } else{
                    this.$emit('secondPhase', this.buy_Phase)
                }
            }
                
        },
            async setCoinDetails(){
                this.dollar_amount = ""
                this.naira_amount = ""
                this.coin_amount = ""
                const addressArray = [this.coin_type[0].first_address, this.coin_type[0].second_address, this.coin_type[0].third_address, this.coin_type[0].fourth_address, this.coin_type[0].fifth_address]
                let randomAddressSelection = Math.floor(Math.random() * addressArray.length) 
                let receivingAddress = addressArray[randomAddressSelection]
                console.log(addressArray[randomAddressSelection]);
                this.coin_address = receivingAddress
                this.selected_coin_name = this.coin_type[0].coin_name
                this.minimum_sell_limit = this.coin_type[0].minimum_sell_limit
                this.minimum_buy_limit = this.coin_type[0].minimum_buy_limit
                this.coin_sell_status = this.coin_type[0].sell_active_status
                this.sell_active_status = this.coin_type[0].sell_active_status
                this.buy_active_status = this.coin_type[0].buy_active_status
                this.coin_sell_rate = this.coin_type[0].sell_rate
                this.coin_buy_rate = this.coin_type[0].buy_rate
                this.coin_shortcode = this.coin_type[0].shortcode 
                this.coin_id = this.coin_type[0].coin_id
                await Api.axios_instance.get("https://min-api.cryptocompare.com/data/pricemulti?fsyms="+this.coin_shortcode+"&tsyms=USD&api_key=f72b59432fb04a56c30fee2cc24adfdca9cda19c8a50b49c7bddba4cc0a469b6")
                .then(response  => {
                    var results = response.data
                        if(this.coin_shortcode === "BTC"){
                            this.current_coin_value = results.BTC.USD
                        }
                        else if(this.coin_shortcode === "ETH"){
                            this.current_coin_value = results.ETH.USD
                        }
                        else if(this.coin_shortcode === "USDT"){
                            this.current_coin_value = results.USDT.USD
                        }
                    else if(this.coin_shortcode === "LTC"){
                        this.current_coin_value = results.LTC.USD
                    }
                    else if(this.coin_shortcode === "DOGE"){
                        this.current_coin_value = results.DOGE.USD
                    }
                    else if(this.coin_shortcode === "XRP"){
                        this.current_coin_value = results.XRP.USD
                    }
                    else if(this.coin_shortcode === "TRX"){
                        this.current_coin_value = results.TRX.USD
                    }
                    
                })
                
            },
            reloadPage(){
                location.reload()
            },
            
            /* Calculate Coin and Naira Value based on Dollar input value*/
            dollarBasedCalculation(trade_type){ 
                if(trade_type === 'SELL'){
                    this.naira_amount = this.dollar_amount*this.coin_sell_rate
                }else{
                    this.naira_amount = this.dollar_amount*this.coin_buy_rate
                }
                this.coin_amount = this.dollar_amount/this.current_coin_value
            },

            /* Calculate Dollar Value and Naira Value based on Coin Input Value  */
            coinBasedCalculation(trade_type){
                if(trade_type === 'SELL'){
                    this.naira_amount = this.dollar_amount*this.coin_sell_rate
                }else{
                    this.naira_amount = this.dollar_amount*this.coin_buy_rate
                }
                this.dollar_amount = this.coin_amount*this.current_coin_value
            },
            
            /* Calculate Dollar Value and Coin Value based on Coin Input Value  */
            nairaBasedCalculation(trade_type){
                if(trade_type === 'SELL'){
                    this.dollar_amount = this.naira_amount/this.coin_sell_rate
                }else{
                    this.dollar_amount = this.naira_amount/this.coin_buy_rate
                }
                this.coin_amount = this.dollar_amount/this.current_coin_value
            },

        }
    }
</script>