<template>
    <div class="container mx-auto d-flex logo-box">
        <router-link :to="{ name: 'home' }">
            <div class="logo-thumb">
                <img src="/public/imgs/logo/logo1.png" alt="">
            </div>
        </router-link>
    </div>

    <!-- HERO RESTAURANT -->
    <div class="image-container">
        <img class="restaurant-img" :src="getImageUrl(restaurant.img)" alt="">
    </div>
    <!-- /HERO RESTAURANT -->

    <!-- RESTAURANT INFOS -->
    <section class="restaurant-section">
        <div class="mx-auto container restaurant-box">
            <div class="d-flex justify-content-between align-items-center box-bg shadow">
                <div class="container px-5 py-3">
                    <h1 class="mb-3 restaurant-name">{{ restaurant.name }}</h1>
                    <p class="restaurant-address d-none d-sm-block">{{ restaurant.address }}</p>
                </div>

                <div class="animation d-none d-sm-block">
                    <iframe src="https://embed.lottiefiles.com/animation/99271"></iframe>
                </div>
            </div>
        </div>
    </section>
    <!-- /RESTAURANT INFOS -->

    <!-- DISHES -->
    <div class="container card-box mx-auto w-100 w-lg-50">
        <div
            class="d-flex justify-content-center justify-content-md-between justify-content-xl-between flex-wrap row-gap-5">
            <div class="col col-md-6 col-lg-6 col-xl-6 col-xxl-8">

                <ul
                    class="d-flex align-items-center justify-content-center justify-content-xxl-between flex-wrap row-gap-3">

                    <li :class="['my-card', 'col-12', 'col-xxl-5', 'gap-3', 'd-flex', 'flex-column', 'justify-content-between', dish.is_visible === 1 ? '' : 'd-none']"
                        v-for="dish in dishes" :key="dish.id">

                        <div class="row justify-content-center justify-content-sm-between gap-2 text-center ">
                            <div class="food-img col-4">
                                <img class="food-thumb" :src="getImageUrl(dish.img)" alt="">
                            </div>

                            <div class="mb-3 col-8 text-center text-sm-start ">
                                <div class="food-title">{{ dish.name }}</div>
                                <div class="food-desc">{{ dish.description }}</div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="d-flex align-items-center flex-sm-row justify-content-between">
                                <p class="d-flex col-8">{{ dish.price }} €</p>
                                <div class="d-flex flex-row-reverse col-sm-4 gap-2">
                                    <AddFoodButton class="card-buttons text-center" @click="addFoodToCartWithRID(dish)" />
                                    <!-- <RemoveFoodButton class="card-buttons text-center" @click="deleteFoodFromCard(dish)" /> -->
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
            <div class="col-12 col-md-5 col-lg-5 col-xl-5 col-xxl-4 flex-{grow|shrink}-0">
                <!-- CART-->
                <section>

                    <div class="card">
                        <div class="card-body px-4">
                            <h3 class="card-title text-center fw-bold py-4">
                                Il tuo Deliveboo
                            </h3>
                            <p class="text-center mb-3" v-if="this.cart.length !== 0">Hai aggiunto piatti del ristorante {{
                                this.localRestaurantName }}.</p>
                            <p class="text-center my-text" v-else="this.cart.length === 0">Aggiungi dei piatti al
                                carrello...
                            </p>
                            <div v-for="(dish, index) in cart" :key="dish.id">
                                <div class="box-cart d-flex align-items-center align-items-sm-start">

                                    <span class="col-2 fw-bold fs-5">{{ dish.quantity }}x</span>
                                    <p class="col-5 m-0">{{ dish.name }}</p>
                                    <p class="col-3 m-0 fw-bold text-center">{{ dish.quantity * dish.price }}€</p>
                                    <div class="row col-2 justify-content-center row-gap-1 gap-1">
                                        <DeleteButton class="col-5" @click="deleteFoodQuantity(dish, index)" />
                                        <DeleteEntityButton @click="deleteFoodEntity(dish, index)" class="col-5" />
                                    </div>
                                </div>
                            </div>

                            <div class="d-flex justify-content-center pt-3 flex-wrap row-gap-3 align-items-center"
                                v-if="this.cart.length != []">
                                <DeleteAllFoodButton @click="deleteAllFood()" class="col-12" />
                                <span class="block-msg col-12 text-center">
                                    Hai ancora dei piatti di {{ this.localRestaurantName }} nel carrello, svuotalo per
                                    aggiugerne altri
                                </span>
                            </div>

                            <div class="confirm-button d-flex justify-content-center py-3">
                                <button v-if="showButtonConfirm" @click="showOrderForm">
                                    Conferma ordine
                                    <span class="fw-bold" v-if="totalCart === 0 ? '' : totalCart">{{ totalCart }}€</span>
                                </button>
                            </div>
                        </div>

                        <div v-if="showForm">
                            <div class="card-body px-4">
                                <h5 class="card-title text-center fw-bold pb-2">
                                    Prosegui con l'ordine
                                </h5>

                                <form @submit.prevent="goToPay()" action="http://127.0.0.1:8000/orders/create" method="GET"
                                    class="form" id="payment-form">
                                    <!-- action to rotta api -->
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Nome</label>
                                        <input @blur="formCheckFirstName()" type="text" class="form-control ps-2" id="name"
                                            placeholder="Nome..." name="firstName" v-model="firstName">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-firstName" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'nome' non può essere vuoto
                                    </div>
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Cognome</label>
                                        <input @blur="formCheckLastName()" type="text" class="form-control ps-2"
                                            id="surname" placeholder="Cognome..." name="lastName" v-model="lastName">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-lastName" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'cognome' non può essere vuoto
                                    </div>
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Email</label>
                                        <input @blur="formCheckEmail()" type="email" class="form-control ps-2" id="email"
                                            placeholder="name@example.com" name="email" v-model="email">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-email" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'email' non può essere vuoto
                                    </div>
                                    <div class="err-regex-eMail" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'email' ha un formato non valido
                                    </div>
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Numero</label>
                                        <input @blur="formCheckPhone()" type="text" class="form-control ps-2" id="number"
                                            placeholder="Numero..." name="phone" v-model="phone">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-phone" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'numero' non può essere vuoto
                                    </div>
                                    <div class="err-regex-phone" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'numero' ha un formato non valido
                                    </div>
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Indirizzo</label>
                                        <input @blur="formCheckAddress()" type="text" class="form-control ps-2" id="address"
                                            placeholder="Indirizzo..." name="address" v-model="address">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-address" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'indirizzo' non può essere vuoto
                                    </div>
                                    <div class="">
                                        <label for="exampleFormControlInput1" class="form-label ps-2">Codice postale</label>
                                        <input @blur="formCheckPostalCode()" type="text" class="form-control ps-2"
                                            id="postal-code" placeholder="Codice postale..." name="postal_code"
                                            v-model="postalCode">
                                        <span id="payment-error" class="message-error text-danger"></span>
                                    </div>
                                    <div class="err-msg-postalCode" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'codice postale' non può essere vuoto
                                    </div>
                                    <div class="err-regex-postalCode" style="color: red; display: none; font-size: 10px;">
                                        Il campo 'codice postale' ha un formato non valido
                                    </div>
                                    <input type="hidden" name="order_id" v-model="orderID">

                                    <div class="col-12 d-flex justify-content-center py-3">
                                        <button type="submit" class="px-4" id="btn-sub-login" :disabled="!isFormValid">
                                            Paga <span class="fw-bold">{{ totalCart }}€</span>
                                        </button>
                                    </div>

                                </form>
                            </div>
                        </div>
                    </div>
                </section>
                <!-- /CART -->
            </div>
        </div>
    </div>
    <!-- /DISHES -->

    <div class="container-auto bg-wave pt-5">
        <img class="wave-bottom" src="/imgs/waves/footer-wave-desktop.svg" alt="">
    </div>
