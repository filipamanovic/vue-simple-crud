<template>
  <div id="new-employee">
    <h3>New employee</h3>
    <div class="row">
      <form @submit.prevent="saveEmployee" class="col s12">
        <div class="row">
          <div class="input-field col-12">
            <input type="text" v-model="employee_id" required/>
            <label>Employee ID#</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col-12">
            <input type="text" v-model="name"
                   required
                   pattern="[A-z\dđšžćčĐŠŽĆČ\s]{2,20}"
                   title="Only characters and numbers 2 - 20"/>
            <label>Name</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col-12">
            <input type="text" v-model="dept"
                   required
                   pattern="[A-z\dđšžćčĐŠŽĆČ\s\d]{2,40}"
                   title="Only characters and numbers 2 - 40"/>
            <label>Department</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col-12">
            <input type="text" v-model="position"
                   required
                   pattern="[A-z\dđšžćčĐŠŽĆČ\s\d]{2,40}"
                   title="Only characters and numbers 2 - 40"/>
            <label>Position</label>
          </div>
        </div>
        <button type="submit" class="btn">Save</button>
        <router-link to="/" class="btn grey">Cancel</router-link>
      </form>
    </div>
  </div>
</template>

<script>
  import db from '../firebase/firebaseInit'
  export default {
    name: 'new-employee',
    data() {
      return {
        employee_id: null,
        name: null,
        dept: null,
        position: null
      }
    },
    methods: {
      saveEmployee () {
        db.collection('employees').add({
          employee_id: this.employee_id,
          name: this.name,
          dept: this.dept,
          position: this.position
        }).then(docRef => {
          this.$router.push('/')
        }).catch(error => console.log(error))

      }
    }
  }
</script>
