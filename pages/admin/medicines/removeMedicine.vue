<template>
    <div>
      <admin-nav-bar/>
      <br><br>
      <b-container>
        <h2>Remover um Determindo Medicamento</h2>
        <br>
        <form @submit.prevent="consult" :disabled="!isFormValid">
            <b-form-group
                id="code"
                description="O código é necessário"
                label-for="code"
                :invalid-feedback="invalidCodeFeedback"
                :state="isCodeValid"
            >
                <b-input v-model.trim="code" :state="isCodeValid" required placeholder="Insira o código do medicamento" />
            </b-form-group>
            <p v-show="errorMsg" class="text-danger">
                {{ errorMsg }}
            </p>
            <button class="btn btn-primary btn-lg btn-block" @click.prevent="remove" :disabled="!isFormValid">Remover</button>
            <br>
            <p align="center"><a class="primary" @click="$router.go(-1)">Voltar a Trás</a></p>
        </form>
      </b-container>
    </div>
 </template>

<script>
  export default {
    data(){
        return {
            code: null,
            errorMsg: null
        }
    },

    computed: {
        invalidCodeFeedback () {
            if (!this.code) {
                return null
            }
            let codeLen = this.code.length
            if (codeLen < 1 || codeLen > 30) {
                return 'The code must be between [1, 30] number characters.'
            }
            return ''
        },

        isCodeValid () {
            if (!this.invalidCodeFeedback === null) {
                return null
            }
            return this.invalidCodeFeedback === ''
        },
        isFormValid () {
            if (! this.isCodeValid) {
                return false
            }
            return true
        }
    },
    methods: {
        remove(){
            this.$axios.$delete('/api/medicine/' + this.code)
                .then(() => {
                    this.$router.push('/profhealthcare')
                })
                .catch((error) => {
                    this.errorMsg = error.response.data
                })
        },
        signOut(){
            this.$auth.logout()
            this.$router.push('/')
        }
    }
  }
</script>