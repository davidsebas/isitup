<template>
    <main class="align-middle h-100">
        <div class="container">
            <div class="row justify-content-center align-items-center">
                <div class="col-md-10">
                    <div class="card">
                        <div class="card-body mt-4">
                            <div v-if="result == false">
                                <div class="form-group mt-2 row" >
                                    <div id="is" class="col-md-3 outside mt-1 align-content-center">
                                        <label>Is</label>
                                    </div>
                                    <div class="col-md-6">
                                        <input
                                                v-model="url"
                                                ref="check"
                                                id="email"
                                                v-shortkey="['enter']"
                                                @shortkey="checkStatus"
                                                type="text" class="form-control search-box"
                                                name="url"
                                                value=""
                                                required
                                                autofocus
                                                v-validate="'required'"
                                        >
                                    </div>
                                    <div id="up" class="col-md-3 outside  mt-1 align-content-center">
                                        <label>up?</label>
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-12 form-group text-center">
                                        <input @click="checkStatus"  type="button" class="btn btn-info align-content-center" value="Check Status">
                                    </div>
                                </div>
                            </div>
                            <!--result-->
                            <div v-if="result == true">
                                <div class="form-group mt-2 row ">
                                    <div class="col-md-12 text-center ">
                                        <label class="result-url">{{url}}</label>
                                        <label class="result-text"> is </label>
                                        <label class="result-text status result-url">{{resultText}}</label>
                                    </div>
                                </div>
                                <div class="form-group row result-row " v-if="ms != null">
                                    <div class="col-md-12 text-center ">
                                        <label class="result-ms">It took <label>{{ms}}</label> ms for a 200 response.</label>
                                    </div>
                                </div>

                                <div class="form-group mt-2 row ">
                                    <div class="col-md-12 text-center ">
                                        <input type="button" v-shortkey="['enter']" @shortkey="reset" @click="reset" class="btn btn-info" value="Go Back"/>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>
</template>

<style>
    .card{
        box-shadow: -1px 4px 28px 3px #6a5384;
    }
</style>

<script>
    import {mapActions} from 'vuex';
    export default {
        inject: {
            $validator: '$validator'
        },
        data(){
          return{
              'url':null,
              'result':false,
              'ms':null,
              'resultText':'up.',
          }
        },
        methods:{
            ...mapActions([
                "getUpStatus",
            ]),
            checkStatus:function(){
                this.$validator.validate().then(result => {
                    if(result == true){
                        this.$Progress.start();
                        this.getUpStatus(this.url).then((response)=>{
                            this.$Progress.finish();
                            this.result = true;
                            if(response.status == 200){
                                this.ms = response.data.timeInMillisecond;
                                this.resultText = ' up.';
                                this.$confetti.start({
                                    shape:'rect'
                                });
                                setTimeout(()=> {
                                    this.$confetti.stop();
                                }, 3000);
                            } else {
                                this.ms = null;
                                this.resultText = ' down.';
                            }

                        });
                    }
                    else {
                        this.$noty.error("Please enter a website URL or IP Address")
                    }

                });



            },
            reset:function () {
                this.result = false;
                this.url = null;
                this.$confetti.stop();
                this.$nextTick(() => {
                    this.$refs.check.focus();
                    console.log(this.$refs.check);
                });



            }
        }
    }

</script>
