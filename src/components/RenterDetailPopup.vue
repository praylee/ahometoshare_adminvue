<template>
  <v-layout row justify-center>
    <v-snackbar v-model="snackbar" :timeout="4000" top :color="snackbarColor">
      <span>{{snackbarText}}</span>
      <v-btn flat color="white" @click="snackbar=false">close</v-btn>
    </v-snackbar>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Renter Profile</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="renter.email" label="Email*" required readonly></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="renter.firstName" label="First Name*" required clearable></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="renter.lastName" label="Last Name*" required clearable></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="renter.dateOfBirth" label="Year Of Birth*" required number clearable></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-select :items="genders" label="Gender*" v-model="renter.gender" required clearable></v-select>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="renter.phone" label="Phone"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-checkbox v-model="renter.criminalityCheck" label="Criminality Check"></v-checkbox>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-select item-text="text" v-model="renter.student" item-value="value" :items="options" label="Student" clearable></v-select>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-select item-text="text" v-model="renter.employed" item-value="value" :items="options" label="Employed" clearable></v-select>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-select item-text="text" v-model="renter.smoker" item-value="value" :items="options" label="Smoker" clearable></v-select>
              </v-flex>
            </v-layout>
          </v-container>
          <small>*indicates required field</small>
        </v-card-text>
        <v-card-actions>
          <ConfirmDeleteRenterPopup/>
          <v-btn color="blue darken-1" flat @click="dialog = false">Close</v-btn>
          <v-btn color="blue darken-1" flat @click="saveRenter">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-layout>
</template>

<script>
  import {userDetailBus} from '../main'
  import ConfirmDeleteRenterPopup from '../components/ConfirmDeleteRenterPopup'

  export default {
    components: {
      ConfirmDeleteRenterPopup
    },
    data () {
        return {
            snackbar:false,
            snackbarColor:"success",
            snackbarText: "Operation Success",
            dialog: false,
            options: [
                {
                text:'NO',
                value:false
                },
                {
                text:'YES',
                value:true
                }
            ],
            genders: [
                {
                text:'Male',
                value:0
                },
                {
                text:'Female',
                value:1
                },
                {
                text:'Unknown',
                value:2
                }
            ],
            renter:{
                id:null,
                email:'',
                firstName:'',
                lastName:'',
                dateOfBirth:'',
                gender:null,
                criminalityCheck:false,
                phone:null,
                student:null,
                employed:null,
                smoker:null,
                referralSource:null
            }
        }
    },
    methods:{
      saveRenter(){
        let url = "admin/updateRenter";
        this.api.post(url,this.renter).then(data =>{
            this.snackbar = true;
            userDetailBus.$emit("renterUpdated");
            this.dialog = false;
        });
      },
      deleteRenter(){
        let url ="admin/deleteRenter";
        this.api.post(url,{
          renterId: this.renter.id
        }).then(data => {
          userDetailBus.$emit('hostUpdated',data);
          this.snackbar = true;
          this.clearRenter();
        })
      },
      clearRenter(){
      for (var key in this.renter ) {
          this.renter[key] = null;
        }
        this.dialog = false;
      }
    },
    created(){
      userDetailBus.$on('showRenterDetail',renter =>{
      this.dialog = true;
      let url="admin/getRenterDetailById";
      this.api.get(url,{
          params:{
              id:renter.id
          }
          }).then(data =>{
          for(let key in this.renter){
              this.renter[key] = data.data[key];
          }
      });
      userDetailBus.$on('deleteRenter', ()=>{
        this.deleteRenter();
      });
    });
  }
  }
</script>
