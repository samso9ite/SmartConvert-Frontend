<template>
    <div id="">
        
        <div class="authincation section-padding" style=" 
          background: linear-gradient(to right, rgba(50, 70, 80, 0.7), rgba(0, 0, 0, 0.7)), url('../assets/images/main_bg.jpg')  !important;
            background-repeat:no-repeat !important;
            object-fit: contain !important;
            background-size: cover !important; ">
            <div class="container h-100">
                <div class="row justify-content-center h-100 align-items-center">
                    <div class="col-xl-5 col-md-6">
                        <div class="mini-logo text-center my-5">
                            <img src="../../public/assets/main_logo.png" alt="">
                        </div>
                        <div class="auth-form card">
                            <div class="card-header justify-content-center">
                                <h4 class="card-title" v-if="registered">Verification Email Sent</h4>
                                <h4 class="card-title" v-else>Sign up your account</h4>
                            </div>
                            <div class="card-body">
                               <div class="alert alert-danger fade in" v-if="errors.length">
                                    <p v-for="error in errors" :key="error">
                                       {{error}}
                                    </p>
                                </div>
                                <div style="background-color:white;  border-radius: 10px;" v-if="registered">
                                    <h4 style="color:black; padding: 10%; text-align: center;"> 
                                        An activation link has been sent to the registered email, open your email to verify your account.
                                    </h4>
                                </div>
                                <form method="post" class="signup_validate" @submit.prevent="submitForm" v-else>
                                    <div class="mb-3">
                                        <label>Email</label>
                                        <input type="email" class="form-control" placeholder="hello@example.com"
                                            name="email" v-model="email">
                                    </div>
                                    <div class="mb-3">
                                        <label>Mobile Number</label>
                                        <input type="test" class="form-control" placeholder="Mobile Number"  v-model="mobile_number">
                                    </div>
                                    <div class="mb-3">
                                        <label>First Name</label>
                                        <input type="text" class="form-control" placeholder="First Name" v-model="first_name">
                                    </div>
                                    <div class="mb-3">
                                        <label>Last Name</label>
                                        <input type="text" class="form-control" placeholder="Last Name" v-model="last_name">
                                    </div>
                                    <div class="mb-3" v-show="$store.state.campaign.status == true">
                                        <label>Promo Code</label>
                                        <input type="text" class="form-control" placeholder="Promo Code" v-model="coupon_code">
                                    </div>
                                    <div class="mb-3">
                                        <label>Password</label>
                                        <VuePassword
                                            v-model="password"
                                            :disableStrength = true
                                        />
                                    </div>
                                    <div class="text-center mt-4">
                                        <button type="submit" class="btn btn-success btn-block" :disabled="loading" style="background-color:rgb(122 21 61); border: none;" :disbled="loading">Sign up</button>
                                    </div>
                                </form>
                                <div class="new-account mt-3">
                                    <p>Already have an account? <a class="text-primary"> <router-link to="/signin"> Sign in </router-link></a>
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

       
    </div>
</template>

<script>
import Api from './Api.js'
import VuePassword from 'vue-password'

    export default{
        name: 'Signup',
        components: {VuePassword},

        data(){
            return{
                email: '',
                mobile_number: '',
                first_name: '',
                last_name:'',
                errors: [],
                password:'',
                loading: false,
                registered: false,
                campaign: {},
                coupon_code: '',
                buy_bonus_status: false,
                sell_bonus_status: false,
                bonus_status: false
            }
        },

        methods: {
            async submitForm(e){
                let bonus_data = false
                if( this.$store.state.campaign.code !== this.coupon_code && this.coupon_code !== ''){
                    this.$toast.error({
                        title:'Oops!',
                        message:'You entered a wrong or expired code',
                        showDuration: 200
                    })
                } else{
                
                    if(this.$store.state.campaign.code === this.coupon_code){
                        this.bonus_status = true,
                        this.sell_bonus_status = true,
                        this.buy_bonus_status = true
                    }

                    const formData = {
                        password : this.password,
                        re_password: this.password,
                        first_name: this.first_name,
                        last_name: this.last_name,
                        phone_number: this.mobile_number,
                        email : this.email,
                        bonus_status: this.bonus_status,
                        sell_bonus_status: this.sell_bonus_status,
                        buy_bonus_status: this.buy_bonus_status
                    }
                    this.loading = true
                    await   Api.axios_instance.post(Api.baseUrl+'auth/users/', formData,  {mode: 'no-cors'})
                    .then(res => {
                        this.$toast.success({
                            title:'Welldone!',
                            message:'Account Created Successfully '
                        })
                        this.registered = true
                    })
                    .catch(error => {
                        if (error.response){
                            for (const property in error.response.data){
                                this.errors.push(`${property}: ${error.response.data[property]}`)
                            }
                            } else if (error.message){
                                console.log(JSON.stringify(error.message));
                            } else{
                                console.log(JSON.stringify(error));
                            }
                    })
                    .finally(() => {
                        this.loading = true
                    })
                }
            }, 
        },

        mounted() {
            this.$store.dispatch('Set_Campaign')
        },
    }
</script> 

<style>
.VuePassword__Toggle{
    padding-left: 9rem !important;
}
</style>