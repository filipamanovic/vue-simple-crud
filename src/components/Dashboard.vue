<template>
  <div id="dashboard" class="mt-4">
    <table class="table table-striped">
      <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
        <th scope="col">Department</th>
        <th scope="col">Position</th>
        <th scope="col">***</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="employee in employees">
        <td class="font-weight-bold">{{employee.employee_id}}</td>
        <td>{{employee.name}}</td>
        <td>{{employee.dept}}</td>
        <td>{{employee.position}}</td>
        <td>
          <router-link v-bind:to="{name: 'view-employee',
                      params:{employee_id: employee.employee_id}}"
                       class="secondary-content btn btn-outline-dark btn-sm">
            Details
          </router-link>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  import db from '../firebase/firebaseInit'
  export default {
    name: 'dashboard',
    data() {
      return {
        employees : []
      }
    },
    created () {
      db.collection('employees').orderBy('employee_id').get().then
      (querySnapshot => {
        querySnapshot.forEach(doc => {
          const data = {
            'id': doc.id,
            'employee_id': doc.data().employee_id,
            'name': doc.data().name,
            'dept': doc.data().dept,
            'position': doc.data().position
          }
          this.employees.push(data)
        })
      })
    }
  }
</script>
