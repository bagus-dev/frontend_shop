<template>
    <div class="container-fluid mb-5 mt-4">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <div class="card border-0 shadow rounded">
                    <div class="card-body">
                        <h4>REGISTER</h4>
                        <hr>
                        <form @submit.prevent="register">
                            <div class="row">
                                <div class="col-md-6">
                                    <div class="form-group">
                                        <label>Full Name</label>
                                        <input type="text" class="form-control" placeholder="Full Name" v-model="user.name">
                                    </div>
                                    <div class="mt-2 alert alert-danger" v-if="validation.name">
                                        {{ validation.name[0] }}
                                    </div>
                                </div>
                                <div class="col-md-6">
                                    <div class="form-group">
                                        <label>Email Address</label>
                                        <input type="email" class="form-control" placeholder="Email Address" v-model="user.email">
                                    </div>
                                    <div class="mt-2 alert alert-danger" v-if="validation.email">
                                        {{ validation.email[0] }}
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-md-6">
                                    <div class="form-group">
                                        <label>Password</label>
                                        <input type="password" class="form-control" placeholder="Password" v-model="user.password">
                                    </div>
                                    <div class="mt-2 alert alert-danger" v-if="validation.password">
                                        {{ validation.password[0] }}
                                    </div>
                                </div>
                                <div class="col-md-6">
                                    <div class="form-group">
                                        <label>Konfirmasi Password</label>
                                        <input type="password" class="form-control" placeholder="Konfirmasi Password" v-model="user.password_confirmation">
                                    </div>
                                </div>
                            </div>
                            <button type="submit" class="btn btn-primary btn-block">REGISTER</button>
                        </form>
                    </div>
                </div>
                <div class="register mt-3 text-center">
                    <p>
                        Sudah punya akun? <router-link :to="{name: 'login'}">Login Di sini !</router-link>
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import { ref, reactive } from 'vue'
    import { useStore } from 'vuex'
    import { useRouter } from 'vue-router'

    export default {
        setup() {
            const user = reactive({
                name: '',
                email: '',
                password: '',
                password_confirmation: ''
            })

            const validation = ref([])
            const store = useStore()
            const router = useRouter()

            function register() {
                let name                  = user.name
                let email                 = user.email
                let password              = user.password
                let password_confirmation = user.password_confirmation

                store.dispatch('auth/register', {
                    name,
                    email,
                    password,
                    password_confirmation
                })
                .then(() => {
                    router.push({name: 'dashboard'})
                }).catch(error => {
                    validation.value = error
                })
            }

            return {
                user,
                validation,
                register
            }
        }
    }
</script>