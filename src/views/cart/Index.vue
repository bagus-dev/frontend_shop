<template>
    <div class="container-fluid mb-5 mt-4">
        <div class="row">
            <div class="col-md-6">
                <div class="card border-0 shadow rounded">
                    <div class="card-body">
                        <h5><i class="fa fa-shopping-cart"></i> DETAIL PESANAN</h5>
                        <hr>
                        <table class="table" style="border-style:solid !important;border-color:rgb(198,206,214) !important;">
                            <tbody>
                                <tr v-for="cart in carts" :key="cart.id" style="background:#edf2f7">
                                    <td class="b-none" width="25%">
                                        <div class="wrapper-image-cart">
                                            <img :src="cart.product.image" style="width:100%;border-radius:.5rem">
                                        </div>
                                    </td>
                                    <td class="b-none" width="50%">
                                        <h5><b>{{ cart.product.title }}</b></h5>
                                        <table class="table-borderless" style="font-size:14px">
                                            <tr>
                                                <td style="padding:.20rem">QTY</td>
                                                <td style="padding:.20rem">:</td>
                                                <td style="padding:.20rem"><b>{{ cart.quantity }}</b></td>
                                            </tr>
                                        </table>
                                    </td>
                                    <td class="b-none text-right">
                                        <p class="m-0 font-weight-bold">Rp. {{ moneyFormat(cart.price) }}</p>
                                        <p class="m-0">
                                            <s style="text-decoration-color:red">Rp. {{ moneyFormat(cart.product.price * cart.quantity) }}</s>
                                        </p>
                                        <br>
                                        <div class="text-right">
                                            <button @click.prevent="removeCart(cart.id)" class="btn btn-sm btn-danger">
                                                <i class="fa fa-trash"></i>
                                            </button>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>

                        <table class="table table-default">
                            <tbody>
                                <tr>
                                    <td class="set-td text-left" width="60%">
                                        <p class="m-0">JUMLAH </p>
                                    </td>
                                    <td class="set-td text-right" width="30%">&nbsp; : Rp.</td>
                                    <td class="text-right set-td">
                                        <p class="m-0" id="subtotal">{{ moneyFormat(cartTotal) }}</p>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="set-td text-left border-0">
                                        <p class="m-0">ONGKOS KIRIM (<strong>{{ cartWeight }}</strong> gram)</p>
                                    </td>
                                    <td class="set-td border-0 text-right">&nbsp; : Rp.</td>
                                    <td class="set-td border-0 text-right">
                                        <p class="m-0" id="ongkir-cart">
                                            {{ moneyFormat(state.courier_cost) }}
                                        </p>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="text-left border-0">
                                        <p class="font-weight-bold m-0 h5 text-uppercase">Grand Total </p>
                                    </td>
                                    <td class="border-0 text-right">&nbsp; : Rp.</td>
                                    <td class="border-0 text-right">
                                        <p class="m-0 font-weight-bold h5" align="right">
                                            {{ moneyFormat(state.grandTotal) }}
                                        </p>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            <div class="col-md-6">
                <div class="card border-0 shadow rounded">
                    <div class="card-body">
                        <h5><i class="fa fa-user-circle"></i> RINCIAN PENGIRIMAN</h5>
                        <hr>
                        <div class="row">
                            <div class="col-md-6">
                                <div class="form-group">
                                    <label class="font-weight-bold">NAMA LENGKAP</label>
                                    <input type="text" id="nama_lengkap" class="form-control" placeholder="Nama Lengkap" v-model="state.name">
                                    <div class="mt-2 alert alert-danger" v-if="validation.name">
                                        Masukkan Nama Lengkap
                                    </div>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="form-group">
                                    <label class="font-weight-bold">NO. HP / WHATSAPP</label>
                                    <input type="number" id="phone" class="form-control" placeholder="No. HP / WhatsApp" v-model="state.phone">
                                    <div class="mt-2 alert alert-danger" v-if="validation.phone">
                                        Masukkan No. Telp
                                    </div>
                                </div>
                            </div>
                            <div class="col-md-12">
                                <div class="form-group">
                                    <label class="font-weight-bold">PROVINSI</label>
                                    <select class="form-control" v-model="state.province_id" @change="getCities">
                                        <option value="">-- Pilih Provinsi --</option>
                                        <option v-for="province in state.provinces" :key="province.id" :value="province.province_id">
                                            {{ province.name }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-12">
                                <div class="form-group">
                                    <label class="font-weight-bold">KOTA / KABUPATEN</label>
                                    <select class="form-control" v-model="state.city_id" @change="getCourier">
                                        <option value="">-- Pilih Kota / Kabupaten --</option>
                                        <option v-for="city in state.cities" :key="city.id" :value="city.city_id">
                                            {{ city.name }}
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-12">
                                <div class="form-group" v-if="state.courier">
                                    <label class="font-weight-bold">KURIR PENGIRIMAN</label>
                                    <br>    
                                    <div class="form-check form-check-inline">
                                        <input type="radio" name="courier" id="ongkos_kirim-jne" class="form-check-input select-courier" value="jne" v-model="state.courier_type" @change="getOngkir">
                                        <label for="ongkos_kirim-jne" class="form-check-label font-weight-bold mr-4">JNE</label>

                                        <input type="radio" name="courier" id="ongkos_kirim-tiki" class="form-check-input select-courier" value="tiki" v-model="state.courier_type" @change="getOngkir">
                                        <label for="ongkos_kirim-tiki" class="form-check-label font-weight-bold mr-4">TIKI</label>

                                        <input type="radio" name="courier" id="ongkos_kirim-pos" class="form-check-input select-courier" value="pos" v-model="state.courier_type" @change="getOngkir">
                                        <label for="ongkos_kirim-pos" class="form-check-label font-weight-bold mr-4">POS</label>
                                    </div>
                                </div>
                            </div>
                            <div class="col-md-12">
                                <div class="form-group" v-if="state.cost">
                                    <hr>
                                    <label class="font-weight-bold">SERVICE KURIR</label>
                                    <br>
                                    <div class="form-check form-check-inline" v-for="value in state.costs" :key="value.service">
                                        <input type="radio" name="cost" :id="value.service" class="form-check-input" :value="value.cost[0].value+'|'+value.service" v-model="state.costService" @change="getCostService">
                                        <label :for="value.service" class="form-check-label font-weight-normal mr-5">
                                            {{ value.service }} - Rp. {{ moneyFormat(value.cost[0].value) }}
                                        </label>
                                    </div>
                                </div>
                            </div>
                            <div class="col-md-12">
                                <div class="form-group">
                                    <label class="font-weight-bold">ALAMAT LENGKAP</label>
                                    <textarea id="alamat" rows="3" class="form-control" placeholder="Alamat Lengkap&#10;&#10;Contoh: Perumahan Puri Krakatau Hijau, Rumah Blok B3 No. 37, Kotasari, Gerogol, Banten, 42162" v-model="state.address"></textarea>
                                    <div class="mt-2 alert alert-danger" v-if="validation.address">
                                        Masukkan Alamat Lengkap
                                    </div>
                                </div>
                            </div>

                            <div class="col-md-12" v-if="state.buttonCheckout">
                                <button class="btn btn-primary btn-lg btn-block" @click.prevent="checkout">CHECKOUT</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import { onMounted, computed, reactive } from 'vue'
    import { useStore } from 'vuex'
    import Api from '../../api/Api'
    import { useRouter } from 'vue-router'
    
    export default {
        setup() {
            const store = useStore()
            const router = useRouter()
            
            onMounted(() => {
                store.dispatch('cart/cartCount')
                store.dispatch('cart/cartTotal')
                store.dispatch('cart/cartWeight')
            })

            const carts = computed(() => {
                return store.getters['cart/getCart']
            })

            const cartTotal = computed(() => {
                return store.state.cart.cartTotal
            })

            const cartWeight = computed(() => {
                return store.state.cart.cartWeight
            })

            function removeCart(cart_id)
            {
                store.dispatch('cart/removeCart', cart_id)
            }

            const state = reactive({
                name:           '',
                phone:          '',
                address:        '',
                provinces:      [],
                province_id:    '',
                cities:         [],
                city_id:        '',
                courier:        false,
                courier_type:   '',
                cost:           false,
                costs:          '',
                costService:    '',
                courier_cost:   0,
                courier_service:'',
                buttonCheckout: false,
                grandTotal:     0
            })

            const validation = reactive({
                name        : false,
                phone       : false,
                address     : false
            })

            const provinces = onMounted(() => {
                Api.get('/rajaongkir/provinces').then(response => {
                    state.provinces = response.data.data
                }).catch(error => {
                    console.log(error)
                })
            })

            function getCities()
            {
                Api.get('/rajaongkir/cities', {
                    params: {
                        province_id: state.province_id
                    }
                }).then(response => {
                    state.cities = response.data.data
                }).catch(error => {
                    console.log(error)
                })
            }

            function getCourier()
            {
                state.courier = true
            }

            function getOngkir()
            {
                if(cartWeight.value == 0) {
                    alert('Silakan pilih produk terlebih dahulu!')
                    return
                }

                Api.post('/rajaongkir/checkOngkir', {
                    city_destination: state.city_id,
                    weight: cartWeight.value,
                    courier: state.courier_type
                }).then(response => {
                    state.cost = true
                    state.costs = response.data.data[0].costs
                }).catch(error => {
                    console.log(error)
                })
            }

            function getCostService()
            {
                let shipping = state.costService.split("|")

                state.courier_cost = shipping[0]
                state.courier_service = shipping[1]

                const token = store.state.auth.token

                Api.defaults.headers.common['Authorization'] = "Bearer " + token
                Api.get('cart/total').then(response => {
                    state.grandTotal = parseInt(response.data.total) + parseInt(state.courier_cost)
                })

                state.buttonCheckout = true
            }

            function checkout()
            {
                if(state.name && state.phone && state.address && cartWeight.value) {
                    let data = {
                        name:               state.name,
                        phone:              state.phone,
                        province_id:        state.province_id,
                        city_id:            state.city_id,
                        courier_type:       state.courier_type,
                        courier_service:    state.courier_service,
                        courier_cost:       state.courier_cost,
                        weight:             cartWeight.value,
                        address:            state.address,
                        grandTotal:         state.grandTotal
                    }

                    store.dispatch('cart/checkout', data).then(response => {
                        router.push({
                            name: 'detail_order',
                            params: {
                                snap_token: response[0].snap_token
                            }
                        })
                    }).catch(error => {
                        console.log(error)
                    })
                }

                if(!state.name) {
                    validation.name = true
                }

                if(!state.phone) {
                    validation.phone = true
                }

                if(!state.address) {
                    validation.address = true
                }
            }

            return {
                carts, cartTotal, cartWeight, removeCart, state, validation, provinces, getCities, getCourier, getOngkir, getCostService, checkout
            }
        }
    }
</script>