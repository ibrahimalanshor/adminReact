<template>
<div>
<CCard class="mb-4">
          <CCardHeader class="bg-white">Users List</CCardHeader>
          <CCardBody>
            <router-link  :to="{name:'Create Users'}">
              <CButton class="me-2" color="primary">
              <CIcon class="text-white" name="cil-plus"/> Add User
              </CButton>
            </router-link>
            <router-link  :to="{name:'Import Users'}">
              <CButton color="success">
              <CIcon class="text-white" name="cil-plus"/> Import User
              </CButton>
            </router-link>
              <CTable align="middle" class="mt-3 mb-0 mt-2 border border-1" hover responsive>
              <CTableHead color="light">
                <CTableRow>
                  <CTableHeaderCell class="text-center">
                    <CIcon name="cil-people" />
                  </CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Name</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Username</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Email</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Role</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Rekening</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">IP Address</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Action</CTableHeaderCell>
                </CTableRow>
              </CTableHead>
              <CTableBody>
                <CTableRow v-for="(item,index) in users" :key="index">
                  <CTableDataCell class="text-center">
                    <CAvatar size="md" :src="item.avatar" />
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.name }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.username }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{item.email}}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                     <div>{{item.role}}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <ul class="list-unstyled" v-if="item.banks?.length">
                      <li v-for="(bank, key) in item.banks" :key="key"><CBadge color="primary">{{ bank.username }}</CBadge></li>
                    </ul>
                    <span v-else>
                      -
                    </span>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <ul class="list-unstyled" v-if="item.ip?.length">
                      <li v-for="(ip, key) in item.ip" :key="key"><CBadge color="primary">{{ ip }}</CBadge></li>
                    </ul>
                    <span v-else>
                      -
                    </span>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>
                    <router-link :to="{name:'Update Users',params:{id:item._id}}">
                    <CBadge class="me-2 rounded" color="warning">
                      <CIcon class="text-white" name="cil-pencil"/>
                    </CBadge>
                    </router-link>
                    <CBadge color="danger" class="rounded" @click="destroy(item._id,index)">
                      <CIcon class="text-white" name="cil-trash"/>
                    </CBadge>
                    </div>
                  </CTableDataCell>
                </CTableRow>
                <CTableRow>
                  <CTableDataCell colspan="6" v-show="users.length <= 0">
                    No records found
                  </CTableDataCell>
                </CTableRow>
              </CTableBody>
            </CTable>
          </CCardBody>
</CCard>

</div>
</template>
<script>
import axios from 'axios'
import {onMounted,ref} from 'vue'
export default {
  name:'UserList',
  setup() {
    //reactive state
    let users = ref([]);

    onMounted(()=> {
      // get data
      axios.get(`${process.env.VUE_APP_URL_API}/users`,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then((result) => {
        users.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });
    });

    function destroy(id,index) {
      axios.delete(`${process.env.VUE_APP_URL_API}/users/${id}`,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then(()=> {
        users.value.splice(index,1);
        // router.push({
        //   name:'Users'
        // })
      }).catch((err)=>{
        console.log(err.response);
      })
    }

    return {
      users,
      destroy
    }
  }
}
</script>
