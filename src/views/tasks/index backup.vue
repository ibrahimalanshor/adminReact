<template>
  <div>
    <CCard class="mb-4">
        <CCardHeader>Task List {{ taskStat }}</CCardHeader>
      <CCardBody>
      <div class="flex-column">
      <!-- <CButton color="primary" class="d-inline-block me-2" @click="() => { modalAdd=true}">
          <CIcon class="text-white" name="cil-plus"/> Add Task
      </CButton> -->
      <CButton color="danger" class="text-white d-inline-block me-2" @click="clearAssign()">
          <CIcon class="text-white" name="cil-trash"/> Unnasigned
      </CButton>
      <CButton color="dark" class="d-inline-block me-2" @click="() => { modalAssign=true}">
          <CIcon class="text-white" name="cil-touch-app"/> Assign Task
      </CButton>
      <CButton color="secondary" class="d-inline-block text-white" @click="() => { visibleLiveDemo = true }"><CIcon class="text-white" name="cil-clipboard"/> Tasks History</CButton>


    <!-- <CFormSelect v-model="statFilter" @change="filtersel" class="float-end status">
      <option value="all">Select</option>
      <option value="unprocess">Unprocess</option>
      <option value="assigned">Assigned</option>
      <option value="processed">Processed</option>
      <option value="done">Done</option>
    </CFormSelect> -->

      <CFormSelect
      @change="filterSelect"
        class="float-end status"
        aria-label="Default select example"
        :options="[
          { label: 'Unprocess', value: 'unprocess' },
          { label: 'Assign', value: 'assigned' },
          { label: 'Processed', value: 'processed' },
          { label: 'Done', value: 'done' },
      ]">
    </CFormSelect>
    <div class="float-end me-2">
          <CFormInput type="text" id="search" @keyup="searchTitle" placeholder="search"/>
    </div>
    <div class="float-none"></div>
      </div>
      <!-- <div class="table-wrapper mt-2"> -->
              <!-- Fixed header table-->
                <div class="table-responsive mt-3">
                    <table class="table table-fixed text-center">
                        <thead>
                            <tr>
                                <th scope="col" class="col-1"><CIcon name="cil-people" /></th>
                                <th scope="col" class="col-2">Assigned</th>
                                <th scope="col" class="col-5">Task Title</th>
                                <th scope="col" class="col-2">Status</th>
                                <th scope="col" class="col-2">Detail Task</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(item,index) in tasks" :key="index" :class="'table-'+cek(item.taskStatus)">
                                <td scope="row" class="col-1">
                                  <div v-if="item.taskStatus!='processed'">
                                  <CFormCheck :disabled="cekCheck(item.taskAssigne)" id="taskchekbox" :value="item._id" @change="additem"/>
                                  </div>
                                  <div v-if="item.taskStatus=='processed'">
                                    <CFormCheck disabled/>
                                  </div>
                                </td>
                                <td class="col-2">{{ item.taskAssigne }}</td>
                                <td class="col-5">{{ item.taskTittle }}</td>
                                <td class="col-2">{{ item.taskStatus }}</td>
                                <td class="col-2">
                                  <div v-if="item.taskStatus!='processed'">
                                    <CBadge class="me-2 rounded-full" color="dark" @click="process(item.taskData.account_number,item.taskData.anRekening,item.taskData.amount,item.taskData.mutation_id,item.taskData.bank_type,item._id,item.taskAssigne,item.taskTittle,item.taskRefNumber,item.taskExpiredTime,item.taskCreatedBy,item.taskStatus)">
                                      <CIcon class="text-white" name="cil-aperture"/>
                                    </CBadge>
                                    </div>
                                    <div v-if="item.taskStatus=='processed'">
                                      <CIcon class="text-dark" name="cil-aperture"/>
                                  </div>
                              </td>
                            </tr>
                        </tbody>
                    </table>
                </div><!-- End -->
      <!-- </div> -->

      </CCardBody>
    </CCard>

    <!-- Modal Add Tasks -->
  <CModal  :visible="modalAdd" @close="() => { modalAdd = false }">
    <CModalHeader>
      <CModalTitle>Add Task</CModalTitle>
    </CModalHeader>
    <CModalBody>
     <CForm @submit.prevent="store()">
            <div class="mb-3">
              <CFormLabel for="taskTittle">Task Title</CFormLabel>
              <CFormInput type="text" v-model="tsk.taskTittle" id="taskTittle" placeholder="Task title"/>
              <!-- <div class="text-danger">Task Title Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskRefNumber">Ref Number</CFormLabel>
              <CFormInput type="text" v-model="tsk.taskRefNumber" id="taskRefNumber" placeholder="Task Ref Number"/>
              <!-- <div class="text-danger">Task Ref Number Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskSlaTime">Sla Time</CFormLabel>
              <CFormInput type="date" id="taskSlaTime" v-model="tsk.taskSlaTime" placeholder="Task Sla Time"/>
              <!-- <div class="text-danger">Task Sla Time Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskExpiredTime">Expired Time</CFormLabel>
              <CFormInput type="date" id="taskExpiredTime" v-model="tsk.taskExpiredTime" placeholder="Task Expired Time"/>
              <!-- <div class="text-danger">Task Expired Time Required</div> -->
            </div>
            <div class="mb-3">
              <CButton color="primary" class="rounded">Save</CButton>
            </div>
          </CForm>

    </CModalBody>
  </CModal>
    <!-- Toast Confirm -->

    <!-- Modal Assigment -->
    <CModal  :visible="modalAssign" @close="() => { modalAssign = false }">
    <CModalHeader>
      <CModalTitle>Modal Assignment</CModalTitle>
    </CModalHeader>
    <CModalBody>
      <CForm @submit.prevent="updateWorker()">
      <div class="mb-3">
      <CFormSelect v-model="work" aria-label="Default select example">
        <option>Unassigned</option>
        <option v-for="(user,index) in users" :key="index" :value="user.username">{{ user.username }}</option>
      </CFormSelect>
      </div>
      <CButton color="primary">Save changes</CButton>
    </CForm>
    </CModalBody>
  </CModal>
    <!-- Modal Assigment -->

    <!-- Modal History -->
  <CModal  :visible="visibleLiveDemo" @close="() => { visibleLiveDemo = false }">
    <CModalHeader>
      <CModalTitle>Modal title</CModalTitle>
    </CModalHeader>
    <CModalBody>Woohoo, you're reading this text in a modal!</CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="() => { visibleLiveDemo = false }">
        Close
      </CButton>
      <CButton color="primary">Save changes</CButton>
    </CModalFooter>
  </CModal>
  <!-- Modal History -->

    <!-- Modal Detail Task -->
  <CModal :visible="modalDetail" @close="() => { modalDetail = false }">
    <CModalHeader :close-button="false">
      <CModalTitle>Task Detail</CModalTitle>
    </CModalHeader>
    <CModalBody>
    <div class="d-flex justify-content-between">
      <div class="">
      <p class="mb-0 fw-bold">Task Title :</p>
      <p>{{ taskTittle }}</p>
      <p class="mb-0 fw-bold">Task Assigne :</p>
      <p>{{ taskAssigne }}</p>
      <p class="mb-0 fw-bold">Task Ref Number :</p>
      <p>{{ taskRefNumber }}</p>
      <p class="mb-0 fw-bold">Task Sla Time :</p>
      <p>{{ taskSlaTime }}</p>
      <p class="mb-0 fw-bold">Task Expired Time :</p>
      <p>{{ taskExpiredTime }}</p>
      <p class="mb-0 fw-bold">Task Status :</p>
      <p>{{ taskStatus }}</p>
      </div>
      <div>
      <p class="mb-0 fw-bold">Account Number :</p>
      <p>{{ account_number }}</p>
      <p class="mb-0 fw-bold">Card Holder :</p>
      <p>{{ anRekening }}</p>
      <p class="mb-0 fw-bold">Bank Type :</p>
      <p>{{ bank_type }}</p>
      <p class="mb-0 fw-bold">Task Created By :</p>
      <p>{{ taskCreatedBy }}</p>
      <p class="mb-0 fw-bold">Amount :</p>
      <p>{{ amount }}</p>
      <p class="mb-0 fw-bold">Mutation ID :</p>
      <p>{{ mutation_id }}</p>
      </div>
    </div>
    </CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="() => { modalDetail = false }">
        Cancel
      </CButton>
      <CButton color="primary" @click="proc()">Process</CButton>
    </CModalFooter>
  </CModal>
  <!-- Modal Detail Task -->
  </div>
  <div>
    <CCard class="mb-4">
        <CCardHeader>Task List {{ taskStat }}</CCardHeader>
      <CCardBody>
      <div class="flex-column">
      <!-- <CButton color="primary" class="d-inline-block me-2" @click="() => { modalAdd=true}">
          <CIcon class="text-white" name="cil-plus"/> Add Task
      </CButton> -->
      <CButton color="danger" class="text-white d-inline-block me-2" @click="clearAssign()">
          <CIcon class="text-white" name="cil-trash"/> Unnasigned
      </CButton>
      <CButton color="dark" class="d-inline-block me-2" @click="() => { modalAssign=true}">
          <CIcon class="text-white" name="cil-touch-app"/> Assign Task
      </CButton>
      <CButton color="secondary" class="d-inline-block text-white" @click="() => { visibleLiveDemo = true }"><CIcon class="text-white" name="cil-clipboard"/> Tasks History</CButton>


    <!-- <CFormSelect v-model="statFilter" @change="filtersel" class="float-end status">
      <option value="all">Select</option>
      <option value="unprocess">Unprocess</option>
      <option value="assigned">Assigned</option>
      <option value="processed">Processed</option>
      <option value="done">Done</option>
    </CFormSelect> -->

      <CFormSelect
      @change="filterSelect"
        class="float-end status"
        aria-label="Default select example"
        :options="[
          { label: 'Unprocess', value: 'unprocess' },
          { label: 'Assign', value: 'assigned' },
          { label: 'Processed', value: 'processed' },
          { label: 'Done', value: 'done' },
      ]">
    </CFormSelect>
    <div class="float-end me-2">
          <CFormInput type="text" id="search" @keyup="searchTitle" placeholder="search"/>
    </div>
    <div class="float-none"></div>
      </div>
      <!-- <div class="table-wrapper mt-2"> -->
            <CTable align="middle" class="mt-3 mb-0 mt-2 border border-1" hover responsive>
              <CTableHead color="light">
                <CTableRow>
                  <CTableHeaderCell class="text-center">
                    <CIcon name="cil-people" />
                    <!-- <CFormCheck/> -->
                  </CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Assigned</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Title</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Ref Number</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Sla Time</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Expired Time</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Status</CTableHeaderCell>
                  <CTableHeaderCell class="text-center">Detail Task</CTableHeaderCell>
                </CTableRow>
              </CTableHead>
              <CTableBody>
                <CTableRow v-for="(item,index) in tasks" :key="index" :color="cek(item.taskStatus)">
                  <CTableDataCell class="text-center">
                    <CForm id="checkStatusAssigne">
                    <div v-if="item.taskStatus!='processed'">
                      <CFormCheck :disabled="cekCheck(item.taskAssigne)" id="taskchekbox" :value="item._id" @change="additem"/>
                    </div>
                    <div v-if="item.taskStatus=='processed'">
                      <CFormCheck disabled/>
                    </div>
                    </CForm>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.taskAssigne }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-left">
                    <div>{{ item.taskTittle }}</div>
                    <!-- <div>{{ item.taskStatus != 'processed' ? item.taskTittle.split(',')[0] + " " + item.taskTittle.split(',')[1] + " ********" : item.taskTittle }}</div> -->
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.taskRefNumber }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.taskSlaTime }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                     <div>{{ item.taskExpiredTime }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div>{{ item.taskStatus }}</div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <div v-if="item.taskStatus!='processed'">
                    <CBadge class="me-2 rounded-full" color="dark" @click="process(item.taskData.account_number,item.taskData.anRekening,item.taskData.amount,item.taskData.mutation_id,item.taskData.bank_type,item._id,item.taskAssigne,item.taskTittle,item.taskRefNumber,item.taskExpiredTime,item.taskCreatedBy,item.taskStatus)">
                      <CIcon class="text-white" name="cil-aperture"/>
                    </CBadge>
                    <!-- <CBadge class="me-2 rounded" color="warning">
                      <CIcon class="text-white" name="cil-pencil"/>
                    </CBadge> -->
                    <!-- <CBadge color="danger" class="rounded">
                      <CIcon class="text-white" name="cil-trash"/>
                    </CBadge> -->
                    </div>
                    <div v-if="item.taskStatus=='processed'">
                      <CIcon class="text-dark" name="cil-aperture"/>
                    </div>

                  </CTableDataCell>
                </CTableRow>
              </CTableBody>
            </CTable>
      <!-- </div> -->

      </CCardBody>
    </CCard>

    <!-- Modal Add Tasks -->
  <CModal  :visible="modalAdd" @close="() => { modalAdd = false }">
    <CModalHeader>
      <CModalTitle>Add Task</CModalTitle>
    </CModalHeader>
    <CModalBody>
     <CForm @submit.prevent="store()">
            <div class="mb-3">
              <CFormLabel for="taskTittle">Task Title</CFormLabel>
              <CFormInput type="text" v-model="tsk.taskTittle" id="taskTittle" placeholder="Task title"/>
              <!-- <div class="text-danger">Task Title Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskRefNumber">Ref Number</CFormLabel>
              <CFormInput type="text" v-model="tsk.taskRefNumber" id="taskRefNumber" placeholder="Task Ref Number"/>
              <!-- <div class="text-danger">Task Ref Number Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskSlaTime">Sla Time</CFormLabel>
              <CFormInput type="date" id="taskSlaTime" v-model="tsk.taskSlaTime" placeholder="Task Sla Time"/>
              <!-- <div class="text-danger">Task Sla Time Required</div> -->
            </div>
            <div class="mb-3">
              <CFormLabel for="taskExpiredTime">Expired Time</CFormLabel>
              <CFormInput type="date" id="taskExpiredTime" v-model="tsk.taskExpiredTime" placeholder="Task Expired Time"/>
              <!-- <div class="text-danger">Task Expired Time Required</div> -->
            </div>
            <div class="mb-3">
              <CButton color="primary" class="rounded">Save</CButton>
            </div>
          </CForm>

    </CModalBody>
  </CModal>
    <!-- Toast Confirm -->

    <!-- Modal Assigment -->
    <CModal  :visible="modalAssign" @close="() => { modalAssign = false }">
    <CModalHeader>
      <CModalTitle>Modal Assignment</CModalTitle>
    </CModalHeader>
    <CModalBody>
      <CForm @submit.prevent="updateWorker()">
      <div class="mb-3">
      <CFormSelect v-model="work" aria-label="Default select example">
        <option>Unassigned</option>
        <option v-for="(user,index) in users" :key="index" :value="user.username">{{ user.username }}</option>
      </CFormSelect>
      </div>
      <CButton color="primary">Save changes</CButton>
    </CForm>
    </CModalBody>
  </CModal>
    <!-- Modal Assigment -->

    <!-- Modal History -->
  <CModal  :visible="visibleLiveDemo" @close="() => { visibleLiveDemo = false }">
    <CModalHeader>
      <CModalTitle>Modal title</CModalTitle>
    </CModalHeader>
    <CModalBody>Woohoo, you're reading this text in a modal!</CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="() => { visibleLiveDemo = false }">
        Close
      </CButton>
      <CButton color="primary">Save changes</CButton>
    </CModalFooter>
  </CModal>
  <!-- Modal History -->

    <!-- Modal Detail Task -->
  <CModal :visible="modalDetail" @close="() => { modalDetail = false }">
    <CModalHeader :close-button="false">
      <CModalTitle>Task Detail</CModalTitle>
    </CModalHeader>
    <CModalBody>
    <div class="d-flex justify-content-between">
      <div class="">
      <p class="mb-0 fw-bold">Task Title :</p>
      <p>{{ taskTittle }}</p>
      <p class="mb-0 fw-bold">Task Assigne :</p>
      <p>{{ taskAssigne }}</p>
      <p class="mb-0 fw-bold">Task Ref Number :</p>
      <p>{{ taskRefNumber }}</p>
      <p class="mb-0 fw-bold">Task Sla Time :</p>
      <p>{{ taskSlaTime }}</p>
      <p class="mb-0 fw-bold">Task Expired Time :</p>
      <p>{{ taskExpiredTime }}</p>
      <p class="mb-0 fw-bold">Task Status :</p>
      <p>{{ taskStatus }}</p>
      </div>
      <div>
      <p class="mb-0 fw-bold">Account Number :</p>
      <p>{{ account_number }}</p>
      <p class="mb-0 fw-bold">Card Holder :</p>
      <p>{{ anRekening }}</p>
      <p class="mb-0 fw-bold">Bank Type :</p>
      <p>{{ bank_type }}</p>
      <p class="mb-0 fw-bold">Task Created By :</p>
      <p>{{ taskCreatedBy }}</p>
      <p class="mb-0 fw-bold">Amount :</p>
      <p>{{ amount }}</p>
      <p class="mb-0 fw-bold">Mutation ID :</p>
      <p>{{ mutation_id }}</p>
      </div>
    </div>
    </CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="() => { modalDetail = false }">
        Cancel
      </CButton>
      <CButton color="primary" @click="proc()">Process</CButton>
    </CModalFooter>
  </CModal>
  <!-- Modal Detail Task -->
  </div>
</template>

<style>
.table-wrapper {
  max-height: 350px;
  overflow: auto;
}
.status {
  max-width: 130px;
}
/* .fixed_header{
    table-layout: fixed;
} */

/* .fixed_header tbody{
  display:block;
  overflow:auto;
  height:200px;
  width:100%;
}

.fixed_header thead tr{
  display:block;
  width: 100%;
}

.fixed_header th, .fixed_header td {
  text-align: left;
  width:100%;
} */
.table-fixed tbody {
    height: 300px;
    overflow-y: auto;
    width: 100%;
}

.table-fixed thead,
.table-fixed tbody,
.table-fixed tr,
.table-fixed td,
.table-fixed th {
    display: block;
}

.table-fixed tbody td,
.table-fixed tbody th,
.table-fixed thead > tr > th {
    float: left;
    position: relative;
}
.table-fixed thead > tr > th ::after {
   content: '';
        clear: both;
        display: block;
}

</style>

<script>
import router from '../../router'
import axios from 'axios'
import {reactive,onMounted,ref} from 'vue'
export default {
  name: 'TaskList',
  data() {
    return {
      tasks: [],
        visibleLiveDemo: false,
        modalAdd: false,
        modalAssign:false,
        modalDetail:false,
        account_number: '',
        anRekening: '',
        amount: '',
        mutation_id: '',
        bank_type: '',
        _id:'',
        taskTitle:'',
        taskRefNumber:'',
        taskAssigne:'',
        taskSlaTime:'',
        taskExpiredTime:'',
        taskStatus:'',
        taskCreatedBy:'',
        sel: [],
        work:'',
        taskStat:'',
        statFilter:'all'



    }
  },
  // watch() {
  //       socket.on('tasks created', (message) => {
  //       console.log('New message created', message);
  //       loadTask();
  //       console.log(tasks);

  //     });
  // },
  methods: {
    // filtersel(){
    //   if(this.statFilter!='') {
    //     console.log(this.statFilter);
    //   }
    // },
    additem(event){
      console.log(event.target.checked);
      console.log(event.target.value);
      if(event.target.checked)
      {
        this.sel.push(event.target.value);
      } else {
        this.sel.pop();
      }
    },
    updateWorker(){
      console.log(this.sel);
      this.sel.forEach(element => {
        axios.patch(`${process.env.VUE_APP_URL_API}/tasks/${element}`,{taskAssigne:this.work,taskStatus:'Assigned'},{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then(()=> {
      }).catch((err)=>{
        console.log(err);
      })
      });
      this.$swal('Saved','','success');
      //  router.go()
      document.querySelectorAll('input[type="checkbox]').forEach((item)=>{
              item.checked = false;
      });


    },
    clearAssign() {
      this.$swal({title:'Are Sure ?',icon:'info',showCancelButton:true,focusConfirm:false,confirmButtonText:'Yes, Sure',cancelButtonText:'Cancel'})
      .then((result)=> {
        if(result.isConfirmed) {
        this.sel.forEach(element => {
        axios.patch(`${process.env.VUE_APP_URL_API}/tasks/${element}`,{taskAssigne:'unassigne',taskStatus:'unassigne'},{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then(()=> {
       //
      }).catch((err)=>{
        console.log(err);
      })
      });
          this.$swal('Saved','','success');
          // router.go()
            document.querySelectorAll('input[type="checkbox]').forEach((item)=>{
              item.checked = false;
            });

        }
      })
    },
    process(account_number,anRekening,amount,mutation_id,bank_type,_id,taskAssigne,taskTittle,taskRefNumber,taskExpiredTime,taskCreatedBy,taskStatus)
    {
      this.modalDetail = true;
      this.account_number = account_number;
      this.anRekening = anRekening;
      this.amount = amount;
      this.mutation_id = mutation_id;
      this.bank_type = bank_type;
      this._id = _id;
      this.taskAssigne=taskAssigne;
      this.taskTittle=taskTittle;
      this.taskRefNumber=taskRefNumber;
      this.taskExpiredTime=taskExpiredTime;
      this.taskCreatedBy=taskCreatedBy;
      this.taskStatus = taskStatus;
    },
    proc(){
      this.$swal({title:'Are you sure ?',icon:'info',showCancelButton:true,focusConfirm:false,confirmButtonText:'Process',cancelButtonText:'Cancel'})
      .then((result)=>{
        if(result.isConfirmed) {
        // Update data
        axios.patch(`${process.env.VUE_APP_URL_API}/tasks/${this._id}`,{taskStatus:'processed'},{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
        })
          .then(()=> {
            //
          }).catch((err)=>{
            console.log(err);
        });

            // Feedback
            this.$swal('Saved','','success');
            // router.go()
            document.querySelectorAll('input[type="checkbox]').forEach((item)=>{
              item.checked = false;
            });

        }
      })
    },
    change(){
      alert('ok');
      // let check = document.getElementById("taskchekbox");
    },

  },
  watch() {
    loadTask(taskStats.value)
  },
  setup() {
    // let taskStat = ref([]);
    let searchTitt = ref([]);
    let taskStats = ref([]);
    let tasks = ref([]);
    let selected = ref([]);
    let users = ref({});
    let worker = reactive({
      username:''
    });
    let tsk = reactive({
      taskTittle:'',
      taskCreatedBy:window.localStorage.getItem('username'),
      taskRefNumber:'',
      taskSlaTime:'',
      taskExpiredTime:'',
      taskStatus:'Unassigned',
    });


    onMounted(()=> {

        socket.on('tasks created', (message) => {
        // console.log('New message created', message);
           loadTask(taskStats)

        // console.log(tasks);

      });
        socket.on('tasks updated', (message) => {
        // console.log('New message updated', message);
             loadTask(taskStats.value)

        // console.log(tasks);

      });
        socket.on('tasks patched', (message) => {
        // console.log('New message patched', message);
              loadTask(taskStats.value)


        // console.log(tasks);

      });
      // get data
      loadTask(taskStats.value)

      // get worker
      axios.get(`${process.env.VUE_APP_URL_API}/users?role=worker`,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      }).then((result)=> {
        // console.log(result.data);
        users.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });


    });
    //delete
    function destroy(id,index) {
      axios.delete(`${process.env.VUE_APP_URL_API}/users/${id}`,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then(()=> {
        tasks.value.splice(index,1);
        // router.push({
        //   name:'Users'
        // })
      }).catch((err)=>{
        console.log(err.response);
      })
    }


    // select function
    function getSelect(event) {
      // console.log(event.target.checked);
      // if(event.target.checked == false){
      //   selected.value.splice(1)
      // }
        this.sel.push(event.target.value)
      // selected.value.push(event.target.value);
    }
    //assigning function
    function assigning() {
      console.log(worker.username);

     for(let i in this.sel){
       console.log(this.sel[i]);
      // axios.patch(`http://localhost:3030/tasks/${selected[i]}`,{taskAssigne:worker.username},{
      //   headers: {
      //     Authorization:window.localStorage.getItem('accessToken')
      //   }
      // })
      // .then(()=> {
      //   router.push({
      //     name:'Tasks'
      //   })
      // }).catch((err)=>{
      //   console.log(err);
      // })
     }
    }
    function cek(status) {
      if(status == 'processed')
      {
        return 'danger';
      } else {
        return 'light';
      }
    }
    function cekCheck(assign){
      if(assign != 'unassigne') {
        return 'true';
      }
    }
    function loadTask(taskStat,searchTit) {
        console.log(taskStat)
    if(taskStats.value.length == 0) {
      console.log(true);
          axios.get(`${process.env.VUE_APP_URL_API}/tasks`,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then((result) => {
        console.log("hasil",result)
        tasks.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });
      // jika search title
      if(searchTitt.value.length!=0){
         console.log('hake');
          axios.get(`${process.env.VUE_APP_URL_API}/tasks?taskTittle=`+searchTit,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then((result) => {
        console.log("hasil",result)
        tasks.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });

      }
    } else {
      console.log(false)
      axios.get(`${process.env.VUE_APP_URL_API}/tasks?taskStatus=`+taskStat,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then((result) => {
        console.log(result)
        tasks.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });

       // jika search title
      if(searchTitt.value.length!=0){
         console.log('hake');
          axios.get(`${process.env.VUE_APP_URL_API}/tasks?taskStatus=`+taskStat+`&taskTittle=`+searchTit,{
        headers: {
          Authorization:window.localStorage.getItem('accessToken')
        }
      })
      .then((result) => {
        console.log("hasil",result)
        tasks.value = result.data;
      }).catch((err) =>{
        console.log(err.response);
      });

      }




    }

    }

    function filterSelect(e){
      if(e.target.options.selectedIndex > -1){
        let filter = e.target.options[e.target.selectedIndex].value;
        // console.log(filter)
       taskStats.value = e.target.options[e.target.selectedIndex].value

       loadTask(taskStats.value)
      // console.log(taskStats.value);
      //  console.log(statFilter);
      //  console.log(taskStats.value);
      //   axios.get(`${process.env.VUE_APP_URL_API}/tasks?taskStatus=${filter}`,{
      //   headers: {
      //     Authorization:window.localStorage.getItem('accessToken')
      //   }
      // })
      // .then((result) => {
      //   // console.log(result.data.data)
      //   tasks.value = result.data;
      // }).catch((err) =>{
      //   console.log(err.response);
      // });
      }
    }
    function searchTitle(e) {
      searchTitt.value = e.target.value
      // console.log(searchTit.value);
      loadTask(taskStats.value,searchTitt.value)

    }

    return {
      tasks,
      destroy,
      getSelect,
      selected,
      users,
      assigning,
      worker,
      tsk,
      cek,
      loadTask,
      cekCheck,
      taskStats,
      filterSelect,
      searchTitle
    }
  }
}
</script>
