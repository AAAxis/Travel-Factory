
<template>
<div class="w3-container">


  <div id="id01"  class="w3-modal">
    <div style="width:40%" class="w3-modal-content">
      <header class="w3-container w3-teal"> 
        <span onclick="document.getElementById('id01').style.display='none'" 
        class="w3-button w3-display-topright">&times;</span>
        <h2>Create Contact   <i class="fa fa-address-card-o"></i></h2>
      </header>
      <form  @submit.prevent="createContact">
      <div  class="w3-container">
    
      <input type="text"  placeholder="Name" id="name" v-model="newContact.name" required>
          <input type="text" placeholder="Company" id="company" v-model="newContact.company" required>
       <input type="text" placeholder="Address" id="address" v-model="newContact.address" required>
          <input type="text" placeholder="Image" id="image" v-model="newContact.image" required>
          <input type="text" placeholder="Job" id="job" v-model="newContact.job" required>

              <input
              name="phone" placeholder="Phone" id="phone"
              :class="{ valid: isValidphone, invalid: !isValidphone }"
              v-model="newContact.phone"
              type="text"
              @keyup="validatephone"/>

    <div class="invalid-warning" v-if="!isValidphone">
      Invalid phone number!
    </div>
     
        <button type="submit"  class="w3-button w3-black">Save</button><br>
      </div>


      </form>
 
 
    </div>
  </div>



      <div class="contact-card-container">
      <div v-for="(contact, index) in contacts" :key="index" class="contact-card">
        <div class="left"><img :src="contact.image" width="298" alt="Contact Image" />    <p style="text-align: center;">{{ contact.job}}</p>  </div> 
        <div v-if="editing !== index" class="contact-details">
          <div class="right">
          <h2><b>{{ contact.name }}</b></h2>
          <p>{{ contact.company }}</p>
          <p>{{ contact.address }}</p>
      
          <p>{{ contact.phone }}</p>
      </div>
        </div>
        <div v-else class="edit-contact-details">
          <input ref="name" v-model="contact.name" placeholder="Name" />
          <input ref="company" v-model="contact.company" placeholder="Company" />
          <input ref="job" v-model="contact.job" placeholder="Job" />
          <input ref="address" v-model="contact.address" placeholder="Address" />
          <input ref="phone" v-model="contact.phone" placeholder="Phone" />
          <input ref="image" v-model="contact.image" placeholder="Image URL" />
          <input ref="lat" v-model="contact.lat" placeholder="Lat" />
          <input ref="lng" v-model="contact.lng" placeholder="Lng" />
          <button @click="saveContact(index)">Save</button>
         
        </div>
        <div class="actions">
          <button @click="deleteContact(index)">
            <i class="fa fa-trash"></i>
          </button>
          <button @click="editContact(index)">
            <i class="fa fa-pencil"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
  <button style="margin-left: 50%; margin-top: 2rem; " onclick="document.getElementById('id01').style.display='block'" class="w3-button w3-black">+</button>

  
</template>




<script>
export default {
  data() {
    return {
    
      isValidphone: true,

      contacts: [
        { name: 'John Smith', company: 'Instagram', job: 'Graphic Designer', phone: '05445678944', address: 'Ben Gurion 35, Hertzlia',  lat: 0,
        lng: 0, image: '/Assets/john-smith.jpg' },
        { name: 'Alex Johnatan', company: 'Facebook', job: 'Backend Developer', phone: '05265432154', address: 'Igal Alon 10, Tel Aviv',  lat: 0,
        lng: 0, image: '/Assets/alex jonathan.jpg' },
        { name: 'Monica Smith', company: 'Hawlet packard', job: 'SEO', phone: '054595584554', address: 'King George 47, Tel Aviv',  lat: 0,
        lng: 0, image: '/Assets/monica smith.jpg' },
        { name: 'Michael Zimber', company: 'Twiter', job: 'Graphic Designer', phone: '05345678945', address: 'Herzel 65, Haifa',  lat: 0,
        lng: 0, image: '/Assets/michael zimber.jpg' },
        { name: 'Sandra Smith', company: 'Microsoft', job: 'Frontend Developer', phone: '05465432112', address: 'Habarbanel 51, Tel Aviv',  lat: 0,
        lng: 0, image: '/Assets/sandra smith.jpg' },
        { name: 'Joneth Carton', company: 'Facebook', job: 'IT Operations', phone: '052556155534', address: 'Trumpeldorf 77, Tel Aviv',  lat: 0,
        lng: 0, image: '/Assets/janeth carton.jpg' }
   
      ],
      editing: null,
      newContact: {
        name: '',
        phone: '', 
        address: '',
        company: '',
        job: '',
        image: '',
        lat: '',
        lng: ''
      }
    };
  },
  methods: {


    validatephone() {
      const validationRegex = /^\d{10}$/;
      if (this.newContact.phone.match(validationRegex)) {
        this.isValidphone = true;
      } else {
        this.isValidphone = false;
      }
    },
    deleteContact(index) {
      this.contacts.splice(index, 1);
    },
    editContact(index) {
      if (this.editing === index) {
        // If the clicked contact is already being edited, toggle off the edit mode
        this.editing = null;
      } else {
        // Otherwise, toggle on the edit mode for the clicked contact
        this.editing = index;
        const contact = this.contacts[index];
        this.$nextTick(() => {
          this.$refs['name'][index].focus();
          this.$refs['name'][index].value = contact.name;
          this.$refs['company'][index].value = contact.company;
          this.$refs['phone'][index].value = contact.phone;
          this.$refs['address'][index].value = contact.address;
          this.$refs['image'][index].value = contact.image;
          this.$refs['job'][index].value = contact.job;
          this.$refs['lat'][index].value = contact.lat;
          this.$refs['lng'][index].value = contact.lng;
        });
      }
    },
    saveContact(index) {
      this.editing = null;
    },

    createContact() {
      if (!this.isValidphone) {
    return;
  }
    
  const addressEncoded = encodeURIComponent(this.newContact.address);
  fetch(`https://maps.googleapis.com/maps/api/geocode/json?address=${addressEncoded}&key=AIzaSyABzslaHKfp7Hl1JZCMxAdUneiuu3O8QTQ`)
    .then((response) => response.json())
    .then((data) => {
      const location = data.results[0].geometry.location;
      this.newContact.lat = location.lat;
      this.newContact.lng = location.lng;
      this.contacts.push(this.newContact);
      this.newContact = {
        name: '',
        phone: '',
        address: '',
        image: '',
        company: '',
        job: '',
        
      };
   
    })
    .catch((error) => {
      console.error(error);
    });
  },
  watch: {
    editing(index) {
      if (index !== null) {
        this.$nextTick(() => {
          this.$refs['name'][index].focus();
        });
      }
    },
  },
 },
 }

</script>


