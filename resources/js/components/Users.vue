<template>
    <div class="container">
        <div class="card mt-5">
            <div class="card-header">
                <h3 class="card-title">Users Table</h3>
                <div class="card-tools">
                    <button
                        type="button"
                        class="btn btn-success"
                        data-toggle="modal"
                        data-target="#addUser"
                    >
                        Add User <i class="fa fa-user-plus"></i>
                    </button>
                </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
                <table class="table table-hover text-nowrap">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>User</th>
                            <th>Email</th>
                            <th>Bio</th>
                            <th>Type</th>
                            <th>Registered At</th>
                            <th>Modify</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for='user in users' :key='user.id'>
                            <td>{{user.id}}</td>
                            <td>{{user.name}}</td>
                            <td>{{user.email}}</td>
                            <td>{{user.bio}}</td>
                            <td>{{user.type | upText}}</td>
                            <td>{{user.created_at | myDate}}</td>
                            <td> <a href="#">

                              <i class="fa fa-edit "></i>
                              </a>
                              /
                            <a href="#" @click="deleteUser(user.id)">  
                              <i class="fa fa-trash"></i>

                            </a>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <!-- /.card-body -->
        </div>

        <div
            class="modal fade"
            id="addUser"
            tabindex="-1"
            aria-labelledby="exampleModalLabel"
            aria-hidden="true"
        >
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">
                            Add User
                        </h5>
                        <button
                            type="button"
                            class="close"
                            data-dismiss="modal"
                            aria-label="Close"
                        >
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <form @submit.prevent="createUser">
                        <div class="modal-body">
                            <div class="form-group">
                                <input
                                    placeholder="Name"
                                    v-model="form.name"
                                    type="text"
                                    name="name"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('name')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="name"
                                ></has-error>
                            </div>
                            <div class="form-group">
                                <input
                                    placeholder="Email"
                                    v-model="form.email"
                                    type="mail"
                                    name="email"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('email')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="email"
                                ></has-error>
                            </div>
                            <div class="form-group">
                                <textarea
                                    placeholder="Bio(Optional)"
                                    v-model="form.bio"
                                    type="text"
                                    name="bio"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('bio')
                                    }"
                                />
                                <has-error :form="form" field="bio"></has-error>
                            </div>
                            <div class="form-group">
                                <select
                                    v-model="form.type"
                                    type="text"
                                    name="type"
                                    id="type"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('type')
                                    }"
                                >
                                    <has-error
                                        :form="form"
                                        field="type"
                                    ></has-error>
                                    <option value="">Select User Role </option>
                                    <option value="admin">Admin</option>
                                    <option value="author">Author</option>
                                    <option value="user">User</option>
                                </select>
                            </div>

                            <div class="form-group">
                                <input
                                    placeholder="password"
                                    v-model="form.password"
                                    type="password"
                                    name="password"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has(
                                            'password'
                                        )
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="password"
                                ></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button
                                type="button"
                                class="btn btn-danger"
                                data-dismiss="modal"
                            >
                                Close
                            </button>
                            <button type="submit" class="btn btn-primary">
                                Create
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {

              users:{},
            form: new Form({
                name: "",
                email: "",
                password: "",
                type: "",
                bio: "",
                photo: ""
            })
        };
    },
    methods: {
        createUser() {
            this.$Progress.start()
            Light.$emit('UserAdded')
          this.form.post("api/user").then(()=>{
              Light.$emit("userCreated")
              $('#addUser').modal('hide')

                    Toast.fire({
                        icon: 'success',
                        title: 'User Created successfully'
                            })
                      this.$Progress.finish()
             })
                    .catch(()=>{
                        this.$Progress.fail()
          })
              
        },
        loadUsers(){
                    axios.get('api/user').then(({data})=> (this.users =data.data) );
        },
                 deleteUser(id){

                                Swal.fire({
                                        title: 'Are you sure?',
                                        text: "You won't be able to revert this!",
                                        icon: 'warning',
                                        showCancelButton: true,
                                        confirmButtonColor: '#3085d6',
                                        cancelButtonColor: '#d33',
                                        confirmButtonText: 'Yes, delete it!'
                                        }).then((result) => {

                                                    if(result.value) {
                                                        this.form.delete('api/user/'+id).then(()=>{

                                                                    Swal.fire(
                                                                    'Deleted!',
                                                                    'Your file has been deleted.',
                                                                    'success'
                                                                    )
                                                                Light.$emit("userDeleted")
                                                            })
                                                            .catch(()=>{
                                                                Swal.fire('Failed' ,"Something went wrong","warning")
                                                                    });
                                                         }
                                                 })
                                
                                            
                        }

    },

    created(){


          this.loadUsers()

          Light.$on('userCreated', ()=> {

              this.loadUsers();

          })
          Light.$on("userDeleted" ,()=>{

                this.loadUsers();

          })
    }
};
</script>

<style></style>
