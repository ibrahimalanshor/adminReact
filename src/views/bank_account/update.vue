<template>
  <div>
    <CCard class="mb-4">
      <CCardHeader class="bg-white">Update Bank Account</CCardHeader>
      <CCardBody>
        <router-link :to="{ name: 'Bank Account' }">
          <CButton color="primary">
            <CIcon class="text-white" name="cil-arrow-left" /> Back
          </CButton>
        </router-link>

        <div class="mt-3">
          <CForm @submit.prevent="update()">
            <div class="mb-3">
              <CFormLabel for="username">username</CFormLabel>
              <CFormInput type="text" id="username" placeholder="username" v-model="bank.username"/>
              <div v-if="validation.username" class="text-danger">{{ validation.username.message }}</div>
            </div>
            <div class="mb-3">
              <CFormLabel for="password">password</CFormLabel>
              <CFormInput type="password" id="password" placeholder="Password" v-model="bank.password"/>
              <div v-if="validation.name" class="text-danger">{{ validation.password.message }}</div>
            </div>
            <div class="mb-3">
              <CButton color="primary" class="rounded">Save</CButton>
            </div>
          </CForm>
        </div>
      </CCardBody>
    </CCard>
  </div>
</template>
<script>
import { reactive, ref,onMounted } from 'vue'
import routers from '../../router'
import {useRoute} from 'vue-router'
import axios from 'axios'
export default {
  name: 'Update Bank',
  setup() {
    // data binding
    let bank = reactive({
      username: '',
      password:'',
    });
    const validation = ref([]);
    const router = routers;
    const route = useRoute();

    onMounted(()=>{
      // console.log(route.params.id);
      axios.get(`${process.env.VUE_APP_URL_API}/bank/${route.params.id}`,{
         headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      }).then((result)=>{
        bank.username = result.data.username;
      }).catch((err)=>{
        console.log(err.response.data)
      })
    });

    function update() {
      let banks = (bank.password!='') ? {username:bank.username,password:bank.password} : {username:bank.username}
      // console.log(banks);
      axios.patch(`${process.env.VUE_APP_URL_API}/bank/${route.params.id}`,banks,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then(()=> {
        router.push({
          name:'Bank Account'
        })
      }).catch((err)=>{
        validation.value = err.response.data.errors
      })
    }
    return {
      bank,
      validation,
      router,
      update
    }
  }
}
</script>