</template>

<script>
import AddFoodButton from '../elements/AddFoodButton.vue'
import DeleteButton from '../elements/DeleteButton.vue'
import DeleteEntityButton from '../elements/DeleteEntityButton.vue'
import DeleteAllFoodButton from '../elements/DeleteAllFoodButton.vue'
import RemoveFoodButton from '../elements/RemoveFoodButton.vue'
import store from '../../store'
import axios from 'axios'
import { counter } from '@fortawesome/fontawesome-svg-core'

export default {
    components: {
        AddFoodButton,
        DeleteButton,
        DeleteEntityButton,
        DeleteAllFoodButton,
        RemoveFoodButton
    },

    methods: {
        formCheckFirstName() {
            // reset messaggi di errore
            const errMsgEmpty = document.querySelector('.err-msg-firstName')
            errMsgEmpty.style.display = 'none'


            if (this.firstName.length != 0) {
                this.isAvailableForm()
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-firstName')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        formCheckLastName() {
            // reset messaggi di errore
            const errMsgEmpty = document.querySelector('.err-msg-lastName')
            errMsgEmpty.style.display = 'none'


            if (this.lastName.length != 0) {
                this.isAvailableForm()
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-lastName')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        formCheckEmail() {
            // reset messaggi di errore
            const errMsgEmail = document.querySelector('.err-regex-eMail')
            errMsgEmail.style.display = 'none'
            const errMsgEmpty = document.querySelector('.err-msg-email')
            errMsgEmpty.style.display = 'none'

            const reEmail = /[a-zA-Z0-9_\-\.]+[@][a-z]+[\.][a-z]{2,3}/

            if (this.email.length != 0) {

                if (reEmail.test(this.email)) {
                    this.isAvailableForm()
                } else {
                    const errMsgEmail = document.querySelector('.err-regex-eMail')
                    errMsgEmail.style.display = 'block'
                    this.isFormValid = false;
                }
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-email')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        formCheckPhone() {
            // reset messaggi di errore
            const errMsgPhone = document.querySelector('.err-regex-phone')
            errMsgPhone.style.display = 'none'
            const errMsgEmpty = document.querySelector('.err-msg-phone')
            errMsgEmpty.style.display = 'none'

            if (this.phone.length != 0) {

                if (this.phone.length == 10) {
                    this.isAvailableForm()
                } else {
                    const errMsgPhone = document.querySelector('.err-regex-phone')
                    errMsgPhone.style.display = 'block'
                    this.isFormValid = false;
                }
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-phone')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        formCheckAddress() {
            // reset messaggi di errore
            const errMsgEmpty = document.querySelector('.err-msg-address')
            errMsgEmpty.style.display = 'none'


            if (this.address.length != 0) {
                this.isAvailableForm()
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-address')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        formCheckPostalCode() {
            // reset messaggi di errore
            const errMsgPostalCode = document.querySelector('.err-regex-postalCode')
            errMsgPostalCode.style.display = 'none'
            const errMsgEmpty = document.querySelector('.err-msg-postalCode')
            errMsgEmpty.style.display = 'none'

            if (this.postalCode.length != 0) {

                if (this.postalCode.length == 5) {
                    this.isAvailableForm()
                } else {
                    const errMsgPostalCode = document.querySelector('.err-regex-postalCode')
                    errMsgPostalCode.style.display = 'block'
                    this.isFormValid = false;
                }
            } else {
                const errMsgEmpty = document.querySelector('.err-msg-postalCode')
                errMsgEmpty.style.display = 'block'
                this.isFormValid = false;
            }
        },
        isAvailableForm() {
            if (this.firstName.length != 0 &&
                this.lastName.length != 0 &&
                this.email.length != 0 &&
                this.phone.length != 0 &&
                this.address.length != 0 &&
                this.postalCode.length != 0) {
                this.isFormValid = true;
            } else {
                this.isFormValid = false;
            }
        },
        fetchRestaurantBySlug() {

            const slug = this.$route.params.slug

            axios.get(`http://127.0.0.1:8000/api/restaurants/${slug}`)
                .then(res => {

                    this.restaurant = res.data.results

                    this.dishes = this.restaurant.dishes

                    this.store.restaurantID = this.restaurant.id

                    this.store.restaurantName = this.restaurant.name

                }).catch((err) => {
                    this.$router.push('/404')
                })

            window.scrollTo({
                top: 0,
                behavior: 'smooth'
            })

        },
        getImageUrl(imagePath) {
            if (imagePath) {
                return `http://127.0.0.1:8000/storage/${imagePath}`
            }
        },
        addFoodToCartWithRID(dish) {

            // controllo se esite il rid salvato
            // se esiste lo prendo e lo confronto con il current rid
            // se è uguale aggiungo il paitto al carrello
            // altrimenti creo un rid e lo salvo persistentemente

            if (localStorage.getItem('RID')) {
                const savedRID = localStorage.getItem('RID')

                const savedRestaurantName = localStorage.getItem('RName')
                this.localRestaurantName = savedRestaurantName


                if (parseInt(savedRID) === parseInt(this.store.restaurantID)) {
                    this.addFoodToCart(dish)
                } else {
                    this.showBlockMsg()
                }

            } else {

                localStorage.setItem('RID', JSON.stringify(this.store.restaurantID))
                localStorage.setItem('RName', JSON.stringify(this.store.restaurantName))

                const savedRestaurantName = localStorage.getItem('RName')
                this.localRestaurantName = savedRestaurantName

                this.addFoodToCart(dish)
            }

        },
        removeRID() {
            if (this.cart.length == 0) {
                localStorage.removeItem('RID')
            }
        },
        removeRName() {
            if (this.cart.length == 0) {
                localStorage.removeItem('RName')
            }
        },
        addFoodToCart(dish) {

            //controllo se filtrando il cart mi trova l'elemento dish (se > 0)
            if (this.cart.filter(value => value.id === dish.id).length > 0) {
                let filteredCart = this.cart.filter(value => value.id === dish.id) //mi salvo l'array filtrato con il mio dish
                filteredCart[0].quantity++ //tanto c'è solo un elemento in questo array
                this.totalCart += parseFloat(dish.price)
            } else {
                dish.quantity = 1
                this.cart.push(dish)
                this.totalCart += parseFloat(dish.price)
            }
            //STEP:1 salvare dati nel local storage ad ogni modifica
            localStorage.setItem('cart', JSON.stringify(this.cart)) // salvo il carrello come stringa JSON nel local storage quando aggiungo un piatto
            localStorage.setItem('totalCart', JSON.stringify(this.totalCart)) // salvo il totale nel local storage quando aggiungo un piatto
        },
        deleteFoodQuantity(dish, index) {

            //se il carrello ha un elemento e, quell'elemento ha 1 sola quantità e, il 'conferma ordine' è nascosto
            if (this.cart.length === 1 && dish.quantity === 1 && !this.showButtonConfirm) {
                //togli prezzo ed elemento da carrello
                this.totalCart -= parseFloat(dish.price)
                this.cart.splice(index, 1)

                //riportami il 'conferma ordine'
                this.showForm = false
                this.showButtonConfirm = true
            }

            //se abbiamo elementi nel carrello, gestisci il delete e totale
            if (this.cart.includes(dish)) {
                if (dish.quantity > 1) {
                    dish.quantity -= 1
                    this.totalCart -= parseFloat(dish.price)
                } else if (dish.quantity === 1) {
                    this.totalCart -= parseFloat(dish.price)
                    this.cart.splice(index, 1)
                }
            }

            // STEP:1
            localStorage.setItem('cart', JSON.stringify(this.cart)) // salvo il carrello come stringa JSON nel local storage quando rimuovo un piatto
            localStorage.setItem('totalCart', JSON.stringify(this.totalCart)) // salvo il totale nel local storage quando rimuovo un piatto

            this.removeRName()
            this.removeRID()
        },
        deleteFoodEntity(dish, index) {
            //se abbiamo elementi nel carrello, gestisci il delete e totale
            if (this.cart.includes(dish)) {
                this.totalCart -= parseFloat(dish.price) * dish.quantity
                dish.quantity = 0
                this.cart.splice(index, 1)
            }

            if (this.cart.length === 0 && !this.showButtonConfirm) {
                this.showForm = false
                this.showButtonConfirm = true
            }

            // STEP:1
            localStorage.setItem('cart', JSON.stringify(this.cart)) // salvo il carrello come stringa JSON nel local storage quando rimuovo un piatto
            localStorage.setItem('totalCart', JSON.stringify(this.totalCart)) // salvo il totale nel local storage quando rimuovo un piatto

            this.removeRName()
            this.removeRID()
        },
        deleteAllFood() {
            this.cart = []
            this.totalCart = 0

            this.showForm = false
            this.showButtonConfirm = true

            // STEP:1
            localStorage.setItem('cart', JSON.stringify(this.cart)) // salvo il carrello come stringa JSON nel local storage quando rimuovo un piatto
            localStorage.setItem('totalCart', JSON.stringify(this.totalCart)) // salvo il totale nel local storage quando rimuovo un piatto

            this.removeRName()
            this.removeRID()
        },
        showOrderForm() {

            if (this.totalCart === null) {
                this.showForm = false
                this.showButtonConfirm = true
            } else if (this.totalCart !== 0) {
                this.showForm = true
                this.showButtonConfirm = false
            }
        },
        showBlockMsg() {
            const blockMsg = document.querySelector('.block-msg')
            blockMsg.style.display = 'flex'
            setTimeout(() => {
                const blockMsg = document.querySelector('.block-msg')
                blockMsg.style.display = 'none'
            }, 3000)
        },
        // STEP:2 creiamo una funzionare per assegnare i dati del carrello in local storage ai dati della pagina ricaricata
        getCartFromLocalStorage() {
            // recupero il carrello JSON dal local storage e 
            // SE esiste lo converto in array 
            // ALTRIMENTI metto l'array vuoto
            const savedCartData = localStorage.getItem('cart')
            let localCart = []

            if (savedCartData) {
                localCart = JSON.parse(savedCartData)
            } else {
                localCart = []
            }

            // recupero il total local storage e 
            // SE esiste diventa float 
            // ALTRIMENTI metto il totale a 0
            const savedTotalCart = localStorage.getItem('totalCart')
            let localTotalCart = 0

            if (savedTotalCart !== 0) {
                localTotalCart = JSON.parse(savedTotalCart)
            }

            this.cart = localCart
            this.totalCart = localTotalCart
        },
        goToPay() {

            localStorage.removeItem('RName')
            localStorage.removeItem('RID')

            // chiamata axios
            axios.post('http://localhost:8000/api/order/pay', {
                cart: this.cart,
                form: {
                    firstName: this.firstName,
                    lastName: this.lastName,
                    email: this.email,
                    phone: this.phone,
                    address: this.address,
                    postalCode: this.postalCode
                }

            })
                .then((res) => {

                    this.orderID = res.data.results.order_id; //salvo l'id dell'ordine appena creato
                    //Messaggio da inviare al ristoratore
                    let messageRestaurant = {
                        message: "Hai ricevuto un nuovo ordine da consegnare a: " + this.firstName + " " + this.lastName + " in " + this.address + ", " + this.postalCode,
                        cart: this.cart
                    };
                    //Messaggio da inviare al cliente
                    let messageClient = {
                        message: "Hai ordinato da: " + this.restaurant.name,
                        cart: this.cart
                    };
                    //Invio la mail al ristoratore
                    axios.post('http://127.0.0.1:8000/api/leads', {
                        name: this.firstName + " " + this.lastName,
                        email: this.restaurant.user.email,
                        message: JSON.stringify(messageRestaurant)
                    })
                        .then((res) => {

                        });
                    //Invio la mail al cliente
                    axios.post('http://127.0.0.1:8000/api/leads', {
                        name: this.firstName + " " + this.lastName,
                        email: this.email,
                        message: JSON.stringify(messageClient)
                    })
                        .then((res) => {

                        });
                })
                .finally(() => {
                    const paymentFormEl = document.getElementById('payment-form'); //prendo il form di pagamento
                    paymentFormEl.submit(); //faccio il submit del form di pagamento 
                })

            this.clearLocalStorage()


        },
        clearLocalStorage() {
            localStorage.removeItem('cart') // elimino dal local storage i dati salvati
            localStorage.removeItem('totalCart') // elimino dal local storage i dati salvati
            // this.totalCart = 0
        }
    },

    mounted() {
        this.fetchRestaurantBySlug()
        this.getCartFromLocalStorage()


        if (localStorage.getItem('RID')) {
            const savedRestaurantName = localStorage.getItem('RName')
            this.localRestaurantName = savedRestaurantName
        }
    },

    data() {
        return {
            store,
            showForm: false,
            showButtonConfirm: true,
            isFormValid: false,
            totalCart: 0,
            restaurant: [],
            dishes: [],
            cart: [], // NON TOCCARE MAREMMAHANE
            localRestaurantName: '',
            orderID: '', //id dell' ordine
            firstName: '', //nome del cliente
            lastName: '', //cognome del cliente
            email: '', //email del cliente
            phone: '', //telefono del cliente
            address: '', //indirizzo del cliente
            postalCode: '', //codice postal del cliente
        }
    }
}
</script>

<style lang="scss" scoped>
@use '../src/styles/main.scss' as *;
@use '../src/styles/partials/resets.scss' as *;
@use '../../styles/partials/colors.scss' as *;

.logo-box {
    padding: 0 2rem !important;
    margin-top: 2rem !important;
    background-color: rgba($color: #000000, $alpha: 0);
    position: relative;
    z-index: 0;

    .logo-thumb {
        width: 180px;
        background-color: rgba($color: white, $alpha: 0.3);
        padding: 0 15px;
        border-radius: 30px;
        z-index: 10;
        position: relative;
    }
}

.image-container {
    width: 100%;
    height: 420px;
    position: relative;
    top: -150px;
    left: 0;
    z-index: -1;
    // filter: blur(5px);

    .restaurant-img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        object-position: center;
    }
}

.restaurant-section {
    z-index: 1;
    background: linear-gradient(to top,
            white 65%,
            white 65%,
            rgba(0, 0, 0, 0) 100%);

    .restaurant-box {
        padding: 0rem 2rem 6rem !important;
        margin-top: -280px;
        position: relative;


        .box-bg {
            background-color: white;
        }

        .shadow {
            box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
            border-radius: 9px;
        }

        .restaurant-name {
            font-size: 3rem;
            font-weight: bold;
        }

        .restaurant-address {
            font-size: 1.5rem;
        }
    }
}

.card-box {
    padding: 0rem 2rem 6rem !important;

    .my-card {
        padding: 1.5rem;
        border-radius: 1rem;
        margin-right: auto;
        box-shadow: rgba(0, 0, 0, 0.16) 0px 10px 36px 0px, rgba(0, 0, 0, 0.06) 0px 0px 0px 1px;
    }

    .card-buttons {
        width: 40px;
    }

    .food-title {
        font-size: 0.875rem;
        font-weight: bold;
    }

    .food-desc {
        font-size: 0.75rem;
    }

    .food-img {
        width: 80px;
        height: 80px;
        box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;
        border-radius: 50%;

        .food-thumb {
            aspect-ratio: 1;
            object-fit: cover;
            object-position: center;
            border-radius: 50%;
        }
    }
}

.card {
    margin: 0 auto;
    border-radius: 1rem;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 10px 36px 0px, rgba(0, 0, 0, 0.06) 0px 0px 0px 1px;

    .block-msg {
        display: none;
        color: red;
        transition: all .4s;
    }

    .delete-all-food-btn {
        flex-shrink: 0;
    }
}

.my-text {
    font-size: 0.875rem;
    color: rgba(0, 0, 0, 0.495)
}

.box-cart {
    padding: 0.5em;
}

.form * {
    padding: 0.5em 0;
    margin: 0;
}

button {
    padding: 10px 20px;
    max-width: 90%;
    border-radius: 50px;
    border: 0;
    background-color: $orange-1;
    box-shadow: rgb(0 0 0 / 5%) 0 0 8px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    font-size: 15px;
    transition: all .5s ease;
}

button:hover {
    letter-spacing: 3px;
    background-color: hsl(261deg 80% 48%);
    color: hsl(0, 0%, 100%);
    box-shadow: rgb(93 24 220) 0px 7px 29px 0px;
}

button:active {
    letter-spacing: 3px;
    background-color: hsl(261deg 80% 48%);
    color: hsl(0, 0%, 100%);
    box-shadow: rgb(93 24 220) 0px 0px 0px 0px;
    transform: translateY(10px);
    transition: 100ms;
}

.wave-bottom {
    min-width: 100%;
    position: relative;
    bottom: 0;
}

@media (max-width: 388px) {
    .wave-bottom {
        bottom: -1px;
    }
}

@media (max-width: 418px) {
    .wave-bottom {
        margin-bottom: -2px;
    }
}
</style>