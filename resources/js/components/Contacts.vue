<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <h1>Add Contact</h1>
                <hr>
                <form @submit.prevent="edit ? updateContact(contact.id) : createContact()">
                  <div class="form-group">
                    <label for="Name">Name</label>
                    <input v-model="contact.name" type="text" name="name" class="form-control" placeholder="Enter name">
                  </div>
                  <div class="form-group">
                    <label for="exampleInputEmail1">Email address</label>
                    <input v-model="contact.email" type="email" class="form-control" placeholder="Enter email">
                  </div>
                  <div class="form-group">
                    <label for="exampleInputPhone">Phone</label>
                    <input v-model="contact.phone" type="phone" class="form-control" placeholder="Phone">
                  </div>
                  <div class="form-group">
                    <button v-show="!edit" type="submit" class="btn btn-primary">New Contact</button>
                    <button v-show="edit" type="submit" class="btn btn-primary">Update Contact</button>
                  </div>
                </form>

                <h1>Contacts List</h1>
                <hr>
                <div v-for="contact in contacts" class="card">
                    <div class="card-header">{{ contact.id }} - {{ contact.name }}</div>

                    <div class="card-body">
                        <p>Email: {{ contact.email }}</p>
                        <p>Phone: {{ contact.phone }}</p>
                        <button @click="showContact(contact.id)" class="btn btn-info btn-sm">Edit</button>
                        <button @click="deleteContact(contact.id)" class="btn btn-danger btn-sm">Delete</button>
                    </div>  
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                edit: false,
                contacts: [],
                contact: {
                    id: '',
                    name: '',
                    email: '',
                    phone: ''
                }
            }
        },
        methods: {
            createContact(){
                //let self = this;
                let params = Object.assign({}, this.contact);
                axios.post('api/contact/store', params)
                    .then(() => {
                        this.contact.name = '';
                        this.contact.email = '';
                        this.contact.phone = '';
                        this.edit = false;
                        this.fetchContactList();
                    }).catch((error) => {
                        console.log(error)
                    });
            },
            fetchContactList(){
                axios.get('api/contacts')
                    .then((response) => {
                        console.log(response.data);
                        this.contacts = response.data;
                    }).catch((error) => {
                        console.log(error)
                    })
            },
            updateContact(id){
                let params = Object.assign({}, this.contact);
                axios.patch('api/contact/' + id, params)
                    .then(() => {
                        this.contact.name = '';
                        this.contact.email = '';
                        this.contact.phone = '';
                        this.edit = false;
                        this.fetchContactList();
                    }).catch((error) => {
                        console.log(error)
                    });
            },
            showContact(id){
                //let self = this;
                axios.get('api/contact/' + id)
                    .then(response => {
                        this.contact.id = response.data.id;
                        this.contact.name = response.data.name;
                        this.contact.email = response.data.email;
                        this.contact.phone = response.data.phone;
                    })
                    this.edit = true;
            },
            deleteContact(id){
                axios.delete('api/contact/' + id)
                    .then(response => {
                        this.fetchContactList();
                    }).catch(error => {
                        console.log(error);
                    });
            }

        },
        mounted() {
            console.log('Component mounted.')
            this.fetchContactList();
        }
    }
</script>
