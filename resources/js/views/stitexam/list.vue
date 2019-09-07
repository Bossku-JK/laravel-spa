<template>
 <b-container fluid>
    <div class="btn-wrapper">
      <router-link to="/stitexam/create" class="btn btn-primary btn-sm">New</router-link>
    
   
    </div>

     <!-- User Interface controls -->
    <b-row>
      <b-col md="6" class="my-1">
        <b-form-group label-cols-sm="3" label="Filter" class="mb-0">
          <b-input-group>
            <b-form-input v-model="filter" placeholder="Type to Search"></b-form-input>
            <b-input-group-append>
              <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-form-group>
      </b-col>

      <b-col md="6" class="my-1">
        <b-form-group label-cols-sm="3" label="Sort" class="mb-0">
          <b-input-group>
            <b-form-select v-model="sortBy" :options="sortOptions">
              <option slot="first" :value="null">-- none --</option>
            </b-form-select>
            <b-form-select v-model="sortDesc" :disabled="!sortBy" slot="append">
              <option :value="false">Asc</option> <option :value="true">Desc</option>
            </b-form-select>
          </b-input-group>
        </b-form-group>
      </b-col>

      <b-col md="6" class="my-1">
        <b-form-group label-cols-sm="3" label="Sort direction" class="mb-0">
          <b-form-select v-model="sortDirection">
            <option value="asc">Asc</option>
            <option value="desc">Desc</option>
            <option value="last">Last</option>
          </b-form-select>
        </b-form-group>
      </b-col>

      <b-col md="6" class="my-1">
        <b-form-group label-cols-sm="3" label="Per page" class="mb-0">
          <b-form-select v-model="perPage" :options="pageOptions"></b-form-select>
        </b-form-group>
      </b-col>
    </b-row>

 <!-- Main table element -->
    <b-table
      show-empty
      stacked="md"
       :bordered="bordered"
      :items="stitexams"
      :fields="fields"
      :current-page="currentPage"
      :per-page="perPage"
      :filter="filter"
      :sort-by.sync="sortBy"
      :sort-desc.sync="sortDesc"
      :sort-direction="sortDirection"
      @filtered="onFiltered"
    >
     <template slot="index" slot-scope="data">
        {{ data.index + 1 }}
      </template>
        <template slot="th_explace" slot-scope="data">
        
                <b-img
                thumbnail
                fluid
                width="50"
                v-bind:src="'/png250px/' + data.item.th_country"
                alt="Image 1"
              ></b-img> 
              {{ data.item.th_explace }}
      </template>
      <template slot="actions" slot-scope="stitexam">
       
             
          <b-button variant="outline-success" :to="`/stitexam/show/${stitexam.item.edu_year}/${stitexam.item.id_explace}/${stitexam.item.id_level}`" :key="$route.fullPath">View</b-button>
             <b-button variant="outline-primary" :to="`/stitexam/edit/${stitexam.item.edu_year}/${stitexam.item.id_explace}/${stitexam.item.id_level}`">edit</b-button>
            <b-button variant="outline-danger">Danger</b-button>
      </template>

    </b-table>

    <b-row>
      <b-col md="12" class="my-1">
        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
        
          align="fill"
         
        >
           <span class="text-success" slot="first-text">หน้าแรก</span>
      <span class="text-danger" slot="prev-text">หน้าก่อนหน้านี้</span>
      <span class="text-warning" slot="next-text">หน้าต่อไป</span>
      <span class="text-info" slot="last-text">หน้าสุดท้าย</span>
      <div slot="ellipsis-text">
        <b-spinner small type="grow"></b-spinner>
        <b-spinner small type="grow"></b-spinner>
        <b-spinner small type="grow"></b-spinner>
      </div>
      <span slot="page" slot-scope="{ page, active }">
        <b v-if="active">{{ page }}</b>
        <i v-else>{{ page }}</i>
      </span>
        </b-pagination>
      </b-col>
    </b-row>
    {{totalRows}}

 </b-container>
  
</template>

<script>

import store from "../../store/index";
import { mapGetters, mapActions } from "vuex";
export default {
  data(){
    return {
 fields: [
     'index',
          { key: 'stit1', label: 'ส่งสอบ', sortable: true, class: 'text-center'},
          { key: 'stit2', label: 'ขาดสอบ', sortable: true, class: 'text-center' },
          { key: 'stit3', label: 'คงสอบ', sortable: true, class: 'text-center' },
          { key: 'stit4', label: 'สอบบได้', sortable: true, class: 'text-center' },
          { key: 'stit5', label: 'สอบตก', sortable: true, class: 'text-center' },
          { key: 'level', label: 'ระดับชั้น', sortable: true },
          { key: 'edu_year', label: 'ปีการศึกษา', sortable: true },
          { key: 'th_explace', label: 'สนามสอบ', sortable: true },
          { key: 'actions', label: 'Actions' }
        ],
            bordered: true,
        totalRows: 1,
        currentPage: 1,
        perPage: 5,
        pageOptions: [5, 10, 15,25,50,100],
        sortBy: null,
        sortDesc: false,
        sortDirection: 'asc',
        filter: null,
        infoModal: {
          id: 'info-modal',
          title: '',
          content: ''
        },
        title:{}
    }
  },
  name: "list",
computed: {
      // totalRowss() {
      //   return this.totalRows = this.stitexams.length
      // },

     sortOptions() {
        // Create an options list from our fields
        return this.fields
          .filter(f => f.sortable)
          .map(f => {
            return { text: f.label, value: f.key }
          })
      },
    ...mapGetters({
      stitexams: "stitexam/stitexams",
      currentUser: "register/currentUser"
    })
    //              test() {
    //   axios.get('/api/stitexam')
    //   .then((response) => {
    //       console.log(response.data)
    //          });
    //    },
    // stitexams() {
    //     // return this.$store.state.stitexams;
    //      return this.$store.getters.stitexams
    // }
  },
   mounted() {

     // รอให้ function อื่นๆ ทำงานเสร้จก่อนใช้ function นี้
 

      // this.totalrow()
    //   .then((response) => {
    //       console.log(response.data)
    //          });
    //    },
    // this.$store.dispatch("register/getUser");
    this.$store.dispatch("stitexam/getstitexams").then(response => {
      this.$bar.finish();
      this.totalRows = response.length
      console.log()
    })   .catch(error => {
   console.log(error);
    // localStorage.removeItem('token')
        // commit('register/errorGetuser')
          // this.$router.push("/login");
   
       });

  
    
  },
  methods:{
//     totalrow(){
//        this.totalRows = this.stitexams.length
// console.log(this.totalRows)
//     },
    info(item, index, button) {
        this.infoModal.title = `Row index: ${index}`
        this.infoModal.content = JSON.stringify(item, null, 2)
        this.$root.$emit('bv::show::modal', this.infoModal.id, button)
      },
      resetInfoModal() {
        this.infoModal.title = ''
        this.infoModal.content = ''
      },
      onFiltered(filteredItems) {
        // Trigger pagination to update the number of buttons/pages due to filtering
        this.totalRows = filteredItems.length
        this.currentPage = 1
      }
       
  },
  
};
</script>