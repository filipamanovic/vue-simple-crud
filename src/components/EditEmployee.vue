<template>
  <form class="mt-4 col-8" @submit.prevent="updateEmployee">
    <h4>Edit employee</h4>
    <div class="form-row">
      <div class="form-group col-md-3">
        <label>EmployeeID</label>
        <input type="text" class="form-control"
               v-model="employee.employee_id"
               id="employeeID" disabled>
      </div>
      <div class="form-group col-md-5">
        <label>Name</label>
        <input type="text" class="form-control" v-model="employee.name"
               pattern="[A-z\dđšžćčĐŠŽĆČ\s]{2,20}"
               title="Only characters and numbers 2 - 20">
      </div>
      <div class="form-group col-md-4">
        <label>Position</label>
        <input type="text" class="form-control" v-model="employee.position"
               pattern="[A-z\dđšžćčĐŠŽĆČ\s]{2,30}"
               title="Only characters and numbers 2 - 20">
      </div>
    </div>
    <div class="form-row">
      <div class="form-group col-md-8">
        <label>Email</label>
        <input type="email" class="form-control" v-model="employee.email" required>
      </div>
      <div class="form-group col-md-4">
        <label>Date of Birth</label>
        <input type="date" class="form-control" v-model="employee.dateOfBirth" required>
      </div>
    </div>
    <div class="form-group">
      <label>Department</label>
      <select class="form-control" @change="deptSelected($event)" v-model="employee.departments[0]" required>
        <option v-for="dept, key in departments" :key="key" :value="dept.deptName">{{dept.deptName}}</option>
      </select>
    </div>
    <div class="form-group" v-if="subDept1">
      <label>Sub department</label>
      <select class="form-control" @change="subDeptSelected($event)" v-model="employee.departments[1]" required>
        <option v-for="dept, key in subDept1Data" :key="key">{{dept.subDeptName}}</option>
      </select>
    </div>
    <div class="form-group" v-if="subDept2">
      <label>Sub sub department</label>
      <select class="form-control" @change="subSubSelected($event)" v-model="employee.departments[2]" required>
        <option v-for="dept, key in subDept2Data" :key="key">{{dept.subSubDeptName}}</option>
      </select>
    </div>
    <div class="form-row">
      <div class="form-group col-md-6">
        <button
          class="btn btn-outline-info btn-block"
          @click.prevent="addSkill"
          :disabled="employee.skills.length > 4">
          Add Skill
        </button>
      </div>
      <div class="form-group col-md-6" v-if="employee.skills.length > 0">
        <button class="btn btn-outline-warning btn-block"
                @click.prevent="removeSkill">
          Remove skill
        </button>
      </div>
    </div>
    <div class="form-row" v-for="(skill, index) in employee.skills" :key="index">
      <div class="form-group col-md-6">
        <label v-if="index==0">Choose skill</label>
        <select class="form-control" v-model="skill.skillName"
                required
                @change="onSelected($event)"
                @click="onFocus($event)">
          <option v-for="skillDemo in skills"
                  :value="skillDemo.skillName"
                  :disabled="!skillDemo.skillAvailable">
            {{skillDemo.skillName}}</option>
        </select>
      </div>
      <div class="form-group col-md-6">
        <label v-if="index==0">Skill value</label>
        <select class="form-control" v-model="skill.skillValue" required>
          <option v-for="mark in marks" :value="mark.markValue">{{mark.markValue}}</option>
        </select>
      </div>
    </div>
    <div class="form-row">
      <div class="form-group col-md-10">
        <button type="submit" class="btn btn-outline-dark btn-block">Update employee</button>
      </div>
      <div class="form-group col-md-2">
        <router-link v-bind:to="{name: 'view-employee',
                      params:{employee_id: employee.employee_id}}"
                     class="secondary-content btn btn-outline-dark btn-block">
          Back
        </router-link>
      </div>
    </div>
  </form>
</template>

