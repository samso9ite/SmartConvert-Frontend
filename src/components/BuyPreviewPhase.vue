<template>
    <div style="margin-top:-0.7rem">
        <center>
            <h2>Make Payment</h2>

            <h4 style="color: yellow;">Please note that paying with an account name different from your registered name would render the order unprocessed </h4>
            <table class="table table-striped" style="font-size:14px; font-weight: 500; color: white;">
            <tbody>
            <tr>
                <td>Reference no</td>
                <td style="color:white"><b><span style="font-size: 21px !important;">{{transaction_ref}}</span><br> Copy and paste reference no on remark/narration when making payment</b> </td>
            </tr>
            <tr v-if="coin_amount != Infinity">
                <td>Coin Amount</td>
                <td>{{coin_amount}} {{coin_name}}</td>
            </tr>
            <tr v-if="coin_address != Infinity">
                <td>Coin Address</td>
                <td>{{coin_address}}</td>
            </tr>
            <tr>
                <td>Dollar Value </td>
                <td v-if="tradeDetails.confirmation_fee !== 0">${{parseFloat(dollar_amount) + parseFloat(tradeDetails.confirmation_fee)}}</td>
                <td v-else>{{ dollar_amount }}</td>
            </tr>
            <tr>
                <td>Naira Value </td>
                <td>
                    <span style="font-size: 20px !important;" v-if="tradeDetails.confirmation_fee !== 0">₦{{(parseFloat(tradeDetails.confirmation_fee) * 
                        parseFloat(tradeDetails.buy_rate))+parseFloat(tradeDetails.naira_amount)}}
                    </span>
                    <span v-else>
                        {{ tradeDetails.naira_amount }}
                    </span>
                </td>
            </tr>
            <tr>
                <td>Account Number</td>
                <td><span style="font-size: 20px !important;">{{bankDetails.admin_bank_number}}</span></td>
            </tr>
            <tr>
                <td>Account Name</td>
                <td>{{ bankDetails.admin_bank_name }} </td>
            </tr>
            <tr>
                <td>Bank</td>
                <td>{{ bankDetails.admin_bank }}</td>
            </tr>
            
            </tbody>
            </table>
            <button type="submit" @click="successPhase()" class="btn btn-success btn-block">Click After Payment</button>
        </center>
    </div>
</template>

<script>
import Api from '../views/Api'
export default({
    name: "buyPreviewPhase",
    props: ['coin_amount', 'naira_amount', 'dollar_amount', 'coin_name', 'transaction_ref', 'coin_address'],
    data(){
        return{
            currentPhase: 'SuccessPhase',
        }
    },  

    computed: {
        bankDetails: function(){
            return this.$store.state.currentTrade
        },
        tradeDetails: function(){
            return this.$store.state.currentTrade
        }
    },

    methods: {
        successPhase(){
            this.$emit('successPhase', this.currentPhase)
        },
    },
})
</script>