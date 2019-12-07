<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Example Component</div>

                    <div class="card-body">
                        <button @click="showUserModal()" class="btn btn-success">Add new User</button><br><br>

                        <table class="table">
                            <thead>
                                <th>ID</th>
                                <th>Name</th>
                                <th>Email</th>
                                <th>Actions</th>
                            </thead>
                            <tbody>
                                <tr v-for="(user, index) in users" :key="user.id">
                                    <td>{{user.id}}</td>
                                    <td>{{user.name}}</td>
                                    <td>{{user.email}}</td>
                                    <td>
                                        <button @click="showUser(index)" class="btn btn-sm btn-info">Edit User</button>
                                        <button @click="deleteUser(index)" class="btn btn-sm btn-danger">Delete User</button>
                                        <button @click="showContacts(user.id)" class="btn btn-sm btn-primary">View Contact Numbers</button>
                                    </td>
                                    
                                </tr>
                            </tbody>
                        </table>


                    </div>
                </div>
            </div>
        </div>
        <!--modal-->
        <div id="addusermodal" class="modal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">Add New User</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">

                        <form>
                            <div class="form-group">
                                <label for="exampleInputEmail1">Full Name </label>
                                <input v-model="name" type="email" class="form-control" id="exampleInputEmail1"
                                    aria-describedby="emailHelp" placeholder="Enter Full Name">

                            </div>
                            <div class="form-group">
                                <label for="exampleInputEmail1">Email address </label>
                                <input v-model="email" type="email" class="form-control" id="exampleInputEmail1"
                                    aria-describedby="emailHelp" placeholder="Enter email">
                                <small id="emailHelp" class="form-text text-muted">We'll never share your email with
                                    anyone else.</small>
                            </div>
                            <div class="form-group">
                                <label for="exampleInputPassword1">Password </label>
                                <input v-model="password" type="password" class="form-control"
                                    id="exampleInputPassword1" placeholder="Password">
                            </div>

                        </form>


                    </div>
                    <div class="modal-footer">
                        <button @click="submitNewUser()" class="btn btn-primary">Save changes</button>
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>
                </div>
            </div>
        </div>


        <!--view contacts modal-->
        <div id="viewusercontactmodal" class="modal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">View Contacts</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <ul>
                            <li v-for="user_contact in user_contacts" :key="user_contact.id">
                                {{user_contact.contact_number}}
                            </li>
                        </ul>
                      
                        <br>
                        <label for="text">Contact Number</label>
                        <input v-model="contactnumber" type="text" class="form-control">
                    </div>
                    <div class="modal-footer">
                        <button @click="submitContactNumber" class="btn btn-primary">Add Contact</button>
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>
                </div>
            </div>
        </div>

        <!--update modal-->
        <div id="updateusermodal" class="modal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">Edit User</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">

                        <form>
                            <div class="form-group">
                                <label for="exampleInputEmail1">Full Name </label>
                                <input v-model="user.name" type="email" class="form-control" id="exampleInputEmail1"
                                    aria-describedby="emailHelp" placeholder="Enter Full Name">

                            </div>
                            <div class="form-group">
                                <label for="exampleInputEmail1">Email address  </label>
                                <input v-model="user.email" type="email" class="form-control" id="exampleInputEmail1"
                                    aria-describedby="emailHelp" placeholder="Enter email">
                                <small id="emailHelp" class="form-text text-muted">We'll never share your email with
                                    anyone else.</small>
                            </div>
                            <div class="form-group">
                                <label for="exampleInputPassword1">Password  </label>
                                <input v-model="user.password" type="password" class="form-control"
                                    id="exampleInputPassword1" placeholder="Password">
                            </div>

                        </form>


                    </div>
                    <div class="modal-footer">
                        <button @click="submitUpdatedUser()" class="btn btn-primary">Save changes</button>
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
    export default {
        mounted() {
            this.getUsers();
        },
        data: () => ({
            users: '',
            name: '',
            email: '',
            password: '',
            user: [],
            user_contacts: [],
            contactnumber: '',
            user_contact_id: '',
        }),
        methods: {
            submitContactNumber(){
                axios.post('/contacts',{
                    user_id:this.user_contact_id,
                    contactnumber:this.contactnumber
                }).then(response =>{
                    $('#viewusercontactmodal').modal('hide')
                    this.contactnumber=""
                })
            },
            showContacts(id){
                this.user_contact_id=id;
                axios.get('/contacts/' +id, {
                    id: id,
                }).then(response => {
                    this.user_contacts =response.data.contacts
                    $('#viewusercontactmodal').modal('show')
                })
            },
            deleteUser(index){
                axios.delete('/users/'+this.users[index].id,{
                    id:this.users[index].id,
                }).then(response => {
                    this.getUsers();
                    alert(JSON.stringify(response.data.message));
                })
            },
            submitUpdatedUser(){
                axios.patch('/users/' +this.user.id, {
                    id: this.user.id,
                    name: this.user.name,
                    email: this.user.email,
                    password: this.user.password,
                }).then(response => {
                    this.getUsers();
                    $('#updateusermodal').modal('hide');
                })
            },
            showUser(index) {
                this.user = this.users[index];
                 $('#updateusermodal').modal('show');
            },
            submitNewUser() {
                axios.post('/users', {
                    name: this.name,
                    email: this.email,
                    password: this.password,
                }).then(response => {
                    this.getUsers();
                    $('#addusermodal').modal('hide');
                })

            },
            showUserModal() {
                $('#addusermodal').modal('show');

            },
            getUsers() {
                axios.get('/users/')
                    .then(response => {
                        this.users = response.data.users;
                        //alert(JSON.stringify(this.users));
                    })
            },
        },

    }

</script>
