<template>
    <div id="customer">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                      <h3 class="card-title" style="margin-bottom: 0 !important">Customers</h3>
                      <div class="card-tools">
                        <button class="btn btn-primary" @click="reload()" style="position: absolute; top: .5rem; right: 1rem">
                          Relead <i class="fas fa-sync pl-1"></i>
                        </button>
                      </div>
                    </div>

                    <div class="card-body">
                      <div action="" class="mb-3">
                        <div class="row">

                          <div class="col-2 pl-5">
                            <strong class="">Search By : </strong>
                          </div>

                          <div class="col-3">
                            <select id="feilds" class="form-control" v-model="queryFeild">
                              <option value="name">Name</option>
                              <option value="email">Email</option>
                              <option value="phone">Phone</option>
                              <option value="address">Address</option>
                              <option value="total">Total</option>
                            </select>
                          </div>

                          <div class="col-7">
                            <input v-model="query" type="text" class="form-control" placeholder="Search">
                          </div>

                        </div>
                      </div>
                        <div class="table-responsive">
                            <table class="table table-hover table-bordered table-striped">
                                <thead>
                                    <tr>
                                      <th scope="col">#</th>
                                      <th scope="col">Name</th>
                                      <th scope="col">Email</th>
                                      <th scope="col">Phone</th>
                                      <th scope="col">Total</th>
                                      <th scope="col" class="text-center">Action</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-show="customers.length" v-for="(customer, index) in customers" :key="customer.id">
                                      <th scope="row">{{ index + 1 }}</th>
                                      <td>{{ customer.name }}</td>
                                      <td>{{ customer.email }}</td>
                                      <td>{{ customer.phone }}</td>
                                      <td>{{ customer.total }}</td>
                                      <td class="text-center">
                                        <button type="button" class="btn btn-info btn-sm">
                                          <i class="fas fa-eye"></i>
                                        </button>
                                        <button type="button" class="btn btn-primary btn-sm">
                                          <i class="fas fa-edit"></i>
                                        </button>
                                        <button type="button" class="btn btn-danger btn-sm">
                                          <i class="fas fa-trash-alt"></i>
                                        </button>
                                      </td>
                                    </tr>
                                    <tr v-show="!customers.length">
                                      <td colspan="6" >
                                        <div class="alert alert-danger" role="alert"> 
                                          Sorry! No data found.
                                        </div>
                                      </td>
                                    </tr>
                                </tbody>
                            </table>
                            <pagination v-if="pagination.last_page > 1"
                              :pagination="pagination"
                              :offset="5"
                              @paginate="query === '' ? getData() : searchData()"
                              ></pagination>
                        </div>
                   </div>
                </div>
            </div>
        </div>
        <vue-snotify></vue-snotify>
        <vue-progress-bar></vue-progress-bar>
    </div>
</template>

<script>
    export default {
      data(){
        return{
          query: '',
          queryFeild: 'name',
          customers: [],
          pagination:{
            current_page: 1,
          }
        }
      },
      watch:{
        query:function (newQ, old) {
          if (newQ === '') {
            this.getData();
          }
          else{
            // console.log(newQ) // to check saerch data
            this.searchData()
          }
        }
      },
      mounted() {
          console.log('Component mounted.')
          this.getData();
      },
      methods: {
        getData(){  
          this.$Progress.start()
          axios.get('/api/customers?page='+this.pagination.current_page)
          .then(response =>  {
            this.customers = response.data.data
            this.pagination = response.data.meta
            this.$Progress.finish()
          })
          .catch(e => {
            console.log(e)
            this.$Progress.fail()
          })
        },
        searchData(){
          this.$Progress.start()
          axios.get('/api/search/customers/'+this.queryFeild+'/'+this.query+'?page='+this.pagination.current_page)
          .then(response => {
            this.customers = response.data.data
            this.pagination = response.data.meta
            this.$Progress.finish()
          })
          .catch(e => {
            console.log(e)
            this.$Progress.fail()
          })
        },
        reload(){
          this.getData()
          this.query = ''
          this.queryFeild = 'name'
          this.$snotify.success('Data Successfully Refresh','Success')
        }
      }
    }
</script>