<script>
  import db from '../firebase/firebaseInit'
  export default {
    name: 'edit-employee',
    data() {
      return {
        employee: {
          employee_id: '/',
          name: '',
          position: '',
          email: '',
          dateOfBirth: '',
          skills: [],
          departments: []
        },
        employee_id: '/',
        skills: [],
        marks: [],
        focusedSkill: '',
        departments: [],
        subDept1: false,
        subDept1Data: [],
        subDept2: false,
        subDept2Data: []
      }
    },
    beforeRouteEnter(to, from, next) {
      db.collection('employees').where('employee_id', '==', to.params.employee_id)
        .get().then(querySnapshot => {
        querySnapshot.forEach(doc => {
          next(vm => {
            vm.employee.employee_id = doc.data().employee_id;
            vm.employee.name = doc.data().name;
            vm.employee.department = doc.data().dept;
            vm.employee.position = doc.data().position;
            vm.employee.email = doc.data().email;
            vm.employee.dateOfBirth = doc.data().dateOfBirth;
            vm.employee.skills = doc.data().skills;
            vm.employee.departments = doc.data().departments
          })
        })
      });
    },
    created() {
      db.collection('skills').get().then(querySnapshot => querySnapshot.forEach(doc => {
        const data = {
          'skillName': doc.data().skillName,
          'skillAvailable': true
        };
        this.skills.push(data);
      }));
      db.collection('marks').get().then(querySnapshot => querySnapshot.forEach(doc => {
        const data = {
          'markValue': doc.data().markValue
        };
        this.marks.push(data)
      }));
      db.collection('departments').get().then
      (querySnapshot => {
        querySnapshot.forEach(doc => {
          this.departments.push(doc.data());
        })
      });
    },
    watch: {
      '$router': 'fetchData'
    },
    methods: {
      fetchData () {
        db.collection('employees').where('employee_id', '==', this.$route.params.employee_id)
          .get().then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.employee.employee_id = doc.data().employee_id;
            this.employee.name = doc.data().name;
            this.employee.department = doc.data().dept;
            this.employee.position = doc.data().position;
            this.employee.email = doc.data().email;
            this.employee.dateOfBirth = doc.data().dateOfBirth;
            this.employee.skills = doc.data().skills;
            this.employee.departments = doc.data().departments;
          })
        });
      },
      updateEmployee() {
        db.collection('employees').where('employee_id', '==', this.$route.params.employee_id)
          .get().then(querySnapshot => {
          querySnapshot.forEach(doc => {
            doc.ref.update({
              name: this.employee.name,
              position: this.employee.position,
              email: this.employee.email,
              dateOfBirth: this.employee.dateOfBirth,
              skills: this.employee.skills,
              departments: this.employee.departments
            }).then(() => {
              this.$router.push({name: 'view-employee',
                params: {employee_id: this.employee.employee_id}})
            })
          })
        })
      },
      addSkill() {
        this.employee.skills.push({skillName: 'Choose skill..', skillValue: 'Skill value..'});
        // console.log(this.skills);
      },
      removeSkill() {
        let skillName = (this.employee.skills.pop().skillName);
        if (skillName !== 'Choose skill..') {
          this.skills.find(s => s.skillName === skillName).skillAvailable = true
        }
      },
      onSelected(event){
        if (this.focusedSkill !== '') {
          this.skills.find(s => s.skillName === this.focusedSkill).skillAvailable = true;
        }
        this.skills.find(s => s.skillName === event.target.value).skillAvailable = false;
      },
      onFocus(event){
        this.focusedSkill = event.target.value;
        for (let item of this.employee.skills) {
          if (item.skillName !== 'Choose skill..') {
            this.skills.find(s => s.skillName === item.skillName).skillAvailable = false;
          }
        }
      },
      deptSelected(event) {
        if (event.target.value !== undefined) {
          this.subDept1Data = this.departments.find(d => d.deptName === event.target.value).subDept;
          this.subDept2 = false;
          this.employee.departments[0] = event.target.value;
          this.subDept1 = true;
        }
      },
      subDeptSelected(event) {
        this.employee.departments[1] = event.target.value;
        this.subDept2Data = this.subDept1Data.find(d => d.subDeptName === event.target.value).subSubDeptName;
        if (this.subDept2Data !== undefined) {
          this.subDept2 = true;
        } else {
          this.subDept2 = false;
        }
      },
      subSubSelected($event) {
        this.employee.departments[2] = event.target.value;
      }
    }
  }
</script>
