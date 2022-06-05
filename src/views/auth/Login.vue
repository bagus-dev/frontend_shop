<template>
    <div class="container-fluid mb-5 mt-4">
        <div class="row justify-content-center">
            <div class="col-md-4">
                <div class="mt-2 alert alert-danger" v-if="validation.message">
                    {{ validation.message }}
                </div>
                <div class="card border-0 shadow rounded">
                    <div class="card-body">
                        <h4>LOGIN</h4>
                        <hr>
                        <form @submit.prevent="login">
                            <div class="form-group">
                                <label>Email Address</label>
                                <input type="email" class="form-control" placeholder="Email Address" v-model="user.email">
                            </div>
                            <div class="mt-2 alert alert-danger" v-if="validation.email">
                                {{ validation.email[0] }}
                            </div>
                            <div class="form-group">
                                <label>Password</label>
                                <input type="password" class="form-control" placeholder="Password" v-model="user.password">
                            </div>
                            <div class="mt-2 alert alert-danger" v-if="validation.password">
                                {{ validation.password[0] }}
                            </div>
                            <div class="form-group form-check">
                                <input type="checkbox" id="exampleCheck1" class="form-check-input">
                                <label for="exampleCheck1" class="form-check-label">Ingat Saya</label>
                            </div>
                            <button type="submit" class="btn btn-primary btn-block">LOGIN</button>
                        </form>
                    </div>
                </div>
                <div class="register mt-3 text-center">
                    <p>
                        Belum punya akun? <router-link :to="{name: 'register'}">Daftar Sekarang!</router-link>
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
                email: '',
                password: ''
            })

            const validation = ref([])
            const store = useStore()
            const router = useRouter()

            function login()
            {
                let email    = user.email
                let password = user.password

                store.dispatch('auth/login', {
                    email, password
                }).then(() => {
                    router.push({name: 'dashboard'})
                }).catch(error => {
                    validation.value = error
                })
            }

            return {
                user, validation, login
            }
        }
    }
</script>