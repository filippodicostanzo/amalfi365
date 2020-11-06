<template>
    <div class="row pt-5">
        <div class="offset-3"></div>
        <div class="col-md-6 col-sm-12">
            <div class="card">
                <form @submit.prevent="submit">
                    <div class="form-group" :class="{ 'form-group--error': $v.form.firstname.$error }">
                        <label class="form__label">Nome</label>
                        <input class="form__input" v-model.trim="$v.form.firstname.$model" name="firstname"/>
                    </div>
                    <div class="error" v-if="!$v.form.firstname.required">
                        Il Nome è obbligatorio.
                    </div>
                    <div class="error" v-if="!$v.form.firstname.minLength">
                        Il nome deve contenere almeno
                        {{ $v.form.firstname.$params.minLength.min }} caratteri.
                    </div>

                    <div class="form-group" :class="{ 'form-group--error': $v.form.lastname.$error }">
                        <label class="form__label">Cognome</label>
                        <input class="form__input" v-model.trim="$v.form.lastname.$model" name="lastname"/>
                    </div>
                    <div class="error" v-if="!$v.form.lastname.required">
                        Il Cognome è obbligatorio.
                    </div>
                    <div class="error" v-if="!$v.form.lastname.minLength">
                        Il Cognome deve contenere almeno
                        {{ $v.form.lastname.$params.minLength.min }} caratteri.
                    </div>

                    <div
                            class="form-group"
                            :class="{ 'form-group--error': $v.form.email.$error }"
                    >
                        <label class="form__label">Email</label>
                        <input class="form__input" v-model.trim="$v.form.email.$model" name="email"/>
                    </div>
                    <div class="error" v-if="!$v.form.email.required">
                        L'Email è obbligatoria.
                    </div>
                    <div class="error" v-if="!$v.form.email.minLength">
                        L'Email deve contenere almeno
                        {{ $v.form.email.$params.minLength.min }} caratteri.
                    </div>
                    <div class="error" v-if="!$v.form.email.email">
                        L'Email non è corretta.
                    </div>

                    <div class="form-group" :class="{ 'form-group--error': $v.form.repeatemail.$error }">
                        <label class="form__label">Email</label>
                        <input class="form__input" v-model.trim="$v.form.repeatemail.$model"/>
                    </div>
                    <div class="error" v-if="!$v.form.repeatemail.sameAsEmail">
                        L'Email deve essere identica.
                    </div>

                    <div class="form-group" :class="{ 'form-group--error': $v.form.age.$error }">
                        <label class="form__label">Età</label>
                        <input class="form__input" v-model.trim="$v.form.age.$model" name="age"/>
                    </div>
                    <div class="error" v-if="!$v.form.age.required">
                        L'Età è richiesta.
                    </div>
                    <div class="error" v-if="!$v.form.age.between">
                        L'Età deve essere compresa tra
                        {{ $v.form.age.$params.between.min }} e
                        {{ $v.form.age.$params.between.max }}
                    </div>

                    <div class="form-group row" :class="{ 'form-group--error': $v.item.language.$error }">
                        <div class="col-12">
                            <label class="form__label">Competenze</label>
                        </div>
                        <div v-for="language in languages" :key="language.id" class="col-4">
                            <input
                                    type="checkbox"
                                    v-model.trim="item.language"
                                    :id="'language_' + language.id"
                                    :value="language.name"
                                    name="language[]"
                            />
                            &nbsp;
                            <label class="form__label--inline">{{ language.name }}</label>
                        </div>
                    </div>
                    <div class="error" v-if="!$v.item.language.required">
                        Seleziona almeno un campo
                    </div>

                    <button class="btn btn-primary" type="submit" :disabled="submitStatus === 'PENDING'">
                        Invia!
                    </button>
                    <p class="typo__p" v-if="submitStatus === 'OK'">
                        Grazie per la tua iscrizione
                    </p>
                    <p class="typo__p" v-if="submitStatus === 'ERROR'">
                        Si prega di compilare tutti i campi
                    </p>
                    <p class="typo__p" v-if="submitStatus === 'PENDING'">Invio in corso...</p>
                    <p class="typo__p" v-if="submitStatus === 'SERVER_ERROR'">
                        Errore nell'invio dei dati al Server...
                    </p>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
    import {between, email, minLength, required, sameAs,} from "vuelidate/lib/validators";
    import emailjs from 'emailjs-com';



    export default {
        name: "Form",

        data() {
            return {

                form: {
                    firstname: "",
                    lastname: "",
                    email: "",
                    repeatemail: "",
                    age: "",
                },
                languages: [
                    {
                        id: 1,
                        name: "Nessuna",
                    },
                    {
                        id: 2,
                        name: "HTML",
                    },
                    {
                        id: 3,
                        name: "JS",
                    },
                    {
                        id: 4,
                        name: "CSS",
                    },
                    {
                        id: 5,
                        name: "PHP",
                    },
                    {
                        id: 6,
                        name: "Java",
                    },
                    {
                        id: 7,
                        name: "C++",
                    },
                    {
                        id: 8,
                        name: "Python",
                    },
                    {
                        id: 9,
                        name: "Go",
                    },
                    {
                        id: 10,
                        name: "Dart",
                    },
                ],
                item: {
                    language: ["Nessuna"],
                },
                submitStatus: null,
            };
        },
        validations: {
            form: {
                firstname: {
                    required,
                    minLength: minLength(4),
                },
                lastname: {
                    required,
                    minLength: minLength(4),
                },
                email: {
                    required,
                    email,
                    minLength: minLength(4),
                },
                repeatemail: {
                    sameAsEmail: sameAs("email"),
                },
                age: {
                    required,
                    between: between(18, 35),
                },
            },
            item: {
                language: {
                    required,
                },
            },
        },
        methods: {
            submit(e) {


                this.$v.$touch();
                if (this.$v.$invalid) {
                    this.submitStatus = "ERROR";
                } else {
                    // do your submit logic here
                    this.submitStatus = "PENDING";
                    console.log(process.env.VUE_APP_SENDMAIL_JS_PROJECT);
                    emailjs.sendForm(process.env.VUE_APP_SENDMAIL_JS_PROJECT, process.env.VUE_APP_SENDMAIL_JS_TEMPLATE, e.target, process.env.VUE_APP_SENDMAIL_JS_ID)
                        .then((result) => {
                            if (result.status === 200) {
                                this.submitStatus = "OK";
                            }
                        }, (error) => {
                            this.submitStatus="SERVERERROR";
                            console.log('FAILED...', error);
                        });
                }

            }
        },
    };
</script>

<style scoped>
    .card {
        padding: 30px;
    }
</style>
